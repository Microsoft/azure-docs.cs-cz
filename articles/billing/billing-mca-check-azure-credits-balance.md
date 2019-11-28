---
title: Sledování zůstatku kreditu Azure u smlouvy se zákazníkem Microsoftu
description: Přečtěte si, jak sledovat zůstatek kreditu Azure u smlouvy se zákazníkem Microsoftu.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: bbd456f82e333ab8e096e5695a55be43c2084c6d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223795"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Sledování zůstatku kreditu Azure u smlouvy se zákazníkem Microsoftu

Zůstatek kreditu Azure u fakturačního účtu pro smlouvu se zákazníkem Microsoftu můžete zobrazit na webu Azure Portal. 

Pomocí kreditů můžete platit poplatky, na které se tyto kredity vztahují. Pokud používáte produkty, na které se kredity nevztahují, nebo při využívání překročíte zůstatek kreditu, začnou se vám účtovat poplatky. Další informace najdete v seznamu [produktů, na které se nevztahují kredity Azure](#products-that-arent-covered-by-azure-credits).

V rámci fakturačního účtu pro smlouvu se zákazníkem Microsoftu se kredity přiřazují k fakturačnímu profilu. Každý fakturační profil má vlastní kredity. K zobrazení zůstatku kreditu Azure u fakturačního profilu musíte mít roli vlastníka, přispěvatele, čtenáře nebo správce faktur daného fakturačního profilu nebo roli vlastníka, přispěvatele nebo čtenáře u fakturačního účtu. Další informace o rolích najdete v tématu [Vysvětlení rolí pro správu smluv se zákazníky Microsoftu v Azure](billing-understand-mca-roles.md).

Tento článek se týká fakturačního účtu smlouvy se zákazníky Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Kontrola zůstatku kreditu na webu Azure Portal

1. Přihlaste se k webu [Azure Portal]( https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Na levé straně vyberte **Kredity Azure**. V závislosti na úrovni přístupu možná budete muset vybrat fakturační účet nebo fakturační profil a teprve pak zvolit **Kredity Azure**.

4. Stránka Kredity Azure obsahuje následující informace:

   ![Snímek obrazovky se zůstatkem kreditu a transakcemi pro určitý fakturační profil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Označení               | Definice                           |
   |--------------------|--------------------------------------------------------|
   | Odhadovaný zůstatek  | Odhadované množství vašich kreditů po zvážení všech fakturovaných a nevyřízených transakcí |
   | Aktuální zůstatek    | Množství kreditů podle vaší poslední faktury. Nezahrnuje žádné nevyřízené transakce |
   | Transakce       | Fakturační transakce, které ovlivnily zůstatek kreditu Azure |

   Když odhadovaný zůstatek klesne na 0, začne se vám účtovat veškeré využití, a to i produktů, na které se jinak vztahují kredity.

6. Seznam kreditů pro daný fakturační profil zobrazíte výběrem položky **Seznam kreditů**. Seznam kreditů obsahuje následující informace:

   ![Snímek obrazovky se seznamy kreditů pro určitý fakturační profil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Označení | Definice |
   |---|---|
   | Zdroj | Zdroj pořízení kreditu |
   | Počáteční datum | Datum získání kreditu |
   | Datum vypršení platnosti | Datum, kdy vyprší platnost kreditu |
   | Aktuální zůstatek | Zůstatek na základě poslední faktury |
   | Původní množství | Původní množství kreditů |
   | Status | Aktuální stav kreditů. Možné stavy: Aktivní, Použito, Platnost vypršela nebo Platnost vyprší |

## <a name="check-your-credit-balance-programmatically"></a>Programová kontrola zůstatku kreditu

K programovému získání zůstatku kreditu u fakturačního účtu můžete použít rozhraní API pro [spotřebu](https://docs.microsoft.com/rest/api/consumption/) nebo [fakturaci Azure](https://docs.microsoft.com/rest/api/billing/).

V následujících příkladech se používají rozhraní REST API. PowerShell ani Azure CLI se v současné době nepodporují.

### <a name="find-billing-profiles-you-have-access-to"></a>Vyhledání fakturačních profilů, ke kterým máte přístup

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
V odpovědi rozhraní API se vrátí seznam fakturačních účtů a příslušných fakturačních profilů.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Pomocí vlastnosti `displayName` fakturačního profilu můžete identifikovat fakturační profil, u kterého chcete zkontrolovat zůstatek kreditu. Zkopírujte vlastnost `id` fakturačního profilu. Pokud například chcete zkontrolovat zůstatek kreditu u fakturačního profilu **Vývoj**, zkopírujte hodnotu ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="get-azure-credit-balance"></a>Získání zůstatku kreditu Azure 

Proveďte následující požadavek, ve kterém nahraďte `<billingProfileId>` hodnotou `id`, kterou jste zkopírovali v prvním kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

V odpovědi rozhraní API se vrátí odhadovaný a aktuální zůstatek u fakturačního profilu.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Název elementu  | Popis                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Odhadované množství vašich kreditů po zvážení všech fakturovaných a nevyřízených transakcí |
| `currentBalance`   | Množství kreditů na základě poslední faktury. Nezahrnuje žádné nevyřízené transakce.    |
| `pendingCreditAdjustments`      | Úpravy, jako jsou refundace, které se ještě nefakturovaly  |
| `expiredCredit`      |  Kredit, jehož platnost od poslední faktury vypršela  |
| `pendingEligibleCharges`  | Poplatky, na které se vztahují kredity, ale které se ještě nefakturovaly   |

### <a name="get-list-of-credits"></a>Získání seznamu kreditů

Proveďte následující požadavek, ve kterém nahraďte `<billingProfileId>` hodnotou `id`, kterou jste zkopírovali v prvním kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
V odpovědi rozhraní API se vrátí seznam kreditů Azure pro fakturační profil.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Název elementu  | Popis                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Původní množství kreditů |
| `closedBalance`   | Zůstatek na základě poslední faktury    |
| `source`      | Zdroj definující způsob získání kreditu |
| `startDate`      |  Datum aktivace kreditu  |
| `expirationDate`  | Datum vypršení platnosti kreditu   |
| `poNumber`  | Číslo nákupní objednávky faktury, na kterou se kredit účtoval   |

### <a name="get-transactions-that-affected-credit-balance"></a>Získání transakcí, které ovlivnily zůstatek kreditu

Proveďte následující požadavek, ve kterém nahraďte `<billingProfileId>` hodnotou `id`, kterou jste zkopírovali v prvním kroku (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Pokud chcete získat transakce za určité období, musíte předat hodnoty **startDate** a **endDate**.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
V odpovědi rozhraní API se vrátí všechny transakce, které ovlivnily zůstatek kreditu u vašeho fakturačního profilu.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Název elementu  | Popis                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Datum uskutečnění transakce |
| `description` | Popis transakce |
| `adjustments`   | Úpravy kreditu v rámci transakce    |
| `creditExpired`      | Množství kreditu, jehož platnost vypršela |
| `charges`      |  Poplatky za transakci  |
| `closedBalance`  | Zůstatek po transakci   |
| `eventType`  | Typ transakce   |
| `invoiceNumber`  | Číslo faktury, na kterou se transakce fakturovala. V případě nevyřízené transakce bude tato hodnota prázdná.   |

## <a name="how-credits-are-used"></a>Způsob uplatňování kreditů

Na fakturačním účtu pro smlouvu se zákazníkem Microsoftu spravujete své faktury a způsoby platby pomocí fakturačních profilů. Pro každý fakturační profil se jednou za měsíc generuje faktura a k úhradě této faktury používáte zvolené způsoby platby.

Kredity, které získáte, přiřadíte k fakturačnímu profilu. Ve chvíli vytváření faktury pro tento fakturační profil se kredity automaticky uplatní na celkové poplatky a vypočítá se částka, kterou máte zaplatit. Zbývající částku uhradíte pomocí nastavených způsobů platby, například šekem, bezhotovostním převodem nebo platební kartou.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty, na které se nevztahují kredity Azure

 Kredity Azure se nevztahují na následující produkty. Za využívání těchto produktů se vám budou bez ohledu na zůstatek kreditu účtovat poplatky:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registrovaný uživatel
- Openlogic
- Registrovaný uživatel s oprávněním ke vzdálenému přístupu XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (měsíční)
- Visual Studio Enterprise (roční)
- Visual Studio Professional (měsíční)
- Visual Studio Professional (roční)
- Produkty z Azure Marketplace
- plánům podpory Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Ověření přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Vysvětlení fakturačních účtů pro smlouvu se zákazníkem Microsoftu](billing-mca-overview.md)
- [Vysvětlení výrazů na faktuře za smlouvu se zákazníkem Microsoftu](billing-mca-understand-your-invoice.md)
