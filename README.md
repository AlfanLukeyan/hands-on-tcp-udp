# Hands on Wireshark TCP UDP

## Authors
Alfan Lukeyan Rizki - 5025211046

## Contents
- [TCP Segment](#tcp-segement)
- [UDP Segment](#tcp-segement)

## TCP Segment
### Requirements
Answer the following questions3, either from your own live trace, or by opening the Wireshark captured packet file tcp-wireshark-trace1-1 in http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-8.1.zip

1.  What is the IP address and TCP port number used by the client computer (source) that is transferring the alice.txt file to gaia.cs.umass.edu?
    - Pertama, lakukan ping terhadap url `gaia.cs.umass.edu` di terminal untuk mendapatkan IP dari alamat url tersebut
    ![ping-url](/img/Screenshot%202023-10-16%20at%2018.35.33.png)
    - Selanjutnya, tambahkan filter expression `http`
    ![soal-1](<img/Screenshot 2023-10-17 at 00.21.44.png>)
    - Berdasarkan hasil tersebut maka diapatkan IP Adress dari client adalah `192.168.86.68` dengan port `55639`

2.  What is the IP address of gaia.cs.umass.edu? On what port number is it sending and receiving TCP segments for this connection?
    - IP Destination = `128.119.245.12` and Destination Port = `80`

3. What is the sequence number of the TCP SYN segment that is used to initiate the TCP connection between the client computer and gaia.cs.umass.edu? What is it in this TCP segment that identifies the segment as a SYN segment? Will the TCP receiver in this session be able to use Selective Acknowledgments (allowing TCP to function a bit more like a “selective repeat” receiver, see section 3.4.5 in the text)?
    - Packet inisial --> Transmission Control Protocol
    ![soal-3](<img/Screenshot 2023-10-17 at 00.33.44.png>)
    - Sequence Number: `0` and Sequence Number (raw) : `4236649187`, Because has Flags: `0x002 (SYN)`, Yes, flag `SYN: Set` berarti packet tersebut adalah SYN segment, sehingga dapat digunakan sebagai selective acknowledgments

4.  What is the sequence number of the SYNACK segment sent by gaia.cs.umass.edu to the client computer in reply to the SYN? What is it in the segment that identifies the segment as a SYNACK segment? What is the value of the Acknowledgement field in the SYNACK segment? How did gaia.cs.umass.edu determine that value?
    - Packet SYNACK, sebagai berikut:
    ![soal-4](<img/Screenshot 2023-10-17 at 00.39.00.png>)
    - Sequence Number: `0` and Sequence Number (raw) : `1068969752` of the SYNACK segment
    - th segment that indentifies the segment as a SYNACK segment is Flags : `0x012 (SYN, ACK)`
    - value of the Acknowledgement field in the SYNACK segment `4236649188`
    - Acknowledgement number didapatkan dari sequence number dari SYN segment ditambah 1 `(ACK=Seq no+1)`.

5.  What is the sequence number of the TCP segment containing the header of the HTTP POST command? Note that in order to find the POST message header, you’ll need to dig into the packet content field at the bottom of the Wireshark window, looking for a segment with the ASCII text “POST” within its DATA field4,5. How many bytes of data are contained in the payload (data) field of this TCP segment? Did all of the data in the transferred file alice.txt fit into this single segment?
    - Packet HTTP POST dengan expression filter `tcp && http.request.method == "POST"`, sebagai berikut:
    ![soal-5](<img/Screenshot 2023-10-17 at 00.47.59.png>)
    - the HTTP POST segment sequence number = `152041` atau sequence number (raw) = `4236801228`
    - TCP payload (`1385 bytes`)
    - Informasi dalam paket menunjukkan bahwa paket tersebut dikirim menggunakan multipart atau `MIME`, yang berarti data yang dikirim tidak hanya terdiri dari satu segmen, tetapi beberapa segmen. Payload yang dikirim memiliki ukuran `1385 byte`, sementara alice.txt memiliki ukuran `149KB`.

6.  Consider the TCP segment containing the HTTP “POST” as the first segment in
the data transfer part of the TCP connection.
    Packet HTTP POST, sebagai berikut :
    ![soal-6](<img/Screenshot 2023-10-17 at 00.54.49.png>)
    - At what time was the first segment (the one containing the HTTP POST) in the data-transfer part of the TCP connection sent?
            ![soal-6.1](<img/Screenshot 2023-10-17 at 00.59.39.png>)
        -   Berdasarkan tangkapan layar yang diberikan, dapat dilihat bahwa paket pertama dikirim pada frame ke-4, yang dikirim pada `0.24047 seconds`.
    - At what time was the ACK for this first data-containing segment received?
            ![soal-6.2](<img/Screenshot 2023-10-17 at 01.00.28.png>)
        -   Berdasarkan screenshot tersebut, ACK dari packet pertama diterima pada `frame: 7` dimana frame tersebut diterima pada `0.052671 seconds`
    - What is the RTT for this first data-containing segment?
        -   RTT dari segment pertama adalah `0.028624 seconds`
    - What is the RTT value the second data-carrying TCP segment and its ACK?
            ![soal-g.3](<img/Screenshot 2023-10-17 at 01.01.26.png>)
        -   RTT dari segment kedua adalah `0.028628 seconds`
    - What is the EstimatedRTT value (see Section 3.5.3, in the text) after the
    ACK for the second data-carrying segment is received? Assume that in making this calculation after the received of the ACK for the second segment,

7.  What is the length (header plus payload) of each of the first four data-carrying TCP segments?6
    -   `4 * (Payload + Header)` = 4 * (1448 + 32) = 4 * 1480 = `5920 byte`

8.  What is the minimum amount of available buffer space advertised to the client by gaia.cs.umass.edu among these first four data-carrying TCP segments7? Does the lack of receiver buffer space ever throttle the sender for these first four data- carrying segments?

    ![soal-8](<img/Screenshot 2023-10-17 at 01.12.32.png>)
    - Berdasarkan tangkapan layar yang diberikan, diketahui bahwa jumlah minimum buffer space yang tersedia adalah `131712`, yang diperoleh dari `window size value`. Selain itu, dari tangkapan layar tersebut juga terlihat bahwa ruang buffer penerima tidak pernah memperlambat pengirim karena `window size value` selalu lebih besar dari `panjang` segmen yang dikirimkan.

9. Are there any retransmitted segments in the trace file? What did you check for (in the trace) in order to answer this question?
    - Yes, there are
    - Retransmitted segments can be detected via sequence number. When resending, there are packets where the sequence number of the next packet is not greater than the sequence number of the previous packet.

10. How much data does the receiver typically acknowledge in an ACK among the first ten data-carrying segments sent from the client to gaia.cs.umass.edu? Can you identify cases where the receiver is ACKing every other received segment (see Table 3.2 in the text) among these first ten data-carrying segments?
    - `1448 byte`
    - If the data is doubled, the acking segment for each segment is received, for example in the second segment the data is doubled from `1448` to `2896 bytes`

11. What is the throughput (bytes transferred per unit time) for the TCP connection? Explain how you calculated this value.