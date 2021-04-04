---
title: Zachovat soubory v Azure Cloud Shell | Microsoft Docs
description: Návod, jak Azure Cloud Shell přetrvává soubory
services: azure
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
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: f1846c126e81ca5851cfbb1d782e5315ae10a82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152269"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Zachovat soubory v Azure Cloud Shell
Cloud Shell využívá službu Azure File Storage k uchování souborů napříč relacemi. Při počátečním spuštění vás Cloud Shell vyzve k přidružení nové nebo existující sdílené složky, která bude uchovávat soubory napříč relacemi.

> [!NOTE]
> Bash a prostředí PowerShell sdílí stejnou sdílenou složku. K automatickému připojení v Cloud Shell může být přidružena pouze jedna sdílená složka.

> [!NOTE]
> Brána firewall služby Azure Storage není podporovaná pro účty úložiště Cloud Shell.

## <a name="create-new-storage"></a>Vytvořit nové úložiště

Když použijete základní nastavení a vyberete jenom předplatné, Cloud Shell v rámci podporované oblasti, která je nejbližší, vytvoří tři prostředky za vás:
* Skupina prostředků: `cloud-shell-storage-<region>`
* Účet úložiště: `cs<uniqueGuid>`
* Sdílená složka: `cs-<user>-<domain>-com-<uniqueGuid>`

![Nastavení předplatného](media/persisting-shell-storage/basic-storage.png)

Sdílená složka se připojí jako `clouddrive` ve vašem `$Home` adresáři. Jedná se o jednorázovou akci a sdílená složka se v následných relacích automaticky připojí. 

Sdílená složka také obsahuje 5 GB image, která se vytvoří pro vás, která automaticky uchovává data v `$Home` adresáři. To platí pro bash i PowerShell.

## <a name="use-existing-resources"></a>Použití existujících prostředků

Pomocí možnosti Upřesnit můžete přidružit stávající prostředky. Při výběru Cloud Shell oblasti musíte vybrat záložní účet úložiště společně umístěný ve stejné oblasti. Pokud je vaše přiřazená oblast například Západní USA pak musíte přidružit sdílenou složku, která se nachází v rámci Západní USA také.

Když se zobrazí výzva k nastavení úložiště, vyberte **Zobrazit upřesňující nastavení** a zobrazte další možnosti. Vyplněné možnosti úložiště filtr pro místně redundantní úložiště (LRS), geograficky redundantní úložiště (GRS) a účty redundantního úložiště (ZRS) zóny. 

> [!NOTE]
> Použití účtů úložiště GRS nebo ZRS se doporučuje pro zajištění vyšší odolnosti pro vaše záložní sdílení souborů. Typ redundance závisí na vašich cílech a cenové preference. [Přečtěte si další informace o možnostech replikace pro účty Azure Storage](../storage/common/storage-redundancy.md).

![Nastavení skupiny prostředků](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Zabezpečení přístupu k úložišti
V případě zabezpečení by měl každý uživatel zřídit vlastní účet úložiště.  Pro řízení přístupu na základě role Azure (Azure RBAC) musí mít uživatelé oprávnění Přispěvatel nebo vyšší na úrovni účtu úložiště.

Cloud Shell používá ke sdílení souborů Azure v účtu úložiště v rámci zadaného předplatného. Kvůli zděděným oprávněním budou mít uživatelé s dostatečným oprávněním pro přístup k předplatnému přístup ke všem účtům úložiště a sdíleným složkám obsaženým v předplatném.

Uživatelé by měli uzamknout přístup ke svým souborům nastavením oprávnění v účtu úložiště nebo na úrovni předplatného.

## <a name="supported-storage-regions"></a>Podporované oblasti úložiště
Pokud chcete zjistit aktuální oblast, kterou můžete spustit `env` v bash, najděte proměnnou `ACC_LOCATION` nebo spusťte prostředí PowerShell `$env:ACC_LOCATION` . Sdílené složky obdrží obrázek o velikosti 5 GB, který jste vytvořili, abyste zachovali svůj `$Home` adresář.

Cloud Shell počítače existují v následujících oblastech:

|Plošný|Oblast|
|---|---|
|Amerika|Východní USA, Střed USA – jih Západní USA|
|Evropa|Severní Evropa, Západní Evropa|
|Asie a Tichomoří|Indie – střed, jihovýchodní Asie|

Zákazníci by si měli zvolit primární oblast, pokud nemají požadavek na uložení uložených dat v určité oblasti. Pokud mají takový požadavek, měla by se použít oblast sekundárního úložiště.

### <a name="secondary-storage-regions"></a>Sekundární oblasti úložiště
Pokud se používá sekundární oblast úložiště, přidružený účet služby Azure Storage se nachází v jiné oblasti jako Cloud Shell počítač, ke kterému je připojujete. Jana může například nastavit účet úložiště tak, aby byl umístěný v oblasti Kanada – východ, Sekundární oblast, ale počítač, ke kterému je připojený, je pořád umístěný v primární oblasti. Jeho data v klidovém umístění jsou umístěna v Kanadě, ale jsou zpracována v USA.

> [!NOTE]
> Při použití sekundární oblasti může být přístup k souborům a čas spuštění pro Cloud Shell pomalejší.

Uživatel může spustit `(Get-CloudDrive | Get-AzStorageAccount).Location` prostředí PowerShell, aby se zobrazilo umístění své sdílené složky.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Omezení vytváření prostředků pomocí zásad prostředků Azure
Účty úložiště, které vytvoříte v Cloud Shell jsou označené jako `ms-resource-usage:azure-cloud-shell` . Pokud chcete uživatelům zakázat vytváření účtů úložiště v Cloud Shell, vytvořte [zásady prostředků Azure pro značky](../governance/policy/samples/index.md) , které se spouštějí touto konkrétní značkou.

## <a name="how-cloud-shell-storage-works"></a>Jak funguje úložiště Cloud Shell 
Cloud Shell uchovává soubory prostřednictvím obou následujících metod: 
* Vytvořením bitové kopie disku v `$Home` adresáři zachovejte veškerý obsah v adresáři. Bitová kopie disku je uložena v zadané sdílené složce jako `acc_<User>.img` at `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` a automaticky synchronizuje změny. 
* Vaše Zadaná sdílená složka se připojuje jako `clouddrive` v `$Home` adresáři pro přímou interakci sdílení souborů. `/Home/<User>/clouddrive` je namapována na `fileshare.storage.windows.net/fileshare` .
 
> [!NOTE]
> Všechny soubory ve vašem `$Home` adresáři, jako jsou klíče SSH, jsou trvalé na uživatelském disku, který je uložený v připojené sdílené složce souborů. Pokud uchováváte informace ve vašem `$Home` adresáři a připojené sdílené složce, použijte doporučené postupy.

## <a name="clouddrive-commands"></a>příkazy clouddrive

### <a name="use-the-clouddrive-command"></a>Použití `clouddrive` příkazu
V Cloud Shell můžete spustit příkaz s názvem `clouddrive` , který umožňuje ručně aktualizovat sdílenou složku, která je připojená k Cloud Shell.

![Spuštění příkazu "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Seznamu `clouddrive`
Pokud chcete zjistit, která sdílená složka je připojená `clouddrive` , spusťte `df` příkaz. 

Cesta k souboru clouddrive zobrazuje název vašeho účtu úložiště a sdílenou složku v adrese URL. Například `//storageaccountname.file.core.windows.net/filesharename`.

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Připojit nový clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Předpoklady ručního připojení
Sdílenou složku, která je přidružená k Cloud Shell, můžete aktualizovat pomocí `clouddrive mount` příkazu.

Pokud připojíte existující sdílenou složku, musí být účty úložiště umístěny ve vaší vybrané Cloud Shell oblasti. Načtěte umístění spuštěním `env` a kontrolou `ACC_LOCATION` .

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount`Příkaz

> [!NOTE]
> Pokud připojujete novou sdílenou složku, vytvoří se pro váš adresář nová uživatelská image `$Home` . Předchozí `$Home` Obrázek je uložený v předchozí sdílené složce.

Spusťte `clouddrive mount` příkaz s následujícími parametry:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Pokud chcete zobrazit další podrobnosti, spusťte `clouddrive mount -h` , jak je znázorněno zde:

![Spuštění clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odpojit clouddrive
Sdílenou složku, která je připojená k Cloud Shell, můžete kdykoli odpojit. Vzhledem k tomu, že Cloud Shell vyžaduje, aby se použila připojená sdílená složka, budete při další relaci vyzváni k vytvoření a připojení další sdílené složky.

1. Spusťte `clouddrive unmount`.
2. Potvrďte a potvrďte výzvy.

Sdílená složka bude i nadále existovat, dokud ji neodstraníte ručně. Cloud Shell už po dalších relacích tuto sdílenou složku nebude hledat. Pokud chcete zobrazit další podrobnosti, spusťte `clouddrive unmount -h` , jak je znázorněno zde:

![Spuštění clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> I když se spustí tento příkaz, neodstraní žádné prostředky, ručně odstraní skupinu prostředků, účet úložiště nebo sdílenou složku, která je namapovaná na Cloud Shell vymaže vaši `$Home` image disku adresáře a všechny soubory ve sdílené složce. Tuto akci nejde vrátit zpátky.
## <a name="powershell-specific-commands"></a>Příkazy specifické pro PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Vypsat `clouddrive` sdílené složky Azure
`Get-CloudDrive`Rutina načte informace o sdílené složce Azure, které jsou aktuálně připojené `clouddrive` v Cloud Shell. <br>
![Spuštění Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odpojte `clouddrive`
Sdílenou složku Azure, která je připojená k Cloud Shell, můžete kdykoli odpojit. Pokud se sdílená složka Azure odebrala, budete při další relaci vyzváni k vytvoření nové sdílené složky Azure a připojení k ní.

`Dismount-CloudDrive`Rutina odpojí sdílenou složku Azure od aktuálního účtu úložiště. Odpojení `clouddrive` ukončí aktuální relaci. Uživatel bude při další relaci vyzván k vytvoření a připojení nové sdílené složky Azure.
![Spuštění odpojení – CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Poznámka: Pokud potřebujete definovat funkci v souboru a zavolat ji z rutin PowerShellu, musí být zahrnut operátor tečka. Například:. .\MyFunctions.ps1

## <a name="next-steps"></a>Další kroky
[Rychlý Start Cloud Shell](quickstart.md) <br>
[Informace o úložišti Microsoft Azurech souborů](../storage/files/storage-files-introduction.md) <br>
[Další informace o značkách úložiště](../azure-resource-manager/management/tag-resources.md) <br>