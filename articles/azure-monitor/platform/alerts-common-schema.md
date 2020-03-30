---
title: Běžné schéma výstrah pro výstrahy monitorování Azure
description: Principy společného schématu výstrah, proč byste ho měli používat a jak ho povolit
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249045"
---
# <a name="common-alert-schema"></a>Běžné schéma upozornění

Tento článek popisuje, co je společné schéma výstrahy, výhody jeho použití a jak ji povolit.

## <a name="what-is-the-common-alert-schema"></a>Jaké je běžné schéma výstrahy?

Společné schéma výstrah standardizuje možnosti spotřeby pro oznámení výstrah v Azure dnes. Historicky tři typy výstrah v Azure dnes (metrika, protokol a protokol aktivit) měli své vlastní šablony e-mailu, webhook schémata, atd. Pomocí společného schématu výstrah můžete nyní přijímat oznámení s konzistentním schématem.

Každá instance výstrahy popisuje **prostředek, který byl ovlivněn** a **příčinu výstrahy**, a tyto instance jsou popsány ve společném schématu v následujících částech:
* **Essentials**: Sada **standardizovaných polí**, která jsou společná pro všechny typy výstrah, které popisují, **v jakém prostředku** se výstraha nachází, spolu s dalšími společnými metadaty výstrah (například závažnost nebo popis). 
* **Kontext výstrahy**: Sada polí, která popisují **příčinu výstrahy**, s poli , která se liší **v závislosti na typu výstrahy**. Například upozornění metriky by měla pole, jako je název metriky a hodnota metriky v kontextu výstrahy, zatímco výstraha protokolu aktivit by měla informace o události, která výstrahu vygenerovala. 

Typické scénáře integrace, které slyšíme od zákazníků, zahrnují směrování instance výstrahy do příslušného týmu na základě určitého pivotu (například skupiny prostředků), po kterém na něm začne pracovat odpovědný tým. Díky společnému schématu výstrah můžete mít standardizovanou logiku směrování napříč typy výstrah využitím základních polí a ponechat pole kontextu stejně jako pro příslušné týmy dále zkoumat.

To znamená, že můžete mít potenciálně méně integrace, takže proces jejich správy a údržby _je mnohem_ jednodušší úkol. Budoucí obohacování datové části výstrahy (například přizpůsobení, diagnostické obohacení atd.) se navíc zobrazí pouze ve společném schématu.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Jaká vylepšení přináší společné schéma výstrahy?

Společné schéma výstrah se projeví především ve vašich oznámeních výstrah. Vylepšení, která uvidíte, jsou uvedena níže:

| Akce | Vylepšení|
|:---|:---|
| SMS | Konzistentní šablona SMS pro všechny typy výstrah. |
| E-mail | Konzistentní a podrobná šablona e-mailu, která vám umožní snadno diagnostikovat problémy na první pohled. Vložené přímé odkazy na instanci výstrahy na portálu a ovlivněné mno župy zajišťují, že můžete rychle přejít do procesu nápravy. |
| Aplikace Webhook/Logika/Sada funkcí Azure/automatizace | Konzistentní struktura JSON pro všechny typy výstrah, která umožňuje snadno vytvářet integrace napříč různými typy výstrah. |

Nové schéma také umožní bohatší prostředí pro správu výstrah na příčce portálu Azure i mobilní aplikace Azure v nejbližší budoucnosti. 

[Další informace o definicích schématu pro sady Runhooks/Logic Apps/Azure Functions/Automation Runbook.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Následující akce nepodporují společné schéma výstrah: KONEKTOR ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Jak povolím společné schéma výstrahy?

Můžete se přihlásit nebo odhlásit společné schéma výstrah prostřednictvím skupin akcí, na portálu i prostřednictvím rozhraní REST API. Přepínač pro přepnutí na nové schéma existuje na úrovni akce. Například musíte samostatně přihlásit pro e-mailovou akci a webhooku.

> [!NOTE]
> 1. Následující typy výstrah podporují společné schéma ve výchozím nastavení (není vyžadováno žádné přihlášení):
>     * Výstrahy inteligentní detekce
> 1. Následující typy výstrah aktuálně nepodporují společné schéma:
>     * Výstrahy generované [Službou Azure Monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Výstrahy generované službou [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Prostřednictvím portálu Azure

![Běžné schéma výstrah y opt-in](media/alerts-common-schema/portal-opt-in.png)

1. Otevřete libovolnou existující nebo novou akci ve skupině akcí. 
1. Chcete-li povolit společné schéma výstrah, jak je znázorněno, vyberte možnost Ano.

### <a name="through-the-action-groups-rest-api"></a>Prostřednictvím rozhraní REST API pro akční skupiny

Rozhraní [API skupin akcí](https://docs.microsoft.com/rest/api/monitor/actiongroups) můžete také použít k přihlášení ke schématu běžných výstrah. Při [vytváření nebo aktualizaci](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) volání rozhraní REST API můžete nastavit příznak "useCommonAlertSchema" na "true" (opt in) nebo "false" (odhlásit se) pro některou z následujících akcí - e-mail/ webhook /aplikace logiky / Azure Function/automation runbook.

Například následující tělo požadavku provedené v [rozhraní API pro vytvoření nebo aktualizaci](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) rozhraní REST provede následující akce:

* Povolte společné schéma výstrah pro e-mailovou akci "E-mail Jana Doea"
* Zakázání schématu běžných výstrah pro e-mailovou akci "E-mail Jane Smithové"
* Povolte společné schéma výstrah pro akci webhooku "Ukázkový webhook"

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

- [Běžné definice schématu výstrah pro webhooky/logické aplikace/azure funkce/automatizační sady Runbook.](https://aka.ms/commonAlertSchemaDefinitions)
- [Zjistěte, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah ke zpracování všech vašich výstrah.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



