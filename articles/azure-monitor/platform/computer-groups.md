---
title: Skupiny počítačů v dotazech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Skupiny počítačů ve službě Azure Monitor umožňují oborové dotazy na konkrétní sadu počítačů.  Tento článek popisuje různé metody, které můžete použít k vytvoření skupin počítačů a jak je používat v dotazu protokolu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274772"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Skupiny počítačů v dotazech protokolu Azure Monitor
Skupiny počítačů ve službě Azure Monitor umožňují [oborové dotazy na](../log-query/log-query-overview.md) konkrétní sadu počítačů.  Každá skupina je naplněna počítači, které buď používají dotaz, který definujete, nebo importem skupin z různých zdrojů.  Pokud je skupina zahrnuta do dotazu protokolu, výsledky jsou omezeny na záznamy, které odpovídají počítačům ve skupině.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Vytvoření skupiny počítačů
Skupinu počítačů můžete vytvořit ve službě Azure Monitor pomocí libovolné metody v následující tabulce.  Podrobnosti o jednotlivých metodách jsou uvedeny v následujících částech. 

| Metoda | Popis |
|:--- |:--- |
| Protokolovací dotaz |Vytvořte dotaz protokolu, který vrátí seznam počítačů. |
| Rozhraní API pro prohledávání protokolů |Pomocí rozhraní API pro vyhledávání protokolů můžete programově vytvořit skupinu počítačů na základě výsledků dotazu protokolu. |
| Active Directory |Automaticky prohledá členství ve skupině všech počítačů agentů, které jsou členy domény služby Active Directory, a vytvořte skupinu v programu Azure Monitor pro každou skupinu zabezpečení. (Pouze počítače se systémem Windows)|
| Configuration Manager | Importujte kolekce z Nástroje pro konfiguraci koncového bodu Microsoft A vytvořte skupinu v Azure Monitoru pro každou z nich. |
| Windows Server Update Services |Automaticky prohledávat servery nebo klienty služby WSUS pro skupiny cílení a vytvořit skupinu ve službě Azure Monitor pro každou z nich. |

### <a name="log-query"></a>Protokolovací dotaz
Skupiny počítačů vytvořené z dotazu protokolu obsahují všechny počítače vrácené dotazem, který definujete.  Tento dotaz je spuštěn při každém použití skupiny počítačů tak, aby se projevily všechny změny od vytvoření skupiny.  

Můžete použít libovolný dotaz pro skupinu počítačů, ale musí vrátit `distinct Computer`odlišnou sadu počítačů pomocí .  Následuje typický příklad dotazu, který můžete použít jako skupinu počítačů.

    Heartbeat | where Computer contains "srv" | distinct Computer

Pomocí následujícího postupu vytvořte skupinu počítačů z hledání protokolu na webu Azure Portal.

1. Klikněte na **protokoly** v nabídce **Azure Monitor** na webu Azure Portal.
1. Vytvořte a spusťte dotaz, který vrátí požadované počítače ve skupině.
1. V horní části obrazovky klikněte na **Uložit.**
1. Změňte **možnost Uložit jako** **funkci** a vyberte Uložit tento dotaz **jako skupinu počítačů**.
1. Zadejte hodnoty pro každou vlastnost pro skupinu počítačů popsanou v tabulce a klepněte na tlačítko **Uložit**.

Následující tabulka popisuje vlastnosti, které definují skupinu počítačů.

| Vlastnost | Popis |
|:---|:---|
| Name (Název)   | Název dotazu, který se má zobrazit na portálu. |
| Alias funkce | Jedinečný alias používaný k identifikaci skupiny počítačů v dotazu. |
| Kategorie       | Kategorie pro uspořádání dotazů na portálu. |


### <a name="active-directory"></a>Active Directory
Když nakonfigurujete Azure Monitor pro import členství ve skupinách služby Active Directory, analyzuje členství ve skupinách všech počítačů se spojenými s doménami Windows s agentem Log Analytics.  Skupina počítačů se vytvoří ve službě Azure Monitor pro každou skupinu zabezpečení ve službě Active Directory a každý počítač se systémem Windows se přidá do skupin počítačů odpovídajících skupinám zabezpečení, které jsou členy.  Toto členství je průběžně aktualizováno každé 4 hodiny.  

> [!NOTE]
> Importované skupiny služby Active Directory obsahují pouze počítače se systémem Windows.

Azure Monitor nakonfigurujete tak, aby importoval skupiny zabezpečení služby Active Directory z **rozšířeného nastavení** v pracovním prostoru Log Analytics na webu Azure Portal.  Vyberte **položky Skupiny počítačů**, **službu Active Directory**a potom **importujte členství ve skupinách služby Active Directory z počítačů**.  Není nutná žádná další konfigurace.

![Skupiny počítačů ze služby Active Directory](media/computer-groups/configure-activedirectory.png)

Po importu skupin je v nabídce uveden počet počítačů se zjištěným členstvím ve skupině a počet importovaných skupin.  Kliknutím na některý z těchto odkazů můžete vrátit záznamy **ComputerGroup** s těmito informacemi.

### <a name="windows-server-update-service"></a>Služba Aktualizace systému Windows Server
Když nakonfigurujete Azure Monitor pro import členství ve skupinách WSUS, analyzuje členství ve skupině cílení všech počítačů s agentem Log Analytics.  Pokud používáte cílení na straně klienta, každý počítač, který je připojený k Azure Monitora a je součástí všech skupin cílení služby WSUS má své členství ve skupině importované do Azure Monitoru. Pokud používáte cílení na straně serveru, agent Log Analytics by měl být nainstalován na serveru WSUS, aby se informace o členství ve skupině importovaly do služby Azure Monitor.  Toto členství je průběžně aktualizováno každé 4 hodiny. 

Azure Monitor nakonfigurujete tak, aby importoval skupiny WSUS z **rozšířeného nastavení** v pracovním prostoru Log Analytics na webu Azure Portal.  Vyberte **položky Skupiny počítačů**, **wsus**a potom **importujte členství ve skupinách wsus**.  Není nutná žádná další konfigurace.

![Skupiny počítačů ze systému WSUS](media/computer-groups/configure-wsus.png)

Po importu skupin je v nabídce uveden počet počítačů se zjištěným členstvím ve skupině a počet importovaných skupin.  Kliknutím na některý z těchto odkazů můžete vrátit záznamy **ComputerGroup** s těmito informacemi.

### <a name="configuration-manager"></a>Configuration Manager
Když nakonfigurujete Azure Monitor pro import členství kolekce Configuration Manager, vytvoří skupinu počítačů pro každou kolekci.  Informace o členství v kolekci jsou načteny každé 3 hodiny, aby byly skupiny počítačů aktuální. 

Před importem kolekcí Nástroje configuration manageru je nutné [připojit nástroj Configuration Manager ke službě Azure Monitor](collect-sccm.md).  

![Počítačové skupiny z SCCM](media/computer-groups/configure-sccm.png)

Po importu kolekcí je v nabídce uveden počet počítačů se zjištěným členstvím ve skupině a počet importovaných skupin.  Kliknutím na některý z těchto odkazů můžete vrátit záznamy **ComputerGroup** s těmito informacemi.

## <a name="managing-computer-groups"></a>Správa skupin počítačů
Skupiny počítačů, které byly vytvořeny z dotazu protokolu nebo rozhraní API pro vyhledávání protokolů, můžete zobrazit z **rozšířeného nastavení** v pracovním prostoru Log Analytics na webu Azure Portal.  Vyberte **položku Skupiny počítačů** a potom **vyberte uložené skupiny**.  

Chcete-li odstranit skupinu počítačů, klepněte na tlačítko **x** ve sloupci **Odebrat.**  Kliknutím na ikonu **Zobrazit členy** skupiny spusťte hledání protokolu skupiny, které vrací členy.  Skupinu počítačů nelze změnit, ale místo toho ji musíte odstranit a znovu vytvořit s upraveným nastavením.

![Uložené skupiny počítačů](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Použití skupiny počítačů v dotazu protokolu
Skupinu Počítač vytvořenou z dotazu protokolu v dotazu použijete tak, že jeho alias považujete za funkci, obvykle s následující syntaxí:

  `Table | where Computer in (ComputerGroup)`

Pomocí následujícího příkazu můžete například vrátit záznamy UpdateSummary pouze pro počítače ve skupině počítačů s názvem mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Importované skupiny počítačů a jejich zahrnuté počítače jsou uloženy v tabulce **ComputerGroup.**  Následující dotaz by například vrátil seznam počítačů ve skupině Domain Computers ze služby Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Následující dotaz by vrátil záznamy UpdateSummary pouze pro počítače v domain computers.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Záznamy skupiny počítačů
Záznam je vytvořen v pracovním prostoru Log Analytics pro každé členství ve skupině počítačů vytvořené ze služby Active Directory nebo služby WSUS.  Tyto záznamy mají typ **ComputerGroup** a mají vlastnosti v následující tabulce.  Záznamy nejsou vytvořeny pro skupiny počítačů na základě dotazů protokolu.

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |*Skupina Počítače* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Název členského počítače. |
| `Group` |Název skupiny. |
| `GroupFullName` |Úplná cesta ke skupině včetně zdrojového a zdrojového názvu. |
| `GroupSource` |Zdroj, ze kterého byla tato skupina shromážděna. <br><br>ActiveDirectory<br>WSUS<br>Cílení na službu WSUSClientTargeting |
| `GroupSourceName` |Název zdroje, ze kterého byla skupina shromážděna.  Pro službu Active Directory se jedná o název domény. |
| `ManagementGroupName` |Název skupiny pro správu agentů SCOM.  Pro ostatní agenty se jedná\<o ID pracovního prostoru AOI.\> |
| `TimeGenerated` |Datum a čas vytvoření nebo aktualizace skupiny počítačů. |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení.  

