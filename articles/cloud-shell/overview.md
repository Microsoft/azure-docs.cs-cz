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
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89468653"
---
# <a name="overview-of-azure-cloud-shell"></a>Přehled služby Azure Cloud Shell

Azure Cloud Shell je interaktivní prostředí pro správu prostředků Azure, které je po ověření dostupné z webového prohlížeče. Umožňuje flexibilně zvolit prostředí, které nejlépe vyhovuje vašemu stylu práce – Bash nebo PowerShell.

K Cloud Shell můžete přistupovat třemi způsoby:

- **Přímý odkaz**: Otevřete prohlížeč na [https://shell.azure.com](https://shell.azure.com) .

- **Azure Portal**: vyberte ikonu Cloud Shell na [Azure Portal](https://portal.azure.com):

    ![Ikona pro spuštění Cloud Shell z Azure Portal](media/overview/portal-launch-icon.png)

- **Fragmenty kódu**: v [docs.Microsoft.com]() a [Microsoft Learn](/learn/)vyberte tlačítko **vyzkoušet** , které se zobrazí v rozhraní příkazového řádku Azure CLI a Azure PowerShell fragmenty kódu:

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    Tlačítko **vyzkoušet** Cloud Shell otevře přímo vedle dokumentace pomocí bash (pro fragmenty rozhraní příkazového řádku Azure CLI) nebo PowerShellu (pro Azure PowerShell fragmenty).

    Chcete-li spustit příkaz, použijte příkaz **Kopírovat** ve fragmentu kódu, použijte **kombinaci kláves CTRL** + **SHIFT** + **v** (Windows/Linux) nebo **cmd** + **SHIFT** + **v** (MacOS) a stiskněte klávesu **ENTER**.

## <a name="features"></a>Funkce

### <a name="browser-based-shell-experience"></a>Prostředí prostředí založené na prohlížeči

Cloud Shell umožňuje přístup k prostředí příkazového řádku založenému na prohlížeči, které je vytvořené pomocí úloh správy Azure. Využijte Cloud Shell pro práci, která je z místního počítače nepřipojená, způsobem, který může poskytnout jenom Cloud.

### <a name="choice-of-preferred-shell-experience"></a>Volba preferovaného prostředí prostředí

Uživatelé si můžou vybrat mezi bash nebo PowerShellem.

1. Vyberte **Cloud Shell**.

    ![Ikona Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Vyberte **bash** nebo **PowerShell**.

    ![Zvolit buď bash nebo PowerShell](media/overview/overview-choices.png)

    Po prvním spuštění můžete použít rozevírací ovládací prvek typ prostředí k přepínání mezi bash a PowerShellem:

    ![Rozevírací ovládací prvek pro výběr bash nebo PowerShellu](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Ověřená a nakonfigurovaná pracovní stanice Azure

Cloud Shell spravuje Microsoft, takže se dodává s oblíbenými nástroji příkazového řádku a jazykovou podporou. Cloud Shell taky automaticky ověřuje pro okamžitý přístup k vašim prostředkům pomocí rutin Azure CLI nebo Azure PowerShell.

Zobrazí úplný [seznam nástrojů nainstalovaných v Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor integrovaných Cloud Shell

Cloud Shell nabízí integrovaný grafický textový editor založený na Open Source editoru Monako. Jednoduše vytvářejte a upravujte konfigurační soubory spuštěním `code .` pro bezproblémové nasazení prostřednictvím rozhraní příkazového řádku Azure nebo Azure PowerShell.

[Přečtěte si další informace o editoru Cloud Shell](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Více přístupových bodů

Cloud Shell je flexibilní nástroj, který lze použít z:

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentace k Azure CLI](/cli/azure)
* [Dokumentace k Azure PowerShellu](/powershell/azure/)
* [Mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Připojení úložiště Microsoft Azurech souborů

Cloud Shell počítače jsou dočasné, ale soubory se chovají dvěma způsoby: prostřednictvím bitové kopie disku a prostřednictvím připojené sdílené složky s názvem `clouddrive` . Při prvním spuštění Cloud Shell zobrazí výzvu k vytvoření skupiny prostředků, účtu úložiště a sdílené složky Azure Files pod vaším účtem. Jedná se o jednorázový krok, který se automaticky připojí pro všechny relace. Jedna sdílená složka může být namapovaná a v Cloud Shell bude používat jak bash, tak i PowerShell.

Přečtěte si další informace o tom, jak připojit [Nový nebo existující účet úložiště](persisting-shell-storage.md) , nebo se dozvíte o [mechanismech trvalosti používaných v Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Brána firewall služby Azure Storage není podporovaná pro účty úložiště Cloud Shell.

## <a name="concepts"></a>Koncepty

* Cloud Shell běží na dočasném hostiteli, který je zadaný na jednotlivých relacích, podle jednotlivých uživatelů.
* Cloud Shell časový limit po 20 minutách bez interaktivní aktivity
* Cloud Shell vyžaduje, aby byla připojena sdílená složka Azure.
* Cloud Shell používá stejnou sdílenou složku Azure pro bash i prostředí PowerShell.
* Cloud Shell je každému uživatelskému účtu přiřazený jeden počítač.
* Cloud Shell přetrvává $HOME použití image o velikosti 5 GB uchovávané ve sdílené složce
* Oprávnění se nastaví jako běžný uživatel se systémem Linux v bash.

Přečtěte si další informace o funkcích v [bash v Cloud Shell](features.md) a [powershellu v Cloud Shell](./features.md).

## <a name="pricing"></a>Ceny

Počítač hostující Cloud Shell je zdarma, a to s předpokladem připojené sdílené složky Azure Files. Platí běžné náklady na úložiště.

## <a name="next-steps"></a>Další kroky

[Bash v rychlém startu Cloud Shell](quickstart.md) <br>
[PowerShell v Cloud Shell rychlý Start](quickstart-powershell.md)