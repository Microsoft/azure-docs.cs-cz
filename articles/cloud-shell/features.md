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
ms.openlocfilehash: a052364b06ac1b9b30cef76db10a79e8ed85b9a3
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89470149"
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
Soubory mimo `$HOME` počítač a stav počítače nejsou v relacích trvalé. Používejte osvědčené postupy při ukládání tajných kódů, jako jsou klíče SSH. Pro služby, jako [je Azure Key Vault, jsou k dispozici kurzy pro instalaci](../key-vault/general/manage-with-cli2.md#prerequisites).

[Přečtěte si další informace o trvalém ukládání souborů v Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Jednotka Azure (Azure:)

PowerShell v Cloud Shell poskytuje jednotku Azure ( `Azure:` ). Můžete přepnout na jednotku Azure s `cd Azure:` a zpátky do svého domovského adresáře pomocí `cd  ~` .
Jednotka Azure umožňuje snadné zjišťování a navigaci prostředků Azure, jako jsou výpočetní prostředky, síť, úložiště atd., podobně jako navigace systému souborů.
Ke správě těchto prostředků můžete dál používat známé [Azure PowerShell rutiny](/powershell/azure) bez ohledu na jednotku, ve které jste.
Jakékoli změny provedené v prostředcích Azure provedené přímo v Azure Portal nebo prostřednictvím rutin Azure PowerShell se projeví na jednotce Azure.  Můžete spustit `dir -Force` pro aktualizaci prostředků.

![Snímek obrazovky se spuštěným Azure Cloud Shell a seznamem prostředků adresáře.](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Správa Exchange Online

Prostředí PowerShell v Cloud Shell obsahuje soukromé sestavení modulu Exchange Online.  Spusťte příkaz `Connect-EXOPSSession` a získejte rutiny Exchange.

![Snímek obrazovky Azure Cloud Shell s příkazy Connect-EXOPSSession a Get-User](media/features-powershell/exchangeonline.png)

 Spuštěním příkazu `Get-Command -Module tmp_*`
> [!NOTE]
> Název modulu by měl začínat `tmp_` , pokud máte moduly se stejnou předponou, jejich rutiny budou také Surface. 

![Snímek obrazovky Azure Cloud Shell s příkazem Get-Command-Module tmp_ *](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Hluboká integrace s open source nástroji

Cloud Shell zahrnuje předem nakonfigurované ověřování pro open source nástroje, jako jsou Terraformu, Ansible a INSPEC. Vyzkoušejte si to z ukázkových návodů.

## <a name="tools"></a>Nástroje

|Kategorie   |Name   |
|---|---|
|Nástroje pro Linux            |bash<br> zsh<br> SH<br> tmux<br> dig<br>               |
|Nástroje Azure            |[Azure CLI](https://github.com/Azure/azure-cli) a [Azure Classic CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](../storage/common/storage-use-azcopy-v10.md)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](../service-fabric/service-fabric-cli.md)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editory textu           |kód (Editor Cloud Shell)<br> vim<br> nano<br> emacs    |
|Správa zdrojového kódu         |git                    |
|Nástroje pro sestavení            |make<br> maven<br> npm<br> PIP         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU DC/OS](https://github.com/dcos/dcos-cli)         |
|Databáze              |Klient MySQL<br> Klient PostgreSql<br> [Nástroj sqlcmd](/sql/tools/sqlcmd-utility)<br> [skript MSSQL](https://github.com/Microsoft/sql-xplat-cli) |
|Jiné                  |Klient iPython<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Nespec](https://www.chef.io/inspec/)<br> [Puppet](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Pack](https://www.packer.io/)<br> [Sada Office 365 CLI](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>Podpora jazyků

|Jazyk   |Verze   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Přejít         |1,9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 a 3,5 (výchozí)|

## <a name="next-steps"></a>Další kroky
[Bash v rychlém startu Cloud Shell](quickstart.md) <br>
[PowerShell v Cloud Shell rychlý Start](quickstart-powershell.md) <br>
[Další informace o Azure CLI](/cli/azure/) <br>
[Informace o Azure PowerShell](/powershell/azure/) <br>