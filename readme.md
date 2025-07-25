# Verifiable Credential (VC) Schema Design Approaches

This repository contains VC (Verifiable Credential) schemas used for various types of certificates like **Enrolment Certificate**, **Caste Certificate**, **Marksheet**, **Income Certificate**, etc.

When designing VC schemas, we considered two different approaches for structuring data. Below is a simple explanation of both approaches and why we prefer one over the other.

---

## 🛠️ Approach 1: Entity-Level Standard Schema

In this approach, we define reusable schemas for core entities such as:

- `Person`
- `Organization`
- `Issuer`
- `Result`
- `Marksheet`
- `Subject`
- `Address`

Once these standard entities are defined, we use them to build VC schemas by referencing them.

### Example

```json
{
  "credentialSubject": {
    "person": { "$ref": "#/definitions/Person" },
    "issuer": { "$ref": "#/definitions/Issuer" },
    "marksheet": { "$ref": "#/definitions/Marksheet" }
  }
}
```

### **✅ Advantages**

- Promotes **reusability** of common data structures.  

- Ensures **consistency** across different VC types that use the same entity.  

### **❌ Disadvantages**

- Difficult to **customize entity data** based on different certificate use cases.  

- Risk of enforcing unnecessary fields across contexts.  

#### **Example Scenario**

Suppose the Person schema defines dateOfBirth as a required field.

- In a **Marksheet**, this is fine.  

- In a **Caste Certificate**, DOB might not be necessary — just name, gender, and caste may suffice.  

This rigidity makes it harder to handle certificates that have different requirements for the same entity.

## **🧾 Approach 2: Flat Schema per Certificate (Preferred)**

In this approach, **each certificate defines its own schema** without depending on shared entity definitions. Even if fields like fullName, dateOfBirth, or address are reused across certificates, they are defined independently.

### **Example**

```json
{
  "credentialSubject": {
    "fullName": "Amit Kumar",
    "fatherName": "Ramesh Kumar",
    "annualIncome": 300000,
    "businessIncome": 50000,
    "issueDate": "2024-07-01"
  }
}
```

### **✅ Advantages**

- Provides **maximum flexibility** for schema customization.  

- Allows tailoring fields (and whether they are required) for **each use case**.  

- Makes it easier to handle **state-wise or department-wise variations**.  

### **❌ Disadvantages**

- Duplicate field definitions across schemas.  

- Slightly increased maintenance effort.  

#### **Example Scenario**

In **Income Certificates**:

- All versions may require annualIncome.  

- Some states also require familyId, agriculturalIncome, or incomeFromSalary.  

With flat schemas, each state's requirements can be modeled independently without affecting others.

## **🎯 Why We Chose Flat Schema (Approach 2)**

We chose the **Flat Schema per Certificate** approach because:

- Different certificates have **different business rules**.  

- The same entity (like Person) may have **different mandatory or optional fields** depending on the context.  

- It supports **customization per issuer**, e.g., different states or departments.  

## **💡 Summary Comparison**

| **Feature** | **Entity-Based Schema** | **Flat Schema (Preferred)** |
| --- | --- | --- |
| Reusability | ✅ Yes | ❌ No |
| Flexibility | ❌ Limited | ✅ High |
| Custom field rules per use case | ❌ Difficult | ✅ Easy |
| Supports evolving use cases | ❌ No | ✅ Yes |
| Schema complexity | ✅ Lower | ❌ Slightly higher |

## **📌 Final Thoughts**

The flat schema approach gives us the flexibility and control needed to support real-world certificate issuance processes where rules may vary by region, department, or regulation. While it leads to some repetition, it ensures that every certificate is self-contained and easy to understand, validate, and evolve.