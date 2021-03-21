---
title: Správa přístupu k datům Sentinel Azure podle prostředků | Microsoft Docs
description: Tento článek vysvětluje, jak můžete spravovat přístup k datům Sentinel Azure pomocí prostředků, ke kterým má uživatel přístup. Správa přístupu podle prostředků vám umožní poskytovat přístup jenom k určitým datům, a to bez celého prostředí Azure Sentinel. Tato metoda se označuje také jako kontext prostředku RBAC.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: 26124f8f650e1006244b4871e26962d417d90fd4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054631"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Správa přístupu k datům Sentinel Azure podle prostředků

Obvykle uživatelé, kteří mají přístup k pracovnímu prostoru Sentinel Azure, mají také přístup ke všem datům v pracovním prostoru, včetně obsahu zabezpečení. Správci můžou pomocí [rolí Azure](roles.md) nakonfigurovat přístup ke konkrétním funkcím v konfiguraci Azure Sentinel v závislosti na požadavcích na přístup v týmu.

Je ale možné, že někteří uživatelé potřebují přístup k určitým datům v pracovním prostoru Sentinel Azure, ale nemají přístup k celému prostředí Sentinel Azure. Například může být vhodné poskytnout týmu neSOCch operací, který má přístup k datům událostí systému Windows pro servery, které vlastní.

V takových případech doporučujeme nakonfigurovat řízení přístupu na základě role (RBAC) na základě prostředků, které jsou uživatelům povolené, a nemusíte jim poskytnout přístup k pracovnímu prostoru Azure Sentinel nebo konkrétním funkcím Azure Sentinel. Tato metoda je také známá jako nastavení správy **prostředků v kontextu**.

Když uživatelé mají přístup k datům Sentinel Azure prostřednictvím prostředků, ke kterým mají přístup, místo pracovního prostoru Azure Sentinel můžou zobrazit protokoly a sešity pomocí následujících metod:

- **Prostřednictvím samotného prostředku**, jako je třeba virtuální počítač Azure. Tuto metodu použijte, chcete-li zobrazit protokoly a sešity pouze pro konkrétní prostředek.

- **Prostřednictvím Azure monitor**. Tuto metodu použijte, pokud chcete vytvářet dotazy, které přesahují více prostředků nebo skupin prostředků. Když přejdete na protokoly a sešity v Azure Monitor, definujte svůj rozsah na jednu nebo více konkrétních skupin prostředků nebo prostředků.

Povolit v Azure Monitor práci v kontextu prostředků Další informace najdete v tématu [Správa přístupu k datům protokolů a pracovním prostorům v Azure monitor](/azure/azure-monitor/logs/manage-access).

> [!NOTE]
> Pokud vaše data nejsou prostředkem Azure, jako jsou například syslog, CEF nebo data AAD nebo data shromážděná vlastním kolektorem, budete muset ručně nakonfigurovat ID prostředku, které slouží k identifikaci dat a povolení přístupu.
>
> Další informace najdete v tématu [explicitní konfigurace správy prostředků v kontextu](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Scénáře pro použití v kontextu prostředku – RBAC

V následující tabulce jsou vysvětlené scénáře, ve kterých je funkce RBAC kontextu prostředků nejužitečnější. Všimněte si rozdílů v požadavcích na přístup mezi SOC týmy a týmy mimo SOC.

| Typ požadavku |SOC tým  |Tým, který není SOC  |
|---------|---------|---------|
|**Oprávnění**     | Celý pracovní prostor        |   Pouze konkrétní prostředky      |
|**Přístup k datům**     |  Všechna data v pracovním prostoru       | Pouze data pro prostředky, ke kterým má tým oprávnění pro přístup        |
|**Prostředí**     |  Úplné prostředí Azure Sentinel, které je možné omezit [funkčními oprávněními](roles.md) přiřazenými uživateli       |  Pouze dotazy a sešity protokolu       |
|     |         |         |

Pokud má váš tým podobné požadavky na přístup k SOC týmu, který je popsaný v tabulce výše, může být pro vaši organizaci dobrým řešením RBAC kontext prostředků.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Alternativní metody pro implementaci správy kontextu prostředků (RBAC)

V závislosti na oprávněních požadovaných ve vaší organizaci nemusí použití RBAC kontextu prostředků poskytovat úplné řešení.

Následující seznam popisuje scénáře, kdy další řešení pro přístup k datům mohou lépe vyhovovat vašim požadavkům:

|Scenario  |Řešení  |
|---------|---------|
|**Pobočka má tým SOC, který vyžaduje úplné prostředí Azure Sentinel**.     |  V takovém případě použijte architekturu s více pracovními prostory k oddělení svých oprávnění k datům. <br><br>Další informace naleznete v tématu: <br>- [Rozšiřování Sentinel Azure mezi pracovními prostory a klienty](extend-sentinel-across-workspaces-tenants.md)<br>    - [Práce s incidenty v mnoha pracovních prostorech najednou](multiple-workspace-view.md)          |
|Chcete **poskytnout přístup k určitému typu události**.     |  Poskytněte například správce systému Windows s přístupem k událostem zabezpečení systému Windows ve všech systémech. <br><br>V takových případech můžete k definování oprávnění pro jednotlivé tabulky použít [RBAC na úrovni tabulky](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) .       |
| **Omezte přístup na podrobnější úroveň, a to buď na základě prostředku, nebo jenom na podmnožinu polí v události.**   |   Můžete například chtít omezit přístup k protokolům Office 365 na základě dceřiné společnosti uživatele. <br><br>V takovém případě poskytněte přístup k datům pomocí integrované integrace s [Power BI řídicími panely a sestavami](/azure/azure-monitor/platform/powerbi).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Explicitně konfigurovat prostředek-kontext RBAC

Následující postup použijte, pokud chcete nakonfigurovat nastavení RBAC pro kontext prostředku, ale vaše data nejsou prostředkem Azure.

Například data v pracovním prostoru Azure Sentinel, která nejsou prostředky Azure, zahrnují data syslog, CEF nebo AAD nebo data shromážděná vlastním kolektorem.

**Postup explicitního konfigurování RBAC kontextu prostředků**:

1. Ujistěte se, že jste [povolili práci v kontextu prostředků](/azure/azure-monitor/platform/manage-access) v Azure monitor. 

1. [Vytvořte skupinu prostředků](/azure/azure-resource-manager/management/manage-resource-groups-portal) pro každého týmu uživatelů, kteří potřebují mít přístup k prostředkům bez celého prostředí Sentinel Azure.

    Přiřaďte [oprávnění čtenářů protokolů](/azure/azure-monitor/platform/manage-access#resource-permissions) pro každého člena týmu.

1. Přiřaďte prostředky k vytvořeným skupinám týmu prostředků a označte události pomocí relevantních ID prostředků.

    Když prostředky Azure odesílají data do služby Azure Sentinel, záznamy protokolu se automaticky označí pomocí ID prostředku zdroje dat.

    > [!TIP]
    > Doporučujeme seskupit prostředky, kterým udělujete přístup, do konkrétní skupiny prostředků vytvořené pro účely.
    >
    > Pokud nemůžete, ujistěte se, že váš tým má oprávnění čtečky protokolů přímo k prostředkům, ke kterým mají přístup.
    >

    Další informace o ID prostředků najdete v těchto tématech:

    - [ID prostředků s předáváním protokolů](#resource-ids-with-log-forwarding)
    - [ID prostředků s kolekcí Logstash](#resource-ids-with-logstash-collection)
    - [ID prostředků s Log Analytics kolekcí rozhraní API](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>ID prostředků s předáváním protokolů

Při shromažďování událostí pomocí protokolu [CEF (Common Event Format)](connect-common-event-format.md) nebo [syslog](connect-syslog.md)se předávání protokolů používá ke shromažďování událostí z více zdrojových systémů.

Pokud třeba virtuální počítač pro předávání CEF nebo syslog naslouchá pro zdroje odesílající události syslogu a předává je do služby Azure Sentinel, ID prostředku virtuálního počítače pro předávání protokolu se přiřadí všem událostem, které předají.

Pokud máte více týmů, ujistěte se, že máte samostatné virtuální počítače pro předávání protokolu zpracovávající události pro každý samostatný tým.

Například oddělení virtuálních počítačů zajistí, aby události syslog, které patří do týmu A, byly shromažďovány pomocí virtuálního počítače kolektoru A.

> [!TIP]
> - Pokud používáte místní virtuální počítač nebo jiný cloudový virtuální počítač, jako je například AWS, zajistěte, aby měl ID prostředku implementující [Azure ARC](/azure/azure-arc/servers/overview).
> - Pokud chcete škálovat prostředí virtuálního počítače pro předávání protokolů, zvažte vytvoření [sady škálování virtuálního počítače](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) pro shromáždění protokolů CEF a Sylog.


### <a name="resource-ids-with-logstash-collection"></a>ID prostředků s kolekcí Logstash

Pokud shromažďujete data pomocí [modulu plug-in Azure Sentinel Logstash Output](connect-logstash.md), použijte pole **azure_resource_id** ke konfiguraci vlastního kolektoru tak, aby obsahovalo ID prostředku ve výstupu.

Pokud používáte RBAC kontextu prostředků a chcete, aby byly události shromážděné rozhraním API dostupné konkrétním uživatelům, použijte ID prostředku skupiny prostředků, kterou jste [pro uživatele vytvořili](#explicitly-configure-resource-context-rbac).

Například následující kód ukazuje Ukázkový konfigurační soubor Logstash:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Možná budete chtít přidat více `output` oddílů, chcete-li odlišit značky použité pro různé události.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>ID prostředků s Log Analytics kolekcí rozhraní API

Při shromažďování pomocí [rozhraní API kolekce log Analyticsch dat](/azure/azure-monitor/platform/data-collector-api)můžete přiřadit události s ID prostředku pomocí HLAVIČKY žádosti HTTP [*x-MS-AzureResourceId*](/azure/azure-monitor/platform/data-collector-api#request-headers) .

Pokud používáte RBAC kontextu prostředků a chcete, aby byly události shromážděné rozhraním API dostupné konkrétním uživatelům, použijte ID prostředku skupiny prostředků, kterou jste [pro uživatele vytvořili](#explicitly-configure-resource-context-rbac).


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [oprávnění v Azure Sentinel](roles.md).
