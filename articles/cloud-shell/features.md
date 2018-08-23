---
title: Funkce Azure Cloud Shell | Dokumentace Microsoftu
description: Přehled funkcí z prostředí Bash ve službě Azure Cloud Shell
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: juluk
ms.openlocfilehash: 1321645d97e7f6ff2faed1e61ddb608afcb7b413
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42056790"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkce a nástroje pro Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell spouští na `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funkce

### <a name="secure-automatic-authentication"></a>Automatické ověřování zabezpečení

Cloud Shell bezpečně a automaticky ověří přístup k účtu pro Azure CLI 2.0 a Azure Powershellu.

### <a name="home-persistence-across-sessions"></a>Trvalost $Home napříč relacemi

K trvalému ukládání souborů napříč relacemi, Cloud Shell vás provede připojením sdílené složky Azure při prvním spuštění.
Po dokončení Cloud Shell automaticky připojit úložiště (připojit jako `$Home\clouddrive`) pro všechny budoucí relace.
Kromě toho vaše `$Home` adresáře se ukládají jako img do sdílené složky Azure.
Souborů mimo `$Home` a stav počítače nejsou trvalé napříč relacemi. Použijte osvědčené postupy při ukládání tajných klíčů, jako jsou klíče SSH. Služeb jako [Azure Key Vault mít podrobné pokyny pro nastavení](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Další informace o zachování souborů ve službě Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Jednotka Azure (Azure:)

PowerShell ve službě Cloud Shell (Preview) se spustí můžete v Azure disk (`Azure:`).
Jednotka Azure umožňuje snadné zjišťování a navigace Azure prostředky, jako jsou výpočetní prostředky, sítě, úložiště atd podobný navigace systému souborů.
Můžete dál používat známá [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure) spravovat tyto prostředky bez ohledu na jednotce v.
Všechny změny provedené u prostředků Azure, buď přímo na webu Azure portal nebo prostřednictvím rutin Powershellu pro Azure, se projeví v jednotce Azure.  Můžete spustit `dir -Force` aktualizovat vaše prostředky.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>Těsnou integraci s open source nástroje

Cloud Shell zahrnuje předem nakonfigurované ověřování pro open source nástroje, jako je například Terraformu, Ansible a Chef InSpec. Vyzkoušejte si z návody pro příklad.

## <a name="tools"></a>Nástroje

|Kategorie   |Název   |
|---|---|
|Nástroje pro Linux            |Bash<br> zsh<br> TV<br> tmux<br> Ponořte se<br>               |
|Nástroje Azure            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) a [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) |
|Textových editorů           |VIM<br> nano<br> (emacs)       |
|Správy zdrojového kódu         |git                    |
|Nástroje sestavení            |Ujistěte se<br> Maven<br> npm<br> PIP         |
|Containers             |[Rozhraní příkazového řádku dockeru](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Příkaz Helm](https://github.com/kubernetes/helm)<br> [ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU DC/OS](https://github.com/dcos/dcos-cli)         |
|Databáze              |Klient MySQL<br> Klient PostgreSql<br> [Nástroj SQLCMD](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Ostatní                  |iPython klienta<br> [Cloud Foundry rozhraní příkazového řádku](https://github.com/cloudfoundry/cli)<br> [Terraformu](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>Podpora jazyků

|Jazyk   |Verze   |
|---|---|
|.NET Core  |2.0.0       |
|Přejít         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.1.0-Preview.4](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 a 3.5 (výchozí)|

## <a name="next-steps"></a>Další postup
[Bash v Cloud Shellu Quickstart](quickstart.md) <br>
[Prostředí PowerShell v Cloud Shellu (Preview) Quickstart](quickstart-powershell.md) <br>
[Další informace o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Další informace o Azure Powershellu](https://docs.microsoft.com/powershell/azure/) <br>
