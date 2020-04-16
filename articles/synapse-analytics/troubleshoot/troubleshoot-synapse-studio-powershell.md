---
title: Poradce při potížích s připojením PowerShellu Azure Synapse Studio (preview)
description: Poradce při potížích s připojením Azure Synapse Studio pomocí PowerShellu
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431472"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnostika problémů s připojením Azure Synapse Studio (preview) se skriptem PowerShellu

Azure Synapse Studio (preview) závisí na sadě koncových bodů webového rozhraní API správně fungovat. Tato příručka vám pomůže identifikovat příčiny problémů s připojením, když:
- konfigurace místní sítě (například sítě za podnikovou bránou firewall) pro přístup k Azure Synapse Studio.
- dochází k problémům s připojením pomocí Azure Synapse Studio.

## <a name="prerequisite"></a>Požadavek

* PowerShell 5.0 nebo vyšší verze ve Windows nebo
* PowerShell Core 6.0 nebo vyšší verze ve Windows nebo Linuxu.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Klikněte pravým tlačítkem myši na následující odkaz a klikněte na "Uložit cíl jako":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Případně můžete otevřít odkaz přímo a uložit otevřený soubor skriptu. Neukládejte adresu výše uvedeného odkazu, protože se může v budoucnu změnit.

V průzkumníku souborů klikněte pravým tlačítkem myši na stažený soubor skriptu a klikněte na "Spustit pomocí prostředí PowerShell".

![Spuštění staženého souboru skriptu pomocí prostředí PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Po zobrazení výzvy zadejte název pracovního prostoru Azure Synapse, který právě dochází k potížím nebo který chcete otestovat připojení a stiskněte klávesu ENTER.

![Zadání názvu pracovního prostoru](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Bude spuštěna diagnostická relace. Počkejte, až se to dokončí.

![Počkejte na dokončení diagnózy](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Nakonec se zobrazí souhrn diagnózy. Pokud se váš počítač nemůže připojit k jednomu nebo více koncovým bodům, zobrazí se v části Souhrn některé návrhy.

![Zkontrolovat souhrn diagnostiky](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Kromě toho bude ve stejné složce jako skript pro řešení potíží vygenerován diagnostický soubor protokolu pro tuto relaci. Jeho umístění je zobrazeno v`D:\TestAzureSynapse_2020....log`sekci "Obecné tipy" ( ). V případě potřeby můžete tento soubor odeslat technické podpoře.

Pokud jste správce sítě a ladění konfigurace brány firewall pro Azure Synapse Studio, technické podrobnosti uvedené výše v části "Souhrn" může pomoci.

* Všechny testovací položky (požadavky) označené "Předáno" znamenají, že prošly testy připojení, bez ohledu na stavový kód HTTP.
 U neúspěšných požadavků je důvod zobrazen žlutě, například `NamedResolutionFailure` nebo `ConnectFailure`. Tyto důvody vám mohou pomoci zjistit, zda existují chybné konfigurace v síťovém prostředí.


## <a name="next-steps"></a>Další kroky
Pokud předchozí kroky nepomáhají k vyřešení [problému: Vytvořte lístek podpory](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
