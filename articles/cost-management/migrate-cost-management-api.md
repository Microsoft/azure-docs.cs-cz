---
title: Migrace z smlouvy Enterprise do rozhraní API smlouvy zákazníků společnosti Microsoft – Azure | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit vliv na rozhraní API při migraci z Microsoft Enterprise Agreement (EA) na základě smlouvy Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/02/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: a12360e60026fd4251cc232caccd1bc52a34d2ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344706"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migraci na smlouvu Enterprise pro smlouvy zákazníků společnosti Microsoft

Tento článek vám pomůže pochopit vliv na rozhraní API při migraci z Microsoft Enterprise Agreement (EA) na základě smlouvy Microsoft. Následující nová rozhraní API slouží k zobrazení nákladů a ceny:

- Rozhraní Pricesheet API pro smlouvy Microsoft zákazníka
  - Ceník pomocí faktury
  - Ceník profilem fakturace
  - Ceník otevřít fakturačního období
- Podrobnosti o použití rozhraní API pro smlouvy Microsoft zákazníka
  - Podrobnosti o použití pomocí faktury, fakturace profilu (filtrovat podle části faktury)
    - Podrobnosti o použití podle data využití
    - Podrobnosti o použití pro fakturační účet
    - Podrobnosti o použití profilu fakturace
    - Podrobnosti o použití části faktury
    - Podrobnosti o použití pro předplatné
  - Podrobnosti o využití za měsíc k datu
    - Podrobnosti o použití pro fakturační účet
    - Podrobnosti o použití profilu fakturace
    - Podrobnosti o použití části faktury
    - Podrobnosti o použití pro předplatné
  - Podrobnosti o využití podle fakturační období
- Kredity rozhraní API pro smlouvy Microsoft zákazníka
  - Aktuální zůstatek souhrn rozhraní API profilem fakturace
  - Kredit ve výši události rozhraní API Billing profil
  - Kredit ve výši velké API profilem fakturace
- Rozhraní API pro dotazy
- Poplatky za rozhraní API
  - Poplatky za rozhraní API podle fakturační účet
  - Poplatky za rozhraní API podle profilu fakturace
  - Poplatky za rozhraní API z oddílu faktury
- Invoice List rozhraní API
  - Fakturační profil
  - Ve fakturační účet
- Rozhraní API pro rezervace
- Integrace Power BI rozhraní API
- Export rozhraní API
  - ve fakturační účet
  - Fakturační profil
  - V části faktury
- Transakce rozhraní API
- Rozpočty rozhraní API
- Souhrnné náklady na rozhraní API

## <a name="effect-on-automation"></a>Vliv na služby automation

Rozhraní API používaná se smlouvou Microsoft zákazníka se liší od těch, které jsou součástí smlouvy Enterprise. Rozhraní API nebo rozhraní API založená na Azure Resource Manageru pro podnikové registrace může mít nastavení rozhraní API automation pomocí dříve enterprise na základě klíčů. Pokud ano, aktualizujte konfiguraci služby automation pro použití s Microsoft zákaznických smluv novějších rozhraní API.

Pokud chcete zobrazit seznam starší nepodporované rozhraní API podle zákazníka smlouvu se společností Microsoft, naleznete v tématu [nepodporované rozhraní API](#unsupported-apis).

Rozhraní API pro Microsoft zákaznických smluv podporují jenom ověřování Azure Resource Manageru – ověřování pomocí klíče rozhraní API nepodporují.

## <a name="pricesheet-apis-show-prices-for-azure-services"></a>Rozhraní Pricesheet API zobrazit ceny pro služby Azure

Rozhraní Pricesheet API pro smlouvy zákazníka Microsoftu patří cenové informace pro všemi službami, které jsou hodnoceny pro fakturaci, na základě množství využití. Rozhraní API neobsahuje nákupy nárok. Například nákupy na Marketplace nárok, rezervované instance ceny a nabídky odborné pomoci. Rozhraní API poskytuje jedinečné ceny pro každou službu pro konkrétní název objednávky měřidla a produktu.

### <a name="view-the-pricesheet-for-azure-consumption-services-by-billing-profile"></a>Fakturační profil zobrazit ceník služby Azure Consumption

Ceny platné pro všechny služby Azure Consumption první a třetí strany můžete zobrazit v ceník pro fakturační profil. Chcete-li zobrazit ceny služeb Azure Consumption pomocí rozhraní API. Pro fakturační profil uvedené ceny platí pro všechna předplatná, která patří do fakturační profil.

- Formát CSV
  - PŘÍSPĚVEK -`https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31& format=csv`
- Formát JSON
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json`

### <a name="view-current-open-billing-or-service-period-estimated-prices"></a>Zobrazit aktuální otevřít fakturace nebo služby období odhad ceny

Chcete-li zobrazit odhadované ceny na jakoukoli službu v otevřeném aktuálního fakturačního cyklu nebo služby, můžete zobrazit Pricesheet ve formátu CSV nebo JSON.

- Formát CSV
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`
- Formát JSON
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

Uvedené v Pricesheet vždy aktuální období služby ceny odrážejí nejnovější ceny platné pro měřidla. Ceny se aktualizují každý den, pomocí nejnovějšími cenami pro měření a produktu pořadí.

Při kontrole nákladů na základě ceny v otevřeném období, jsou odhadované náklady. Poslední uzamčené cena na konci období open service platí pro použití v otevřeném období.

Otevřít období je aktuální období, služby, kde využití nebyl dosud hodnocení pro výpočet nákladů a fakturace.

### <a name="view-invoice-by-billing-profile-prices"></a>Zobrazit fakturu ve fakturační profil ceny

Zobrazit ceny služeb Azure Consumption, které se účtují v konkrétní fakturu za fakturační profil. Chcete-li zobrazit ceny platné pouze pro služby, které jsou uvedené na faktuře pomocí rozhraní API. Faktura je identifikován číslo faktury.

- Formát CSV
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`

- Formát JSON
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

`{invoiceId}` Uvedené dříve je stejné jako **číslo faktury** uvedené v souboru PDF souhrn faktury. Tady je příklad.

![Zobrazuje číslo faktury, která odpovídá InvoiceID nebyl obrázku](./media/migrate-cost-management-api/invoicesummary.png)

## <a name="pricesheet-api-asynchronous-response"></a>Asynchronní odpověď rozhraní Pricesheet API

Podpora rozhraní API [REST Azure asynchronní](../azure-resource-manager/resource-manager-async-operations.md) formátu. Po volání rozhraní API pomocí GET, zobrazí se následující odpověď:

```
            No Response Body
            HTTP Status 202 Accepted
```

 Následující záhlaví se posílají umístění výstupu.

```
Location: https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
Azure-AsyncOperation: https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
   Retry-After: 10
 OData-EntityId: {operationId}
```

Klienta můžete provést volání GET na umístění. Odpověď na volání GET se shodují s informacemi o příklad uvedenému výše, dokud operace dosaženo stavu dokončení nebo selhání. Po dokončení odpověď na volání pro získání na umístění vrátí stejnou odpověď, jako kdyby se provedl operaci ve stejnou dobu. Tady je příklad.

```
HTTP Status 200
i.e.
            {
    “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
    “name”: {operationId},
    “type”: “Microsoft.Consumption/operationResults”,
    “properties” : {
          “downloadUrl”: {urltoblob},
          “vaildTill”: “Date”
}
}
```

Kromě toho může klienta také provést volání GET `Azure-AsyncOperation`. Koncový bod vrátí stav operace. Tady je příklad:

```
HTTP Status 200
               {
"id": “providers/Microsoft.Consumption/operationStatus/{operationId}",
"name": "operationId",
"status" : "Succeeded | Failed | Canceled",
"startTime": "<DateLiteral per ISO8601>",
"endTime": "<DateLiteral per ISO8601>",
"percentComplete": <double between 0 and 100>
"properties": {
    /\* The resource provider can choose the values here, but it should only be
       returned on a successful operation (status being "Succeeded"). \*/
},
"error" : {
    /\* This is the OData v4 format, used by the RPC and will go into the
     v2.2 Azure REST API guidelines \*/
    "code": "BadArgument",
    "message": "The provided database &#39;foo&#39; has an invalid username."
}
}
```

### <a name="pricesheet-fields"></a>Ceník pole

Přečtěte si informace na [vysvětlení podmínek vašeho ceníku zákaznické smlouvy Microsoft](../billing/billing-mca-understand-pricesheet.md) zobrazíte všechna pole Ceník a jejich popisy.

Ceny na základě využití, cena použití uzamčen pro službu období od začátku období. Cena služby je použít a uzamčení k datu nákupu. Limitu služby je obvykle kalendářní měsíc pro služby Azure.

Cena za službu Azure Consumption v zákaznické smlouvy Microsoft je jedinečný pro produkt pořadí a měření.

Pole, které lze použít k jednoznačné identifikaci ceny služby Azure Consumption jsou:

- productOrderName
- meterId (také meterName)

Ceny pro smlouvy Microsoft zákazníků je definována jinak, než kde je jedinečný pro ceny služby v registraci smlouvy Enterprise:

- Produkt
- PartNumber
- Měření
- nabídka

### <a name="pricesheet-support"></a>Podpora ceník

Rozhraní Pricesheet API v současné době podporuje pouze ceny smlouvy zákazníka Microsoftu pro Azure první strany a služby poskytované třetích stran. Nepodporuje ceny za služby webu Marketplace, rezervované instance a výhody hybridní Azure (AHUB).

Rozhraní API poskytuje jen je cena ze jednotku, která se bude poplatek účtovat podle služby. Tato cena je rovno nebo menší než vyjednané ceně.

## <a name="query-for-usage-and-costs"></a>Dotaz na využití a nákladů

Rozhraní API podrobnosti o použití poskytuje přístup k Azure a využití služeb Marketplace jiných výrobců a náklady. Ceny jsou založené na vyjednávací ceny pro vaši smlouvu. Zákazníkům se smlouvou EA přesunutí na základě smlouvy používejte podrobnosti o použití fakturačnímu účtu nebo fakturace oboru profilu. Může být dotaz na žádný rozsah, s následujícími výjimkami:

|   | Zákazníkem | Smlouva Enterprise | Jednotlivé smlouvy (průběžné platby) |
| --- | --- | --- | --- |
| Fakturační účet | Ano | Ano | neuvedeno |
| Profily fakturace (faktura) | Ano | Není k dispozici. Zobrazí fakturační účet. | Není k dispozici. Zobrazit předplatná. |
| Oddíly faktury | Ne. Fakturační profilů pomocí filtru. | neuvedeno | neuvedeno |
| Oddělení EA | neuvedeno | Ano | neuvedeno |
| EA registrační účty | neuvedeno | Ano | Není k dispozici. Zobrazit předplatná. |
| Skupiny pro správu | Ne. Fakturační účet používejte. | Ano | Ano |
| Předplatná | Ano | Ano | Ano |
| Skupiny prostředků | Ne. Pomocí filtru předplatných. | Ne. Pomocí filtru předplatných. | Ne. Pomocí filtru předplatných. |

Vlastnosti vrácené UsageDetails za typ smlouvy se liší. Zobrazit [dokumentace k rozhraní API UsageDetails](/rest/api/consumption/usagedetails/) zobrazit úplný seznam.

### <a name="power-bi-integration"></a>Integrace Power BI

Zákazníkům se smlouvou EA přesunutí na základě smlouvy Microsoft by se měla zastavit pomocí jakékoli existující integrace s Power BI a Microsoft Azure Consumption Insights pomocí své smlouvy EA informace. Místo toho můžete použít:

- Power BI Desktop – nový Power BI vytvořit sestavy pro smlouvy Microsoft Customer s [Azure Cost Management connector](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights).
- Služba Power BI – přesun z [Microsoft Azure Consumption Insights](https://docs.microsoft.com/power-bi/service-connect-to-azure-consumption-insights) balíčku obsahu do aplikace Azure Cost Management (smlouvy zákazníka), který je k dispozici na [AppSource](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview).

## <a name="unsupported-apis"></a>Nepodporované rozhraní API

Microsoft zákaznických smluv nejsou podporovány následující rozhraní API smlouvy Enterprise. Alternativy k nepodporované rozhraní API jsou popsány.

[Získat ceník podle předplatného](/rest/api/consumption/pricesheet/get/) se nepodporuje. Vypíše ceny pro obor ID předplatného. Místo toho můžete použít **Pricesheet fakturační profil**. Ceny služby v rámci předplatného je stejné jako ceny v oboru fakturační profil. Uživatele, který volá rozhraní API musí mít požadovaná oprávnění.

**Cena list - získat podle fakturační období** se nepodporuje. Získá ceníku pro obor tak, že ID předplatného a fakturačního období. Místo toho můžete použít **Pricesheet fakturační profil**. Cena služby v oboru předplatného se rovná cena služby ve fakturační oboru profilu. Uživatele, který volá rozhraní API musí mít požadovaná oprávnění.

[Ceník pro podnikové zákazníky pomocí registrace](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) se nepodporuje. A [ceník pro podnikové zákazníky pomocí registrace pro fakturační období](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) se nepodporuje. Oba poskytují příslušné frekvence jednotlivých měření pro danou registrace a fakturační období. Místo toho můžete použít **Pricesheet fakturační profil**. Ceník v oboru registrace je k dispozici pro fakturační profil ceník. Uživatele, který volá rozhraní API musí mít požadovaná oprávnění.

## <a name="see-also"></a>Další informace najdete v tématech
- Další informace o [Azure Consumption REST API](/rest/api/consumption/).
