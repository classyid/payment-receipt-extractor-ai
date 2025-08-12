# API Ekstraksi Data Bukti Pembayaran

## Deskripsi
API untuk menganalisis dan mengekstrak data dari bukti pembayaran/transfer bank menggunakan Gemini AI dengan sistem load balancing multi-key.

**Base URL:** `http://localhost:5579`  
**Version:** `2.0.0`

## Fitur Utama
- ✅ Multi API Key Load Balancing
- ✅ Automatic retry mechanism
- ✅ SQLite database storage
- ✅ Support berbagai format bukti pembayaran
- ✅ Real-time processing status
- ✅ Logging dan monitoring
- ✅ Pagination untuk history

---

## 📋 Endpoints

### 1. Process Payment Receipt
**Endpoint:** `POST /api/process-payment`  
**Deskripsi:** Memproses dokumen bukti pembayaran dan mengekstrak data transaksi

#### Request Headers
```
Content-Type: application/json
```

#### Request Body
```json
{
  "fileData": "base64_encoded_image_data",
  "fileName": "bukti_transfer.jpg",
  "mimeType": "image/jpeg"
}
```

#### Response Success (Valid Payment Receipt)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "original": {
      "fileId": "550e8400-e29b-41d4-a716-446655440000",
      "fileName": "bukti_transfer.jpg",
      "mimeType": "image/jpeg",
      "filePath": "uploads/550e8400-e29b-41d4-a716-446655440000.jpg"
    },
    "analysis": {
      "raw": "AI response text...",
      "parsed": {
        "status": "success",
        "transaction": {
          "status": "BERHASIL",
          "date": "11/08/2025",
          "time": "13:02:09",
          "type": "m-Transfer",
          "amount": "Rp xx",
          "admin_fee": "Rp 2,500.00",
          "total_amount": "Rp xx",
          "reference_number": "xx",
          "transaction_number": "xx",
          "receipt_number": "xx"
        },
        "sender": {
          "name": "xx",
          "bank": "xx",
          "account_number": "******xx"
        },
        "receiver": {
          "name": "xx",
          "bank": "BSI",
          "account_number": "xx",
          "address": "xx"
        },
        "additional_info": {
          "description": "xx",
          "purpose": "TRANSAKSI Lainnya",
          "method": "BI FAST",
          "terminal": "********xx",
          "device_id": ""
        }
      }
    }
  }
}
```

#### Response Success (Invalid Document)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "original": {
      "fileId": "550e8400-e29b-41d4-a716-446655440000",
      "fileName": "document.jpg",
      "mimeType": "image/jpeg",
      "filePath": "uploads/550e8400-e29b-41d4-a716-446655440000.jpg"
    },
    "analysis": {
      "raw": "Dokumen ini bukan bukti pembayaran",
      "parsed": {
        "status": "not_payment_receipt",
        "message": "Dokumen yang diberikan bukan merupakan bukti pembayaran"
      }
    }
  }
}
```

#### Response Error
```json
{
  "status": "error",
  "message": "Parameter wajib tidak ada: fileData, fileName, dan mimeType harus disediakan",
  "code": 400
}
```

---

### 2. API Documentation
**Endpoint:** `GET /api/docs`  
**Deskripsi:** Mendapatkan dokumentasi API dalam format JSON

#### Response
```json
{
  "api_name": "API Ekstraksi Data Bukti Pembayaran",
  "version": "2.0.0",
  "description": "API untuk menganalisis dan mengekstrak data dari bukti pembayaran/transfer bank menggunakan Gemini AI dengan load balancing",
  "features": [
    "Multi API Key Load Balancing",
    "Automatic retry mechanism",
    "SQLite database storage",
    "Support untuk berbagai format bukti pembayaran",
    "Real-time processing status"
  ],
  "endpoints": [
    {
      "path": "/api/process-payment",
      "method": "POST",
      "description": "Process payment receipt document",
      "parameters": {
        "fileData": "base64 encoded image/PDF data",
        "fileName": "original filename",
        "mimeType": "file MIME type (image/* or application/pdf)"
      }
    }
  ]
}
```

---

### 3. API Statistics
**Endpoint:** `GET /api/stats`  
**Deskripsi:** Mendapatkan statistik penggunaan API dan database

#### Response
```json
{
  "status": "success",
  "data": {
    "api_keys": {
      "usage_count": {
        "xx": 150
      },
      "error_count": {
        "xx": 5
      }
    },
    "documents": {
      "by_type": {
        "PAYMENT": 125
      },
      "by_validity": {
        "valid": 120,
        "invalid": 5
      }
    },
    "payments": {
      "total": 125,
      "successful": 118
    },
    "recent_activity": [
      {
        "date": "2025-08-12",
        "count": 15
      },
      {
        "date": "2025-08-11",
        "count": 23
      }
    ]
  }
}
```

---

### 4. Payment History
**Endpoint:** `GET /api/payments/history`  
**Deskripsi:** Mendapatkan riwayat pemrosesan bukti pembayaran dengan pagination

#### Query Parameters
- `page` (optional): Nomor halaman (default: 1)
- `per_page` (optional): Jumlah item per halaman (default: 10)

#### Example Request
```
GET /api/payments/history?page=1&per_page=5
```

#### Response
```json
{
  "status": "success",
  "data": {
    "payments": [
      {
        "timestamp": "2025-08-12T10:30:00",
        "filename": "transfer_bsi.jpg",
        "file_id": "550e8400-e29b-41d4-a716-446655440000",
        "transaction_status": "BERHASIL",
        "transaction_date": "11/08/2025",
        "transaction_type": "m-Transfer",
        "amount": "Rp xx",
        "reference_number": "xx",
        "sender_name": "xx",
        "receiver_name": "xx"
      },
      {
        "timestamp": "2025-08-12T09:15:00",
        "filename": "byond_transfer.jpg",
        "file_id": "660e8400-e29b-41d4-a716-446655440001",
        "transaction_status": "Transaksi Berhasil",
        "transaction_date": "12 Aug 2025",
        "transaction_type": "Nominal Transfer",
        "amount": "Rp xx",
        "reference_number": "xx",
        "sender_name": "xx",
        "receiver_name": "xxx"
      }
    ],
    "pagination": {
      "page": 1,
      "per_page": 5,
      "total": 125,
      "pages": 25
    }
  }
}
```

---

## 📝 Data Fields yang Diekstrak

### Transaction Info
- `status`: Status transaksi (BERHASIL, GAGAL, PENDING, dll)
- `date`: Tanggal transaksi
- `time`: Waktu transaksi
- `type`: Jenis transaksi (m-Transfer, BI Fast, dll)
- `amount`: Nominal transfer/pembayaran
- `admin_fee`: Biaya admin/biaya transaksi
- `total_amount`: Total amount
- `reference_number`: Nomor referensi
- `transaction_number`: Nomor transaksi
- `receipt_number`: Nomor struk

### Sender Info
- `name`: Nama pengirim
- `bank`: Bank pengirim
- `account_number`: Nomor rekening pengirim (biasanya sebagian disembunyikan)

### Receiver Info
- `name`: Nama penerima
- `bank`: Bank penerima
- `account_number`: Nomor rekening penerima
- `address`: Alamat/keterangan penerima

### Additional Info
- `description`: Berita/keterangan transfer
- `purpose`: Tujuan transaksi
- `method`: Metode pembayaran (Mobile Banking, Internet Banking, dll)
- `terminal`: Terminal/channel yang digunakan
- `device_id`: ID device/terminal

---

## 🔧 Format File yang Didukung

### Image Formats
- PNG (`.png`)
- JPEG (`.jpg`, `.jpeg`)
- GIF (`.gif`)
- BMP (`.bmp`)
- WebP (`.webp`)

### Document Formats
- PDF (`.pdf`)

### Ukuran File
- Maksimal: 16MB per file

---

## ⚠️ Error Codes

| Code | Status | Deskripsi |
|------|--------|-----------|
| 200 | Success | Request berhasil diproses |
| 400 | Bad Request | Parameter wajib tidak lengkap |
| 500 | Internal Server Error | Kesalahan server internal |

---

## 📊 Database Schema

### Table: payment_receipts
```sql
CREATE TABLE payment_receipts (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp TEXT NOT NULL,
    filename TEXT NOT NULL,
    file_id TEXT NOT NULL,
    transaction_status TEXT,
    transaction_date TEXT,
    transaction_time TEXT,
    transaction_type TEXT,
    amount TEXT,
    admin_fee TEXT,
    total_amount TEXT,
    reference_number TEXT,
    transaction_number TEXT,
    receipt_number TEXT,
    sender_name TEXT,
    sender_bank TEXT,
    sender_account TEXT,
    receiver_name TEXT,
    receiver_bank TEXT,
    receiver_account TEXT,
    receiver_address TEXT,
    description TEXT,
    purpose TEXT,
    payment_method TEXT,
    terminal TEXT,
    device_id TEXT,
    raw_response TEXT,
    FOREIGN KEY (file_id) REFERENCES metadata (file_id)
);
```

---

## 🛡️ Security & Privacy

### Data Handling
- File disimpan dengan UUID untuk mencegah collision
- Data sensitif seperti nomor rekening biasanya sudah tersamar dalam bukti pembayaran
- Semua aktivitas dicatat dalam log untuk audit trail

### API Key Management
- Support multiple API keys untuk load balancing
- Automatic retry dengan key rotation
- Error tracking per API key

---

## 📝 Example Usage

### Python Example
```python
import requests
import base64

# Read and encode image file
with open('bukti_transfer.jpg', 'rb') as f:
    file_data = base64.b64encode(f.read()).decode('utf-8')

# Prepare request
payload = {
    "fileData": file_data,
    "fileName": "bukti_transfer.jpg",
    "mimeType": "image/jpeg"
}

# Send request
response = requests.post(
    'http://localhost:5579/api/process-payment',
    json=payload,
    headers={'Content-Type': 'application/json'}
)

# Process response
if response.status_code == 200:
    result = response.json()
    if result['status'] == 'success':
        payment_data = result['data']['analysis']['parsed']
        print(f"Transaction Status: {payment_data['transaction']['status']}")
        print(f"Amount: {payment_data['transaction']['amount']}")
        print(f"Reference: {payment_data['transaction']['reference_number']}")
    else:
        print(f"Error: {result['message']}")
else:
    print(f"HTTP Error: {response.status_code}")
```

### JavaScript Example
```javascript
// File input handler
document.getElementById('fileInput').addEventListener('change', async (e) => {
    const file = e.target.files[0];
    if (!file) return;
    
    // Convert to base64
    const reader = new FileReader();
    reader.onload = async (event) => {
        const base64Data = event.target.result.split(',')[1];
        
        // Send to API
        try {
            const response = await fetch('/api/process-payment', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    fileData: base64Data,
                    fileName: file.name,
                    mimeType: file.type
                })
            });
            
            const result = await response.json();
            
            if (result.status === 'success') {
                console.log('Payment data:', result.data.analysis.parsed);
            } else {
                console.error('Error:', result.message);
            }
        } catch (error) {
            console.error('Network error:', error);
        }
    };
    
    reader.readAsDataURL(file);
});
```

---

## 🔄 Load Balancing & Retry Logic

API menggunakan sistem load balancing dengan multiple Gemini API keys:

1. **Round-robin key selection**
2. **Error tracking per key**
3. **Automatic failover**
4. **Health checking**

Jika satu API key mengalami error rate > 50%, system akan otomatis beralih ke key lain.

---

## 📞 Support

Untuk pertanyaan teknis atau bug report, silakan hubungi tim development atau buat issue di repository project.
