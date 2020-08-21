Congestion Control
------------------

- Paramers:
    - std::chrono::microseconds _rcInterval;          // UDT Rate control interval
- Timers:
    - QElapsedTimer _lastRCTime;                      // last rate increase time
- Flags:
    - bool _slowStart{ true };                        // if in slow start phase
    - bool _loss{ false };                            // if loss happened since last rate increase
- Counters:
    - PacketID _lastAck;                              // last ACKed seq no
    - PacketID _lastDecSeq;                           // biggest sequence number when last time the packet sending rate is decreased
    - std::chrono::microseconds _lastDecPeriod{ 0 };  // value of PacketSendPeriod when last decrease happened
    - unsigned _nakCount{ 0 };                        // current number of NAKs in the current period
    - unsigned _decRandom{ 1 };                       // random threshold on decrease by number of loss events
    - unsigned _avgNAKNum{ 0 };                       // average number of NAKs in a congestion period
    - unsigned _decCount{ 0 };                        // number of decreases in a congestion epoch
