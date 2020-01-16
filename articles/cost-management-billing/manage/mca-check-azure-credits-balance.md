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
ms.openlocfilehash: c8ea083e216331904c9d5741b97f69b7f5a8249a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991331"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Sledování zůstatku kreditu Azure u smlouvy se zákazníkem Microsoftu

Můžete kontrolovat zůstatek kreditu Azure pro fakturační účet smlouvy o zákaznících Microsoftu ve Azure Portal nebo prostřednictvím rozhraní REST API.

V rámci fakturačního účtu pro smlouvu se zákazníkem Microsoftu se kredity přiřazují k fakturačnímu profilu. Každý Fakturační profil má své vlastní kredity, které se automaticky vztahují na poplatky na faktuře. K zobrazení zůstatku kreditu Azure u fakturačního profilu musíte mít roli vlastníka, přispěvatele, čtenáře nebo správce faktur daného fakturačního profilu nebo roli vlastníka, přispěvatele nebo čtenáře u fakturačního účtu. Další informace o rolích najdete v tématu [Vysvětlení rolí pro správu smluv se zákazníky Microsoftu v Azure](understand-mca-roles.md).

Tento článek se týká fakturačních účtů smluv se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Kontrola zůstatku kreditu

### <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Na stránce fakturační obory vyberte fakturační účet, pro který chcete sledovat zůstatek dal. Fakturační účet by měl být typu **smlouva Microsoft Customer Agreement**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Azure Portal pamatuje poslední obor fakturace, ke kterému přistupujete, a tento obor se zobrazí při příštím Cost Management + fakturační stránce. Pokud jste dříve navštívili Cost Management + fakturace, nezobrazí se stránka fakturační obory. Pokud ano, ověřte, že jste ve [správném oboru](#check-access-to-a-microsoft-customer-agreement). V takovém případě [Přepněte obor](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) a vyberte fakturační účet smlouvy o zákaznících Microsoftu.

3. Na levé straně vyberte **způsoby platby** a potom vyberte **kredity Azure**.

   ![Snímek obrazovky s vyrovnáváním kreditu pro fakturační profil](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. Stránka kredity Azure obsahuje následující oddíly:

   #### <a name="balance"></a>Zůstatek

   Část vyvážení zobrazuje souhrn zůstatku vašeho kreditu Azure.

   ![Snímek obrazovky s vyrovnáváním kreditu pro fakturační profil](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Doba účinnosti               | Definice                           |
   |--------------------|--------------------------------------------------------|
   | Odhadovaný zůstatek  | Odhadované množství vašich kreditů po zvážení všech fakturovaných a nevyřízených transakcí |
   | Aktuální zůstatek    | Množství kreditů podle vaší poslední faktury. Nezahrnuje žádné nevyřízené transakce |

   Když odhadovaný zůstatek klesne na 0, začne se vám účtovat veškeré využití, a to i produktů, na které se jinak vztahují kredity.

   #### <a name="credits-list"></a>Seznam kreditů

   V části seznam kreditů se zobrazí seznam kreditů Azure.

   ![Snímek obrazovky se seznamy kreditů pro určitý fakturační profil](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Doba účinnosti | Definice |
   |---|---|
   | Zdroj | Zdroj pořízení kreditu |
   | Počáteční datum | Datum získání kreditu |
   | Datum vypršení platnosti | Datum, kdy vyprší platnost kreditu |
   | Aktuální zůstatek | Zůstatek na základě poslední faktury |
   | Původní množství | Původní množství kreditů |
   | Stav | Aktuální stav kreditů. Možné stavy: Aktivní, Použito, Platnost vypršela nebo Platnost vyprší |

   #### <a name="transactions"></a>Transakce

   V části transakce se zobrazí všechny transakce, které ovlivnily zůstatek kreditu.

   ![Snímek kreditních transakcí pro fakturační profil](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Doba účinnosti | Definice |
   |---|---|
   | Datum transakce | Datum, kdy se transakce stala |
   | Popis | Popis transakce |
   | Částka| Částka transakce |
   | Zůstatek | Zůstatek po transakci |

    > [!NOTE]
    >
    > Pokud nevidíte kredity Azure na stránce způsoby platby, buď nemáte kredity, nebo jste nevybrali správný obor. Vyberte fakturační účet, který má kredity nebo jeden z jeho fakturačních profilů. Informace o tom, jak změnit obory, najdete [v tématu Switch Scope scopes in the Azure Portal](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Pokud zobrazujete kredity Azure v oboru fakturačního účtu a fakturační účet má více než jeden Fakturační profil, stránka kredity Azure zobrazí tabulku se shrnutím kreditů Azure pro každý Fakturační profil. V seznamu vyberte fakturační profil, vyberte způsoby platby a potom kredity Azure, abyste si zobrazili podrobnosti pro fakturační profil.

    ![Snímek obrazovky se seznamem kreditů pro fakturační účet](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-apitabrest"></a>[REST API](#tab/rest)

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

Pomocí vlastnosti `displayName` profilu fakturace Identifikujte profil fakturace, pro který chcete kontrolovat zůstatek kreditu. Zkopírujte vlastnost `id` fakturačního profilu. Pokud například chcete zkontrolovat zůstatek kreditu u fakturačního profilu **Vývoj**, zkopírujte hodnotu ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="get-azure-credit-balance"></a>Získání zůstatku kreditu Azure

Proveďte následující požadavek, který nahradí `<billingProfileId>` `id` zkopírovali jste v prvním kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

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

Proveďte následující požadavek, který nahradí `<billingProfileId>` `id` zkopírovali jste v prvním kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

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

Proveďte následující požadavek, který nahradí `<billingProfileId>` `id` zkopírovali jste v prvním kroku (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Pokud chcete získat transakce za určité období, musíte předat hodnoty **startDate** a **endDate**.

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

---

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
- Plány podpory Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Vysvětlení fakturačních účtů pro smlouvu se zákazníkem Microsoftu](../understand/mca-overview.md)
- [Vysvětlení výrazů na faktuře za smlouvu se zákazníkem Microsoftu](../understand/mca-understand-your-invoice.md)
