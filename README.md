
Java basic progroms
import java.io.*;
import java.net.Socket;
import java.security.*;
import javax.crypto.Cipher;
import java.util.Base64;

public class TCPClient {
    private static final String SERVER_ADDRESS = "localhost";
    private static final int PORT = 5000;

    public static void main(String[] args) {
        try {
            // Generate RSA key pair (Client should get the public key from the server securely in a real scenario)
            KeyPair keyPair = generateKeyPair();
            PublicKey publicKey = keyPair.getPublic();

            // Read image file
            File imageFile = new File("input_image.jpg");
            FileInputStream fis = new FileInputStream(imageFile);
            byte[] imageData = new byte[(int) imageFile.length()];
            fis.read(imageData);
            fis.close();

            // Encrypt the image data
            byte[] encryptedData = encryptData(imageData, publicKey);

            // Connect to server and send encrypted data
            Socket socket = new Socket(SERVER_ADDRESS, PORT);
            ObjectOutputStream oos = new ObjectOutputStream(socket.getOutputStream());
            oos.writeObject(encryptedData);
            oos.flush();

            System.out.println("Encrypted image sent to server!");

            // Close connections
            oos.close();
            socket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Generate RSA KeyPair
    private static KeyPair generateKeyPair() throws NoSuchAlgorithmException {
        KeyPairGenerator keyGen = KeyPairGenerator.getInstance("RSA");
        keyGen.initialize(2048);
        return keyGen.generateKeyPair();
    }

    // RSA Encryption
    private static byte[] encryptData(byte[] data, PublicKey publicKey) throws Exception {
        Cipher cipher = Cipher.getInstance("RSA");
        cipher.init(Cipher.ENCRYPT_MODE, publicKey);
        return cipher.doFinal(data);
    }
}



