---
title: Úspory pro Velké instance SAP HANA s využitím rezervace Azure
description: Seznamte se s tím, co byste měli vědět před nákupem rezervace služby Velké instance HANA a jak tento nákup realizovat.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: 579c7b5f66f52551e2504101406e1f7b2bb69e92
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775523"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Úspory pro Velké instance SAP HANA s využitím rezervace Azure

Náklady na Velké instance SAP HANA (HANA Large Instances, HLI) můžete snížit, pokud si předem koupíte rezervace Azure na jeden nebo tři roky. Sleva za rezervace se použije pro zřízené skladové položky HLI, které odpovídají zakoupené rezervované instanci. Tento článek vám pomůže seznámit se s tím, co byste měli vědět před nákupem rezervace a jak tento nákup realizovat.

Zakoupením rezervace se zavazujete využívat HLI na jeden nebo tři roky. Nákup rezervované kapacity HLI zahrnuje výpočetní funkce a úložiště NFS, které se dodávají společně s příslušnou skladovou položkou. Rezervace nezahrnuje náklady na licencování softwaru, jako je operační systém nebo SAP, ani dodatečné náklady na úložiště. Sleva za rezervaci se automaticky uplatní na zřízenou službu SAP HLI. Po skončení období rezervace platí se za zřízený prostředek účtují tarify průběžných plateb.

## <a name="purchase-considerations"></a>Důležité informace o nákupu

Před zahájením nákupu rezervované kapacity už musí být zřízená skladová položka HLI. Za rezervaci se platí předem nebo prostřednictvím měsíčních plateb. Na rezervovanou kapacitu HLI se vztahují následující omezení:

- Sleva za rezervaci se vztahuje jenom na předplatná smlouvy Enterprise a Smlouvy se zákazníkem Microsoftu. Ostatní předplatná se nepodporují.
- Pro rezervovanou kapacitu HLI se nepodporuje flexibilní velikost instance. Rezervace se vztahuje jenom na skladovou položku a oblast, pro kterou ji zakoupíte.
- Samoobslužné zrušení nebo výměna se nepodporují.
- Rezervovaná kapacita je omezená na jeden rozsah, to znamená, že se vztahuje na jedno předplatné a skupinu prostředků. Zakoupenou kapacitu nejde aktualizovat a použít ji pro jiné předplatné.
- Pro rezervovanou kapacitu HANA nejde využít sdílený rozsah rezervace. Rozsah rezervace nelze rozdělit, sloučit ani aktualizovat.
- Pomocí volání rozhraní API pro rezervovanou kapacitu je možné vždycky koupit jednu službu HLI. Pokud jich chcete koupit větší množství, použijte další volání rozhraní API.

Rezervovanou kapacitu můžete koupit na webu Azure Portal nebo pomocí [REST API](/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Nákup rezervace Velké instance HANA

K nákupu rezervace HLI s využitím [rozhraní REST API pro objednávku rezervací](/rest/api/reserved-vm-instances/reservationorder/purchase) použijte následující informace.

### <a name="get-the-reservation-order-and-price"></a>Získání objednávky a ceny rezervace

Nejdřív pomocí rozhraní API pro [výpočet ceny](/rest/api/reserved-vm-instances/reservationorder/calculate) získejte cenu a objednávku rezervace pro skladovou položku Velká instance HANA.

Následující příklad používá nástroj [armclient](https://github.com/projectkudu/ARMClient) k volání REST API s využitím PowerShellu. Objednávka rezervace a požadavek rozhraní API pro výpočet ceny by měly vypadat přibližně takto:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'billingplan': 'Monthly'
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Další informace o datových polích a jejich popisech najdete v tématu [Pole rezervace HLI](#hli-reservation-fields).

Následující ukázková odpověď přibližně ukazuje, co by se vám mělo vrátit. Poznamenejte si hodnotu vrácenou pro `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Realizace nákupu

K nákupu využijte vrácenou hodnotu `quoteId` a identifikátor `reservationOrderId`, který jste získali v předchozí části [Získání objednávky a ceny rezervace](#get-the-reservation-order-and-price).

Tady je ukázkový požadavek:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
       'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
               'billingplan': 'Monthly'

        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Tady je ukázková odpověď. Pokud je objednávka úspěšně zadaná, měl by `provisioningState` mít hodnotu `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Ověření úspěšnosti nákupu

Pokud chcete zobrazit stav nákupní objednávky, spusťte pro objednávku rezervace požadavek GET. Parametr `provisioningState` by měl mít hodnotu `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

Odpověď by měla vypadat přibližně takto:

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>Pole rezervace HLI

Následující informace popisují význam jednotlivých polí rezervace.

  **SKU:** Název skladové položky HLI. Vypadá takto: `SAP_HANA_On_Azure_<SKUname>`.

  **Umístění:** Dostupné oblasti HLI. Dostupné oblasti najdete v tématu věnovaném [skladovým položkám pro SAP HANA v Azure (Velké instance)](../../virtual-machines/workloads/sap/hana-available-skus.md). K získání formátu řetězce umístění použijte [volání rozhraní API pro získání umístění](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Typ rezervovaného prostředku:** `SapHana`

  **Předplatné:** Předplatné použité pro platbu za rezervaci. Náklady na kapacitu se účtují pomocí způsobu platby zvoleného pro toto předplatné. Musí se jednat o předplatné se smlouvou Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo se Smlouvou se zákazníkem Microsoftu. Poplatky se strhávají z Azure Prepaymentu (dřív označovaný jako peněžní závazek), pokud je k dispozici, nebo se účtují jako nadlimitní využití.

  **Rozsah:** Rezervace by měla mít jeden rozsah.

  **Období:** Jeden nebo tři roky. Vypadá takto: `P1Y` nebo `P3Y`.

  **Množství** Počet instancí nakupovaných pro příslušnou rezervaci. Vždy je možné koupit jenom jednu službu HLI. Pokud potřebujete další rezervace, zopakujte volání rozhraní API s odpovídajícími poli.

## <a name="troubleshoot-errors"></a>Řešení chyb

Při nákupu rezervace se vám může zobrazit chyba podobná následující. Možnou příčinou je, že služba HLI není zřízená pro nákup. Pokud tomu tak je, obraťte se na tým účtů Microsoft a nechte si HLI zřídit dřív, než se pokusíte provést nákup rezervace.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu věnovaném [volání rozhraní Azure REST API s využitím nástrojů Postman a cURL](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Seznam dostupných skladových položek a oblastí najdete v tématu věnovaném [skladovým položkám pro SAP HANA v Azure (Velké instance)](../../virtual-machines/workloads/sap/hana-available-skus.md).