
# Build

    autoreconf -iv
    ./configure
    make

# Install

    make install

# Usage

    Usage: tcpkali [OPTIONS] <host:port> [<host:port>...]
    Where OPTIONS are:
      -h, --help                  Display this help screen
      --debug <level=1>           Debug level [0..2].
      -c, --connections <N=1>     Connections to keep open to the destinations
      -r, --connect-rate <R=100>  Limit number of new connections per second
      --connect-timeout <T=1s>    Limit time spent in a connection attempt
      --channel-lifetime <T>      Shut down each connection after T seconds
      --channel-bandwidth <Bw>    Limit single connection bandwidth
      --message-first <string>    Send this message first, once
      -m, --message <string>      Message to repeatedly send to the remote
      -f, --message-file <name>   Read message to send from a file
      --message-rate <R>          Messages per second per connection to send
      -l, --listen-port <port>    Listen on the specified port
      -w, --workers <N=4>         Number of parallel threads to use
      --duration <T=10s>          Load test for the specified amount of time
      --statsd                    Enable StatsD output (default disabled)
      --statsd-host <host>        StatsD host to send data (default is localhost)
      --statsd-port <port>        StatsD port to use (default is 8125)
      --statsd-namespace <string> Metric namespace (default is "tcpkali")
    And variable multipliers are:
      <R>:  k (1000, as in "5k" is 5000)
      <Bw>: kbps, Mbps (bits per second), kBps, MBps (bytes per second)
      <T>:  ms, s, m, h (milliseconds, seconds, minutes, hours)


# Sysctls for high load (N connections, such as 50k):

    kern.maxfiles=10000+2*N         # BSD
    kern.maxfilesperproc=100+N      # BSD
    fs.file-max=10000+2*N           # Linux

    # For load-generating clients.
    net.ipv4.ip_local_port_range="10000  65535"  # Linux.
    net.inet.ip.portrange.hifirst=10000          # BSD/Mac.
    net.inet.ip.portrange.hilast=65535           # (Enough for N < 55535)
    net.ipv4.tcp_tw_reuse=1

    # If using netfilter on Linux:
    net.netfilter.nf_conntrack_max=N
    net.netfilter.nf_conntrack_max=N/4

# Readings:

 * On TIME-WAIT state and its reuse:
     http://vincent.bernat.im/en/blog/2014-tcp-time-wait-state-linux.html
 * On netfliter settings:
     http://serverfault.com/questions/482480/

