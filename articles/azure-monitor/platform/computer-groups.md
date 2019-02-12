---
title: Skupiny počítačů ve službě Azure Monitor protokolu dotazy | Dokumentace Microsoftu
description: Skupiny počítačů ve službě Azure Monitor vám umožní protokolu dotazy na obor konkrétní sadě počítačů.  Tento článek popisuje různé metody, které lze použít k vytvoření skupiny počítačů a jejich použití v dotazu protokolu.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: 7e5d04f3ead4353c88a6b56ab530f41ff15022d1
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005382"
---
# <a name="computer-groups-in-azure-monitor-log-quereies"></a>Skupiny počítačů ve službě Azure Monitor protokolu quereies
Skupiny počítačů ve službě Azure Monitor vám umožní oboru [protokolu dotazy](../log-query/log-query-overview.md) konkrétní sadě počítačů.  Každá skupina se zaplní, buď dotaz, který definujete pomocí počítače nebo importováním skupiny z různých zdrojů.  Skupiny je obsažen v protokolu dotazu, výsledky jsou omezené na záznamy, které odpovídají počítačů ve skupině.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Vytvořit skupinu počítačů
Vytvořit skupinu počítačů ve službě Azure Monitor, pomocí některé z metod v následující tabulce.  Podrobnosti o každé metody jsou k dispozici v následujících částech. 

| Metoda | Popis |
|:--- |:--- |
| Dotaz protokolu |Vytvořte dotaz protokolu, který vrátí seznam počítačů. |
| Rozhraní API pro prohledávání protokolů |Pomocí rozhraní API pro vyhledávání protokolu prostřednictvím kódu programu vytvořit skupinu počítačů na základě výsledků dotazu protokolu. |
| Active Directory |Automaticky vyhledávat členství ve skupině všechny počítače agenta, které jsou členy domény služby Active Directory a vytvoření skupiny pro každou skupinu zabezpečení ve službě Azure Monitor. (Pouze Windows počítače)|
| Configuration Manager | Importovat kolekce z nástroje System Center Configuration Manager a vytvořte pro každou skupinu ve službě Azure Monitor. |
| Windows Server Update Services |Automaticky vyhledat cílí na skupiny služby WSUS servery nebo klienty a vytvořte pro každou skupinu ve službě Azure Monitor. |

### <a name="log-query"></a>Dotaz protokolu
Skupiny počítačů, které jsou vytvořené z protokolu dotazu obsahovat všechny počítače vrácené dotazem, který určíte.  Tento dotaz je spustit pokaždé, když se skupina počítačů se používá tak, aby se projeví všechny změny, protože byla skupina vytvořena.  

Můžete použít jakýkoli dotaz pro skupinu počítačů, ale musí vracet různé sady počítačů pomocí `distinct Computer`.  Toto je typický příklad dotazu, který můžete použít jako skupinu počítačů.

    Heartbeat | where Computer contains "srv" | distinct Computer

Pomocí následujícího postupu můžete vytvořit skupinu počítačů z prohledávání protokolů na webu Azure Portal.

1. Klikněte na tlačítko **protokoly** v **Azure Monitor** nabídky na webu Azure Portal.
1. Vytvořte a spusťte dotaz, který vrátí počítače, které chcete ve skupině.
1. Klikněte na tlačítko **Uložit** v horní části obrazovky.
1. Změna **uložit jako** k **funkce** a vyberte **uložit tento dotaz jako skupinu počítačů**.
1. Zadejte hodnoty pro každou vlastnost pro skupinu počítačů je popsáno v tabulce a klikněte na tlačítko **Uložit**.

Následující tabulka popisuje vlastnosti, které definují skupinu počítačů.

| Vlastnost | Popis |
|:---|:---|
| Název   | Název dotazu na portálu zobrazovat. |
| Alias funkce | Jedinečný alias používaný k identifikaci skupinu počítačů v dotazu. |
| Kategorie       | Kategorie uspořádat dotazy na portálu. |


### <a name="active-directory"></a>Active Directory
Při konfiguraci monitorování Azure pro import členství skupin Active Directory, analyzuje členství ve skupině všechny počítače připojené k doméně Windows pomocí agenta Log Analytics.  Skupina počítačů se vytvoří ve službě Azure Monitor pro každou skupinu zabezpečení ve službě Active Directory a každý počítač Windows je přidán do skupiny počítačů, které jsou členy skupiny zabezpečení odpovídající.  Toto členství se průběžně aktualizuje každé 4 hodiny.  

> [!NOTE]
> Importované skupiny služby Active Directory obsahují pouze počítače s Windows.

Konfigurace Azure Monitor k importu skupin zabezpečení služby Active Directory z **upřesňující nastavení** ve vašem pracovním prostoru Log Analytics na portálu Azure portal.  Vyberte **skupiny počítačů**, **služby Active Directory**a potom **členství ve skupině služby Active Directory Import z počítačů**.  Není nutná žádná další konfigurace.

![Skupiny počítačů ze služby Active Directory](media/computer-groups/configure-activedirectory.png)

Importu skupin v nabídce uvádí počet počítačů s členstvím skupiny zjistila a číslem skupiny importovat.  Kliknutím na některý z těchto odkazů se vraťte **ComputerGroup** záznamy s těmito informacemi.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Při konfiguraci monitorování Azure pro import členství ve skupině WSUS analyzuje cílení členství ve skupině všechny počítače pomocí agenta Log Analytics.  Pokud používáte na straně klienta cílí na skupiny cílí na jakýkoli počítač, který je připojený k Azure Monitor a je součástí libovolné služby WSUS má jeho členství ve skupině importu do Azure monitoru. Pokud používáte server-side cílení, Log Analytics by měl být agent nainstalovaný na serveru WSUS, aby se informace o členství ve skupinách pro import do Azure monitoru.  Toto členství se průběžně aktualizuje každé 4 hodiny. 

Konfigurace Azure Monitor k importu skupin WSUS z **upřesňující nastavení** ve vašem pracovním prostoru Log Analytics na portálu Azure portal.  Vyberte **skupiny počítačů**, **WSUS**a potom **služby WSUS pro Import členství ve skupinách**.  Není nutná žádná další konfigurace.

![Skupiny počítačů služby WSUS](media/computer-groups/configure-wsus.png)

Importu skupin v nabídce uvádí počet počítačů s členstvím skupiny zjistila a číslem skupiny importovat.  Kliknutím na některý z těchto odkazů se vraťte **ComputerGroup** záznamy s těmito informacemi.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Při konfiguraci monitorování Azure pro import členství v kolekcích Configuration Manageru se vytvoří skupina počítačů pro každou kolekci.  Informace o členství v kolekci se načte každé tři hodiny zachovat aktuální skupiny počítačů. 

Před importem kolekcí nástroje Configuration Manager, je nutné [propojení Configuration Manageru do Azure monitoru](collect-sccm.md).  Potom můžete nakonfigurovat importovat z **upřesňující nastavení** ve vašem pracovním prostoru Log Analytics na portálu Azure portal.  Vyberte **skupiny počítačů**, **SCCM**a potom **členství v kolekcích Configuration Manageru Import**.  Není nutná žádná další konfigurace.

![Skupiny počítačů z SCCM](media/computer-groups/configure-sccm.png)

Importu kolekce v nabídce uvádí počet počítačů s členstvím skupiny zjistila a číslem skupiny importovat.  Kliknutím na některý z těchto odkazů se vraťte **ComputerGroup** záznamy s těmito informacemi.

## <a name="managing-computer-groups"></a>Správa skupin počítačů
Můžete zobrazit skupiny počítačů, které byly vytvořeny z dotazu protokolu nebo rozhraní API pro vyhledávání protokolu z **upřesňující nastavení** ve vašem pracovním prostoru Log Analytics na portálu Azure portal.  Vyberte **skupiny počítačů** a potom **uložit skupiny**.  

Klikněte na tlačítko **x** v **odebrat** sloupec, který chcete odstranit skupinu počítačů.  Klikněte na tlačítko **zobrazit členy** ikonu skupiny ke spuštění hledání v protokolu skupiny, která vrací jejích členů.  Nelze upravit skupinu počítačů, ale místo toho musíte odstranit a znovu ji vytvořte s upravená nastavení.

![Uložené skupiny počítačů](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Použití skupiny počítačů v protokolu dotazu
Můžete použít skupinu počítačů vytvořeny z protokolu dotazu v dotazu úpravou jeho alias jako funkce, obvykle s následující syntaxí:

  `Table | where Computer in (ComputerGroup)`

Například můžete použít následující vrátit záznamy UpdateSummary pro pouze počítače ve skupině počítač s názvem mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Importovaný počítač skupiny a jejich zahrnuté počítače jsou uloženy v **ComputerGroup** tabulky.  Například následující dotaz vrátí seznam počítačů ve skupině Domain Computers ze služby Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Následující dotaz vrátí záznamy UpdateSummary pro jenom pro počítače v doméně počítače.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Počítač seskupení záznamů
V pracovním prostoru Log Analytics pro každého členství ve skupině počítačů vytvořené ze služby Active Directory nebo služby WSUS se vytvoří záznam.  Tyto záznamy mají typ **ComputerGroup** a mít vlastnosti v následující tabulce.  Pro skupiny počítačů, které jsou založené na dotazech protokolu nejsou vytvářeny záznamy.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Počítač |Název členské počítače. |
| Skupina |Název skupiny. |
| GroupFullName |Úplná cesta ke skupině, včetně zdroje a název zdroje. |
| GroupSource |Zdroj skupiny bylo shromážděno z. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Název zdroje shromážděné ze skupiny.  Pro Active Directory jde o název domény. |
| ManagementGroupName |Název skupiny pro správu agentů SCOM.  Pro ostatní agenty to je AOI -\<ID pracovního prostoru\> |
| TimeGenerated |Datum a čas vytvoření nebo aktualizace skupiny počítačů. |

## <a name="next-steps"></a>Další postup
* Další informace o [protokolu dotazy](../log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.  

