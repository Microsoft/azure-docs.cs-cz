---
title: Uchování souborů v prostředí Azure Cloud Shell | Dokumenty společnosti Microsoft
description: Návod, jak Azure Cloud Shell uchovává soubory.
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
ms.openlocfilehash: 37005a722d4a1962b4f6e1ddb8bb1c7a1229d28a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273286"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Uchování souborů v prostředí Azure Cloud Shell
Cloud Shell využívá úložiště souborů Azure k uchování souborů napříč relacemi. Při počátečním spuštění vás prostředí Cloud Shell vyzve k přidružení nové nebo existující sdílené složky, aby se udály soubory napříč relacemi.

> [!NOTE]
> Bash a PowerShell sdílejí stejnou sdílenou složku. K automatickému připojení v cloudovém prostředí může být přidružena pouze jedna sdílená složka.

> [!NOTE]
> Brána firewall úložiště Azure není podporovaná pro účty úložiště cloudshellu.

## <a name="create-new-storage"></a>Vytvoření nového úložiště

Když použijete základní nastavení a vyberete jenom předplatné, Cloud Shell vytvoří vaším jménem tři prostředky v podporované oblasti, která je vám nejblíže:
* Skupina prostředků: `cloud-shell-storage-<region>`
* Účet úložiště:`cs<uniqueGuid>`
* Sdílení souborů:`cs-<user>-<domain>-com-<uniqueGuid>`

![Nastavení předplatného](media/persisting-shell-storage/basic-storage.png)

Sdílená složka se `clouddrive` připojí `$Home` jako ve vašem adresáři. Jedná se o jednorázovou akci a sdílená složka se automaticky připojí v následujících relacích. 

Sdílená složka také obsahuje obrázek 5 GB, který je vytvořen `$Home` pro vás, který automaticky uchovává data ve vašem adresáři. To platí pro bash a PowerShell.

## <a name="use-existing-resources"></a>Použití existujících prostředků

Pomocí rozšířené možnosti můžete přidružit existující prostředky. Při výběru oblasti Cloud Shell musíte vybrat záložní účet úložiště, který se nachází ve stejné oblasti. Pokud je například přiřazená oblast – západní USA, musíte přidružit také sdílenou složku, která se nachází v rámci západních USA.

Po zobrazení výzvy k nastavení úložiště zobrazte další **možnosti výběrem možnosti Zobrazit upřesňující nastavení.** Filtr možností nabytého úložiště pro místně redundantní úložiště (LRS), geograficky redundantní úložiště (GRS) a účty zónově redundantního úložiště (ZRS). 

> [!NOTE]
> Použití účtů úložiště GRS nebo ZRS se doporučuje pro další odolnost pro záložní sdílenou složku. Jaký typ redundance závisí na vašich cílech a cenových preferencích. [Přečtěte si další informace o možnostech replikace pro účty Azure Storage](../storage/common/storage-redundancy.md).

![Nastavení skupiny prostředků](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Zabezpečení přístupu k úložišti
Z bezpečnostních důvodů by měl každý uživatel zřídit svůj vlastní účet úložiště.  Pro řízení přístupu na základě rolí (RBAC) musí mít uživatelé přístup přispěvatele nebo vyšší na úrovni účtu úložiště.

Cloud Shell používá Azure File Share v účtu úložiště, uvnitř zadaného předplatného. Z důvodu zděděných oprávnění budou mít uživatelé s dostatečnými přístupovými právy k předplatnému přístup ke všem účtům úložiště a sdíleným složkám obsaženým v předplatném.

Uživatelé by měli uzamknout přístup ke svým souborům nastavením oprávnění na úrovni úložiště nebo na úrovni předplatného.

## <a name="supported-storage-regions"></a>Podporované oblasti úložiště
Chcete-li najít aktuální `env` oblast, můžete spustit `ACC_LOCATION`v Bash a `$env:ACC_LOCATION`vyhledejte proměnnou nebo z prostředí PowerShell spustit . Sdílené složky obdrží obrázek o velikosti `$Home` 5 GB vytvořený pro zachování adresáře.

Cloud Shell počítače existují v následujících oblastech:

|Oblast|Region (Oblast)|
|---|---|
|Amerika|Východní USA, Střední jižní USA, Západní USA|
|Evropa|Severní Evropa, Západní Evropa|
|Asie a Tichomoří|Indie Střední, Jihovýchodní Asie|

Zákazníci by měli zvolit primární oblast, pokud nemají požadavek, aby jejich data v klidovém stavu byla uložena v určité oblasti. Pokud mají takový požadavek, měla by být použita sekundární oblast skladování.

### <a name="secondary-storage-regions"></a>Sekundární oblasti úložiště
Pokud se používá sekundární oblast úložiště, přidružený účet úložiště Azure se nachází v jiné oblasti jako počítač Cloud Shell, do kterého je připojujete. Jana může například nastavit, aby se její účet úložiště nacházel v kanadské východní oblasti, což je sekundární oblast, ale počítač, ke které je připojena, se stále nachází v primární oblasti. Její data v klidovém stavu se nacházejí v Kanadě, ale jsou zpracovávána ve Spojených státech.

> [!NOTE]
> Pokud se používá sekundární oblast, přístup k souborům a spuštění pro Cloud Shell může být pomalejší.

Uživatel může `(Get-CloudDrive | Get-AzStorageAccount).Location` spustit v prostředí PowerShell a zobrazit umístění jejich sdílení souborů.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Omezení vytváření prostředků pomocí zásad prostředků Azure
Účty úložiště, které vytvoříte v `ms-resource-usage:azure-cloud-shell`prostředí Cloud Shell, jsou označeny . Pokud chcete zakázat uživatelům vytvářet účty úložiště v Cloud Shellu, vytvořte [zásady prostředků Azure pro značky,](../azure-policy/json-samples.md) které jsou aktivované touto konkrétní značkou.

## <a name="how-cloud-shell-storage-works"></a>Jak funguje úložiště cloudového prostředí 
Cloud Shell uchovává soubory prostřednictvím obou následujících metod: 
* Vytvoření bitové kopie `$Home` disku adresáře, aby byl zachován veškerý obsah v adresáři. Bitová kopie disku je uložena `acc_<User>.img` `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`ve sdílené složce zadaného souboru jako na obrázku a automaticky synchronizuje změny. 
* Připojení zadané sdílené `clouddrive` složky `$Home` jako v adresáři pro přímou interakci sdílení souborů. `/Home/<User>/clouddrive`je mapován na `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Všechny soubory `$Home` v adresáři, například klíče SSH, jsou trvalé v bitové kopii disku uživatele, která je uložena v připojené sdílené složce. Doporučené postupy použijte při zachování `$Home` informací v adresáři a připojené sdílené složce.

## <a name="clouddrive-commands"></a>clouddrive příkazy

### <a name="use-the-clouddrive-command"></a>Použití `clouddrive` příkazu
V prostředí Cloud Shell můžete `clouddrive`spustit příkaz s názvem , který umožňuje ručně aktualizovat sdílenou složku, která je připojena ke cloudovému prostředí.

![Spuštění příkazu "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Seznamu`clouddrive`
Chcete-li zjistit, která `clouddrive`sdílená `df` složka je připojena jako , spusťte příkaz. 

Cesta k souboru na clouddrive zobrazuje název účtu úložiště a sdílenou složku v adrese URL. Například `//storageaccountname.file.core.windows.net/filesharename`.

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

### <a name="mount-a-new-clouddrive"></a>Připojení nového clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Předpoklady pro ruční montáž
Pomocí `clouddrive mount` příkazu můžete aktualizovat sdílenou složku, která je přidružená k prostředí Cloud Shell.

Pokud připojíte existující sdílenou složku, účty úložiště musí být umístěny ve vybrané oblasti Cloud Shell. Načíst umístění `env` spuštěním a `ACC_LOCATION`kontrolou .

#### <a name="the-clouddrive-mount-command"></a>Příkaz `clouddrive mount`

> [!NOTE]
> Pokud připojujete novou sdílenou složku, vytvoří `$Home` se pro váš adresář nová bitová kopie uživatele. Předchozí `$Home` obrázek se uchovává ve sdílené složce předchozího souboru.

Spusťte `clouddrive mount` příkaz s následujícími parametry:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Chcete-li zobrazit `clouddrive mount -h`další podrobnosti, spusťte , jak je znázorněno zde:

![Spuštění příkazu 'clouddrive mount'](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odpojit clouddrive
Sdílenou složku, která je připojena ke cloudovému prostředí, můžete kdykoli odpojit. Vzhledem k tomu, že prostředí Cloud Shell vyžaduje použití připojené sdílené složky, budete vyzváni k vytvoření a připojení další sdílené složky v další relaci.

1. Spusťte `clouddrive unmount`.
2. Potvrďte a potvrďte výzvy.

Sdílená složka bude existovat i nadále, pokud ji ručně neodstraníte. Cloud Shell již nebude hledat tuto sdílenou složku v následujících relacích. Chcete-li zobrazit `clouddrive unmount -h`další podrobnosti, spusťte , jak je znázorněno zde:

![Spuštění příkazu 'clouddrive unmount'](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Přestože spuštění tohoto příkazu neodstraní žádné prostředky, ruční odstranění skupiny prostředků, účtu úložiště nebo sdílené složky, která je namapována na prostředí Cloud Shell, vymaže bitovou kopii `$Home` disku adresáře a všechny soubory ve sdílené složce. Tuto akci nelze vrátit zpět.
## <a name="powershell-specific-commands"></a>Příkazy specifické pro Prostředí PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Seznam `clouddrive` sdílených složek Azure
Rutina `Get-CloudDrive` načte informace o sdílené složce Azure `clouddrive` aktuálně připojené v cloudovém prostředí. <br>
![Spuštění Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odpojit`clouddrive`
Sdílenou složku Azure, která je připojená ke Cloud Shellu, můžete kdykoli odpojit. Pokud byla sdílená složka Azure odebrána, budete při příští relaci vyzváni k vytvoření a připojení nové sdílené složky Azure.

Rutina `Dismount-CloudDrive` odpojí sdílenou složku Azure z aktuálního účtu úložiště. Demontáž `clouddrive` ukončí aktuální relaci. Uživatel bude vyzván k vytvoření a připojení nové sdílené složky Azure během další relace.
![Spuštění demontáže-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Poznámka: Pokud potřebujete definovat funkci v souboru a volat ji z rutin prostředí PowerShell, musí být zahrnut operátor tečky. Například: . .\MyFunctions.ps1

## <a name="next-steps"></a>Další kroky
[Rychlý start prostředí cloud](quickstart.md) <br>
[Informace o úložišti Souborů Microsoft Azure](../storage/files/storage-files-introduction.md) <br>
[Informace o značkách úložiště](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
