//chatcjsaga

/*

To change this license header, choose License Headers in Project Properties.
To change this template file, choose Tools | Templates
and open the template in the editor. */
package chatt;

import java.awt.*;
import java.awt.event.*;
import java.io.*;
import java.net.*;
//import java.util.Scanner;
import java.util.logging.*;
import javax.swing.*;

/**
 * *
 *
 * @author Cj
 */
public class Chatt {

    public static void main(String[] args) {
        /* Client ourClient = new Client(); Server ourServer = new Server(); ourClient.createSocket(); ourServer.createSocket(); */
        boolean isConnected = true;
        if (isConnected == false) {
            try {
                Server ourServer = new Server();
                //System.out.println("main2");
            } catch (NullPointerException ex) {
                Logger.getLogger(Chatt.class.getName()).log(Level.SEVERE, null, ex);
            }
        } else {
            try {
                Client ourClient = new Client();
                //System.out.println("main1");
            } catch (NullPointerException ex) {
                Logger.getLogger(Chatt.class.getName()).log(Level.SEVERE, null, ex);
            }
        }
        //System.out.println("main");

    }
}

class ChatWindow extends JFrame implements ActionListener {

    private final int FRAME_WIDTH = 450;
    private final int FRAME_HEIGHT = 250;

    private final int AREA_ROWS = 10;
    private final int AREA_COLUMNS = 50;

    private JLabel chattLabel;
    JTextField chattField;
    private JButton chattButton;
    JTextArea chattArea;
    JFrame frame;
    ChatParticipent ourParticipent;

    public ChatWindow(ChatParticipent theParticipent) {
        ourParticipent = theParticipent;
        JFrame frame = new JFrame("Chatt");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setPreferredSize(new Dimension(FRAME_WIDTH, FRAME_HEIGHT));

        chattArea = new JTextArea(AREA_ROWS, AREA_COLUMNS);
        chattArea.setText("");
        chattArea.setEditable(true);

        createTextField();
        createButton();
        createPanel();

        frame.pack();
        frame.setVisible(true);
        frame.setLayout(new BorderLayout());
        frame.add(chattButton, BorderLayout.EAST);
        frame.add(chattLabel, BorderLayout.CENTER);
        frame.add(chattArea, BorderLayout.SOUTH);
        frame.add(chattField, BorderLayout.NORTH);

    }

    private void createTextField() {
        chattLabel = new JLabel("Chatt");
        final int FIELD_WIDTH = 10;
        chattField = new JTextField(FIELD_WIDTH);
        chattField.setText("" + "");
    }

    public void actionPerformed(ActionEvent event) {
        String message = chattField.getText();
        //ourParticipent.objectOutput.writeObject(message);
        ourParticipent.sendMessage(message);
        System.out.println("ActionPerformed2");
        chattArea.append("Me: " + message + "\n");
    }

    private void createButton() {
        chattButton = new JButton("Send");
        chattButton.addActionListener(this);
    }

    private void createPanel() {
        JPanel chattPanel = new JPanel();
        chattPanel.add(chattButton);
        chattPanel.add(chattLabel);
        chattPanel.add(chattField);
        JScrollPane scrollPane = new JScrollPane(chattArea);
        chattPanel.add(scrollPane);
    }
}

class Client implements Serializable {

    String hostName;
    final int PORTNUMBER = 9783;
    Socket mySocket;
    InputStream inStream;
    OutputStream outStream;
    ObjectOutputStream objectOutput;
    ObjectInputStream objectInput;
    String m;
    InetAddress IP;

    Client() {

        /*try {
        hostName = InetAddress.getLocalHost().getHostAddress();
        hostName = IP.getHostAddress();
        System.out.println(hostName);
    } catch (UnknownHostException ex) {
        Logger.getLogger(Client.class.getName()).log(Level.SEVERE, null, ex);
    }*/
        createSocket();
    }

    void createSocket() {
        try {
            //hostName = InetAddress.getLocalHost().getHostAddress();
            mySocket = new Socket("localhost", PORTNUMBER); //BORDE VÄL STÅ "hostName" HÄR?
            ChatParticipent thisParticipent = new ChatParticipent(mySocket);
            /*inStream = mySocket.getInputStream();
        outStream = mySocket.getOutputStream();

        objectOutput = new ObjectOutputStream(outStream);
        objectInput = new ObjectInputStream(inStream);

        objectOutput.writeObject("Saga");
        m = (String) objectInput.readObject();
        System.out.println("Client funkar");

        //Scanner in = new Scanner(instream);
        //PrintWriter out = new PrintWriter(outstream);
        //out.print();
             */
        } catch (UnknownHostException e) {
            System.out.println("Could not find host");
        } catch (IOException e) {
            System.out.println("Error reading from file");
            //} catch (ClassNotFoundException e) {
            //System.out.println("Error in finding file");
        }
        /*finally {
            try {
                mySocket.close();
                inStream.close();
                outStream.flush();
                outStream.close();
                objectOutput.close();
                objectInput.close();
            } catch (IOException e) {
                System.out.println("Not working");
            }
        }*/
    }
}

class Server {

    String hostName;
    int portNumber;
    ServerSocket myServer;
    Socket mySocket;
    InetAddress IP;

    Server() {
        /*try {
        IP = InetAddress.getLocalHost();
        hostName = IP.getHostAddress();
        System.out.println(hostName);
    } catch (UnknownHostException ex) {
        System.out.println("Could not find local IP");
    }*/
        portNumber = 9783;
        try {
            createServerSocket();
            //System.out.println("server");
        } catch (NullPointerException ex) {
            Logger.getLogger(Server.class.getName()).log(Level.SEVERE, null, ex);
        }
    }

    void createServerSocket() {
        //System.out.println("serversocket1");
        try {

            myServer = new ServerSocket(portNumber);
            //System.out.println("serversocket");
            mySocket = myServer.accept();

            System.out.println("Connection is made!");
            ChatParticipent thisParticipent = new ChatParticipent(mySocket);
        } catch (IOException e) {
            System.out.println("Error reading from file");
        } finally {
            //mySocket.close();
            //myServer.close();
            /*  inStream.close();
            outStream.close();
            objectOutput.close();
            objectInput.close(); */
            //GLÖM INTE CLOSEA DESSA!!!

        }
    }
}

class ChatParticipent implements ObjectStreamListener, Serializable {
    //INSTANSVARIABLER

    InputStream inStream;
    OutputStream outStream;
    ObjectOutputStream objectOutput;
    ObjectInputStream objectInput;
    String m;
    ChatWindow theWindow;
    Socket theSocket;
    ObjectStreamManager OSM;

    ChatParticipent(Socket clientOrServerSocket) {
        theSocket = clientOrServerSocket;
        
        streamingProcess();
        startOurGraphic();
    }

    void startOurGraphic() {
        theWindow = new ChatWindow(this);
    }

    void sendMessage(String message) {
        System.out.println("sendMessage startar");
        try {
            System.out.println("lets try sendMessage");
            //objectOutput.writeObject(message);
            objectOutput.writeObject(message);
            System.out.println("sendMessage");
        } catch (IOException e) {
            System.out.println("Går in i exception");
        }
    }

    void streamingProcess() {
        int identityNumber = 1;
        try {
            //outStream = theSocket.getOutputStream();
            objectOutput = new ObjectOutputStream(theSocket.getOutputStream());
            //objectOutput = new ObjectOutputStream(outStream);
            //inStream = theSocket.getInputStream();
            objectInput = new ObjectInputStream(theSocket.getInputStream());
            OSM = new ObjectStreamManager(identityNumber, objectInput, this);
            //m = (String) objectInput.readObject();
            //objectOutput.writeObject("CJ");
            System.out.println("Server funkar");
            //Scanner in = new Scanner(inStream);
            //PrintWriter out = new PrintWriter(outStream);
            //out.print();
            //out.flush();
        } catch (IOException ex) {
            Logger.getLogger(Client.class.getName()).log(Level.SEVERE, null, ex);
            System.out.println("Class not found");
        }
    }

    public void objectReceived(int number, Object theObject, Exception e) {
        System.out.println("inne i objectRecieved");
        if (e == null) {
            System.out.println("Object has been recieved!");
            String theMessage = (String) theObject;
            theWindow.chattArea.append("Friend: " + theMessage + "\n" );
        }
    }
}

/**
 *
 * @author joachimparrow
 *
 * This is the interface for the listener. It must have a method objectReceived.
 * Whenever reading from the stream results in an object being read or exception
 * being thrown, the object or exception is forwarded to the listener through
 * objectReceived().
 *
 *
 */
class ObjectStreamManager {

    private final ObjectInputStream theStream;
    private final ObjectStreamListener theListener;
    private final int theNumber;
    private volatile boolean stopped = false;

    /**
     *
     * This creates and starts a stream manager for a stream. The manager will
     * continually read from the stream and forward objects through the
     * objectReceived() method of the ObjectStreamListener parameter
     *
     *
     * @param number The number you give to the manager. It will be included in
     * all calls to readObject. That way you can have the same callback serving
     * several managers since for each received object you get the identity of
     * the manager.
     * @param stream The stream on which the manager should listen.
     * @param listener The object that has the callback objectReceived()
     *
     *
     */
    ObjectStreamManager(int number, ObjectInputStream stream, ObjectStreamListener listener) {
        theNumber = number;
        theStream = stream;
        theListener = listener;
        new InnerListener().start();  // start to listen on a new thread.
    }

    // This private method accepts an object/exception pair and forwards them
    // to the callback, including also the manager number. The forwarding is scheduled
    // on the Swing thread through an anonymous inner class.
    private void callback(final Object object, final Exception exception) {
        SwingUtilities.invokeLater(
                new Runnable() {
            public void run() {
                if (!stopped) {
                    theListener.objectReceived(theNumber, object, exception);
                    if (exception != null) {
                        closeManager();
                    }
                }
            }
        });
    }

    // This is where the actual reading takes place.
    private class InnerListener extends Thread {

        @Override
        public void run() {
            while (!stopped) {                            // as long as no one stopped me
                try {
                    callback(theStream.readObject(), null); // read an object and forward it
                } catch (Exception e) {                 // if Exception then forward it
                    callback(null, e);
                }
            }
            try {                   // I have been stopped: close stream
                theStream.close();
            } catch (IOException e) {
            }

        }
    }

    /**
     * Stop the manager and close the stream.
     *
     */
    public void closeManager() {
        stopped = true;
    }
}      // end of ObjectStreamManager

interface ObjectStreamListener {

    /**
     * This method is called whenever an object is received or an exception is
     * thrown.
     *
     * @param number The number of the manager as defined in its constructor
     * @param object The object received on the stream
     * @param exception The exception thrown when reading from the stream. Can
     * be IOException or ClassNotFoundException. One of name and exception will
     * always be null. In case of an exception the manager and stream are
     * closed.
     *
     */
    public void objectReceived(int number, Object object, Exception exception);
}
