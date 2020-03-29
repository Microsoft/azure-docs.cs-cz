---
title: Funkce Azure Cloud Shell | Dokumenty společnosti Microsoft
description: Přehled funkcí ve službě Azure Cloud Shell
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
ms.openlocfilehash: 0aa71e4b78df8087093f183b146c525d2a8a0f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366236"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkce & nástrojů pro Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell `Ubuntu 16.04 LTS`běží na .

## <a name="features"></a>Funkce

### <a name="secure-automatic-authentication"></a>Zabezpečené automatické ověřování

Cloud Shell bezpečně a automaticky ověřuje přístup k účtům pro Azure CLI a Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME trvalosti napříč relacemi

Chcete-li zachovat soubory napříč relacemi, Cloud Shell vás provede připojením sdílené složky Azure při prvním spuštění.
Po dokončení cloudové prostředí automaticky připojí `$HOME\clouddrive`vaše úložiště (připojené jako) pro všechny budoucí relace.
Kromě toho `$HOME` váš adresář je trvalý jako .img ve sdílené složce Azure.
Soubory mimo `$HOME` a stav počítače nejsou trvalé napříč relacemi. Při ukládání tajných kódů, jako jsou například klíče SSH, používejte osvědčené postupy. Služby, jako [je Azure Key Vault, mají kurzy pro nastavení](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Přečtěte si další informace o uchování souborů v Cloud Shellu.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Disk Azure (Azure:)

PowerShell v Cloud Shellu vás`Azure:`spustí na disku Azure ( ).
Jednotka Azure umožňuje snadné zjišťování a navigaci prostředků Azure, jako jsou výpočetní prostředky, síť, úložiště atd., podobně jako navigace v souborovém systému.
You can continue to use the familiar [Azure PowerShell cmdlets](https://docs.microsoft.com/powershell/azure) to manage these resources regardless of the drive you are in.
Všechny změny provedené v prostředcích Azure, provedené přímo na webu Azure Portal nebo prostřednictvím rutin Azure PowerShell, se projeví na disku Azure.  Můžete spustit `dir -Force` aktualizovat prostředky.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Správa Exchange Online

Prostředí PowerShell v prostředí Cloud obsahuje privátní sestavení modulu Exchange Online.  Spusťte `Connect-EXOPSSession` a získejte rutiny serveru Exchange.

![](media/features-powershell/exchangeonline.png)

 Spusťte `Get-Command -Module tmp_*`.
> [!NOTE]
> Název modulu by `tmp_`měl začínat na , pokud jste nainstalovali moduly se stejnou předponou, jejich rutiny budou také vynořovány. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Hluboká integrace s open-source nástroji

Cloud Shell obsahuje předkonfigurované ověřování pro nástroje s otevřeným zdrojovým kódem, jako je Terraform, Ansible a Chef InSpec. Vyzkoušejte si to z příkladů návodů.

## <a name="tools"></a>Nástroje

|Kategorie   |Name (Název)   |
|---|---|
|Linuxové nástroje            |bash<br> Zsh<br> Sh<br> tmux<br> Kopat<br>               |
|Nástroje Azure            |[Azure CLI](https://github.com/Azure/azure-cli) a [Azure klasické příkazové příkazové nebo bylo však](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Textové editory           |kód (editor cloudového prostředí)<br> vim<br> nano<br> emacs    |
|Správa zdrojového kódu         |git                    |
|Vytváření nástrojů            |značka<br> maven<br> npm<br> Pip         |
|Kontejnery             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Kormidla](https://github.com/kubernetes/helm)<br> [Cli řadiče domény/os](https://github.com/dcos/dcos-cli)         |
|Databáze              |Klient MySQL<br> Klient PostgreSql<br> [sqlcmd Nástroj](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Ostatní                  |Klient iPython<br> [Cli slévárna slévárna](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Šéfkuchař InSpec](https://www.chef.io/inspec/)<br> [Loutkový šroub](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Podpora jazyků

|Jazyk   |Version   |
|---|---|
|.NET Core  |2.2.402       |
|Přejít         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 a 3.5 (výchozí)|

## <a name="next-steps"></a>Další kroky
[Bash v cloudovém prostředí Rychlý start](quickstart.md) <br>
[PowerShell v cloudovém prostředí Úvodní příručka](quickstart-powershell.md) <br>
[Další informace o azure cli](https://docs.microsoft.com/cli/azure/) <br>
[Další informace o Azure PowerShellu](https://docs.microsoft.com/powershell/azure/) <br>
