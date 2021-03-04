---
title: Běžné schéma výstrah pro výstrahy Azure Monitor
description: Princip běžných schémat výstrah, proč byste ji měli používat a jak ji povolit
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 70e37f8f1c9391eee5901f611bae579efe1cd9b9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737286"
---
# <a name="common-alert-schema"></a>Běžné schéma upozornění

Tento článek popisuje, co je běžné schéma výstrah, výhody jeho používání a jak ho povolit.

## <a name="what-is-the-common-alert-schema"></a>Jaké je běžné schéma výstrah?

Běžné schéma výstrah standardizace prostředí spotřeby pro oznamování výstrah v Azure ještě dnes. V minulosti měly tři typy výstrah v Azure ještě dnes (metrika, protokol a protokol aktivit) vlastní e-mailové šablony, schémata webhooků atd. Pomocí společného schématu výstrah teď můžete dostávat oznámení o výstrahách s konzistentním schématem.

Jakákoli instance výstrahy popisuje **prostředek, který byl ovlivněn** , a **příčinu výstrahy**. tyto instance jsou popsány v běžném schématu v následujících částech:
* **Essentials**: sada **standardizovaných polí** společných pro všechny typy výstrah, které popisují, k **jakým prostředkům** se tato výstraha používá, spolu s dalšími běžnými metadaty výstrah (například závažnost nebo popis). 
* **Kontext výstrahy**: sada polí, která popisují **příčinu výstrahy**, s poli, která se liší **v závislosti na typu výstrahy**. Například výstraha metriky by měla obsahovat pole jako název metriky a hodnotu metriky v kontextu výstrahy, zatímco výstraha protokolu aktivit by měla obsahovat informace o události, která výstrahu vygenerovala. 

Typickými scénáři integrace, které uslyšíme od zákazníků, je směrování instance výstrahy danému týmu na základě nějakého kontingenčního programu (například skupiny prostředků), po kterém na něm příslušný tým začne pracovat. Společně s běžným schématem výstrah můžete mít standardizovanou logiku směrování napříč typy výstrah, a to využitím základních polí a ponechání kontextových polí tak, aby se pro příslušné týmy mohli lépe prozkoumat.

To znamená, že může být možné mít méně integrací, což je proces správy a udržování _mnohem_ jednodušších úkolů. Budoucí rozšiřování datových částí výstrah (například přizpůsobení, obohacení diagnostiky atd.) bude také ve společném schématu.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Jaká vylepšení přináší běžné schéma výstrah?

Společné schéma výstrah se primárně projeví v oznámeních výstrah. Níže jsou uvedená vylepšení, která se zobrazí:

| Akce | Prvky|
|:---|:---|
| SMS | Konzistentní šablona serveru SMS pro všechny typy výstrah. |
| E-mail | Jednotná a podrobná šablona e-mailu, která umožňuje snadnou diagnostikovat problémy na první pohled. Vložené hloubkové odkazy na instanci výstrahy na portálu a ovlivněný prostředek zajistí, že můžete rychle přejít do procesu nápravy. |
| Webhook/aplikace logiky/Azure Functions/Automation Runbook | Konzistentní struktura JSON pro všechny typy výstrah, která umožňuje snadno sestavit integraci napříč různými typy výstrah. |

Nové schéma také umožní širší možnosti spotřeby výstrah napříč Azure Portal i Mobilní aplikace Azure v bezprostřední budoucnosti. 

[Přečtěte si další informace o definicích schématu pro Webhooky/Runbooky Logic Apps/Azure Functions/Automation.](./alerts-common-schema-definitions.md)

> [!NOTE]
> Následující akce nepodporují běžné schéma výstrah: konektor ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Návody povolit společné schéma výstrah?

Pomocí skupin akcí na portálu i REST API můžete použít společné schéma výstrah nebo se na něj rozhodnout. Přepínač pro přepnutí na nové schéma existuje na úrovni akce. Například je nutné samostatně vyjádřit akci e-mailu a akci Webhooku.

> [!NOTE]
> 1. Následující typy výstrah podporují standardně společné schéma (není nutné žádné výslovné povinné):
>     * Výstrahy inteligentní detekce
> 1. Následující typy výstrah aktuálně nepodporují společné schéma:
>     * Výstrahy generované službou [VM Insights](../vm/vminsights-overview.md)
>     * Výstrahy generované [Azure cost management](../../cost-management-billing/manage/cost-management-budget-scenario.md)

### <a name="through-the-azure-portal"></a>Prostřednictvím Azure Portal

![Obecná možnost výslovného souhlasu schématu výstrahy](media/alerts-common-schema/portal-opt-in.png)

1. Otevřete jakoukoli existující nebo novou akci ve skupině akcí. 
1. Kliknutím na tlačítko Ano pro přepínač povolíte společné schéma výstrah, jak je znázorněno na obrázku.

### <a name="through-the-action-groups-rest-api"></a>Prostřednictvím skupin akcí REST API

Pomocí [rozhraní API skupin akcí](/rest/api/monitor/actiongroups) můžete také vyjádřit výslovný souhlas s běžným schématem výstrah. Během volání funkce [vytvořit nebo aktualizovat](/rest/api/monitor/actiongroups/createorupdate) REST API můžete nastavit příznak "useCommonAlertSchema" na "true" (pro výslovný souhlas) nebo "NEPRAVDA" (pro odhlášení) pro některou z následujících akcí: e-mail/Webhook/Logic App/Azure Function/Automation Runbook.

Například následující text žádosti, který se v REST API pro [Vytvoření nebo aktualizaci](/rest/api/monitor/actiongroups/createorupdate) provede, provede následující:

* Povolit společné schéma výstrah pro e-mailovou akci Jan Karásek
* Zakázat běžné schéma výstrah pro e-mailovou akci Jana Novák
* Povolit společné schéma výstrah pro akci Webhooku "Ukázkový Webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Další kroky

- [Běžné definice schémat výstrah pro Webhooky/Logic Apps/Azure Functions/Automation Runbooky.](./alerts-common-schema-definitions.md)
- [Naučte se, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah k obsluze všech výstrah.](./alerts-common-schema-integrations.md)