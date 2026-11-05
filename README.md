# Customer Credit Application Portal

A public online credit application form for a B2B services business, replacing a paper form. The applicant verifies their email with a one-time code, completes the application, signs it, and a PDF record is saved to a restricted SharePoint library for the accounts team.

> Case study. The production code, flows and data belong to my employer and are not published. This repository documents the design, architecture and decisions. All names and values shown are generic.

## Form sections

1. Business details
2. Accounts contact
3. Director or principal
4. Credit limit requested (14-day terms)
5. Bank details
6. Two or more trade references
7. Terms and conditions, declaration and signature

## Flow

```mermaid
flowchart TD
    A["Applicant opens public link"] --> B["Enter email"]
    B --> C["SendOTP flow<br/>emails a one-time code"]
    C --> D["Enter code"]
    D --> E{"VerifyOTP<br/>valid?"}
    E -->|no| D
    E -->|yes| F["Complete form sections"]
    F --> G["Sign and submit"]
    G --> H["Submit flow"]
    H --> I["Build application record"]
    I --> J[("Restricted SharePoint library<br/>credit applications")]
    H --> K["Notify accounts"]
```

## Architecture

```mermaid
flowchart LR
    P["Power Pages<br/>/credit-application"] -->|HTTPS| O1["SendOTP"]
    P --> O2["VerifyOTP"]
    P --> O3["Submit"]
    O1 & O2 --> L[("OTP list")]
    O3 --> D[("Document library<br/>permissions: accounts only")]
```

## Handling sensitive data

- Bank details are kept only inside the saved application document. They are not written to list columns, so they never appear in list views or exports.
- The library is restricted to the accounts team and the service account. Access is controlled by permissions rather than file passwords, so nobody has to manage or share a password.
- The OTP step confirms the applicant controls the email address they give before anything is stored.

## Stack

Power Pages, Power Automate (three HTTP-triggered flows), SharePoint Online, Outlook.
