---
title: Přehled Azure Cloud Shell | Microsoft Docs
description: Přehled Azure Cloud Shell.
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
ms.openlocfilehash: acdf52022d8498a186ed4cff90284cd6e299d6c0
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240844"
---
# <a name="overview-of-azure-cloud-shell"></a>Přehled Azure Cloud Shell
Azure Cloud Shell je interaktivní, ověřené prostředí přístupné pro prohlížeč pro správu prostředků Azure.
Nabízí flexibilitu při výběru prostředí, které nejlépe vyhovuje způsobu práce, a to buď bash nebo PowerShell.

Vyzkoušejte si shell.azure.com Kliknutím níže.

[![Vložit spuštění](https://shell.azure.com/images/launchcloudshell.png "Spustit Azure Cloud Shell")](https://shell.azure.com)

Zkuste z Azure Portal pomocí ikony Cloud Shell.

![Spuštění portálu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkce

### <a name="browser-based-shell-experience"></a>Prostředí prostředí založené na prohlížeči
Cloud Shell umožňuje přístup k prostředí příkazového řádku založenému na prohlížeči, které je vytvořené pomocí úloh správy Azure.
Využijte Cloud Shell pro práci, která je z místního počítače nepřipojená, způsobem, který může poskytnout jenom Cloud.

### <a name="choice-of-preferred-shell-experience"></a>Volba preferovaného prostředí prostředí
Uživatelé si můžou z rozevíracího seznamu prostředí volit mezi bash nebo PowerShellem.

![Bash v Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell ve službě Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Ověřená a nakonfigurovaná pracovní stanice Azure
Cloud Shell spravuje Microsoft, takže se dodává s oblíbenými nástroji příkazového řádku a jazykovou podporou. Cloud Shell taky automaticky ověřuje pro okamžitý přístup k vašim prostředkům pomocí rutin Azure CLI nebo Azure PowerShell.

Zobrazí úplný [seznam nástrojů nainstalovaných v Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor integrovaných Cloud Shell
Cloud Shell nabízí integrovaný grafický textový editor založený na Open Source editoru Monako. Jednoduše vytvářejte a upravujte konfigurační soubory spuštěním `code .` pro bezproblémové nasazení prostřednictvím rozhraní příkazového řádku Azure nebo Azure PowerShell.

[Přečtěte si další informace o editoru Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrace s docs.microsoft.com

Cloud Shell můžete použít přímo z dokumentace hostované na [docs.Microsoft.com](https://docs.microsoft.com). Je integrovaná v [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) a dokumentaci k rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure) – kliknutím na tlačítko vyzkoušet si ve fragmentu kódu otevřete prostředí s moderními prostředími. 

### <a name="multiple-access-points"></a>Několik přístupových bodů
Cloud Shell je flexibilní nástroj, který lze použít z:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentace k rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure)
* [Dokumentace k Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Připojení úložiště Microsoft Azurech souborů
Cloud Shell počítače jsou dočasné, ale soubory se chovají dvěma způsoby: prostřednictvím bitové kopie disku a prostřednictvím připojené sdílené složky s názvem `clouddrive`.  Při prvním spuštění Cloud Shell vyzve k vytvoření skupiny prostředků, účtu úložiště a sdílené složky Azure Files vaším jménem. Jedná se o jednorázový krok, který se automaticky připojí pro všechny relace. Jedna sdílená složka může být namapovaná a v Cloud Shell bude používat jak bash, tak i PowerShell.

Přečtěte si další informace o tom, jak připojit [Nový nebo existující účet úložiště](persisting-shell-storage.md) , nebo se dozvíte o [mechanismech trvalosti používaných v Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

## <a name="concepts"></a>Koncepty
* Cloud Shell běží na dočasném hostiteli, který je zadaný na jednotlivých relacích, podle jednotlivých uživatelů.
* Cloud Shell časový limit po 20 minutách bez interaktivní aktivity
* Cloud Shell vyžaduje, aby byla připojena sdílená složka Azure.
* Cloud Shell používá stejnou sdílenou složku Azure pro bash i prostředí PowerShell.
* Cloud Shell je každému uživatelskému účtu přiřazený jeden počítač.
* Cloud Shell přetrvává $HOME použití image o velikosti 5 GB uchovávané ve sdílené složce
* Oprávnění se nastaví jako běžný uživatel se systémem Linux v bash.

Přečtěte si další informace o funkcích v [bash v Cloud Shell](features.md) a [powershellu v Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Ceny
Počítač hostující Cloud Shell je zdarma, a to s předpokladem připojené sdílené složky Azure Files. Platí běžné náklady na úložiště.

## <a name="next-steps"></a>Další kroky
[Bash v rychlém startu Cloud Shell](quickstart.md) <br>
[PowerShell v Cloud Shell rychlý Start](quickstart-powershell.md)
