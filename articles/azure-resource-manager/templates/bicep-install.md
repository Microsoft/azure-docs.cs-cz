---
title: Nastavení prostředí pro vývoj a nasazení bicep
description: Jak nakonfigurovat prostředí pro vývoj a nasazení bicep
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0e62e6a4633bee09fcbe8b783118cc95ccd5702e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626097"
---
# <a name="install-bicep-preview"></a>Nainstalovat bicep (Preview)

Naučte se, jak nastavit prostředí pro vývoj a nasazení bicep.

## <a name="development-environment"></a>Vývojové prostředí

K získání nejlepšího prostředí pro vytváření bicep potřebujete dvě komponenty:

- **Rozšíření bicep pro Visual Studio Code**. K vytváření souborů bicep potřebujete dobrý Editor bicep. Doporučujeme [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Tyto nástroje poskytují podporu jazyka a automatického dokončování prostředků. Pomůžou vytvořit a ověřit soubory bicep. Další informace o použití Visual Studio Code a rozšíření bicep najdete v tématu [rychlý Start: vytvoření bicep souborů pomocí Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **BICEP CLI**. Pomocí bicep CLI můžete kompilovat soubory bicep a šablony JSON pro ARM a dekompilovat šablony JSON ARM do souborů bicep. Pokyny k instalaci najdete v tématu Instalace rozhraní příkazového [řádku bicep](#install-manually).

## <a name="deployment-environment"></a>Prostředí nasazení

K nasazení místních souborů bicep potřebujete dvě komponenty:

- **Azure CLI verze 2.20.0 nebo novější, nebo Azure PowerShell verze 5.6.0 nebo novější**. Pokyny k instalaci najdete v těchto tématech:

  - [Instalace Azure PowerShellu](/powershell/azure/install-az-ps)
  - [Instalace Azure CLI ve Windows](/cli/azure/install-azure-cli-windows)
  - [Instalace rozhraní příkazového řádku Azure CLI v systému Linux](/cli/azure/install-azure-cli-linux)
  - [Instalace Azure CLI v macOS](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > V současné době můžou Azure CLI a Azure PowerShell nasadit jenom místní soubory bicep. Další informace o nasazování souborů bicep pomocí rozhraní příkazového řádku Azure najdete v tématu [nasazení-CLI](./deploy-cli.md#deploy-remote-template). Další informace o nasazení souborů bicep pomocí Azure PowerShell naleznete v tématu [Deploy-PowerShell]( ./deploy-powershell.md#deploy-remote-template).

- **BICEP CLI**. Rozhraní příkazového řádku bicep je potřeba ke kompilaci souborů bicep do šablon JSON před nasazením. Pokyny k instalaci najdete v tématu Instalace rozhraní příkazového [řádku bicep](#install-bicep-cli).

Po instalaci komponent můžete nasadit soubor bicep pomocí:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Instalace rozhraní příkazového řádku bicep

- Chcete-li použít rozhraní příkazového řádku bicep ke kompilaci a dekompilaci souborů bicep, přečtěte si téma [Ruční instalace](#install-manually).
- Pokud chcete k nasazení souborů bicep použít Azure CLI, přečtěte si téma [použití s Azure CLI](#use-with-azure-cli).
- Pokud chcete použít Azure PowerShell k nasazení souborů bicep, přečtěte si téma [použití s Azure PowerShell](#use-with-azure-powershell).

### <a name="use-with-azure-cli"></a>Použití s Azure CLI

Když je nainstalovaná verze Azure CLI 2.20.0 nebo novější, bicep CLI se automaticky nainstaluje, když se spustí příkaz, který na něm závisí. Například:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

nebo

```azurecli
az bicep ...
```

Můžete také ručně nainstalovat rozhraní příkazového řádku pomocí integrovaných příkazů:

```bash
az bicep install
```

Postup upgradu na nejnovější verzi:

```bash
az bicep upgrade
```

Instalace konkrétní verze:

```bash
az bicep install --version v0.3.126
```

> [!IMPORTANT]
> Azure CLI nainstaluje samostatnou verzi rozhraní příkazového řádku bicep, která není v konfliktu s žádnou jinou instalací bicep, kterou máte k dispozici, a rozhraní příkazového řádku Azure CLI do vaší cesty nepřidá bicep CLI. Pokud chcete použít rozhraní příkazového řádku bicep ke kompilaci nebo dekompilaci souborů bicep nebo k nasazení bicep souborů použít Azure PowerShell, přečtěte si téma [Ruční instalace](#install-manually) nebo [použití s Azure PowerShellem](#use-with-azure-powershell).

Výpis všech dostupných verzí rozhraní příkazového řádku bicep:

```bash
az bicep list-versions
```

Zobrazení nainstalovaných verzí:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Použít s Azure PowerShell

Azure PowerShell ještě nemá schopnost nainstalovat rozhraní příkazového řádku bicep. Azure PowerShell (v 5.6.0 nebo novějším) předpokládá, že rozhraní příkazového řádku bicep je již nainstalováno a je k dispozici v cestě. Proveďte jednu z [metod ruční instalace](#install-manually).

K nasazení souborů bicep se vyžaduje bicep CLI verze 0.3.1 nebo novější. Postup kontroly verze rozhraní příkazového řádku bicep:

```cmd
bicep --version
```

> [!IMPORTANT]
> Azure CLI nainstaluje vlastní samostatnou verzi bicep CLI. Nasazení Azure PowerShell se nezdařilo i v případě, že máte nainstalované požadované verze pro rozhraní příkazového řádku Azure CLI.

Po instalaci rozhraní příkazového řádku bicep se zavolá bicep CLI, kdykoli je to potřeba pro rutinu nasazení. Například:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>Ruční instalace

Následující metody nainstalujte rozhraní příkazového řádku bicep a přidejte je do vaší cesty.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>přes homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>Ruční instalace macOS

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Instalační služba systému Windows

Stáhněte a spusťte [nejnovější instalační službu systému Windows](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). Instalační program nevyžaduje oprávnění správce. Po dokončení instalace se do cesty uživatele přidá bicep CLI. Zavřete a znovu otevřete všechna otevřená okna příkazového prostředí, aby se změna cesty projevila.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Ruční s PowerShellem

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Instalace nočních buildů

Pokud chcete vyzkoušet nejnovější předběžnou verzi služby bicep před jejich vydáním, přečtěte si téma [instalace nočních buildů](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Tyto předběžné verze buildů jsou mnohem pravděpodobnější, že jsou známé nebo neznámé chyby.

## <a name="next-steps"></a>Další kroky

Začněte s [rychlým startem pro bicep](./quickstart-create-bicep-use-visual-studio-code.md).
