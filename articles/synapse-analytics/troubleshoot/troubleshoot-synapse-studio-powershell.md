---
title: Řešení potíží s připojením funkce Synapse Studio
description: Řešení potíží s připojením ke službě Azure synapse Studio pomocí PowerShellu
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 6ff3d985bb24ec852bb5c6cfaedf295fd79a8247
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120355"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Řešení potíží s připojením synapse Studio pomocí PowerShellu

Azure synapse Studio závisí na sadě koncových bodů webového rozhraní API, aby fungovaly správně. Tato příručka vám pomůže identifikovat příčiny potíží s připojením, když máte tyto možnosti:
- Konfigurace místní sítě (například sítě za podnikovou bránou firewall) pro přístup k Azure synapse studiu.
- nastávají problémy s připojením pomocí Azure synapse studia.

## <a name="prerequisite"></a>Požadavek

* PowerShell 5,0 nebo vyšší verze ve Windows nebo
* PowerShell Core 6,0 nebo vyšší verze v systému Windows nebo Linux.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Klikněte pravým tlačítkem na následující odkaz a vyberte Uložit cíl jako:

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternativně můžete odkaz otevřít přímo a uložit otevřený soubor skriptu. Neuloží adresu odkazu výše, jak se může v budoucnu změnit.

V Průzkumníku souborů klikněte pravým tlačítkem na stažený soubor skriptu a vyberte spustit s prostředím PowerShell.

![Spustit stažený soubor skriptu pomocí PowerShellu](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Po zobrazení výzvy zadejte název pracovního prostoru Azure synapse, který aktuálně má problém nebo který chcete otestovat pro připojení, a stiskněte klávesu ENTER.

![Zadejte název pracovního prostoru.](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Spustí se diagnostická relace. Počkejte, než se dokončí.

![Počkat na dokončení diagnostiky](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Na konci se zobrazí souhrn diagnostiky. Pokud se Váš počítač nemůže připojit k jednomu nebo více koncovým bodům, zobrazí se v části Shrnutí některé návrhy.

![Přehled diagnostiky](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Kromě toho se soubor diagnostického protokolu pro tuto relaci vygeneruje ve stejné složce jako skript pro odstraňování potíží. Jeho umístění se zobrazuje v části Obecné tipy ( `D:\TestAzureSynapse_2020....log` ). V případě potřeby můžete tento soubor poslat technické podpoře.

Pokud jste správcem sítě a vyladěním konfigurace brány firewall pro Azure synapse Studio, může vám pomáhat technické podrobnosti zobrazené výše v části "Souhrn".

* Všechny položky testu (požadavky) označené "Pass" znamenají, že prošly testy připojení bez ohledu na stavový kód HTTP.
 V případě neúspěšných požadavků je důvod zobrazen žlutě, například `NamedResolutionFailure` nebo `ConnectFailure` . Tyto důvody vám pomůžou zjistit, jestli se v síťovém prostředí vyskytly neúspěšné konfigurace.


## <a name="next-steps"></a>Další kroky
Pokud vám předchozí kroky nepomohly problém vyřešit, [vytvořte lístek podpory](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).