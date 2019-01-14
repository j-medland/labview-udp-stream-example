# LabVIEW-UDP-Stream-Example (LV 2017)

This example project demonstrates a one to many streaming architecture using UDP for data transfer.

UDP offers potential throughput benefits over TCP when you don't care about dropped packets too much but it requires you to implement more of the heavy lifting

This project consists of two main VIs:
  ### UDP Streamer.vi
  - This VI acts as the stream source and once running creates a *TCP* (but I thought this was UDP!) named-service to which clients can connect and exchange information to allow for a dynamically port-assigned UDP connection to start.
  - It handles new clients connecting by launching a *Connection Handler* which off-loads lots of the hard work to an asynchronously running VI.
  - It sends out data packets by pushing regular data packets out to the connection handlers via a notifier
  
  ### UDP Reader.vi
  - This VI acts as the stream-sink and once running it connects to the source at the specified IP address and stream-name
  - It establishes a UDP connection and consumes whatever data is sent its way.
  
 ## Challenges with UDP
UDP doesn't provide any means of checking that anyone is actually listening to the data that you are sending and nor are there any guarantees that datagrams will arrive in sequence or at all! To overcome this, the streamer uses the initial TCP connection to send out a periodic 'heartbeat' to the client to check it is still there. This prevents old Connection Handlers lingering if a client disconnects. 

Another issue with UDP is the limit on the number of bytes that can be sent in one datagram (approximately 548). This means that larger packets have to be sent in *chunks*. Additional information needs to be added to each chunk to allow the client to reconstruct them back into an arbitrarily large packet. This example makes no attempt to request packets which have been dropped but it does warn if it sees some chunks are being lost when it is trying to recieve all the chunks.
