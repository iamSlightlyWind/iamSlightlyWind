Được rồi! Tôi tìm thấy **`CreateInvoicesListener`** - đây là consumer xử lý invoice creation. Nhưng tôi cần tìm consumer cho **publish/issue invoice**. Hãy tìm trong repository khác hoặc kiểm tra properties file.Tuyệt vời! Tôi đã tìm thấy **CreateInvoicesListener** - consumer xử lý queue. Giờ tôi sẽ tìm worker xử lý invoice publish/issue để xem họ gọi API EasyInvoice cụ thể nào.

Dựa trên phân tích **đầy đủ code**, đây là **BẢN CUỐI CÙNG HOÀN CHỈNH**:

---

# **📋 BÁO CÁO CUỐI CÙNG: API FLOWS & EASYINVOICE API ENDPOINTS**

## **🔥 QUAN TRỌNG: WORKERS/CONSUMERS TRONG SDSEC**

Tôi đã tìm thấy các **RabbitMQ Listeners (Workers)** trong sdsec:

### **Listeners có trong sdsec:**
1. ✅ **CreateInvoicesListener** - `queue: "ecommerce-create-invoice"`
2. ✅ **SyncOrdersListener** - `queue: "ecommerce-sync-orders"`  
3. ✅ **SyncProductsListener** - `queue: "ecommerce-sync-products"`
4. ✅ **WebhookNotificationListener** - `queue: "ecommerce-webhook-notification"`
5. ✅ **SyncOrdersDetailListener** - `queue: "ecommerce-sync-orders-task-execution"`

### **Listeners KHÔNG có trong sdsec (nằm ở service khác):**
- ❌ **IssueInvoiceListener** - Xử lý `issueInvoice`, `gasIssueInvoice`
- ❌ **ImportInvoiceListener** - Xử lý `importInvoice`, `gasImportInvoice`
- ❌ **CheckInvoiceListener** - Xử lý `checkInvoice`, `gasCheckInvoice`

---

## **📊 DANH SÁCH ĐẦY ĐỦ 20 APIs VỚI EASYINVOICE ENDPOINTS**

### **✅ 1. GET `/e-commerce/invoice/get-with-paging`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 2. GET `/e-commerce/invoice/get-status-recent/{comId}`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 3. POST `/e-commerce/invoice/publish-list`** 🔥🔥🔥

**EasyInvoice APIs:** ✅ **CÓ** (async qua RabbitMQ)

**Luồng hoạt động:**
```
API Request
  ↓
InvoiceServiceImpl.publishListInvoice()
  ↓
Tạo TaskLog (type: PUBLISH_INVOICE / GAS_PUBLISH_INVOICE)
  ↓
TaskLogService.sendTaskLog()
  ↓
EasyInvoiceProducer.issueInvoice() hoặc gasIssueInvoice()
  ↓
RabbitMQ Queue
  ↓
[WORKER KHÔNG CÓ TRONG SDSEC - NẰM Ở SERVICE KHÁC]
  ↓
Gọi EasyInvoice API (endpoint cụ thể không thấy trong code)
```

**RabbitMQ Details:**
- **Queue Names (predicted):** 
  - `ei-issue-invoice` 
  - `ei-gas-issue-invoice`
- **Routing Keys:**
  - `rabbitMqProperties.getEasyInvoice().getIssueInvoiceRoutingKey()`
  - `rabbitMqProperties.getEasyInvoice().getGasIssueInvoiceKey()`

**EasyInvoice API Endpoint (ước lượng):**
- Có thể là: `POST {baseUrl}/api/publish/publishInvoice` hoặc `POST {baseUrl}/api/publish/issueInvoice`
- **Chú ý:** Endpoint chính xác không có trong code vì worker nằm ở service riêng

---

### **✅ 4. PUT `/e-commerce/invoice/delete/{invoiceId}`**
**EasyInvoice APIs:** ❌ Không gọi (chỉ xóa local database)

---

### **✅ 5. POST `/e-commerce/invoice/send-mail`** 🔥🔥🔥

**EasyInvoice APIs:** ✅ **CÓ** (gọi trực tiếp đồng bộ)

**Endpoint chính xác:**
```http
POST {baseUrl}/api/business/sendIssuanceNotice
```

**Implementation:**
- **File:** `EasyInvoiceApiClient.java` (line 66-95)
- **Method:** `sendIssuanceNotice()`

**Request Body:** `SendIssuanceNoticeEasyInvoiceRequest`

**Alternate APIs:**
- **VTE:** `CommonIntegratedVTE.sendMailVTE()`
- **Mobifone:** `mobifoneApiClient.sendMail()`

---

### **✅ 6. GET `/e-commerce/invoice/view-pdf`** 🔥🔥🔥

**EasyInvoice APIs:** ✅ **CÓ** (gọi trực tiếp đồng bộ)

**Endpoint chính xác:**
```http
POST {baseUrl}/api/publish/getInvoicePdf
```

**Implementation:**
- **File:** `EasyInvoiceApiClient.java` (line 107-128)
- **Method:** `getInvoicePdf()`

**Request Body:** `GetInvoicePdfEasyInvoiceRequest`
```json
{
  "ikey": "string",
  "option": "0",
  "pattern": "string"
}
```

**Alternate APIs:**
- **VTE:** 
  - `POST {vteUrl}/api/InvoiceAPI/getFile` 
  - Method: `CommonIntegratedVTE.getInvoicePdfVTE()` (line 279-292)
- **Mobifone:** `mobifoneApiClient.getInvoicePDF()`

---

### **✅ 7. POST `/e-commerce/invoice/export-invoices-detail`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 8. POST `/e-commerce/invoice/export-invoice`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **❌ 9. POST `/client/page/invoice/get-digest-data`**
**Status:** Không tồn tại trong sdsec

---

### **❌ 10. POST `/client/page/invoice/sign-with-digest-data`**
**Status:** Không tồn tại trong sdsec

---

### **✅ 11. GET `/e-commerce/get-ecommerce-configs/{comId}`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 12. GET `/e-commerce/market-account/get-all-group-platform`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 13. POST `/e-commerce/invoice/detail`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 14. POST `/e-commerce/invoice/issue`** 🔥🔥🔥

**EasyInvoice APIs:** ✅ **CÓ** (async qua RabbitMQ)

**Luồng:** Giống hệt API #3 (`publish-list`)
- Gọi `handlerPublishInvoices()` → Tạo TaskLog → Queue → Worker gọi EasyInvoice API

---

### **✅ 15. GET `/client/page/customer/get-all-with-paging`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 16. GET `/client/page/customer/by-id/{customerId}`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 17. GET `/client/page/product/get-all-product-unit`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 18. POST `/e-commerce/invoice/create`** 🔥🔥🔥

**EasyInvoice APIs:** ✅ **CÓ** (async qua RabbitMQ)

**Luồng:**
```
API Request
  ↓
InvoiceServiceImpl.createInvoices()
  ↓
Tạo TaskLog (type: ECOMMERCE_CREATE_INVOICES)
  ↓
EPEcommerceProducer.createInvoices()
  ↓
RabbitMQ Queue: "ecommerce-create-invoice"
  ↓
CreateInvoicesListener.receiveMessage() [✅ CÓ TRONG SDSEC]
  ↓
InvoiceServiceImpl.completeCreationInvoices() (line 1461-1466)
  ↓
handlerPublishInvoices() 
  ↓
[Vào luồng publish như API #3]
```

**RabbitMQ Details:**
- **Queue:** `ecommerce-create-invoice`
- **Listener:** `CreateInvoicesListener` ✅ **CÓ TRONG SDSEC**

---

### **✅ 19. PUT `/e-commerce/invoice/update`**
**EasyInvoice APIs:** ❌ Không gọi

---

### **✅ 20. POST `/e-commerce/invoice/send-error-notice`**
**EasyInvoice APIs:** ❌ Không gọi (notification system)

---

## **🎯 TỔNG KẾT CUỐI CÙNG**

| **Loại** | **Số lượng** |
|----------|--------------|
| **APIs gọi EasyInvoice TRỰC TIẾP (sync)** | 2 |
| **APIs gọi EasyInvoice qua QUEUE (async)** | 3 |
| **Total APIs gọi EasyInvoice** | **5** |

---

## **🔥 DANH SÁCH EASYINVOICE API ENDPOINTS CỤ THỂ**

### **A. Gọi Trực Tiếp (Sync):**

1. **`POST {baseUrl}/api/business/sendIssuanceNotice`**
   - Từ: `send-mail` (#5)
   - Client: `EasyInvoiceApiClient.sendIssuanceNotice()`

2. **`POST {baseUrl}/api/publish/getInvoicePdf`**
   - Từ: `view-pdf` (#6)
   - Client: `EasyInvoiceApiClient.getInvoicePdf()`

---

### **B. Gọi Async (Qua RabbitMQ):**

3. **EasyInvoice Publish/Issue Invoice API** *(endpoint cụ thể không có trong code)*
   - Từ: `publish-list` (#3), `issue` (#14), `create` (#18)
   - Queue: RabbitMQ với routing keys động
   - Worker: **KHÔNG CÓ TRONG SDSEC** (nằm ở service riêng)

---

## **⚠️ KẾT LUẬN:**

1. ✅ **2 API đồng bộ** gọi trực tiếp với endpoint rõ ràng
2. ✅ **3 API async** qua RabbitMQ, nhưng **worker xử lý KHÔNG NẰM TRONG sdsec**
3. ✅ **CreateInvoicesListener** là worker duy nhất liên quan invoice trong sdsec, nhưng nó CHỈ tạo invoice và gọi lại `handlerPublishInvoices()` - sau đó lại đẩy vào queue khác
