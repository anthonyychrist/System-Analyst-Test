# System-Analyst-Test

ERD
<img width="663" height="99" alt="Image" src="https://github.com/user-attachments/assets/111b1163-807f-43cf-a28c-0f705b790d43" />

Activity Diagram
![alt text](https://drive.google.com/file/d/1i14U_UMSANXlhRuN_2Q9z7RQqYyCNRoB/view)

Use Case Diagram
<img width="255" height="206" alt="Image" src="https://github.com/user-attachments/assets/1280c3b6-24cb-4147-904c-0b674ddaa7d0" />

Sequence Diagram
![alt text](https://drive.google.com/file/d/1FM-UiB2jNs0HIgcNFfScIa-qjNx2SLGx/view)

Table Design
| Table Name | Column Name | Data Type | Constraint | Description |
|-------------|--------------|------------|-------------|--------------|
| **TabelCabang** | KodeCabang | CHAR(3) | PK | Kode unik cabang |
| | NamaCabang | VARCHAR(50) | NOT NULL | Nama cabang |
| **TabelMotor** | KodeMotor | CHAR(3) | PK | Kode motor |
| | NamaMotor | VARCHAR(50) | NOT NULL | Nama motor |
| **TabelPembayaran** | NoKontrak | CHAR(10) | PK | Nomor kontrak pembayaran |
| | TglBayar | DATETIME | NOT NULL | Waktu pembayaran |
| | JumlahBayar | DECIMAL(12,2) | NOT NULL | Nominal pembayaran |
| | KodeCabang | CHAR(3) | FK → TabelCabang | Cabang asal pembayaran |
| | NoKwitansi | CHAR(11) | NOT NULL | Nomor kwitansi pembayaran |
| | KodeMotor | CHAR(3) | FK → TabelMotor | Jenis motor |

CREATE DATABASE InovasiDayaSolusi
USE InovasiDayaSolusi

CREATE TABLE TabelCabang (
    KodeCabang CHAR(3) PRIMARY KEY,
    NamaCabang VARCHAR(50) NOT NULL
);

CREATE TABLE TabelMotor (
    KodeMotor CHAR(3) PRIMARY KEY,
    NamaMotor VARCHAR(50) NOT NULL
);

CREATE TABLE TabelPembayaran (
    NoKontrak CHAR(10) PRIMARY KEY,
    TglBayar DATETIME NOT NULL,
    JumlahBayar DECIMAL(12,2) NOT NULL,
    KodeCabang CHAR(3) NOT NULL,
    NoKwitansi CHAR(11) NOT NULL,
    KodeMotor CHAR(3) NOT NULL,
    CONSTRAINT fk_Pembayaran_Cabang FOREIGN KEY (KodeCabang)
        REFERENCES TabelCabang(KodeCabang)
        ON UPDATE CASCADE ON DELETE RESTRICT,
    CONSTRAINT fk_Pembayaran_Motor FOREIGN KEY (KodeMotor)
        REFERENCES TabelMotor(KodeMotor)
        ON UPDATE CASCADE ON DELETE RESTRICT
);

INSERT INTO TabelCabang (KodeCabang, NamaCabang) VALUES
('115', 'Jakarta'),
('145', 'Ciputat'),
('175', 'Pandeglang'),
('190', 'Bekasi');

INSERT INTO TabelMotor (KodeMotor, NamaMotor) VALUES
('001', 'Suzuki'),
('002', 'Honda'),
('003', 'Yamaha'),
('004', 'Kawasaki');

INSERT INTO TabelPembayaran (NoKontrak, TglBayar, JumlahBayar, KodeCabang, NoKwitansi, KodeMotor) VALUES
('1151500001', '2014-10-20 17:14:13', 200000, '115', '14102000001', '001'),
('1451500002', '2014-10-20 16:14:13', 300000, '145', '14102000002', '001'),
('1151500003', '2014-10-20 09:14:13', 350000, '115', '14102000003', '003'),
('1751500004', '2014-10-19 16:14:13', 500000, '175', '14101900001', '002');

SELECT * 
FROM TabelPembayaran
WHERE DATE(TglBayar) = '2014-10-20';

INSERT INTO TabelCabang (KodeCabang, NamaCabang)
VALUES ('200', 'Tangerang');

UPDATE TabelPembayaran
SET KodeMotor = '001'
WHERE KodeCabang IN (
  SELECT KodeCabang FROM TabelCabang WHERE NamaCabang = 'Jakarta'
);

SELECT p.NoKontrak, p.TglBayar, p.JumlahBayar,
       c.KodeCabang, c.NamaCabang,
       p.NoKwitansi, m.KodeMotor, m.NamaMotor
FROM TabelPembayaran p
JOIN TabelCabang c ON p.KodeCabang = c.KodeCabang
JOIN TabelMotor m ON p.KodeMotor = m.KodeMotor;

SELECT c.KodeCabang, c.NamaCabang,
       p.NoKontrak, p.NoKwitansi
FROM TabelCabang c
LEFT JOIN TabelPembayaran p 
ON c.KodeCabang = p.KodeCabang;

SELECT c.KodeCabang, c.NamaCabang,
       COUNT(p.NoKontrak) AS TotalData,
       COALESCE(SUM(p.JumlahBayar), 0) AS TotalBayar
FROM TabelCabang c
LEFT JOIN TabelPembayaran p 
ON c.KodeCabang = p.KodeCabang
GROUP BY c.KodeCabang, c.NamaCabang;
