---
title: Migrace z rozhraní API služby Enterprise Reporting na rozhraní API Azure Resource Manageru
description: Tento článek vám pomůže porozumět rozdílům mezi rozhraními API služby Reporting a rozhraními API Azure Resource Manageru, tomu, co je třeba očekávat při migraci na rozhraní API Azure Resource Manageru, a novým funkcím, které jsou s novými rozhraními API Azure Resource Manageru k dispozici.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 24fbf52c8fd0338537862a54e15e8a249541a701
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635786"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Migrace z rozhraní API služby Enterprise Reporting na rozhraní API Azure Resource Manageru

Tento článek pomůže vývojářům, kteří vytvářejí vlastní řešení s využitím [rozhraní API služby Azure Reporting pro podnikové zákazníky](../manage/enterprise-api.md), migrovat na rozhraní API Azure Resource Manageru pro Cost Management. Podpora instančního objektu pro novější rozhraní API Azure Resource Manageru je teď všeobecně dostupná. Rozhraní API Azure Resource Manageru jsou ve fázi aktivního vývoje. Zvažte možnost migrace namísto použití starších rozhraní API služby Azure Reporting pro podnikové zákazníky. Starší rozhraní API se vyřazují z provozu. Tento článek vám pomůže porozumět rozdílům mezi rozhraními API služby Reporting a rozhraními API Azure Resource Manageru, tomu, co je třeba očekávat při migraci na rozhraní API Azure Resource Manageru, a novým funkcím, které jsou s novými rozhraními API Azure Resource Manageru k dispozici.

## <a name="api-differences"></a>Rozdíly rozhraní API

Následující informace popisují rozdíly mezi staršími rozhraními API služby Reporting pro podnikové zákazníky a novějšími rozhraními API Azure Resource Manageru.

| **Použití** | **Rozhraní API smlouvy Enterprise** | **Rozhraní API Azure Resource Manageru** |
| --- | --- | --- |
| Ověřování | Klíč rozhraní API zřízený na portálu EA (Enterprise Agreement) | Ověřování Azure Active Directory (Azure AD) s využitím tokenů uživatelů nebo instanční objektů. Instanční objekty nahrazují klíče rozhraní API. |
| Rozsah a oprávnění | Všechny požadavky jsou v rozsahu registrace. Přiřazení oprávnění klíčů rozhraní API určují, jestli se vrací data pro celou registraci, jedno oddělení nebo konkrétní účet. Bez ověřování uživatelů. | Uživatelům nebo instančním objektům se přiřazuje přístup k rozsahu registrace, oddělení nebo účtu. |
| Koncový bod URI | https://consumption.azure.com | https://management.azure.com |
| Fáze vývoje | V režimu údržby. Chystá se vyřazování z provozu. | Aktivně se vyvíjí. |
| Dostupná rozhraní API | Omezená na aktuální dostupnost. | Jako náhrada jednotlivých rozhraní API EA jsou k dispozici ekvivalentní rozhraní API. <p> K dispozici jsou i další [rozhraní API služby Cost Management](/rest/api/cost-management/), včetně následujících: <p> <ul><li>Rozpočty</li><li>Výstrahy<li>exporty</li></ul> |

## <a name="migration-checklist"></a>Kontrolní seznam pro migraci

- Seznamte se s [rozhraními REST API Azure Resource Manageru](/rest/api/azure).
- Určete, která rozhraní API EA používáte, a v [mapování rozhraní API EA API na rozhraní API Azure Resource Manageru](#ea-api-mapping-to-new-azure-resource-manager-apis) si najděte, na která rozhraní API Azure Resource Manageru se má přejít.
- Nakonfigurujte autorizaci a ověřování pro rozhraní API Azure Resource Manageru.
  - Pokud rozhraní API pro Azure Resource Manager ještě nepoužíváte, [zaregistrujte svou klientskou aplikaci v Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). Registrace vytvoří instanční objekt, který můžete použít k volání rozhraní API.
  - Přiřaďte přístup instančního objektu k potřebným rozsahům, jak je uvedeno níže.
  - Aktualizujte veškerý naprogramovaný kód tak, aby [používal ověřování Azure AD](/rest/api/azure/#create-the-request) s využitím vašeho instančního objektu.
- Otestujte rozhraní API a potom aktualizujte veškerý naprogramovaný kód tak, aby se volání rozhraní API EA nahradila voláními rozhraní API Azure AD.
- Aktualizujte zpracování chyb tak, aby se začaly používat nové kódy chyb. Je přitom potřeba vzít v úvahu některé aspekty:
  - Rozhraní API Azure Resource Manageru mají časový limit 60 sekund.
  - Pro rozhraní API Azure Resource Manageru se uplatňuje omezování rychlosti. Případné překročení rychlosti vede k chybě omezování 429. Sestavujte řešení tak, abyste do krátkého časového intervalu neumístili příliš mnoho volání rozhraní API.
- Projděte si další rozhraní API služby Cost Management dostupná prostřednictvím Azure Resource Manageru a vyhodnoťte je pro pozdější použití. Další informace najdete v tématu věnovaném [použití dalších rozhraní API služby Cost Management](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Přiřazení přístupu instančního objektu k rozhraním API Azure Resource Manageru

Když vytvoříte instanční objekt pro programové volání rozhraní API Azure Resource Manageru API, musíte mu přiřadit správná oprávnění pro autorizaci a spouštění požadavků v Azure Resource Manageru. K dispozici jsou dvě architektury oprávnění pro různé scénáře.

### <a name="azure-billing-hierarchy-access"></a>Hierarchický přístup k fakturaci Azure

Pokud chcete přiřadit oprávnění instančního objektu k fakturačnímu účtu, oddělením nebo oborům účtů pro registraci vaší organizace, přečtěte si téma [přiřazení rolí k hlavním názvům služeb Azure smlouva Enterprise](../manage/assign-roles-azure-service-principals.md).

### <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Nová podpora instančního objektu se rozšiřuje na rozsahy specifické pro Azure, jako jsou skupiny pro správu, předplatná a skupiny prostředků. Oprávnění instančního objektu můžete k těmto rozsahům přiřadit přímo na [webu Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), nebo pomocí [Azure PowerShellu](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role).

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Mapování rozhraní API EA na nová rozhraní API Azure Resource Manageru

Pomocí následující tabulky určete, která rozhraní API EA momentálně využíváte, a vyhledejte rozhraní API Azure Resource Manageru, která se mají použít jako jejich náhrada.

| **Scénář** | **Rozhraní API EA** | **Rozhraní API Azure Resource Manageru** |
| --- | --- | --- |
| Souhrn zůstatků | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Ceník | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – použití pro sjednané ceny <p> [Retail Prices API](/rest/api/cost-management/retail-prices/azure-retail-prices) – použití pro maloobchodní ceny |
| Podrobnosti rezervovaných instancí | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Souhrn rezervovaných instancí | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Doporučení rezervovaných instancí | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Poplatky za rezervované instance | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Podrobnosti o migraci podle rozhraní API

V následujících částech jsou uvedeny příklady požadavků starých rozhraní API a příklady využívající nová náhradní rozhraní API.

### <a name="balance-summary-api"></a>Rozhraní API pro souhrn zůstatků

Při volání nového rozhraní API pro souhrn zůstatků použijte následující identifikátory URI požadavků. Jako billingAccountId by se mělo použít vaše registrační číslo.

#### <a name="supported-requests"></a>Podporované požadavky

[Získání pro registraci](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Změny textu odpovědi

_Původní text odpovědi:_

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Nový text odpovědi:_

Stejná data jsou teď k dispozici v poli `properties` odpovědi nového rozhraní API. Je možné, že dojde k drobným změnám názvů některých polí.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Ceník

Při volání nového rozhraní API pro ceník použijte následující identifikátory URI požadavků.

#### <a name="supported-requests"></a>Podporované požadavky

 Rozhraní API můžete volat s následujícími rozsahy:

- Registrace: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Předplatné: `subscriptions/{subscriptionId}`

[_Získání pro aktuální fakturační období_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Získání pro zadané fakturační období_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Změny textu odpovědi

_Původní odpověď:_

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Nová odpověď:_

Stará data jsou teď v poli `pricesheets` odpovědi nového rozhraní API. K dispozici jsou také podrobné informace o měřičích.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Podrobnosti o využití rezervovaných instancí

Microsoft aktivně nepracuje na synchronních rozhraních API Podrobnosti rezervace. V rámci migrace doporučujeme přejít na novější vzor asynchronního volání rozhraní API s podporou pro hlavní název služby (SPN). Asynchronní požadavky lépe zpracovávají velké objemy dat a omezují chyby vypršení časového limitu.

#### <a name="supported-requests"></a>Podporované požadavky

Při volání nového asynchronního rozhraní API Podrobnosti rezervace použijte následující identifikátory URI požadavků. Jako `billingAccountId` by se mělo použít vaše registrační číslo. Rozhraní API můžete volat s následujícími rozsahy:

- Registrace: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Ukázkový požadavek pro generování sestav podrobností rezervace

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Ukázkový požadavek pro dotaz na stav generování sestav

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Ukázková odpověď dotazu

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Změny textu odpovědi

Odpověď staršího synchronního rozhraní API Podrobnosti rezervace je uvedená níž.

_Původní odpověď:_

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Nová odpověď:_

Nové rozhraní API vytvoří soubor CSV. Projděte si následující pole v souboru.

| **Původní vlastnost** | **Nová vlastnost** | **Poznámky** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Nová vlastnost pro flexibilitu instancí |
|  | InstanceFlexibilityRatio | Nová vlastnost pro flexibilitu instancí |
| instanceId | InstanceName |  |
|  | Druh | Tato vlastnost je nová. Hodnota je `None`, `Reservation` nebo `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Souhrn využití rezervovaných instancí

Při volání nového rozhraní API Přehledy rezervace použijte následující identifikátory URI požadavků.

#### <a name="supported-requests"></a>Podporované požadavky

 Rozhraní API můžete volat s následujícími rozsahy:

- Registrace: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Získání denního souhrnu rezervací_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Získání měsíčního souhrnu rezervací_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Změny textu odpovědi

_Původní odpověď:_

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Nová odpověď:_

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Doporučení rezervovaných instancí

Při volání nového rozhraní API Doporučení pro rezervace použijte následující identifikátory URI požadavků.

#### <a name="supported-requests"></a>Podporované požadavky

 Rozhraní API můžete volat s následujícími rozsahy:

- Registrace: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Předplatné: `subscriptions/{subscriptionId}`
- Skupiny prostředků:`subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Získání doporučení_](/rest/api/consumption/reservationrecommendations/list)

Prostřednictvím tohoto rozhraní API jsou dostupná doporučení pro sdílené i samostatné rozsahy. Jako volitelný parametr rozhraní API můžete také využít filtr podle rozsahu.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Změny textu odpovědi

Doporučení pro sdílené a samostatné rozsahy jsou sloučena do jednoho rozhraní API.

_Původní odpověď:_

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Nová odpověď:_

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Poplatky za rezervované instance

Při volání nového rozhraní API Poplatky za rezervované instance použijte následující identifikátory URI požadavků.

#### <a name="supported-requests"></a>Podporované požadavky

[_Získání poplatků za rezervace podle rozsahu dat_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Změny textu odpovědi

_Původní odpověď:_

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Nová odpověď:_

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Použití dalších rozhraní API služby Cost Management

Po dokončení migrace na rozhraní API Azure Resource Manageru pro stávající scénáře vytváření sestav můžete používat i mnoho dalších rozhraní API. Rozhraní API jsou také k dispozici prostřednictvím Azure Resource Manageru a dají se automatizovat s využitím ověřování založeného na instančních objektech. Tady je stručný přehled nových funkcí, které můžete použít.

- [Rozpočty:](/rest/api/consumption/budgets/createorupdate) Slouží k nastavení prahových hodnot pro aktivní monitorování vašich nákladů, upozorňování náležitých účastníků a automatizaci akcí v reakci na nedodržení prahové hodnoty.

- [Upozornění:](/rest/api/cost-management/alerts) Slouží k zobrazení informací o upozorněních, mimo jiné upozornění na rozpočet, upozornění na fakturu, upozornění na kredit a upozornění na kvótu.

- [Exporty:](/rest/api/cost-management/exports) Slouží k naplánování opakovaného exportu vašich poplatků do účtu Azure Storage podle vaší volby. Jde o doporučené řešení pro zákazníky se značným využitím Azure, kteří chtějí analyzovat svá data a využívat je ve svých interních systémech.

## <a name="next-steps"></a>Další kroky

- Seznamte se s [rozhraními REST API Azure Resource Manageru](/rest/api/azure).
- V případě potřeby určete, která rozhraní API EA používáte, a v [mapování rozhraní API EA API na rozhraní API Azure Resource Manageru](#ea-api-mapping-to-new-azure-resource-manager-apis) si najděte, na která rozhraní API Azure Resource Manageru se má přejít.
- Pokud rozhraní API pro Azure Resource Manager ještě nepoužíváte, [zaregistrujte svou klientskou aplikaci v Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- V případě potřeby aktualizujte veškerý naprogramovaný kód tak, aby [používal ověřování Azure AD](/rest/api/azure/#create-the-request) s využitím vašeho instančního objektu.