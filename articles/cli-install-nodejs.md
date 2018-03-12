---
title: Nainstalovat Azure CLI 1.0 | Microsoft Docs
description: "Instalace 1.0 rozhraní příkazového řádku Azure pro Mac, Linux a Windows, pokud chcete začít používat služby Azure"
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
ms.openlocfilehash: a1ca71a81f4fa6dd36b9ed3a2b16fcca0e29601c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="install-the-azure-cli-10"></a>Nainstalovat Azure CLI 1.0
> [!div class="op_single_selector"]
> * [PowerShell](/powershell/azure/overview)
> * [Azure CLI 1.0](cli-install-nodejs.md)
> * [Azure CLI 2.0](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> Toto téma popisuje, jak nainstalovat Azure CLI 1.0. Toto rozhraní příkazového řádku je zastaralá a musí být použit pouze pro podporu s modelem Azure Service Management (ASM) s "classic" prostředky.
> Pro nasazení Azure Resource Manager, použijte [Azure CLI 2.0](/cli/azure).

Rychle nainstalujte rozhraní příkazového řádku Azure (Azure CLI 1.0), abyste mohli použít sadu opensourcových příkazů prostředí pro vytváření a správu prostředků v Microsoft Azure. Máte několik možností instalace těchto nástrojů pro různé platformy ve vašem počítači:

* **balíčku npm** -spustit npm (Správce balíčků pro JavaScript) nainstalovat nejnovější balíček Azure CLI 1.0 na operačního systému nebo distribuce systému Linux. Vyžaduje node.js a npm ve vašem počítači.
* **Instalační program** -stáhnout instalační program pro instalaci snadno na Mac nebo Windows.
* **Kontejner docker** – spuštění pomocí nejnovější rozhraní příkazového řádku v kontejner Docker připravené k použití. Vyžaduje hostitelů Docker v počítači.

Další možnosti a pozadí, najdete v úložišti projektu na [Githubu](https://github.com/azure/azure-xplat-cli).

Po instalaci Azure CLI 1.0 [připojit ke svému předplatnému Azure](/cli/azure/authenticate-azure-cli) a spusťte **azure** příkazy z vaší rozhraní příkazového řádku (Bash, terminálu, příkazový řádek a tak dále) pro práci s vaší Prostředky Azure.

## <a name="option-1-install-an-npm-package"></a>Možnost 1: Instalace balíčku npm
Pokud chcete nainstalovat rozhraní příkazového řádku z balíčku npm, zajistěte, aby byly staženy a nainstalovány [nejnovější Node.js a npm](https://nodejs.org/en/download/package-manager/). Potom spusťte **npm nainstalujte** k instalaci balíčku rozhraní příkazového řádku azure:

```bash
npm install -g azure-cli
```

Na Linuxových distribucích, možná budete muset použít **sudo** k úspěšnému spuštění **npm** příkaz takto:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Pokud potřebujete instalaci nebo aktualizaci Node.js a npm na operačního systému nebo distribuce systému Linux, doporučujeme nainstalovat nejnovější verzi Node.js LTS (4.x). Pokud používáte starší verzi, může docházet k chybám instalace.

Pokud dáváte přednost, stáhněte si nejnovější Linux [vkládání souborů] [ linux-installer] balíčku npm místně. Potom nainstalovat balíček stažené npm následujícím způsobem (na Linuxových distribucích možná budete muset použít **sudo**):

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>Možnost 2: Použít instalační program
Pokud používáte počítači se systémem Mac nebo Windows, jsou k dispozici ke stažení následujících instalačních programů rozhraní příkazového řádku:

* [Instalátor systému Mac OS X][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> V systému Windows, můžete také stáhnout [instalačního programu webové platformy](https://go.microsoft.com/?linkid=9828653) nainstalovat rozhraní příkazového řádku. Tento instalační program vám dává možnost nainstalovat další Azure SDK a nástroje příkazového řádku po instalaci rozhraní příkazového řádku.

## <a name="option-3-use-a-docker-container"></a>Možnost 3: Použití kontejner Docker
Pokud jste nastavili počítač jako [Docker](https://docs.docker.com/engine/understanding-docker/) hostiteli, nejnovější 1.0 rozhraní příkazového řádku Azure můžete spouštět v kontejner Docker. Spusťte následující příkaz (na Linuxových distribucích možná budete muset použít **sudo**):

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Příkazy spouštějte Azure CLI 1.0
Po instalaci Azure CLI 1.0 spustit **azure** příkazu z příkazového řádku uživatelského rozhraní (Bash, terminálu, příkazový řádek a tak dále). Například spusťte příkaz nápovědu, zadejte následující příkaz:

```azurecli
azure help
```

> [!NOTE]
> V některých distribucích systému Linux, může se zobrazit chyba podobná `/usr/bin/env: ‘node’: No such file or directory`. Tato chyba pochází z poslední instalace instaluje na /usr/bin/nodejs Node.js. Chcete-li problém odstranit, vytvořte symbolický odkaz na /usr/bin/node spuštěním tohoto příkazu:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Pokud chcete zobrazit verzi 1.0 rozhraní příkazového řádku Azure, jste nainstalovali, zadejte následující příkaz:

```azurecli
azure --version
```

Nyní jste připraveni! Pro přístup k všechny příkazy rozhraní příkazového řádku pro práci s vlastními prostředky [připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

> [!NOTE]
> Při prvním použití Azure CLI, zobrazí se zpráva s dotazem, pokud chcete povolit Microsoftu shromažďovat informace o využití. Účast je dobrovolná. Pokud se rozhodnete pro účast můžete kdykoli zastavit spuštěním `azure telemetry --disable`. Chcete-li povolit účast kdykoli, spusťte `azure telemetry --enable`.

## <a name="update-the-cli"></a>Aktualizace rozhraní příkazového řádku
Společnost Microsoft vydá často aktualizované verze rozhraní příkazového řádku Azure. Přeinstalujte rozhraní příkazového řádku pomocí Instalační služby operačního systému nebo spustit nejnovější kontejner Docker. Nebo, pokud máte nejnovější Node.js a npm nainstalován, aktualizace pomocí následujícího příkazu (na Linuxových distribucích možná budete muset použít **sudo**).

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Povolit dokončování pomocí tabulátorů
Dokončování pomocí tabulátorů z rozhraní příkazového řádku je podporována pro Mac a Linux.

Chcete-li povolit v zsh, spusťte:

```bash
echo '. <(azure --completion)' >> .zshrc
```

Chcete-li povolit v bash, spusťte:

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Další postup
* [Připojení k předplatnému Azure z rozhraní příkazového řádku](/cli/azure/authenticate-azure-cli) k vytváření a správě prostředků Azure.
* Další informace o Azure CLI, stažení zdrojový kód, problémy sestavy, nebo můžete přispět k projektu, najdete [úložiště GitHub pro rozhraní příkazového řádku Azure](https://github.com/azure/azure-xplat-cli).
* Pokud máte dotazy týkající se používání rozhraní příkazového řádku Azure nebo Azure, přejděte [fóra Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
