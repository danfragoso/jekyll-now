---
layout: post
title: Como fazer um leitor compatível com libnfc no precinho.
---

Como fuçar na libnfc se o kit do Proxmark 3 custa $ 418,00 ? E os leitores do AliExpress custam com frete e impostos quase R$ 200,00 e só chegam depois de 3 meses ? A resposta é **ARDUINO !!!**

Pra montar esse leitor, precisaremos de duas coisas:

 - Arduino UNO

![unor3]({{ site.baseurl }}/images/arduino/unor3.jpg)

- PN532 (ElecHouse)

![pn532]({{ site.baseurl }}/images/arduino/pn532.jpg)


*É fácil encontrar o PN532 no Mercado Livre por volta de R$ 55,00. Já o Arduino UNO, mais ainda.*

### O Hardware

A primeira coisa a se fazer é colocar o PN532 em modo HSU (High Speed Uart), colocando os switches na posição 0.

![switch]({{ site.baseurl }}/images/arduino/switch.jpg)


 E soldar os headers nos pinos: GND, VCC, TXD e RXD. Nesse caso eu fiz um shield usando uma placa de circuito ilhada.

![shield]({{ site.baseurl }}/images/arduino/shield.jpg)


A segunda é preparar o arduino. E pra isso você vai precisar retirar o arduino do arduino ????? Com **CUIDADO** retire o chip do arduino e tenha cuidado pra não amassar os pinos.

![retir]({{ site.baseurl }}/images/arduino/retir.jpg)


- Ligue o TXD do PN532 ao TX do Arduino.
- Ligue o RXD do PN532 ao RX do Arduino.

Ligue o Arduino no cabo USB e pronto, Bob's your uncle !!!

### O Software

Agora pra instalar os drivers do pn532 no computador vamos precisar de uma distro Linux. Kali Linux é bacana pois ele já vem com algumas ferramentas instaladas, como o Mifare Classic Offline Cracker (MFOC) e o MiFare Classic Universal toolKit (MFCUK). Mas em qualquer "ubuntão" funciona muito bem. Use os comandos abaixo pra instalar e configurar a libnfc no PN532 no Uno. ~~pfvr, desligue o arduino do pc antes rodar os comandos abaixo.~~

```bash
apt-get install autoconf libtool libusb-dev libpcsclite-dev build-essential
wget http://dl.bintray.com/nfc-tools/sources/libnfc-1.7.1.tar.bz2
tar -jxvf libnfc-1.7.1.tar.bz2
cd libnfc-1.7.1
autoreconf -vis
./configure --with-drivers=pn532_uart --sysconfdir=/etc --prefix=/usr
make clean all
sudo make install
sudo mkdir /etc/nfc
wget https://raw.githubusercontent.com/danfragoso/libnfc/master/contrib/libnfc/pn532_uart2unousb.conf
sudo cp pn532_uart2unousb.conf /etc/nfc/libnfc.conf
```
Ligue o leitor ao computador.

```bash
sudo nfc-list
```
Se você fez tudo certo o output deve ser parecido com esse.

```bash
nfc-list uses libnfc 1.7.1

NFC device: pn532_uart:/dev/ttyUSB0 opened
```
![Imgur](http://i.imgur.com/musidFA.png)

Se não, tente usar o terminal como root e `ls /dev/` pode te ajudar a descobrir onde está o seu leitor.

Agora nós temos um leitor compatível com a libnfc por menos de R$ 90,00.

![readr]({{ site.baseurl }}/images/arduino/readr.jpg)

Agora é só partir pros ~~hackinagem~~ estudos !
