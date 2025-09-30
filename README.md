# PPILock-8086

Intel 8086 üzerinde **8255A Programmable Peripheral Interface (PPI)** kullanılarak
**8 bitlik şifre doğrulama** ve **7-segment/LED gösterimi** yapılan uygulama.

Devrede DIP-switch/sürücülerden okunan byte, bellekte tutulan `password` ile
karşılaştırılır; sonuç 7-segment/LED üzerinde gösterilir.

## Donanım Özeti
- **CPU:** 8086  
- **PPI:** 8255A (Port A/B/C)  
- **Adres kod çözücü:** 74154 + kapılar (M/IO ile I/O haritalama)  
- **Giriş:** DIP switch (Port B)  
- **Çıkış:** 7-segment/LED (Port A ve/veya Port C)

## I/O Haritalama 
- **Base:** `0x0438`  
  - **Port A:** `0x0438` (display/LED sürme)  
  - **Port B:** `0x043A` (DIP switch girişi)  
  - **Port C:** `0x043C` (digit select / ek LED’ler)  
  - **Control:** `0x043E`
 
<img width="1536" height="842" alt="image" src="https://github.com/user-attachments/assets/156a51b4-d9ff-4bc1-9e14-718e2d5d33c6" />


## 8255A Yapılandırma
- Başlangıçta kontrol kelimesi `0x80` yazılır: `1000 0000b`  
  → **Mode 0**, tüm port yönleri **çıkış**.  
- Not: Şifre okuması için **Port B giriş** olmalı. Bunun için kontrol kelimesini
  `1000 1000b = 0x88` (PA: out, PCupper: out, **PB: in**, PClower: out, Mode0) şeklinde
  ayarlayabilirsiniz.

## Program Akışı
1. Veri segmenti kurulumu (`password = 1010 1010b`).  
2. 8255A kontrol kelimesi yazılır (öneri: `0x88` → PB giriş).  
3. **Port B**'den okunan byte `password` ile karşılaştırılır.  
4. **Eşleşme** durumunda Port A/C’ye “OK”/sabit desen; **hata** durumunda
   yanıp-sönen desen yazılır.  
5. Sonsuz döngüde sürekli kontrol.


