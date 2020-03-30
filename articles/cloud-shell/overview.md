---
title: Přehled cloudového prostředí Azure | Dokumenty společnosti Microsoft
description: Přehled prostředí Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832470"
---
# <a name="overview-of-azure-cloud-shell"></a>Přehled služby Azure Cloud Shell
Azure Cloud Shell je interaktivní prostředí pro správu prostředků Azure, které je po ověření dostupné z webového prohlížeče.
Umožňuje flexibilně zvolit prostředí, které nejlépe vyhovuje vašemu stylu práce – Bash nebo PowerShell.

Zkuste z shell.azure.com kliknutím níže.

[![Spuštění vložení](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

Vyzkoušejte si z portálu Azure pomocí ikony Cloud Shell.

![Spuštění portálu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkce

### <a name="browser-based-shell-experience"></a>Prostředí založené na prohlížeči
Cloud Shell umožňuje přístup k prostředí příkazového řádku založenému na prohlížeči, které je vytvořeno s ohledem na úlohy správy Azure.
Využijte Cloud Shell k práci nespoutanou od místního počítače způsobem, který může poskytnout jenom cloud.

### <a name="choice-of-preferred-shell-experience"></a>Volba preferovaného prostředí
Uživatelé si mohou vybrat mezi Bash nebo PowerShell.
1. Vyberte **cloudové prostředí**.

    ![Ikona cloudového prostředí](media/overview/overview-cloudshell-icon.png)

2. Vyberte **Bash** nebo **PowerShell**.

    ![Zvolte Bash nebo PowerShell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Ověřená a nakonfigurovaná pracovní stanice Azure
Cloud Shell spravuje Microsoft, takže je dodáván s oblíbenými nástroji příkazového řádku a jazykovou podporou. Cloud Shell se také automaticky bezpečně ověřuje pro okamžitý přístup k vašim prostředkům prostřednictvím rutin Azure CLI nebo Azure PowerShell.

Prohlédněte si úplný [seznam nástrojů nainstalovaných v Cloud Shellu.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrovaný editor cloudového prostředí
Cloud Shell nabízí integrovaný grafický textový editor založený na open-source Editoru Monaka. Jednoduše vytvářejte a upravujte konfigurační soubory spuštěním `code .` bezproblémového nasazení prostřednictvím azure cli nebo Azure PowerShellu.

[Další informace o editoru Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrovaná s docs.microsoft.com

Cloud Shell můžete používat přímo z dokumentace hostované na [docs.microsoft.com](https://docs.microsoft.com). Je integrovaná v [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) a [Azure CLI dokumentace](https://docs.microsoft.com/cli/azure) – klikněte na tlačítko "Vyzkoušet" ve fragmentu kódu otevřít pohlcující prostředí prostředí. 

### <a name="multiple-access-points"></a>Více přístupových bodů
Cloud Shell je flexibilní nástroj, který lze použít z:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentace k Azure CLI](https://docs.microsoft.com/cli/azure)
* [Dokumentace k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview)
* [Mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Rozšíření účtu Azure kódu Visual Studia](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Připojení úložiště souborů Microsoft Azure
Cloud Shell počítače jsou dočasné, ale vaše soubory jsou trvalé dvěma způsoby: `clouddrive`prostřednictvím bitové kopie disku a prostřednictvím připojené sdílené složky s názvem .  Při prvním spuštění Cloud Shell zobrazí výzvu k vytvoření skupiny prostředků, účtu úložiště a sdílené složky Azure Files pod vaším účtem. Jedná se o jednorázový krok, který bude automaticky připojen pro všechny relace. Jeden soubor sdílené složky lze namapovat a bude použit jak Bash a PowerShell v Cloud Shell.

Další informace o připojení [nového nebo stávajícího účtu úložiště](persisting-shell-storage.md) nebo o [mechanismech trvalosti používaných v prostředí Cloud Shell najdete](persisting-shell-storage.md#how-cloud-shell-storage-works)v další informace.

> [!NOTE]
> Brána firewall úložiště Azure není podporovaná pro účty úložiště cloudshellu.

## <a name="concepts"></a>Koncepty
* Cloud Shell běží na dočasném hostiteli poskytovaném na základě relace pro jednotlivé uživatele
* Časový nebo nedoslužný čas prostředí Cloud Shell po 20 minutách bez interaktivní aktivity
* Cloud Shell vyžaduje připojení sdílené složky Azure
* Cloud Shell používá stejnou sdílenou složku Azure pro Bash i PowerShell
* Cloud Shell je přiřazen jeden počítač na uživatelský účet
* Cloud Shell uchovává $HOME pomocí obrázku o velikosti 5 GB, který se drží ve sdílené složce
* Oprávnění jsou nastavena jako běžný uživatel Linuxu v Bash

Další informace o funkcích v [Bash v Cloud Shellu](features.md) a [PowerShellu v Cloud Shellu](features-powershell.md).

## <a name="pricing"></a>Ceny
Počítač hostující Cloud Shell je zdarma, s předpokladem připojené sdílené složky Azure Files. Platí běžné náklady na skladování.

## <a name="next-steps"></a>Další kroky
[Bash v rychlém startu Cloud Shellu](quickstart.md) <br>
[PowerShell v cloudovém prostředí rychlý start](quickstart-powershell.md)
