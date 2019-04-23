---
title: Společné schéma oznámení pro výstrahy monitorování Azure
description: Principy společné schéma výstrah, proč byste měli používat a jak se dá povolit
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c18227a491478d0d8010761440a54fd088344b39
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149372"
---
# <a name="common-alert-schema"></a>Běžné schéma upozornění

Tento článek popisuje, co je ve společném schématu produktu výstrah, výhody a jak ji povolit.

## <a name="what-is-the-common-alert-schema"></a>Co je ve společném schématu produktu upozornění?

Ve společném schématu produktu výstrah standardizuje možnosti spotřeby oznámení výstrah v Azure ještě dnes. Tři typy výstrah v Azure ještě dnes (metrik, protokolů a protokolu aktivity) v minulosti, měli vlastní e-mailové šablony, schémata webhooku, atd. Ve společném schématu produktu výstrah můžete nyní získávat oznámení o výstrahách s konzistentní schéma.

Popisuje instanci jakékoli výstrahy **prostředek, který ovlivněných** a **příčiny výstrahy**, a tyto instance jsou popsané ve společném schématu v následujících částech:
* **Základy**: Sada **standardizované pole**běžného napříč všemi typy výstrah, které popisují **jaký prostředek** je výstrahy v společně s další běžné výstrah metadata (například závažnost nebo popis). 
* **Kontext výstrahy**: Sadu polí, které popisují **způsobit, že výstrahy**, vložením polí, která se liší **podle typu výstrahy**. Upozornění na metriku by například mít pole, jako jsou název metriky a hodnota metriky v kontextu výstrahy, že upozornění protokolu aktivit, bude mít informace o události, které upozornění vygenerovalo. 

Integrace typické scénáře, které jsme od zákazníků slyšeli zahrnují směrování instanci výstrahy založené na některé pivot (například skupinu prostředků), po jejímž uplynutí zodpovědná tým začne práce na něm obavy týmu. Se společným schématem výstrah můžete můžete mít standardizované logiku směrování mezi typy výstrah s využitím zásadní pole byste museli opustit kontext pole, stejně jako pro dotčené týmy dále prozkoumat.

To znamená, že je možné používat méně integrace, proces správy a údržby _většinu_ jednodušší úloh. Kromě toho obohacení budoucí datová část oznámení (například přizpůsobení, diagnostické rozšíření, atd.) bude pouze přinášet nahoru ve společném schématu.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Jaká vylepšení ve společném schématu produktu výstrah převést?

Ve společném schématu produktu výstrahy se primárně projevit v oznámení o výstrahách. Vylepšení, které se zobrazí jsou uvedeny níže:

| Akce | Vylepšení|
|:---|:---|
| SMS | Konzistentní SMS šablonu pro všechny typy výstrah. |
| Email | Konzistentní vzhledem k aplikacím a podrobné e-mailovou šablonu, abyste mohli snadno diagnostikovat potíže vhodným způsobem zasáhnout. Vložený hloubkové odkazy na instanci výstrahy na portálu a daný prostředek Ujistěte se, že můžete rychle přejít do procesu nápravy. |
| Webhooku/logika aplikace nebo Azure funkce/Automation Runbook | Konzistentní strukturu JSON pro všechny typy výstrah, které vám umožní snadno vytvářet integrace mezi různé typy výstrah. |

Nové schéma také umožní pohodlnější a pestřejší prostředí upozornění využití napříč na webu Azure portal a mobilní aplikace Azure v blízké budoucnosti. 

[Další informace o definici schématu pro Runbooky funkce/Automation, Webhooků/Logic Apps nebo Azure.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Tyto akce nepodporují ve společném schématu produktu výstrahy: Konektor ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Jak povolit ve společném schématu produktu upozornění?

Můžete vyjádřit výslovný souhlas nebo odhlásit ve společném schématu výstrahy prostřednictvím skupin akcí na i na portálu a prostřednictvím rozhraní REST API. Přepínací tlačítko k přepnutí na nové schéma existuje na úrovni akce. Například můžete mít samostatně vyjádřit výslovný souhlas s e-mailové akce a akce webhooku.

> [!NOTE]
> 1. Následující typy výstrah podporují ve společném schématu produktu ve výchozím nastavení (žádné optimalizované vyžadovat):
>     * Upozornění inteligentního zjišťování
> 1. Následující typy výstrah aktuálně nepodporují ve společném schématu produktu:
>     * Výstrahy vygenerované [monitorování Azure pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Výstrahy vygenerované [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Na webu Azure portal

![Společné schéma produktu výstrah vyjádřit výslovný souhlas](media/alerts-common-schema/portal-opt-in.png)

1. Otevřete jakékoli existující nebo nové akce v skupiny akcí. 
1. Vyberte 'Ano' pro přepínač, který povolí ve společném schématu produktu výstrah, jak je znázorněno.

### <a name="through-the-action-groups-rest-api"></a>Prostřednictvím rozhraní REST API skupiny akcí

Můžete také použít [akce skupiny API](https://docs.microsoft.com/rest/api/monitor/actiongroups) můžete vyjádřit výslovný souhlas ve společném schématu produktu výstrah. Při provádění [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) volání rozhraní REST API můžete nastavit příznak "useCommonAlertSchema" na "true" (se vyjádřit výslovný souhlas) nebo "Nepravda" (výslovný souhlas) pro některý z následujících akcí - e-mailu nebo webhooku/logika aplikace nebo Azure funkce/automatizace sady runbook.

Například následující text provedené žádosti [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) rozhraní REST API se postupujte takto:

* Povolit ve společném schématu produktu upozornění e-mailové akce "John Doe e-mailu"
* Zakázat ve společném schématu produktu upozornění e-mailové akce "Jan Macek e-mail"
* Povolit ve společném schématu produktu oznámení pro akce webhooku "Ukázka webhooku"

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





## <a name="next-steps"></a>Další postup

- [Běžné definice upozornění schémat pro Webhooky a Logic Apps nebo Azure funkce/automatizace sady Runbook.](https://aka.ms/commonAlertSchemaDefinitions)



