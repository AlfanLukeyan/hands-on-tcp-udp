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
    - Selanjutnya, tambahkan filter expression `ip.addr == 128.119.245.12`
    ![soal-1](<img/Screenshot 2023-10-16 at 21.30.26.png>)
    - Berdasarkan hasil tersebut maka diapatkan IP Adress dari client adalah `192.168.86.68` dengan port `55639`
2.  What is the IP address of gaia.cs.umass.edu? On what port number is it sending and receiving TCP segments for this connection?
