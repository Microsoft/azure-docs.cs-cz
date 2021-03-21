---
title: Skupiny počítačů v Azure Monitor dotazy protokolu | Microsoft Docs
description: Skupiny počítačů v Azure Monitor umožňují oborovat dotazy protokolu na konkrétní sadu počítačů.  Tento článek popisuje různé metody, které můžete použít k vytvoření skupin počítačů a jejich použití v dotazu protokolu.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: d8702b498e08561175aa7ee975c7b6b46fdf1687
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031085"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Skupiny počítačů v Azure Monitor dotazy protokolu
Skupiny počítačů v Azure Monitor umožňují oborovat [dotazy protokolu](./log-query-overview.md) na konkrétní sadu počítačů.  Každá skupina se naplní počítači pomocí dotazu, který definujete, nebo importováním skupin z různých zdrojů.  Pokud je skupina zahrnutá v dotazu protokolu, výsledky jsou omezené na záznamy, které odpovídají počítačům ve skupině.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Vytvoření skupiny počítačů
Skupinu počítačů můžete v Azure Monitor vytvořit pomocí kterékoli z metod v následující tabulce.  Podrobnosti o jednotlivých metodách jsou k dispozici v následujících částech. 

| Metoda | Popis |
|:--- |:--- |
| Dotaz protokolu |Vytvořte dotaz protokolu, který vrátí seznam počítačů. |
| Rozhraní API pro prohledávání protokolů |Pomocí rozhraní API pro prohledání protokolu můžete programově vytvořit skupinu počítačů na základě výsledků dotazu protokolu. |
| Active Directory |Automaticky zkontroluje členství všech počítačů s agenty, které jsou členy domény služby Active Directory, a vytvořte skupinu v Azure Monitor pro každou skupinu zabezpečení. (Jenom počítače s Windows)|
| Configuration Manager | Importujte kolekce ze služby Microsoft Endpoint Configuration Manager a vytvořte skupinu v Azure Monitor pro každou z nich. |
| Windows Server Update Services |Automaticky prohledejte servery nebo klienty WSUS pro cílení skupin a vytvořte skupinu v Azure Monitor pro každou z nich. |

### <a name="log-query"></a>Dotaz protokolu
Skupiny počítačů vytvořené z dotazu protokolu obsahují všechny počítače, které jsou vráceny vámi definovaným dotazem.  Tento dotaz se spustí pokaždé, když se použije skupina počítačů, takže se projeví všechny změny od vytvoření skupiny.  

Můžete použít libovolný dotaz pro skupinu počítačů, ale musí vrátit jinou sadu počítačů pomocí `distinct Computer` .  Následuje typický příklad dotazu, který můžete použít jako skupinu počítačů.

```kusto
Heartbeat | where Computer contains "srv" | distinct Computer
```

Následující postup slouží k vytvoření skupiny počítačů z prohledávání protokolu v Azure Portal.

1. V Azure Portal v nabídce **Azure monitor** klikněte na **protokoly** .
1. Vytvořte a spusťte dotaz, který vrátí počítače, které chcete ve skupině.
1. V horní části obrazovky klikněte na **Uložit** .
1. Změňte možnost **Uložit jako** do **funkce** a vyberte **Uložit tento dotaz jako skupinu počítačů**.
1. Zadejte hodnoty pro každou vlastnost skupiny počítačů popsané v tabulce a klikněte na **Uložit**.

Následující tabulka popisuje vlastnosti, které definují skupinu počítačů.

| Vlastnost | Popis |
|:---|:---|
| Název   | Název dotazu, který se má zobrazit na portálu |
| Alias funkce | Jedinečný alias, který slouží k identifikaci skupiny počítačů v dotazu. |
| Kategorie       | Kategorie pro uspořádání dotazů na portálu |


### <a name="active-directory"></a>Active Directory
Když konfigurujete Azure Monitor pro import členství ve skupinách služby Active Directory, analyzuje členství ve skupině všech počítačů připojených k doméně Windows pomocí agenta Log Analytics.  Skupina počítačů se vytvoří v Azure Monitor pro každou skupinu zabezpečení ve službě Active Directory a každý počítač se systémem Windows se přidá do skupin počítačů, které odpovídají skupinám zabezpečení, které jsou členy.  Toto členství se průběžně aktualizuje každé 4 hodiny.  

> [!NOTE]
> Importované skupiny služby Active Directory obsahují pouze počítače se systémem Windows.

Nakonfigurujete Azure Monitor pro import skupin zabezpečení služby Active Directory z **rozšířeného nastavení** v pracovním prostoru Log Analytics v Azure Portal.  Vyberte **skupiny počítačů**, **Active Directory** a pak **importujte členství ve skupinách služby Active Directory z počítačů**.  Není nutná žádná další konfigurace.

![Skupiny počítačů ze služby Active Directory](media/computer-groups/configure-activedirectory.png)

Po importu skupin v nabídce se zobrazí seznam počtu počítačů, ve kterých bylo zjištěno členství ve skupině, a počet importovaných skupin.  Můžete kliknout na kterékoli z těchto odkazů a vrátit se záznamy ze skupina **počítačů** s těmito informacemi.

### <a name="windows-server-update-service"></a>Služba Windows Server Update Service
Když nakonfigurujete Azure Monitor pro import členství ve skupině WSUS, analyzuje členství v cílových skupinách u všech počítačů s agentem Log Analytics.  Pokud používáte cílení na klientské straně, každý počítač, který je připojený k Azure Monitor a je součástí všech skupin cílené na službu WSUS, má členství ve skupině importované do Azure Monitor. Pokud používáte cílení na straně serveru, měl by být agent Log Analytics nainstalován na server WSUS, aby bylo možné do Azure Monitor importovat informace o členství ve skupině.  Toto členství se průběžně aktualizuje každé 4 hodiny. 

Nakonfigurujete Azure Monitor pro import skupin WSUS z **rozšířeného nastavení** v pracovním prostoru Log Analytics v Azure Portal.  Vyberte **skupiny počítačů** a **službu WSUS** a pak **importujte členství ve skupině WSUS**.  Není nutná žádná další konfigurace.

![Skupiny počítačů ze služby WSUS](media/computer-groups/configure-wsus.png)

Po importu skupin v nabídce se zobrazí seznam počtu počítačů, ve kterých bylo zjištěno členství ve skupině, a počet importovaných skupin.  Můžete kliknout na kterékoli z těchto odkazů a vrátit se záznamy ze skupina **počítačů** s těmito informacemi.

### <a name="configuration-manager"></a>Configuration Manager
Když nakonfigurujete Azure Monitor pro import Configuration Manager členství v kolekcích, vytvoří se skupina počítačů pro každou kolekci.  Informace o členství v kolekci se načítají každé 3 hodiny, aby byly skupiny počítačů aktuální. 

Než budete moct importovat kolekce Configuration Manager, musíte [Configuration Manager připojit k Azure monitor](collect-sccm.md).  

![Skupiny počítačů z SCCM](media/computer-groups/configure-sccm.png)

Po importu kolekcí se v nabídce zobrazí seznam počtu počítačů, ve kterých bylo zjištěno členství ve skupině, a počet importovaných skupin.  Můžete kliknout na kterékoli z těchto odkazů a vrátit se záznamy ze skupina **počítačů** s těmito informacemi.

## <a name="managing-computer-groups"></a>Správa skupin počítačů
Můžete zobrazit skupiny počítačů, které byly vytvořeny z dotazu protokolu nebo rozhraní API pro prohledávání protokolu, z **rozšířených nastavení** v pracovním prostoru Log Analytics v Azure Portal.  Vyberte **skupiny počítačů** a pak **uložené skupiny**.  

Kliknutím na **x** ve sloupci **Odebrat** odstraňte skupinu počítačů.  Kliknutím na ikonu **Zobrazit členy** pro skupinu spustíte hledání protokolu skupiny, které vrátí jeho členy.  Nemůžete upravit skupinu počítačů, ale musíte ji odstranit a pak ji znovu vytvořit se změněným nastavením.

![Uložené skupiny počítačů](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Použití skupiny počítačů v dotazu protokolu
Můžete použít skupinu počítačů vytvořenou z dotazu protokolu v dotazu, a to tak, že se její alias považuje za funkci, obvykle s následující syntaxí:

```kusto
Table | where Computer in (ComputerGroup)
```

Například můžete použít následující pro vrácení záznamů UpdateSummary pouze pro počítače ve skupině počítačů s názvem mycomputergroup.

```kusto
UpdateSummary | where Computer in (mycomputergroup)
```

Importované skupiny počítačů a jejich zahrnuté počítače jsou uložené v tabulce skupiny **počítačů** .  Následující dotaz například vrátí seznam počítačů ve skupině Domain Computers ze služby Active Directory. 

```kusto
ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer
```

Následující dotaz vrátí záznamy UpdateSummary pouze pro počítače v doménových počítačích.

```kusto
let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
```

## <a name="computer-group-records"></a>Záznamy skupiny počítačů
V pracovním prostoru Log Analytics se vytvoří záznam pro každé členství ve skupině počítačů vytvořené ze služby Active Directory nebo WSUS.  Tyto záznamy mají typ **počítač** a mají vlastnosti uvedené v následující tabulce.  Pro skupiny počítačů na základě dotazů protokolu nejsou vytvořeny záznamy.

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Název členského počítače |
| `Group` |Název skupiny |
| `GroupFullName` |Úplná cesta ke skupině včetně zdrojového a zdrojového názvu |
| `GroupSource` |Zdroj, ze kterého se skupina shromáždila. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Název zdroje, ze kterého se skupina shromáždila.  V případě služby Active Directory se jedná o název domény. |
| `ManagementGroupName` |Název skupiny pro správu agentů SCOM.  Pro jiné agenty je to AOI-\<workspace ID\> |
| `TimeGenerated` |Datum a čas, kdy byla skupina počítačů vytvořena nebo aktualizována. |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](./log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.