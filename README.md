# chatcjsaga
chatt

/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package chatt;

import java.awt.*;
import java.awt.event.*;
import java.io.*;
import java.net.*;
import java.util.logging.Level;
import java.util.logging.Logger;
import javax.swing.*;

/**
 *
 * @author Cj
 */
public class Chatt {

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        /* Client ourClient = new Client();
        Server ourServer = new Server();
        ourClient.createSocket();
        ourServer.createSocket();
         */
        boolean isConnected = true;
        if (isConnected == false) {
            Server ourServer = new Server();
            System.out.println("main2");
        } else {
            Client ourClient = new Client();
            System.out.println("main1");
        }
        System.out.println("main");
        ChatWindow theWindow = new ChatWindow();

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
    private JTextArea chattArea;
    JFrame frame;

    public ChatWindow() {
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
        String Message = (String) chattField.getText();
        chattArea.append(Message + "\n");
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
    final int PORTNUMBER = 9782;
    Socket mySocket;
    InputStream inStream;
    OutputStream outStream;
    ObjectOutputStream objectOutput;
    ObjectInputStream objectInput;
    String m;
    InetAddress IP;

    Client() throws IOException, ClassNotFoundException {

        /*try {
            IP = InetAddress.getLocalHost();
            hostName = IP.getHostAddress();
            System.out.println(hostName);
        } catch (UnknownHostException ex) {
            Logger.getLogger(Client.class.getName()).log(Level.SEVERE, null, ex);
        }*/
        createSocket();
    }

    void createSocket() throws IOException, ClassNotFoundException {
        System.out.println("client");
        try {
             System.out.println("client2");
            mySocket = new Socket("localhost", PORTNUMBER);
            System.out.println("Client uppkopplad");
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
        } catch (IOException e) {
            System.out.println("Error reading from file");
        } finally {
            mySocket.close();
            inStream.close();
            outStream.flush();
            outStream.close();
            objectOutput.close();
            objectInput.close();
        }
    }
}

class Server implements Serializable {

    String hostName;
    int portNumber;
    ServerSocket myServer;
    InputStream inStream;
    OutputStream outStream;
    ObjectOutputStream objectOutput;
    ObjectInputStream objectInput;
    String m;
    Socket mySocket;
    InetAddress IP;

    Server() throws IOException, ClassNotFoundException {
        /*try {
            IP = InetAddress.getLocalHost();
            hostName = IP.getHostAddress();
            System.out.println(hostName);
        } catch (UnknownHostException ex) {
            System.out.println("Could not find local IP");
        }*/
        portNumber = 9782;
        createSocket();
        System.out.println("server");
    }

    void createSocket() throws IOException, ClassNotFoundException {
        System.out.println("serversocket1");
        try {
        
            myServer = new ServerSocket(portNumber);
                System.out.println("serversocket");
            mySocket = myServer.accept();
            
            System.out.println("Connection is made!");

            /*inStream = mySocket.getInputStream();
            outStream = mySocket.getOutputStream();

            objectOutput = new ObjectOutputStream(outStream);
            objectInput = new ObjectInputStream(inStream);

            m = (String) objectInput.readObject();
            objectOutput.writeObject("CJ");
            System.out.println("Server funkar");
            //Scanner in = new Scanner(instream);
            //PrintWriter out = new PrintWriter(outstream);
            //out.print();
            //out.flush();*/
        } catch (IOException e) {
            System.out.println("Error reading from file");
        } finally {
            mySocket.close();
            myServer.close();
            inStream.close();
            outStream.close();
            objectOutput.close();
            objectInput.close();
        }
    }
}
