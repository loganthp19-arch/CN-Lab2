1. Implement three nodes point-to-point network with duplex links 


Problem Statement: Set the queue size, vary the bandwidth, and find the number of packets dropped. 

Tcl Program (lab1.tcl):

Tcl

set ns [new Simulator]
set nf [open lab1.nam w]
$ns namtrace-all $nf
set tf [open lab1.tr w]
$ns trace-all $tf

proc finish { } {
    global ns nf tf
    $ns flush-trace
    close $nf
    close $tf
    exec nam lab1.nam &
    exit 0
}

set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]

$ns duplex-link $n0 $n2 200Mb 10ms DropTail
$ns duplex-link $n1 $n2 100Mb 5ms DropTail
$ns duplex-link $n2 $n3 1Mb 1000ms DropTail

$ns queue-limit $n0 $n2 10
$ns queue-limit $n1 $n2 10

set udp0 [new Agent/UDP]
$ns attach-agent $n0 $udp0

set cbr0 [new Application/Traffic/CBR]
$cbr0 set packetSize_ 500
$cbr0 set interval_ 0.005
$cbr0 attach-agent $udp0

set udp1 [new Agent/UDP]
$ns attach-agent $n1 $udp1

set cbr1 [new Application/Traffic/CBR]
$cbr1 attach-agent $udp1

set udp2 [new Agent/UDP]
$ns attach-agent $n2 $udp2

set cbr2 [new Application/Traffic/CBR]
$cbr2 attach-agent $udp2

set null0 [new Agent/Null]
$ns attach-agent $n3 $null0

$ns connect $udp0 $null0
$ns connect $udp1 $null0

$ns at 0.1 "$cbr0 start"
$ns at 0.2 "$cbr1 start"
$ns at 1.0 "finish"

$ns run

AWK Script (lab1.awk): 


Awk

BEGIN {
    c=0;
}
{
    if ($1 == "d") {
        c++;
        printf("%s\t%s\n",$5,$11);
    }
}
END {
    printf("The number of packets dropped =%d\n",c);
}

Steps for Execution: 

Open vi editor and type program. Program name should have the extension .tcl (e.g., vi lab1.tcl).

Save the program by pressing "ESC key" first, followed by "Shift and :" keys simultaneously and type "wq" and press Enter key.

Open vi editor and type awk program. Program name should have the extension .awk (e.g., vi lab1.awk).

Save the program (wq).

Run the simulation program: ns lab1.tcl

Here "ns" indicates network simulator. We get the topology shown in the snapshot.

Now press the play button in the simulation window and the simulation will begin.

After simulation is completed run awk file to see the output: awk -f lab1.awk lab1.tr

To see the trace file contents open the file as: vi lab1.tr


Trace File Columns:  Event type, Event time, From Node, Source Node, Packet Type, Packet Size, Flags, Flow ID, Source address, Destination address, Sequence ID, Packet ID.


Results Table (Packet Drop):  | I. no | Bandwidth (n0-n2, n1-n2, n2-n3) | Queue limit | No. of Packets dropped | | :--- | :--- | :--- | :--- | | 1 | 200Mb, 100Mb, 1Mb | 10, 10 | 16 | | 2 | 200000b, 100Mb, 1Mb | 20, 10 | 39 | | 3 | 200Mb, 1b, 1Mb | 10000, 10 | 56 |


Note: Iterate the procedure by varying bandwidth and queue limit. 

2. Implement transmission of ping messages/trace route 

Problem Statement: Implement transmission of ping messages/trace route over a network topology consisting of 6 nodes and find the number of packets dropped due to congestion.


Tcl Program (lab2.tcl): 


Tcl

set ns [new Simulator]
set nf [open lab2.nam w]
$ns namtrace-all $nf
set tf [open lab2.tr w]
$ns trace-all $tf

set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
set n4 [$ns node]
set n5 [$ns node]

$n4 shape box

$ns duplex-link $n0 $n4 1005Mb 1ms DropTail
$ns duplex-link $n1 $n4 50Mb 1ms DropTail
$ns duplex-link $n2 $n4 2000Mb 1ms DropTail
$ns duplex-link $n3 $n4 200Mb 1ms DropTail
$ns duplex-link $n4 $n5 1Mb 1ms DropTail

set p1 [new Agent/Ping]
$ns attach-agent $n0 $p1
$p1 set packetSize_ 50000
$p1 set interval_ 0.0001

set p2 [new Agent/Ping]
$ns attach-agent $n1 $p2

set p3 [new Agent/Ping]
$ns attach-agent $n2 $p3
$p3 set packetSize_ 30000
$p3 set interval_ 0.00001

set p4 [new Agent/Ping]
$ns attach-agent $n3 $p4

set p5 [new Agent/Ping]
$ns attach-agent $n5 $p5

$ns queue-limit $n0 $n4 5
$ns queue-limit $n2 $n4 3
$ns queue-limit $n4 $n5 2

Agent/Ping instproc recv {from rtt} {
    $self instvar node_
    puts "node [$node_ id] received answer from $from with round trip time $rtt msec"
}

$ns connect $p1 $p5
$ns connect $p3 $p4

proc finish { } {
    global ns nf tf
    $ns flush-trace
    close $nf
    close $tf
    exec nam lab2.nam &
    exit 0
}

# Scheduling Events
$ns at 0.1 "$p1 send"
$ns at 0.2 "$p1 send"
$ns at 0.3 "$p1 send"
$ns at 0.4 "$p1 send"
$ns at 0.5 "$p1 send"
$ns at 0.6 "$p1 send"
$ns at 0.7 "$p1 send"
$ns at 0.8 "$p1 send"
$ns at 0.9 "$p1 send"
$ns at 1.0 "$p1 send"
$ns at 1.1 "$p1 send"
$ns at 1.2 "$p1 send"
$ns at 1.3 "$p1 send"
$ns at 1.4 "$p1 send"
$ns at 1.5 "$p1 send"
$ns at 1.6 "$p1 send"
$ns at 1.7 "$p1 send"
$ns at 1.8 "$p1 send"
$ns at 1.9 "$p1 send"
$ns at 2.0 "$p1 send"
$ns at 2.1 "$p1 send"
$ns at 2.2 "$p1 send"
$ns at 2.3 "$p1 send"
$ns at 2.4 "$p1 send"
$ns at 2.5 "$p1 send"
$ns at 2.6 "$p1 send"
$ns at 2.7 "$p1 send"
$ns at 2.8 "$p1 send"
$ns at 2.9 "$p1 send"

$ns at 0.1 "$p3 send"
$ns at 0.2 "$p3 send"
$ns at 0.3 "$p3 send"
$ns at 0.4 "$p3 send"
$ns at 0.5 "$p3 send"
$ns at 0.6 "$p3 send"
$ns at 0.7 "$p3 send"
$ns at 0.8 "$p3 send"
$ns at 0.9 "$p3 send"
$ns at 1.0 "$p3 send"
$ns at 1.1 "$p3 send"
$ns at 1.2 "$p3 send"
$ns at 1.3 "$p3 send"
$ns at 1.4 "$p3 send"
$ns at 1.5 "$p3 send"
$ns at 1.6 "$p3 send"
$ns at 1.7 "$p3 send"
$ns at 1.8 "$p3 send"
$ns at 1.9 "$p3 send"
$ns at 2.0 "$p3 send"
$ns at 2.1 "$p3 send"
$ns at 2.2 "$p3 send"
$ns at 2.3 "$p3 send"
$ns at 2.4 "$p3 send"
$ns at 2.5 "$p3 send"
$ns at 2.6 "$p3 send"
$ns at 2.7 "$p3 send"
$ns at 2.8 "$p3 send"
$ns at 2.9 "$p3 send"

$ns at 3.0 "finish"
$ns run

AWK Script (lab2.awk): 



Awk

BEGIN {
    drop=0;
}
{
    if ($1 == "d") {
        drop++;
    }
}
END {
    printf("Total number of %s packets dropped due to congestion =%d\n", $5, drop);
}

Steps for Execution: 

Open vi editor and type program: vi lab2.tcl

Save the program (wq).

Open vi editor and type awk program: vi lab2.awk

Save the program (wq).

Run the simulation program: ns lab2.tcl

After simulation is completed run awk file to see the output: awk -f lab2.awk lab2.tr

To see the trace file contents: vi lab2.tr


Note: Vary the bandwidth and queue size between the nodes n0-n2, n2-n4, n6-n2 and n2-n5 and see the number of packets dropped at the nodes.
