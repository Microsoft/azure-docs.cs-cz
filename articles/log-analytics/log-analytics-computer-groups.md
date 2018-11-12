---
title: Prohledávání protokolů skupiny počítačů v Azure Log Analytics | Dokumentace Microsoftu
description: Skupiny počítačů v Log Analytics vám umožní rozsah prohledávání protokolů konkrétní sadě počítačů.  Tento článek popisuje různé metody, které lze použít k vytvoření skupiny počítačů a jejich použití v hledání v protokolu.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: f96d18c2e1a00008f2e391302a41d145d6b47efd
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016601"
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Prohledávání protokolů skupiny počítačů v Log Analytics

Skupiny počítačů v Log Analytics vám umožní oboru [prohledávání protokolů](log-analytics-queries.md) konkrétní sadě počítačů.  Každá skupina se zaplní, buď dotaz, který definujete pomocí počítače nebo importováním skupiny z různých zdrojů.  Skupiny je obsažen v prohledávání protokolu, výsledky jsou omezené na záznamy, které odpovídají počítačů ve skupině.

## <a name="creating-a-computer-group"></a>Vytvořit skupinu počítačů
Vytvořit skupinu počítačů v Log Analytics pomocí některé z metod v následující tabulce.  Podrobnosti o každé metody jsou k dispozici v následujících částech. 

| Metoda | Popis |
|:--- |:--- |
| Prohledávání protokolů |Vytvoření prohledávání protokolu, který vrátí seznam počítačů. |
| Rozhraní API pro prohledávání protokolů |Pomocí rozhraní API pro vyhledávání protokolu prostřednictvím kódu programu vytvořit skupinu počítačů na základě výsledků prohledávání protokolu. |
| Active Directory |Automaticky vyhledávat členství ve skupině všechny počítače agenta, které jsou členy domény služby Active Directory a vytvoření skupiny pro každou skupinu zabezpečení ve službě Log Analytics. |
| Configuration Manager | Importovat kolekce z nástroje System Center Configuration Manager a vytvoření skupiny ve službě Log Analytics pro každého. |
| Windows Server Update Services |Automaticky vyhledat cílí na skupiny služby WSUS servery nebo klienty a vytvořte skupinu ve službě Log Analytics pro každého. |

### <a name="log-search"></a>Prohledávání protokolů
Skupiny počítačů, které jsou vytvořené z prohledávání protokolu obsahují všechny počítače vrácené dotazem, který určíte.  Tento dotaz je spustit pokaždé, když se skupina počítačů se používá tak, aby se projeví všechny změny, protože byla skupina vytvořena.  

Můžete použít jakýkoli dotaz pro skupinu počítačů, ale musí vracet různé sady počítačů pomocí `distinct Computer`.  Tady je typickým příkladem vyhledávání, které můžete použít jako skupinu počítačů.

    Heartbeat | where Computer contains "srv" | distinct Computer

Následující tabulka popisuje vlastnosti, které definují skupinu počítačů.

| Vlastnost | Popis |
|:---|:---|
| Zobrazovaný název   | Název hledání můžete zobrazit na portálu. |
| Kategorie       | Kategorie k uspořádání hledání na portálu. |
| Dotaz          | Dotaz pro skupinu počítačů. |
| Alias funkce | Jedinečný alias používaný k identifikaci skupinu počítačů v dotazu. |

Pomocí následujícího postupu můžete vytvořit skupinu počítačů z prohledávání protokolů na webu Azure Portal.

2. Otevřít **prohledávání protokolů** a potom klikněte na tlačítko **uložená hledání** v horní části obrazovky.
3. Klikněte na tlačítko **přidat** a zadejte hodnoty pro každou vlastnost pro skupinu počítačů.
4. Vyberte **uložit tento dotaz jako skupinu počítačů** a klikněte na tlačítko **OK**.



### <a name="active-directory"></a>Active Directory
Při konfiguraci Log Analytics pro import členství skupin Active Directory, analyzuje členství ve skupině všechny počítače připojené k doméně pomocí agenta Log Analytics.  Skupina počítačů se vytvoří v Log Analytics pro každou skupinu zabezpečení ve službě Active Directory a každý počítač přidán do skupiny počítačů, které jsou členy skupiny zabezpečení odpovídající.  Toto členství se průběžně aktualizuje každé 4 hodiny.  

Konfigurace Log Analytics k importu skupin zabezpečení služby Active Directory ze služby Log Analytics **upřesňující nastavení** na webu Azure Portal.  Vyberte **skupiny počítačů**, **služby Active Directory**a potom **členství ve skupině služby Active Directory Import z počítačů**.  Není nutná žádná další konfigurace.

![Skupiny počítačů ze služby Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Importu skupin v nabídce uvádí počet počítačů s členstvím skupiny zjistila a číslem skupiny importovat.  Kliknutím na některý z těchto odkazů se vraťte **ComputerGroup** záznamy s těmito informacemi.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Při konfiguraci Log Analytics pro import členství ve skupině WSUS analyzuje cílení členství ve skupině všechny počítače pomocí agenta Log Analytics.  Pokud používáte na straně klienta cílí na skupiny cílí na jakýkoli počítač, který je připojený ke službě Log Analytics a je součástí libovolné služby WSUS se jeho členství ve skupině naimportovány do služby Log Analytics. Pokud používáte server-side cílení, Log Analytics by měl být agent nainstalovaný na serveru WSUS, aby se informace o členství ve skupinách pro import do Log Analytics.  Toto členství se průběžně aktualizuje každé 4 hodiny. 

Konfigurace Log Analytics k importu skupin WSUS ze služby Log Analytics **upřesňující nastavení** na webu Azure Portal.  Vyberte **skupiny počítačů**, **WSUS**a potom **služby WSUS pro Import členství ve skupinách**.  Není nutná žádná další konfigurace.

![Skupiny počítačů služby WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Importu skupin v nabídce uvádí počet počítačů s členstvím skupiny zjistila a číslem skupiny importovat.  Kliknutím na některý z těchto odkazů se vraťte **ComputerGroup** záznamy s těmito informacemi.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Při konfiguraci Log Analytics pro import členství v kolekcích Configuration Manageru se vytvoří skupina počítačů pro každou kolekci.  Informace o členství v kolekci se načte každé tři hodiny zachovat aktuální skupiny počítačů. 

Před importem kolekcí nástroje Configuration Manager, je nutné [propojení Configuration Manageru k Log Analytics](log-analytics-sccm.md).  Potom můžete nakonfigurovat import ze služby Log Analytics **upřesňující nastavení** na webu Azure Portal.  Vyberte **skupiny počítačů**, **SCCM**a potom **členství v kolekcích Configuration Manageru Import**.  Není nutná žádná další konfigurace.

![Skupiny počítačů z SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Importu kolekce v nabídce uvádí počet počítačů s členstvím skupiny zjistila a číslem skupiny importovat.  Kliknutím na některý z těchto odkazů se vraťte **ComputerGroup** záznamy s těmito informacemi.

## <a name="managing-computer-groups"></a>Správa skupin počítačů
Můžete zobrazit skupiny počítačů, které byly vytvořeny pomocí prohledávání protokolů nebo rozhraní API pro vyhledávání protokolů v Log Analytics **upřesňující nastavení** na webu Azure Portal.  Vyberte **skupiny počítačů** a potom **uložit skupiny**.  

Klikněte na tlačítko **x** v **odebrat** sloupec, který chcete odstranit skupinu počítačů.  Klikněte na tlačítko **zobrazit členy** ikonu skupiny ke spuštění hledání v protokolu skupiny, která vrací jejích členů.  Nelze upravit skupinu počítačů, ale místo toho musíte odstranit a znovu ji vytvořte s upravená nastavení.

![Uložené skupiny počítačů](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Použití skupiny počítačů v prohledávání protokolu
Můžete použít skupinu počítačů vytvořeny z prohledávání protokolů v dotazu úpravou jeho alias jako funkce, obvykle s následující syntaxí:

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
V pracovním prostoru Log Analytics pro každého členství ve skupině počítačů vytvořené ze služby Active Directory nebo služby WSUS se vytvoří záznam.  Tyto záznamy mají typ **ComputerGroup** a mít vlastnosti v následující tabulce.  Pro skupiny počítačů, které jsou založené na prohledávání protokolů nejsou vytvářeny záznamy.

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
* Další informace o [prohledávání protokolů](log-analytics-queries.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.  

