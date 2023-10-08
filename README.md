1) Write a Java program using synchronized threads which demonstrate producer-consumer concepts.

class Q {
    int n;
    boolean valueset = false;

    synchronized int get() {
        while (!valueset)
            try {
                wait();
            } catch (InterruptedException e) {
                System.out.println("Thread Interrupted");
            }
        System.out.println("Got :" + n);
        valueset = false;
        notify();
        return n;
    }

    synchronized void put(int n) {
        while (valueset)
            try {
                wait();
            } catch (InterruptedException e) {
                System.out.println("Thread interrupted");
            }
        this.n = n;
        valueset = true;
        System.out.println("put " + n);
        notify();
    }
}

class Producer implements Runnable {
    Q q;

    Producer(Q q) {
        this.q = q;
        new Thread(this, "Producer").start();
    }

    public void run() {
        int i = 0;
        while (true) {
            q.put(i++);
        }
    }
}

class Consumer implements Runnable {
    Q q;

    Consumer(Q q) {
        this.q = q;
        new Thread(this, "Consumer").start();
    }

    public void run() {
        int i = 0;
        while (true) {
            q.get();
        }
    }
}

class Demo {
    public static void main(String args[]) {
        Q q = new Q();
        new Producer(q);
        new Consumer(q);
        System.out.println("press ctrl+c to exit");
    }
}












Output



 











2) Write a Java Swing program that consists of three tabs named Select Semester, Select Course and Select Electives. The “Select Semester” tab must contain four Buttons. The “Select Course” should contain a list of check boxes named with the courses such as Java, Compiler Design, and Machine Learning.  “The Select Electives” tab should contain a drop down list of elective names of subjects.
Hint: Swing application which uses,
  
 i) JTabbed Pane
 ii) Each  tab should Jpanel which include any one component given below  in each JPanel
 iii)CheckBox/List/RadioButton

import java.awt.Color;
import java.awt.Graphics;

import javax.swing.*;

/*
<applet code="JTabbedPaneDemo" width=400 height=100>
</applet>
*/
public class JTabbedPaneDemo extends JApplet {
    public void init() {
        try {
            SwingUtilities.invokeAndWait(
                    new Runnable() {
                        public void run() {
                            makeGUI();
                        }
                    }
            );
        } catch (Exception exc) {
            System.out.println("Can't create because of " +exc);
        }
    }

    private void makeGUI() {
    	
        JTabbedPane jtp = new JTabbedPane();
        jtp.addTab("Semister", new SemisterPanel());
        jtp.addTab("Course", new CoursePanel());
        jtp.addTab("Elective", new ElectivePanel());
        add(jtp);
    
    }
}

// Make the panels that will be added to the tabbed pane.
class SemisterPanel extends JPanel {
    public SemisterPanel() {
        JButton b1 = new JButton("2");
        add(b1);
        JButton b2 = new JButton("4");
        add(b2);
        JButton b3 = new JButton("6");
        add(b3);
        JButton b4 = new JButton("8");
        add(b4);
        
}
}



class CoursePanel extends JPanel {
    public CoursePanel() {
        JCheckBox cb1 = new JCheckBox("EC");
        add(cb1);
        JCheckBox cb2 = new JCheckBox("ISE");
        add(cb2);
        JCheckBox cb3 = new JCheckBox("CSE");
        add(cb3);
    }
}
class ElectivePanel extends JPanel {
    public ElectivePanel() {
        JComboBox jcb = new JComboBox();
        jcb.addItem("AI");
        jcb.addItem("IoT");
        jcb.addItem("Big Data");
        add(jcb);
    }


}


 


 
 











 
3) Design and implement a simple Client Server Application using RMI. 

AddServerIntf.java

import java.rmi.*;
public interface AddServerIntf extends Remote {
    int add(int x, int y) throws RemoteException;
}

AddServerImpl.java

import java.rmi.*;
import java.rmi.server.*;
public class AddServerImpl extends UnicastRemoteObject implements AddServerIntf{
    public AddServerImpl() throws RemoteException {}
    public int add(int x, int y) throws RemoteException {
        return x+y;
    }
}

AddServer.java

import java.rmi.*;
public class AddServer {
    public static void main(String[] args) {
        try{
            AddServerImpl server = new AddServerImpl();
            Naming.rebind("registerme",server);
            System.out.println("Server is running...");
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}


AddClient.java
import java.rmi.*;
public class AddClient {
    public static void main(String[] args) {
        try{
            AddServerIntf client = (AddServerIntf)Naming.lookup("registerme");
            System.out.println("First number is :" + args[0]);
            int x = Integer.parseInt(args[0]);
            System.out.println("Second number is :" + args[1]);
            int y = Integer.parseInt(args[1]);
            System.out.println("Sum =" + client.add(x,y));
        } catch (Exception e){
            System.out.println(e);
        }
    }
}


Output:
Open a terminal
Navigate to the src folder of your project
 

In another terminal (while previous one is still running)
Navigate to the src folder of your project
 

In third terminal (while previous both are still open)
Navigate to the src folder of your project
 




























4) Design and implement Client Server communication using socket programming (Client requests a file, Server responds to client with contents of that file which is then display on the screen by Client). 

Client.java
import java.net.*;
import java.io.*;
public class Client {
    public static void main(String[] args) {
        Socket client = null;
        BufferedReader br = null;
        try {
            System.out.println(args[0] + " " + args[1]);
            client = new Socket(args[0],Integer.parseInt(args[1]));
        } catch (Exception e){}
        DataInputStream input = null;
        PrintStream output = null;
        try {
            input = new DataInputStream(client.getInputStream());
            output = new PrintStream(client.getOutputStream());
            br = new BufferedReader(new InputStreamReader(System.in));
            String str = input.readLine();  //get the prompt from the server
            System.out.println(str);
            String filename = br.readLine();
            if (filename!=null){
                output.println(filename);
            }
            String data;
            while ((data=input.readLine())!=null) {
                System.out.println(data);
            }
            client.close();
        } catch (Exception e){
            System.out.println(e);
        }
    }
}




Server.java
import java.net.*;
import java.io.*;


public class Server {
    public static void main(String[] args) {
        ServerSocket server = null;
        try {
            server = new ServerSocket(Integer.parseInt(args[0]));
        } catch (Exception e) {
        }
        while (true) {
            Socket client = null;
            PrintStream output = null;
            DataInputStream input = null;
            try {
                client = server.accept();
            } catch (Exception e) {
                System.out.println(e);
            }
            try {
                output = new PrintStream(client.getOutputStream());
                input = new DataInputStream(client.getInputStream());
            } catch (Exception e) {
                System.out.println(e);
            }
            //Send the command prompt to client
            output.println("Enter the filename :>");
            try {
                //get the filename from client
                String filename = input.readLine();
                System.out.println("Client requested file :" + filename);
                try {
                    File f = new File(filename);
                    BufferedReader br = new BufferedReader(new FileReader(f));
                    String data;
                    while ((data = br.readLine()) != null) {
                        output.println(data);
                    }
                } catch (FileNotFoundException e) {
                    output.println("File not found");
                }
                client.close();
            } catch (Exception e) {
                System.out.println(e);
            }
        }
    }
}

Output
Create a file called testfile.txt in the folder where Client.java and Server.java is located. Add some content.
Open two terminals
Navigate to the src folder of your project
 

 











5) Implement a JAVA Servlet Program to implement a dynamic HTML using Servlet (user name and password should be accepted using HTML and displayed using a Servlet). 

Create a new servlet named Servlet9 in the project (as shown in the steps above from Page 37) and then type the following code in it
Servlet9.java


import java.io.*;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/Servlet9")
publicclass Servlet9 extends HttpServlet {

	protectedvoid doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setContentType("text/html");
		PrintWriter out = response.getWriter();
		String str = request.getParameter("uname");
		String str1 = request.getParameter("pname");
		out.println("<html>");
		out.println("<body>");
		out.println("Username is :" + str + "<br/>");
		out.println("Password is :" + str1);
		out.println("</body>");
		out.println("</html>");	}

}

Under WebContent, create a new html file, Program9.html
<html>
<head>
<title>Program 9</title>
</head>
<body bgcolor=orange>
<form method="post" name="form1" action="http://localhost:8080/ProjectName/ServletClassName">
<center>
<b><br/><br/>
            Enter Username : <input type="text" name="uname" size="10"/>
<br/>
            Enter Password : <input type="password" name="pname" size="10"/>
<br/><br/>
<input type="button" value="Submit" onclick="submit()"/>
</center>
<script type="text/javascript">
function validate(){
if(document.form1.uname.value =="" || document.from1.pname.value ==""){
alert("Fields cannot be blank");
return;
                }

            }
</script>
</form>
</body>
</html>
In the above html file, replace ProjectName and ServletClassName with your respective project and filename













Output

 




 




6. Implement JDBC Application Program Using MySQL/ORACLE connectivity, by developing a program to accept book information such as Book-ID, Title, Authors, Edition and Publisher from the table already stored  in the database, to perform the following transactions.
1. Display of Table Contents. 
2. Insertion of Values to the Table.
3. Update and Delete contents as and when required



import java.sql.*;
class prg{
public static void main(String args[]){

try{

Class.forName("oracle.jdbc.driver.OracleDriver");

Connection con=DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:xe","javanetwork","tiger");

Statement stmt=con.createStatement();
//String sql1=("insert into Books values(12,'Java Programming','Herbert',9,'TMH')");
//String sql2=("update Books set edition=9 where Bid=1432");
String sql3=("Delete from Books where Bid=12345");
String sql=("select * from Books");
//ResultSet rs2=stmt.executeQuery(sql1);
//ResultSet rs3=stmt.executeQuery(sql2);
ResultSet rs4=stmt.executeQuery(sql3);
ResultSet rs=stmt.executeQuery(sql);


while(rs.next())
{
System.out.println(rs.getInt(1)+ " " +rs.getString(2)+" "+rs.getString(3)+" "+rs.getInt(4)+" "+rs.getString(5) );

}
con.close();

}catch(Exception e)
{

System.out.println(e);
}
}
}


Note:
1.	Create the table Books using SQL Command Prompt or MySql Command Prompt.
2.	Write an JDBC Program using Ecllipse IDE.

 



PART-B
1. Simulate a three nodes point to point network with duplex links between them. Set the queue size and vary the bandwidth and find the number of packets dropped. 

 
Program(lab1.tcl)
set ns [ new Simulator ]
set tf [ open lab1.tr w ]
$ns trace-all $tf
set nf [ open lab1.nam w ]
$ns namtrace-all $nf
# The below code is used to create the nodes.
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
#This is used to give color to the flow of packets.
$ns color 1 "red"
$ns color 2 "blue"
$n0 label "Source/udp0"
$n1 label "Source/udp1"
$n2 label "destination"
#providing the link
$ns duplex-link $n0 $n2 10kb 100ms DropTail
$ns duplex-link $n1 $n2 10kb 10ms DropTail
# set the queue size b/w the nodes
$ns set queue-limit $n0 $n2  5
$ns set queue-limit $n1 $n2  5
set udp0   [new Agent/UDP]
set udp1  [new Agent/UDP]
$ns attach-agent $n0 $udp0
$ns attach-agent $n1 $udp1
set cbr0  [new Application/Traffic/CBR]
set cbr1 [new Application/Traffic/CBR]
$cbr0 attach-agent $udp0
$cbr1 attach-agent $udp1
set null0  [new Agent/Null]
$ns attach-agent $n2 $null0
#The below code sets the udp0 packets to red and udp1 packets to blue color
$udp0 set class_  1
$udp1 set class_  2
#The below code is used to connect the agents.
$ns connect $udp0 $null0
$ns connect $udp1 $null0
#The below code is used to set the packet size to 500
$cbr0 set packetSize_  500Mb
$cbr1 set packetSize_  500Mb
#The below code is used to set the interval of the packets,
$cbr0 set interval_ 0.01
$cbr1 set interval_ 0.01
proc finish  { }  {
global  ns n f  tf
$ns flush-trace
exec nam lab1.nam &
close $tf
close $nf
exit 0
}
$ns at 0.1 "$cbr0 start"
$ns at 0.1 "$cbr1 start"
$ns at 9.5 "$cbr0 stop"
$ns at 10.0 "$cbr1 stop"
$ns at 10.0 "finish"
$ns run
AWK Script (lab1.awk)

BEGIN{
count=0; 
} 
{ 
if($1=="d")
count++ ;
} 
END{
printf("The Total no of Packets Dropped due to Congestion : %d\n\n", count) 
} 

Output: 
ns lab1.tcl 
awk –f lab1.awk lab1.tr 
The Total no of packets Dropped due to congestion:4560 
Screenshot
 




Note: 

1.	Find the total number of packets dropped by changing the bandwidth   as shown below in the table.



Bandwidth 		
	Queue size	Packets  dropped
10kb 	5	
100kb	5	
1Mb 	5	

2.	Find the total number of packets dropped by changing the queue size   as shown below in the table.


Bandwidth 		
	Queue size	Packets  dropped
100kb	3	
100kb	5	
100kb	10	















2. Simulate an Ethernet lan using n nodes (6-10), change the error rate and data rate and compare the throughput. 

Design:


 Note: 

1. The lan can be created by using the command: 

$ns make-lan“$n0 $n1 $n2” 100Mb 10ms LL Queue/DropTail  Mac/802_3 

2. The Error between the nodes n2 and n4 can be added as follows: 

set err  [ new ErrorModel ] 

$ns lossmodel $err $n2 $n4

$err set rate_ 0.1 # used to set error rate. 

3. The throughput can analyzed by changing the data rate and error rate as shown 
below. 
i.	First, fix the data rate to 0.001 and vary the error rate, as below.Tabulate the throughput.
ii.	Secondly, fix the error rate to 0.1 and vary the data rate, as below. Tabulate the throughput. 

Example:


Error rate	Data rate	Throughput
0.1	0.001	
0.2	0.001	
0.3	0.001	
            0.4	
	0.001
	



Error rate	Data rate	Throughput
0.1	0.1	
0.1	0.01	
0.1	0.001	
0.1	0.0001	



Program(lan.tcl)

set ns  [new Simulator]
set  tf  [open lan.tr w]
$ns trace-all $tf
set nf  [open lan.nam w]
$ns namtrace-all $nf
set n0  [$ns node]
set n1  [$ns node]
set n2  [$ns node]
set n3  [$ns node]
set n4  [$ns node]
set n5  [$ns node]
$ns color 1 "blue"
$n1 label  “Source"
$n2  label  "Error node"
$n5  label  "Destination"

#The below code is used to create two Lans (Lan1 and Lan2).
$ns make-lan "$n0 $n1 $n2" 10Mb 10ms LL Queue/DropTail  Mac/802_3
$ns make-lan "$n3 $n4 $n5" 10Mb 10ms LL Queue/DropTail  Mac/802_3

# connect node n2 and n4
$ns duplex-link $n2 $n4 100Mb 10ms DropTail
$ns duplex-link-op $n2 $n4 color "green"
set udp1 [new Agent/UDP]
$ns attach-agent $n1 $udp1
set cbr1 [new Application/Traffic/CBR]
$cbr1 attach-agent $udp1
set null5 [new Agent/Null]
$ns attach-agent $n5 $null5
$ns connect $udp1 $null5

#data rate- change this to change the data rate
$cbr1 set packetSize_  1000
$cbr1 set interval_  0.001
$udp1 set class_  1

# The below code is used to add an error model between the nodes n2 and n4.
set err  [new ErrorModel]
$ns lossmodel $err $n2 $n4
$err set rate_   0.1
proc finish {}  {
	global ns tf nf
	$ns flush-trace
	exec nam lan.nam&
	close $tf
	close $nf
	exit 0
}
$ns at 0.1 "$cbr1 start"
$ns at 6.0 "finish"
$ns run


AWK Script (lan.awk)
BEGIN{
	pkt=0;
	time=0;
}
{
	if($1=="r"&& $9=="1.0" && $10=="5.0")
	{
		pkt=pkt+$6;
		time=$2;	
	}
}
END{
	printf("Throughput: %f Mbps \n\n",(pkt/time)*(8/1000000));
}

Execution of the program

Type the following commands in the terminal window for executing the programs.

ns lan.tcl 
awk –f lan.awk lan.tr

Output: 

Throughput : 90.3Mbps
Snapshot:


 




















3. Simulate a four node point –to- point network with links connected as follows: 
n0-n2, n1-n2 and n2-n3. Apply TCP agents between n0-n3 and UDP agents between n1-n3. Apply relevant applications over TCP and UDP agents changing the parameter and determine the number of packets sent by TCP/UDP. 


 

Example:

Note: 

1.	Find the total number of packets sent by TCP and UDP by changing the bandwidth 100Mb, 200Mb in the topology and the data rate 0.01, 0.001 as shown below in the table.


BANDWIDTH 		
	PACKET INTERVAL	TCP PACKETS SENT 
	UDP PACKETS SENT
100Mb 	0.01 		
100Mb 	0.001 		
200Mb 	0.01		
200Mb 	0.001 		



PROGRAM (lab3.tcl)

set ns [new Simulator] 
set  tf  [open lab3.tr w] 
$ns trace-all $tf
set nf  [open lab3.nam w] 
$ns namtrace-all $nf
set  n0  [$ns node] 
set  n1  [$ns node] 
set  n2  [$ns node] 
set  n3  [$ns node] 

# The below code is used to set the color and name's to the nodes. 
$ns color 1 "red" 
$ns color 2 "blue" 
$n0 label "Source/TCP" 
$n1 label "Source/UDP" 
$n2 label "Router" 
$n3 label "Destination" 
$ns duplex-link$n0 $n2 100Mb 1ms DropTail
$nsduplex-link $n1 $n2 100Mb 1ms DropTail
$ns duplex-link $n2 $n3 100Mb 1ms DropTail

# The below code is used to set the color and labels to the links. 
$ns duplex-link-op $n0 $n2 color "green" 
$ns duplex-link-op  $n0 $n2 label "from 0-2" 
$ns duplex-link-op  $n1 $n2 color "green" 
$ns duplex-link-op  $n1 $n2 label "from 1-2" 
$ns duplex-link-op  $n2 $n3 color "green" 
$ns duplex-link-op  $n2 $n3 label "from 2-3" 

# The below code is used create TCP and UDP agents and the 
# traffic ftp &cbr respectively. 

set  tcp0 [new Agent/TCP] 
$ns attach-agent $n0 $tcp0 
set ftp0 [new Application/FTP] 
$ftp0 attach-agent $tcp0  
set sink3 [new Agent/TCPSink] 
$ns attach-agent $n3 $sink3 
set udp1 [new Agent/UDP] 
$ns attach-agent $n1 $udp1 
set cbr1 [new Application/Traffic/CBR]
$cbr1 attach-agent $udp1 
set null3 [new Agent/Null] 
$ns attach-agent $n3 $null3 

#The below code is used to set the packet size of ftp and udp. 
$ftp0 set  packetSize_  500
$ftp0 set  interval_  0.001

#The below code is used set the data rate
$cbr1 set  packetSize_  500 
$cbr1 set  interval_  0.001

#This code is used give a color red->tcp and blue ->udp. 
$tcp0 set class_  1
$udp1 set class_  2

# The below code is used connect the agents. 
$ns connect $tcp0 $sink3 
$ns connect $udp1 $null3 

proc finish { } { 
global ns nf tf
$ns flush-trace 
exec nam lab3.nam &
close $nf
close $tf
exit 0 
} 
$ns at 0.1 "$cbr1 start" 
$ns at 0.2 "$ftp0 start" 
$ns at 9.5 “$cbr1 stop"
$ns at 9.5 “$ ftp0 stop"
$ns at 10.0 "finish"
$ns run

AWK Script (lab3.awk)


BEGIN{
tcp=0; 
udp=0; 
} 
{ 
if($1=="r"&&$3=="2"&&$4=="3"&& $5=="tcp") 
tcp++; 
if($1=="r"&&$3=="2"&&$4=="3"&&$5=="cbr") 
udp++; 
} 
END{
printf("\n Total number of packets sent by TCP : %d\n",tcp); 
printf("\n Total number of packets sent by UDP : %d\n",udp); 
} 




Execution of the program

Type the following commands in the terminal window for executing the programs.

ns lab2.tcl 
awk –f lab3.awk lab3.tr



Output: 

Total number of packets sent by TCP : 1200 

Total number of packets sent by UDP : 4500 












Screenshot






4	To create scenario and study the performance of Stop and Wait ARQ Protocol through simulation.

#window-The upper bound on the advertised window for the TCP connection.
#maxcwnd - The upper bound on the congestion window for the TCP connection. Set to zero to ignore. (This is the default.)
#Agents are used to separate protocol states from nodes. They are always associated with nodes. 


An agent has a name, which is a unique identifier of the agent. It is shown as a square with its name inside, and a line link the square to its associated node. The add agent and monitor agent are commands that support agent tracing.
#orient – The orientation of the link (up, down, right, left , right up, right down, left up, left down)
#nam_tracevar_true :It is format for TCP variable, required by nam visualization of TCP.
# detach-agent - Detach the agent of type agent from the node
# trace- annotate: creates trace file. It will provide the information about the packet transfer event at the bottom of the simulation window

Program(stop.tcl)

set ns [new Simulator]
set n0 [$ns node]
set n1 [$ns node]
$ns color 1 "red"
$ns at 0.0 "$n0 label Sender"
$ns at 0.0 "$n1 label Receiver"
set nf  [open stop.nam w]
$ns namtrace-all $nf
set tf [open stop.tr w]
$ns trace-all $tf
$ns duplex-link $n0 $n1 0.2Mb 200ms DropTail
$ns duplex-link-op $n0 $n1 orient right
$ns queue-limit $n0 $n1 10
Agent/TCP set nam_tracevar_ true
set tcp  [new Agent/TCP]
$tcp set window_ 1 
$tcp set maxcwnd_ 1 
$ns attach-agent $n0 $tcp
set sink [new Agent/TCPSink]
$ns attach-agent $n1 $sink
$ns connect $tcp $sink
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ns add-agent-trace $tcp tcp
$ns monitor-agent-trace $tcp
$tcp tracevar cwnd_
$tcp set class_ 1
$ns at 0.1 "$ftp start"
$ns at 3.0 "$ns detach-agent $n0 $tcp ; $ns detach-agent $n1 $sink"
$ns at 3.5 "finish"
$ns at 0.0 "$ns trace-annotate \"Stop and Wait with normal operation\""
$ns at 0.05 "$ns trace-annotate \"FTP starts at 0.1\""
$ns at 0.11 "$ns trace-annotate \"Send Packet_0\""
$ns at 0.35 "$ns trace-annotate \"Receive Ack_0\""
$ns at 0.56 "$ns trace-annotate \"Send Packet_1\""
$ns at 0.79 "$ns trace-annotate \"Receive Ack_1\""
$ns at 0.99 "$ns trace-annotate \"Send Packet_2\""
$ns at 1.23 "$ns trace-annotate \"Receive Ack_2 \""
$ns at 1.43 "$ns trace-annotate \"Send Packet_3\""
$ns at 1.67 "$ns trace-annotate \"Receive Ack_3\""
$ns at 1.88 "$ns trace-annotate \"Send Packet_4\""
$ns at 2.11 "$ns trace-annotate \"Receive Ack_4\""
$ns at 2.32 "$ns trace-annotate \"Send Packet_5\""
$ns at 2.55 "$ns trace-annotate \"Receive Ack_5 \""
$ns at 2.75 "$ns trace-annotate \"Send Packet_6\""
$ns at 2.99 "$ns trace-annotate \"Receive Ack_6\""
$ns at 3.1 "$ns trace-annotate \"FTP stops\""
proc finish { } {
global ns nf
$ns flush-trace
close $nf
puts "running nam..."
exec nam stop.nam &
exit 0	
}
$ns run

Execution of the program 
 
Type the following commands in the terminal window for executing the programs. 
 
ns stop.tcl  

Snapshot : 
 

 
 

5.  Simulate simple ESS and with transmitting nodes in wire-less LAN by simulation and determine the performance with respect to transmission of packets. 
Simulation Parameters:

Area :700m *700m 
Simulation Time :250 simulation sec 
Wireless nodes :4
Routing Protocol :DSDV (Destination Sequenced Distance 
vector) 
Interface queue Type :Queue/DropTail
MAC :802.11 
Application :FTP 
Antenna :Omni antenna

(wireless.tcl) 
set ns [new Simulator]
set tf [open wireless.tr w]
$ns trace-all $tf
set topo [new Topography]
$topo load_flatgrid 700 700
set nf  [open wireless.nam w]
$ns namtrace-all-wireless  $nf  700 700
$ns node-config      -adhocRouting DSDV\
			-llType LL\
			-ifqType Queue/DropTail\
			-macType Mac/802_11\
			-ifqLen 50 \
			-phyTypePhy/WirelessPhy\
			-channelType Channel/WirelessChannel\
			-propType Propagation/TwoRayGround\
			-antType Antenna/OmniAntenna\
			-topoInstance $topo\
			-agentTrace ON\
		          -routerTrace ON
create-god  4	
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
$n0 label "tcp0"
$n1 label "sink1"
$n2 label "tcp1"
$n3 label "sink2"

#The below code is used to give the initial node positions.

$n0 set  X_  50
$n0 set  Y_  50
$n0 set  Z_ 0
$n1 set  X_ 200
$n1 set  Y_ 200
$n1 set  Z_ 0
$n2 set  X_ 400
$n2 set  Y_ 400
$n2 set  Z_ 0
$n3 set  X_  600
$n3 set Y_  600

$n3 set Z_  0
$ns at 0.1  "$n0 setdest 50 50 15"
$ns at 0.1  "$n1  setdest 200 200 25"
$ns at 0.1  "$n2 setdest 400 400 25"
$ns at 0.1  "$n3 setdest 600 600 25"
set tcp0 [new Agent/TCP]
$ns attach-agent $n0 $tcp0
set ftp0  [new Application/FTP]
$ftp0 attach-agent $tcp0
set sink1  [new Agent/TCPSink]
$ns attach-agent $n1 $sink1
$ns connect $tcp0 $sink1
set tcp1  [new Agent/TCP]
$ns attach-agent $n2 $tcp1
set ftp1  [new Application/FTP]
$ftp1 attach-agent $tcp1
set sink2  [new Agent/TCPSink]
$ns attach-agent $n3 $sink2
$ns connect $tcp1 $sink2
$ns at 5 "$ftp0 start"
$ns at 10 "$ftp1 start"

#The below code is used to give node movements .

$ns  at 100  "$n2  setdest  500 500 25"

proc finish {} {
globalnf ns tf
$ns flush-trace
exec nam wireless.nam&
close $tf
exit 0
}
$ns at 250 "finish"
$ns run

AWK Script (wireless.awk)

BEGIN{
	count1=0
	count2=0
	pack1=0
	pack2=0
	time1=0
	time2=0
}
{
	if($1=="r" && $3=="_1_" && $4=="AGT")
	{
		count1++
		pack1=pack1+$8
		time1=$2
	}
	if($1=="r" && $3=="_3_" && $4=="AGT")
	{
		count2++
		pack2=pack2+$8
		time2=$2
	}
}
END{
	printf("The Throughput  from n0 to n1: %fMbps\n",((count1*pack1*8)/(time1*1000000)))

	printf(" The Throughput  from n2 to n3: %fMbps\n",((count2*pack2*8)/(time2*1000000)))
}
Execution of the program

Type the following commands in the terminal window for executing the programs.

ns wireless.tcl 
awk –f wireless.awk wireless.tr



Output:

The Throughput from n0 to n1: 5444Mbps 

The Throughput from n2 to n3: 345Mbps

Snapshot : 
Snap Shot:
