---
title: Funkce Azure Cloud Shell | Microsoft Docs
description: Přehled funkcí v Azure Cloud Shell
services: Azure
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
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 409e8667f03843ae1b1bad56f189ff17b29d549a
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85506561"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkce & nástroje pro Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell běží na `Ubuntu 16.04 LTS` .

## <a name="features"></a>Funkce

### <a name="secure-automatic-authentication"></a>Zabezpečené automatické ověřování

Cloud Shell bezpečně a automaticky ověřuje přístup k účtu Azure CLI a Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME Persistence napříč relacemi

Aby bylo možné zachovat soubory napříč relacemi, Cloud Shell vás při prvním spuštění připojit ke sdílené složce Azure.
Po dokončení Cloud Shell automaticky připojí úložiště (připojené jako `$HOME\clouddrive` ) pro všechny budoucí relace.
Navíc je váš `$HOME` adresář uložený ve sdílené složce Azure jako soubor. img.
Soubory mimo `$HOME` počítač a stav počítače nejsou v relacích trvalé. Používejte osvědčené postupy při ukládání tajných kódů, jako jsou klíče SSH. Pro služby, jako [je Azure Key Vault, jsou k dispozici kurzy pro instalaci](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Přečtěte si další informace o trvalém ukládání souborů v Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Jednotka Azure (Azure:)

PowerShell v Cloud Shell poskytuje jednotku Azure ( `Azure:` ). Můžete přepnout na jednotku Azure s `cd Azure:` a zpátky do svého domovského adresáře pomocí `cd  ~` .
Jednotka Azure umožňuje snadné zjišťování a navigaci prostředků Azure, jako jsou výpočetní prostředky, síť, úložiště atd., podobně jako navigace systému souborů.
Ke správě těchto prostředků můžete dál používat známé [Azure PowerShell rutiny](https://docs.microsoft.com/powershell/azure) bez ohledu na jednotku, ve které jste.
Jakékoli změny provedené v prostředcích Azure provedené přímo v Azure Portal nebo prostřednictvím rutin Azure PowerShell se projeví na jednotce Azure.  Můžete spustit `dir -Force` pro aktualizaci prostředků.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Správa Exchange Online

Prostředí PowerShell v Cloud Shell obsahuje soukromé sestavení modulu Exchange Online.  Spusťte příkaz `Connect-EXOPSSession` a získejte rutiny Exchange.

![](media/features-powershell/exchangeonline.png)

 Spusťte `Get-Command -Module tmp_*`.
> [!NOTE]
> Název modulu by měl začínat `tmp_` , pokud máte moduly se stejnou předponou, jejich rutiny budou také Surface. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Hluboká integrace s open source nástroji

Cloud Shell zahrnuje předem nakonfigurované ověřování pro open source nástroje, jako jsou Terraformu, Ansible a INSPEC. Vyzkoušejte si to z ukázkových návodů.

## <a name="tools"></a>nástroje

|Kategorie   |Název   |
|---|---|
|Nástroje pro Linux            |bash<br> zsh<br> SH<br> tmux<br> dig<br>               |
|Nástroje Azure            |[Azure CLI](https://github.com/Azure/azure-cli) a [Azure Classic CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editory textu           |kód (Editor Cloud Shell)<br> vim<br> nano<br> emacs    |
|Správa zdrojového kódu         |git                    |
|Nástroje pro sestavení            |značka<br> maven<br> npm<br> PIP         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU DC/OS](https://github.com/dcos/dcos-cli)         |
|Databáze              |Klient MySQL<br> Klient PostgreSql<br> [Nástroj Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [skript MSSQL](https://github.com/Microsoft/sql-xplat-cli) |
|Ostatní                  |Klient iPython<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Nespec](https://www.chef.io/inspec/)<br> [Puppet](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Pack](https://www.packer.io/)<br> [Sada Office 365 CLI](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>Podpora jazyků

|Jazyk   |Verze   |
|---|---|
|.NET Core  |2.2.402       |
|Přejít         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 a 3,5 (výchozí)|

## <a name="next-steps"></a>Další kroky
[Bash v rychlém startu Cloud Shell](quickstart.md) <br>
[PowerShell v Cloud Shell rychlý Start](quickstart-powershell.md) <br>
[Další informace o Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Informace o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
