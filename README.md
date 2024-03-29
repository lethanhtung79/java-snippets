### Java Snippets

#### 1. Converting Strings to int and int to String

    String a = String.valueOf(2); 
    int i = Integer.parseInt(a);  

#### 2. Convert String to Date

    java.util.Date = java.text.DateFormat.getDateInstance().parse(date String);

or

    SimpleDateFormat format = new SimpleDateFormat( "dd.MM.yyyy" );
    Date date = format.parse( myString );

#### 3. Convert Java util.Date to sql.Date

    java.util.Date utilDate = new java.util.Date();
    java.sql.Date sqlDate = new java.sql.Date(utilDate.getTime());

#### 4. Using Java JDBC to connect to Oracle

    public class OracleJdbcTest {
        String driverClass = "oracle.jdbc.driver.OracleDriver";
        Connection con;
     
        public void init(FileInputStream fs) throws ClassNotFoundException, SQLException, FileNotFoundException, IOException {
            Properties props = new Properties();
            props.load(fs);
            String url = props.getProperty("db.url");
            String userName = props.getProperty("db.user");
            String password = props.getProperty("db.password");
            Class.forName(driverClass);
            con=DriverManager.getConnection(url, userName, password);
        }
     
        public void fetch() throws SQLException, IOException {
            PreparedStatement ps = con.prepareStatement("select SYSDATE from dual");
            ResultSet rs = ps.executeQuery();
            while (rs.next()) {
                // do the thing you do
            }
            rs.close();
            ps.close();
        }
        
        public static void main(String[] args) {
            OracleJdbcTest test = new OracleJdbcTest();
            test.init();
            test.fetch();
        }
    }
    
#### 5. Create Thumbnail of an Image

    private void createThumbnail(String filename, int thumbWidth, int thumbHeight, int quality, String outFilename) throws InterruptedException, FileNotFoundException, IOException {
        Image image = Toolkit.getDefaultToolkit().getImage(filename);
        MediaTracker mediaTracker = new MediaTracker(new Container());
        mediaTracker.addImage(image, 0);
        mediaTracker.waitForID(0);
        System.out.println(mediaTracker.isErrorAny());
        double thumbRatio = (double)thumbWidth / (double)thumbHeight;
        int imageWidth = image.getWidth(null);
        int imageHeight = image.getHeight(null);
        double imageRatio = (double)imageWidth / (double)imageHeight;
        if (thumbRatio < imageRatio) {
            thumbHeight = (int)(thumbWidth / imageRatio);
        } else {
            thumbWidth = (int)(thumbHeight * imageRatio);
        }
        BufferedImage thumbImage = new BufferedImage(thumbWidth, thumbHeight, BufferedImage.TYPE_INT_RGB);
        Graphics2D graphics2D = thumbImage.createGraphics();
        graphics2D.setRenderingHint(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BILINEAR);
        graphics2D.drawImage(image, 0, 0, thumbWidth, thumbHeight, null);
        BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream(outFilename));
        JPEGImageEncoder encoder = JPEGCodec.createJPEGEncoder(out);
        JPEGEncodeParam param = encoder.getDefaultJPEGEncodeParam(thumbImage);
        quality = Math.max(0, Math.min(quality, 100));
        param.setQuality((float)quality / 100.0f, false);
        encoder.setJPEGEncodeParam(param);
        encoder.encode(thumbImage);
        out.close();
    }
    
#### 6. Generating PDF in Java by using iText JAR

    import java.io.File;
    import java.io.FileOutputStream;
    import java.io.OutputStream;
    import java.util.Date;
    import com.lowagie.text.Document;
    import com.lowagie.text.Paragraph;
    import com.lowagie.text.pdf.PdfWriter;
    public class GeneratePDF {
        public static void main(String[] args) {
            try {
                OutputStream file = new FileOutputStream(new File("C:\\Test.pdf"));
                Document document = new Document();
                PdfWriter.getInstance(document, file);
                document.open();
                document.add(new Paragraph("Hello Kiran"));
                document.add(new Paragraph(new Date().toString()));
                document.close();
                file.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    
#### 7. HTTP Proxy setting

    System.getProperties().put("http.proxyHost", "someProxyURL");
    System.getProperties().put("http.proxyPort", "someProxyPort");
    System.getProperties().put("http.proxyUser", "someUserName");
    System.getProperties().put("http.proxyPassword", "somePassword");
    
#### 8. Capture Screenshot

    import java.awt.Dimension;
    import java.awt.Rectangle;
    import java.awt.Robot;
    import java.awt.Toolkit;
    import java.awt.image.BufferedImage;
    import javax.imageio.ImageIO;
    import java.io.File;
    public void captureScreen(String fileName) throws Exception {
        Dimension screenSize = Toolkit.getDefaultToolkit().getScreenSize();
        Rectangle screenRectangle = new Rectangle(screenSize);
        Robot robot = new Robot();
        BufferedImage image = robot.createScreenCapture(screenRectangle);
        ImageIO.write(image, "png", new File(fileName));
    }
    
#### 9. Files-Directory listing

    File dir = new File("directoryName");
    String[] children = dir.list();
    if (children == null) {
        // Either dir does not exist or is not a directory
    } else {
        for (int i=0; i < children.length; i++) {
            // Get filename of file or directory
            String filename = children[i];
        }
    }
    // It is also possible to filter the list of returned files.
    // This example does not return any files that start with `.'.
    FilenameFilter filter = new FilenameFilter() {
        public boolean accept(File dir, String name) {
            return !name.startsWith(".");
        }
    };
    children = dir.list(filter);
    
    // The list of files can also be retrieved as File objects
    File[] files = dir.listFiles();
    
    // This filter only returns directories
    FileFilter fileFilter = new FileFilter() {
        public boolean accept(File file) {
            return file.isDirectory();
        }
    };
    files = dir.listFiles(fileFilter);
    
#### 10. Create ZIP and JAR files

    import java.util.zip.*;
    import java.io.*;
    public class ZipIt {
        public static void main(String args[]) throws IOException {
            if (args.length < 2) {
                System.err.println("usage: java ZipIt Zip.zip file1 file2 file3");
                System.exit(-1);
            }
            File zipFile = new File(args[0]);
            if (zipFile.exists()) {
                System.err.println("Zip file already exists, please try another");
                System.exit(-2);
            }
            FileOutputStream fos = new FileOutputStream(zipFile);
            ZipOutputStream zos = new ZipOutputStream(fos);
            int bytesRead;
            byte[] buffer = new byte[1024];
            CRC32 crc = new CRC32();
            for (int i=1, n=args.length; i < n; i++) {
                String name = args[i];
                File file = new File(name);
                if (!file.exists()) {
                    System.err.println("Skipping: " + name);
                    continue;
                }
                BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file));
                crc.reset();
                while ((bytesRead = bis.read(buffer)) != -1) {
                    crc.update(buffer, 0, bytesRead);
                }
                bis.close();
                // Reset to beginning of input stream
                bis = new BufferedInputStream(new FileInputStream(file));
                ZipEntry entry = new ZipEntry(name);
                entry.setMethod(ZipEntry.STORED);
                entry.setCompressedSize(file.length());
                entry.setSize(file.length());
                entry.setCrc(crc.getValue());
                zos.putNextEntry(entry);
                while ((bytesRead = bis.read(buffer)) != -1) {
                    zos.write(buffer, 0, bytesRead);
                }
                bis.close();
            }
            zos.close();
        }
    }
    
#### 11. Convert Array to Map

    import java.util.Map;
    import org.apache.commons.lang.ArrayUtils;
    public class Main {
        public static void main(String[] args) {
            String[][] countries = { { "United States", "New York" }, { "United Kingdom", "London" }, { "Netherland", "Amsterdam" }, { "Japan", "Tokyo" }, { "France", "Paris" } };
            Map countryCapitals = ArrayUtils.toMap(countries);
            System.out.println("Capital of Japan is " + countryCapitals.get("Japan"));
        }
    }
    
#### 12. Send Email

    import javax.mail.*;
    import javax.mail.internet.*;
    import java.util.*;
    public void postMail( String recipients[ ], String subject, String message , String from) throws MessagingException {
        boolean debug = false;
 
        //Set the host smtp address
        Properties props = new Properties();
        props.put("mail.smtp.host", "smtp.example.com");

        // create some properties and get the default Session
        Session session = Session.getDefaultInstance(props, null);
        session.setDebug(debug);

        // create a message
        Message msg = new MimeMessage(session);

        // set the from and to address
        InternetAddress addressFrom = new InternetAddress(from);
        msg.setFrom(addressFrom);

        InternetAddress[] addressTo = new InternetAddress[recipients.length]; 
        for (int i = 0; i < recipients.length; i++)
        {
            addressTo[i] = new InternetAddress(recipients[i]);
        }
        msg.setRecipients(Message.RecipientType.TO, addressTo);

        // Optional : You can also set your custom headers in the Email if you Want
        msg.addHeader("MyHeaderName", "myHeaderValue");

        // Setting the Subject and Content Type
        msg.setSubject(subject);
        msg.setContent(message, "text/plain");
        Transport.send(msg);
    }

#### 13. Send HTTP request and fetch data

    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.net.URL;
    public class Main {
        public static void main(String[] args)  {
            try {
                URL my_url = new URL("http://www.viralpatel.net/blogs/");
                BufferedReader br = new BufferedReader(new InputStreamReader(my_url.openStream()));
                String strTemp = "";
                while(null != (strTemp = br.readLine())){
                    System.out.println(strTemp);
                }
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }

#### 14. Using NIO to copy Java file fast

    public static void fileCopy( File in, File out )
            throws IOException
    {
        FileChannel inChannel = new FileInputStream( in ).getChannel();
        FileChannel outChannel = new FileOutputStream( out ).getChannel();
        try
        {
            // magic number for Windows, 64Mb - 32Kb)
            int maxCount = (64 * 1024 * 1024) - (32 * 1024);
            long size = inChannel.size();
            long position = 0;
            while ( position < size )
            {
               position += inChannel.transferTo( position, maxCount, outChannel );
            }
        }
        finally
        {
            if ( inChannel != null )
            {
               inChannel.close();
            }
            if ( outChannel != null )
            {
                outChannel.close();
            }
        }
    }
    
#### 15. Creating JSON data in Java

    import org.json.JSONObject; 
    ... 
    ... 
    JSONObject json = new JSONObject(); 
    json.put("city","Mumbai"); 
    json.put("country", "India"); 
    ... 
    String output = json.toString(); 
    ...

#### 16. Get name of current method

    String methodName = Thread.currentThread().getStackTrace()[1].getMethodName(); 

#### 17. Appending content to file in Java

    Jaxenter out = null;
    try {
        out = new Jaxenter (new FileWriter(”filename”, true));
        out.write(”aString”);
    } catch (IOException e) {
        // error processing code
    } finally {
        if (out != null) {
            out.close();
        }
    }
    
    
