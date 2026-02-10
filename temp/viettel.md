Let me view the actual file directly via URL to get the complete method:Perfect! Now I have the complete implementation. Let me provide you with the **ACTUAL** fields that are being filled and sent to Viettel:

---

# **ACTUAL DATA SENT TO VIETTEL (Based on Code Analysis)**

## **Fields That Are ACTUALLY FILLED:**

### **1. TOP-LEVEL FIELDS:**
```json
{
  "supplierTaxCode": "string",  // ✅ REQUIRED - Company tax code
  "originalInvoiceType": null,   // ❌ NOT SET - left null
  "originalTemplateCode": null   // ❌ NOT SET - left null
}
```

### **2. GENERAL_INVOICE_INFO:** ✅ **MOSTLY FILLED**
```json
{
  "invoiceType": "string",              // ✅ First char of templateCode (e.g., "1")
  "templateCode": "string",             // ✅ Invoice template (e.g., "1C23MNM")
  "invoiceSeries": "string",            // ✅ Pattern (e.g., "C23MNM")
  "invoiceIssuedDate": "string",        // ✅ Epoch milliseconds
  "currencyCode": "string",             // ✅ From invoice.getCurrencyUnit()
  "adjustmentType": null,               // ❌ NOT SET
  "adjustedNote": null,                 // ❌ NOT SET
  "adjustmentInvoiceType": null,        // ❌ NOT SET
  "originalInvoiceId": null,            // ❌ NOT SET
  "originalInvoiceIssueDate": null,     // ❌ NOT SET
  "additionalReferenceDesc": null,      // ❌ NOT SET
  "additionalReferenceDate": null,      // ❌ NOT SET
  "paymentStatus": true,                // ✅ ALWAYS TRUE (hardcoded)
  "cusGetInvoiceRight": true,           // ✅ ALWAYS TRUE (hardcoded)
  "exchangeRate": BigDecimal,           // ⚠️  CONDITIONAL - only if invoice.getExchangeRate() != null
  "transactionUuid": "string",          // ✅ invoice.getIkey()
  "certificateSerial": "string",        // ⚠️  CONDITIONAL - only set for USB token flow
  "originalInvoiceType": null,          // ❌ NOT SET
  "originalTemplateCode": null,         // ❌ NOT SET
  "reservationCode": "string",          // ✅ invoice.getFkey()
  "adjustAmount20": "string",           // ⚠️  CONDITIONAL - only if invoice.getDiscountVatRate() != null
  "invoiceNote": null                   // ❌ NOT SET
}
```

### **3. BUYER_INFO:** ✅ **PARTIALLY FILLED**
```json
{
  "buyerName": "string",                  // ✅ invoice.getBuyerName()
  "buyerCode": "string",                  // ✅ invoice.getCustomerCode()
  "buyerLegalName": "string",             // ✅ invoice.getCustomerName()
  "buyerTaxCode": "string",               // ✅ invoice.getCustomerTaxCode()
  "buyerAddressLine": "string",           // ✅ invoice.getCustomerAddress() OR "Không có thông tin"
  "buyerPhoneNumber": "string",           // ✅ invoice.getCustomerPhone()
  "buyerFaxNumber": null,                 // ❌ NOT SET
  "buyerEmail": null,                     // ❌ NOT SET
  "buyerBankName": null,                  // ❌ NOT SET
  "buyerBankAccount": null,               // ❌ NOT SET
  "buyerDistrictName": null,              // ❌ NOT SET
  "buyerCityName": null,                  // ❌ NOT SET
  "buyerCountryCode": null,               // ❌ NOT SET
  "buyerIdType": null,                    // ❌ NOT SET
  "buyerIdNo": null,                      // ❌ NOT SET
  "buyerBirthDay": null,                  // ❌ NOT SET
  "buyerNotGetInvoice": null              // ❌ NOT SET
}
```

### **4. SELLER_INFO:** ✅ **MOSTLY FILLED** (if companyInfoResponse != null)
```json
{
  "sellerLegalName": "string",            // ✅ companyInfoResponse.getName()
  "sellerTaxCode": "string",              // ✅ companyInfoResponse.getTaxCode()
  "sellerAddressLine": "string",          // ✅ companyInfoResponse.getAddress() OR "Không có thông tin"
  "sellerPhoneNumber": "string",          // ✅ companyInfoResponse.getPhoneNumber()
  "sellerFaxNumber": "string",            // ✅ companyInfoResponse.getFaxNumber()
  "sellerEmail": "string",                // ✅ companyInfoResponse.getEmail()
  "sellerBankName": "string",             // ✅ companyInfoResponse.getBankName()
  "sellerBankAccount": "string",          // ✅ companyInfoResponse.getBankAccount()
  "sellerDistrictName": null,             // ❌ NOT SET
  "sellerCityName": null,                 // ❌ NOT SET
  "sellerCountryCode": null,              // ❌ NOT SET
  "sellerWebsite": null,                  // ❌ NOT SET
  "merchantCode": null,                   // ❌ NOT SET
  "merchantName": null,                   // ❌ NOT SET
  "merchantCity": null                    // ❌ NOT SET
}
```

### **5. PAYMENTS:** ✅ **SIMPLE ARRAY WITH ONE ITEM**
```json
[
  {
    "paymentMethod": null,                  // ❌ NOT SET
    "paymentMethodName": "string"           // ✅ invoice.getPaymentMethod()
  }
]
```

### **6. ITEM_INFO:** ✅ **FULLY POPULATED ARRAY**
```json
[
  {
    "lineNumber": null,                      // ❌ NOT SET (commented out)
    "selection": integer,                    // ✅ Based on feature mapping
    "itemCode": "string",                    // ✅ itemInv.getCode()
    "itemName": "string",                    // ✅ itemInv.getName()
    "unitCode": "",                          // ✅ ALWAYS EMPTY STRING (hardcoded)
    "unitName": "string",                    // ✅ itemInv.getUnit()
    "unitPrice": BigDecimal,                 // ✅ itemInv.getUnitPrice()
    "quantity": BigDecimal,                  // ✅ itemInv.getQuantity()
    "itemTotalAmountWithoutTax": BigDecimal, // ⚠️  CONDITIONAL - itemInv.getAmount()
    "taxPercentage": BigDecimal,             // ⚠️  CONDITIONAL - with VAT rate swap (-1 ↔ -2)
    "taxAmount": BigDecimal,                 // ⚠️  CONDITIONAL - itemInv.getVatAmount()
    "isIncreaseItem": false,                 // ⚠️  CONDITIONAL - only if feature == 3
    "itemNote": null,                        // ❌ NOT SET
    "batchNo": null,                         // ❌ NOT SET
    "expDate": null,                         // ❌ NOT SET
    "discount": null,                        // ❌ NOT SET
    "discount2": null,                       // ❌ NOT SET
    "itemDiscount": BigDecimal,              // ⚠️  CONDITIONAL - itemInv.getDiscountAmount()
    "itemTotalAmountAfterDiscount": BigDecimal, // ⚠️  CONDITIONAL - itemInv.getTotalPreTax()
    "itemTotalAmountWithTax": BigDecimal     // ⚠️  CONDITIONAL - itemInv.getTotalAmount()
  }
]
```

### **7. METADATA:** ❌ **EMPTY**
```json
[] // or null - NOT SET
```

### **8. METER_READING:** ❌ **EMPTY**
```json
[] // or null - NOT SET
```

### **9. SUMMARIZE_INFO:** ✅ **FULLY FILLED**
```json
{
  "sumOfTotalLineAmountWithoutTax": BigDecimal,  // ✅ invoice.getAmount()
  "totalAmountWithoutTax": BigDecimal,           // ✅ invoice.getTotalPreTax()
  "totalTaxAmount": BigDecimal,                  // ✅ invoice.getVatAmount()
  "totalAmountWithTax": BigDecimal,              // ✅ invoice.getTotalAmount()
  "totalAmountWithTaxFrn": null,                 // ❌ NOT SET
  "totalAmountWithTaxInWords": null,             // ❌ NOT SET
  "isTotalAmountPos": null,                      // ❌ NOT SET
  "isTotalTaxAmountPos": null,                   // ❌ NOT SET
  "isTotalAmtWithoutTaxPos": null,               // ❌ NOT SET
  "discountAmount": BigDecimal,                  // ✅ invoice.getDiscountAmount()
  "settlementDiscountAmount": null,              // ❌ NOT SET
  "isDiscountAmtPos": null                       // ❌ NOT SET
}
```

### **10. TAX_BREAKDOWNS:** ✅ **SIMPLE ARRAY WITH ONE ITEM**
```json
[
  {
    "taxPercentage": "string",             // ✅ invoice.getVatRate().toString()
    "taxableAmount": null,                 // ❌ NOT SET
    "taxAmount": null,                     // ❌ NOT SET
    "taxableAmountPos": null,              // ❌ NOT SET
    "taxAmountPos": null,                  // ❌ NOT SET
    "taxExemptionReason": null             // ❌ NOT SET
  }
]
```

---

## **SUMMARY OF WHAT'S ACTUALLY SENT:**

### **✅ FULLY POPULATED (Required):**
1. `supplierTaxCode`
2. `generalInvoiceInfo` (11 fields filled, 12 fields null)
3. `buyerInfo` (6 fields filled, 12 fields null)
4. `sellerInfo` (8 fields filled, 7 fields null)
5. `payments` (1 field filled: `paymentMethodName`)
6. `itemInfo` (full array with 9-12 fields per item)
7. `summarizeInfo` (5 fields filled, 9 fields null)
8. `taxBreakdowns` (1 field filled: `taxPercentage`)

### **❌ NOT SENT (Always null/empty):**
1. `originalInvoiceType` (top-level)
2. `originalTemplateCode` (top-level)
3. `metadata` (empty array)
4. `meterReading` (empty array)
5. All buyer email, bank, district, city, ID fields
6. All seller district, city, website, merchant fields
7. All item notes, batch, expiry date, individual discount fields
8. All summarize info word amounts and position flags
9. All tax breakdown amounts and flags

### **⚠️  CONDITIONALLY FILLED:**
- `exchangeRate` - only if foreign currency
- `adjustAmount20` - only if discount VAT rate exists
- `certificateSerial` - only for USB token flow
- Item-level tax/discount/amount fields - only if not null

---

**The actual JSON sent is ~60% populated with meaningful data, 40% null fields.**
