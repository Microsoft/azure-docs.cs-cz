---
title: Nejčastější dotazy k migraci úložiště Azure | Dokumenty společnosti Microsoft
description: Odpovědi na běžné otázky týkající se migrace Azure Storage
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 39f1137638f9cd4926b712bdd18e681d90adcdc4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668556"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Nejčastější dotazy ohledně migrace služby Azure Storage

Tento článek odpovídá na běžné otázky týkající se migrace azure storage.

## <a name="copy-upload-or-download"></a>Kopírování, nahrávání nebo stahování

**Jak vytvořím skript pro kopírování souborů z jednoho kontejneru do druhého?**

Chcete-li kopírovat soubory mezi kontejnery, můžete použít AzCopy. Prohlédněte si následující příklad:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy používá [kopírovat rozhraní BLOB API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) ke kopírování každého souboru v kontejneru.  

Ke spuštění AzCopy můžete použít libovolný virtuální počítač nebo místní počítač, který má přístup k internetu. Můžete také použít plán Azure Batch k tomu automaticky, ale je to složitější.  

Skript automatizace je navržený pro nasazení Azure Resource Manager namísto manipulace s obsahem úložiště. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md).

**Existuje nějaký poplatek za kopírování dat mezi dvěma sdílenými složkami souborů na stejném účtu úložiště ve stejné oblasti?**

Ne. Za tento proces se neplatí žádný poplatek.

**Jak si můžu stáhnout 1–2 TB dat z webu Azure?**

Použijte AzCopy ke stažení dat. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak lze stáhnout virtuální pevný disk do místního počítače, s tím jiným než pomocí možnosti stažení na portálu?**

[Pomocí Průzkumníka úložiště](https://azure.microsoft.com/features/storage-explorer/) můžete stáhnout virtuální pevný disk.

**Jak můžu stáhnout data do počítače se systémem Linux z účtu úložiště Azure nebo nahrát data z počítače s Linuxem?**

Můžete použít azure cli.

- Stáhněte si jeden objekt blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Nahrát jeden objekt blob:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Jak můžu migrovat objekty BLOB z jednoho účtu úložiště do jiného?**

 Můžete to udělat pomocí našeho [skriptu migrace objektů Blob](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migrace nebo zálohování

**Jak se přesunou ta data z jednoho kontejneru úložiště do jiného?**

Postupujte následovně:

1.  Vytvořte kontejner (složku) v cílovém objektu blob.

2.  Pomocí [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) zkopírujte obsah z původního kontejneru objektů blob do jiného kontejneru objektů blob.

**Jak vytvořím skript PowerShellu pro přesunutí dat z jedné sdílené složky Azure do jiné ho ve Službě Azure?**

Pomocí AzCopy můžete přesunout data z jedné sdílené složky Azure do jiné ve službě Azure Storage. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak nahraju velké soubory .csv do Azure Storage?**

Pomocí AzCopy můžete do Služby Azure Storage nahrát velké soubory .csv. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Musím přesunout protokoly z jednotky D na můj účet úložiště Azure každý den. Jak to mohu automatizovat?**

Můžete použít AzCopy a vytvořit úkol v Plánovač úloh. Nahrajte soubory do účtu úložiště Azure pomocí dávkového skriptu AzCopy. Další informace naleznete v tématu [Konfigurace a spuštění úloh při spuštění pro cloudovou službu](../../cloud-services/cloud-services-startup-tasks.md).

**Jak můžu přesunout svůj účet úložiště mezi předplatnými?**

Pomocí AzCopy můžete přesunout účet úložiště mezi předplatnými. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak lze přesunout přibližně 10 TB dat do úložiště v jiné oblasti?**

K přesunutí dat použijte AzCopy. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak můžu zkopírovat data z místního do Azure Storage?**

Ke kopírování dat použijte AzCopy. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak můžu přesunout data z místního do Azure Files?**

K přesouvání dat použijte AzCopy. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak můžu přesunout spravované disky do jiného účtu úložiště?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Postupujte následovně:

1.  Zastavte virtuální počítač, ke kterému je spravovaný disk připojen.

2.  Zkopírujte virtuální pevný disk spravovaného disku z jedné oblasti do druhé spuštěním následujícího skriptu Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Vytvořte spravovaný disk pomocí souboru Virtuálního pevného disku v jiné oblasti, do které jste zkopírovali virtuální pevný disk. Chcete-li to provést, spusťte následující skript Azure PowerShellu:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

Další informace o nasazení virtuálního počítače ze spravovaného disku naleznete v tématu [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Jak můžu přesunout nebo stáhnout data z účtu úložiště?**

Použijte AzCopy ke stažení dat. Další informace naleznete v [tématu Přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak se přestěhuji z účtu úložiště s prémiovým úložištěm na účet standardního úložiště?**

Postupujte následovně:

1.  Vytvořte si standardní účet úložiště. (Nebo v předplatném použijte existující účet standardního úložiště.)

2.  Stáhnout AzCopy. Spusťte jeden z následujících příkazů AzCopy.

    Kopírování celých disků v účtu úložiště:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Chcete-li zkopírovat pouze jeden disk, zadejte název disku ve **vzoru**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

Operace může trvat několik hodin.

Chcete-li se ujistit, že přenos byl úspěšně dokončen, zkontrolujte kontejner účtu cílového úložiště na webu Azure Portal. Po zkopírování disků do standardního účtu úložiště je můžete připojit k virtuálnímu počítači jako existující disk. Další informace najdete v [tématu Jak připojit spravovaný datový disk k virtuálnímu počítači Windows na webu Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Jak se přestěhuju z klasického účtu úložiště na účet úložiště Azure Resource Manager?**

Můžete použít rutinu **Move-AzureStorageAccount.** Tato rutina má několik kroků (ověřit, připravit, potvrdit). Můžete ověřit přesunout před tím, než jej provedete.

Pokud máte virtuální počítače, musíte před migrací dat účtu úložiště provést další kroky. Další informace najdete [v tématu Migrace prostředků IaaS z klasického do Správce prostředků Azure pomocí Azure PowerShellu](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Jak můžu zálohovat celý účet úložiště na jiný účet úložiště?**

Neexistuje žádná možnost zálohovat celý účet úložiště přímo. Ale můžete ručně přesunout kontejner v tomto účtu úložiště do jiného účtu pomocí AzCopy nebo Storage Explorer. Následující kroky ukazují, jak pomocí AzCopy přesunout kontejner:  

1.  Nainstalujte nástroj příkazového řádku [AzCopy.](storage-use-azcopy.md) Tento nástroj vám pomůže přesunout soubor VHD mezi účty úložiště.

2.  Po instalaci aplikace AzCopy do systému Windows pomocí instalačního programu otevřete okno příkazového řádku a přejděte do instalační složky AzCopy v počítači. Ve výchozím nastavení je azcopy nainstalována do **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** nebo **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Spusťte následující příkaz pro přesunutí kontejneru. Text je nutné nahradit skutečnými hodnotami.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Zadejte identifikátor URI pro účet zdrojového úložiště (až do kontejneru).  
    - `/Dest`: Zadejte identifikátor URI pro cílový účet úložiště (až do kontejneru).  
    - `/SourceKey`: Zadejte primární klíč pro účet zdrojového úložiště. Tento klíč můžete zkopírovat z portálu Azure výběrem účtu úložiště.  
    - `/DestKey`: Zadejte primární klíč pro cílový účet úložiště. Tento klíč můžete zkopírovat z portálu výběrem účtu úložiště.

Po spuštění tohoto příkazu jsou soubory kontejneru přesunuty do cílového účtu úložiště.

> [!NOTE]
> AzCopy CLI nefunguje společně s přepínačem **vzor** při kopírování z jednoho objektu blob Azure do jiného.
>
> Příkaz AzCopy můžete přímo kopírovat a upravovat a křížově kontrolovat, zda **se vzorek** shoduje se zdrojem. Také se ujistěte, že **/S** zástupné znaky jsou v platnosti. Další informace naleznete [v tématu AzCopy parameters](storage-use-azcopy.md).

**Jak můžu zálohovat úložiště souborů Azure?**

Neexistuje žádné řešení zálohování. Soubory Azure však také podporuje asynchronní kopírování. Takže můžete kopírovat soubory:

- Ze sdílené položky do jiné sdílené složky v rámci účtu úložiště nebo do jiného účtu úložiště.

- Ze sdílené složky do kontejneru objektů blob v rámci účtu úložiště nebo do jiného účtu úložiště.

Další informace naleznete v [tématu Přenos dat pomocí azcopy v systému Windows](storage-use-azcopy.md).
## <a name="configuration"></a>Konfigurace

**Jak změním sekundární umístění pro účet úložiště do oblasti Evropa?**

Při vytváření účtu úložiště vyberete primární oblast pro účet. Výběr sekundární oblasti je založen na primární oblasti a nelze ji změnit. Další informace najdete v [tématu Geograficky redundantní úložiště (GRS): Meziregionální replikace pro Azure Storage](storage-redundancy.md).

**Kde můžu získat další informace o šifrování služby Azure Storage Service (SSE)?**  

Viz následující články:

-  [Průvodce zabezpečením azure úložiště](../blobs/security-recommendations.md)

-  [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)

**Jak zašifruju data v účtu úložiště?**

Po povolení šifrování v účtu úložiště nejsou existující data šifrována. Chcete-li zašifrovat existující data, musíte je znovu odeslat do účtu úložiště.

Pomocí aplikace AzCopy zkopírujte data do jiného účtu úložiště a potom je přesuňte zpět. Můžete také použít [šifrování v klidovém stavu](storage-service-encryption.md).

**Existují nějaké předpoklady pro změnu replikace účtu úložiště z geograficky redundantního úložiště na místně redundantní úložiště?**

Ne.

**Jak převoz na Azure Premium Storage pro sdílenou složku?**

Úložiště Premium není povoleno ve sdílené složce Azure.

**Jak můžu upgradovat ze standardního účtu úložiště na účet úložiště s prémií? Jak přejdu z účtu úložiště premium na účet standardního úložiště?**

Musíte vytvořit cílový účet úložiště, zkopírovat data ze zdrojového účtu do cílového účtu a potom zdrojový účet odstranit. Ke kopírování dat můžete použít nástroj, například AzCopy.

Pokud máte virtuální počítače, musíte před migrací dat účtu úložiště provést další kroky. Další informace najdete [v tématu migrace do úložiště Azure Premium (nespravované disky).](storage-migration-to-premium-storage.md)

**Jak můžu ostatním poskytnout přístup k prostředkům úložiště?**

Jak ostatním lidem poskytnout přístup k prostředkům úložiště:

-   Pomocí tokenu sdíleného přístupového podpisu (SAS) můžete poskytnout přístup k prostředku.

-   Poskytněte uživateli primární nebo sekundární klíč pro účet úložiště. Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](storage-account-keys-manage.md).

-   Změňte zásady přístupu tak, aby umožňovaly anonymní přístup. Další informace naleznete v [tématu Udělení oprávnění anonymním uživatelům k kontejnerům a objektům BLOB](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Kde je nainstalovánAzCopy?**

-   Pokud přistupujete k AzCopy z příkazového řádku Úložiště Microsoft Azure, zadejte **AzCopy**. Příkazový řádek je nainstalován vedle AzCopy.

-   Pokud jste nainstalovali 32bitovou verzi, je umístěna zde: **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy**.

-   Pokud jste nainstalovali 64bitovou verzi, je umístěna zde: **%ProgramFiles%\\Microsoft\\SDKs Azure\\AzCopy**.

**Jak se používá vlastní doména HTTPS s účtem úložiště? Například, jak mohu udělat\/"https: /mystorageaccountname.blob.core.windows.net/images/image.gif"\/se zobrazí jako "https: /www.contoso.com/images/image.gif"?**

TLS/SSL není aktuálně podporována na účtech úložiště s vlastnídomény.
Můžete však použít vlastní domény bez protokolu HTTPS. Další informace naleznete [v tématu Konfigurace vlastního názvu domény pro koncový bod úložiště objektů Blob](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Přístup k úložišti

**Jak namapovat složku kontejneru na virtuálním počítači?**

Použijte sdílenou složku Azure.

**Jak se dostanu k redundantnímu úložišti Azure Files?**

Pro přístup k redundantnímu úložišti je vyžadováno geograficky redundantní úložiště pro čtení. Azure Files však podporuje jenom místně redundantní úložiště a standardní geograficky redundantní úložiště, které neumožňuje přístup jen pro čtení.

**Pro účet replikovaného úložiště (například zónově redundantní úložiště, geograficky redundantní úložiště nebo geograficky redundantní úložiště pro čtení) jak lze získat přístup k datům uloženým v sekundární oblasti?**

-   Pokud používáte zónově redundantní úložiště nebo geograficky redundantní úložiště, nemůžete získat přístup k datům ze sekundární oblasti, pokud nezahájíte převzetí služeb při selhání do této oblasti. Další informace o procesu převzetí služeb při selhání najdete v [tématu Převzetí služeb při havárii a převzetí služeb při selhání (preview) v Azure Storage](storage-disaster-recovery-guidance.md).

-   Pokud používáte geograficky redundantní úložiště pro čtení, můžete kdykoli přistupovat k datům ze sekundární oblasti. Použijte jednu z následujících metod:  

    - **AzCopy**: Připojit **-secondary** k názvu účtu úložiště v adrese URL pro přístup k sekundární koncový bod. Například:  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **Token SAS**: Použijte token SAS pro přístup k datům z koncového bodu. Další informace naleznete v tématu [Použití sdílených přístupových podpisů](storage-sas-overview.md).

**Jak se pomocí ftp používá přístup k datům, která jsou v účtu úložiště?**

Neexistuje žádný způsob, jak přistupovat k účtu úložiště přímo pomocí FTP. Můžete však nastavit virtuální počítač Azure a potom nainstalovat server FTP na virtuální mši. Můžete mít soubory úložiště serveru FTP ve sdílené složce Azure Files nebo na datovém disku, který je k dispozici pro virtuální počítač.

Pokud chcete pouze stahovat data bez nutnosti používat Průzkumníka úložiště nebo podobnou aplikaci, můžete použít token SAS. Další informace naleznete v tématu [Použití sdílených přístupových podpisů](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
