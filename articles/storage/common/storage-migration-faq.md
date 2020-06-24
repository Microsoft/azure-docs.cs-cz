---
title: Nejčastější dotazy k migraci Azure Storage | Microsoft Docs
description: Odpovědi na běžné otázky týkající se migrace Azure Storage
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 1d8275d11b845df43238dce82beabe89d6464799
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84944691"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Nejčastější dotazy ohledně migrace služby Azure Storage

Tento článek obsahuje odpovědi na běžné otázky týkající se migrace Azure Storage.

## <a name="copy-upload-or-download"></a>Kopírování, nahrávání nebo stahování

**Návody vytvořit skript pro kopírování souborů z jednoho kontejneru do druhého?**

Chcete-li kopírovat soubory mezi kontejnery, můžete použít AzCopy. Prohlédněte si následující příklad:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy pomocí [rozhraní API kopírování objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) kopíruje jednotlivé soubory v kontejneru.  

Ke spuštění AzCopy můžete použít libovolný virtuální počítač nebo místní počítač, který má přístup k Internetu. Můžete také použít plán Azure Batch k automatickému provedení, ale je jednodušší.  

Skript automatizace je navržený pro Azure Resource Manager nasazení místo manipulace s obsahem úložiště. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

**Platí se za kopírování dat mezi dvěma sdílenými složkami na stejném účtu úložiště ve stejné oblasti.**

Ne. Za tento proces se neúčtují žádné poplatky.

**Jak můžu stáhnout 1-2 TB dat z Azure Portal?**

Pomocí AzCopy si stáhněte data. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Jak můžu stáhnout virtuální pevný disk do místního počítače, a to jinak než pomocí možnosti stažení na portálu?**

K stažení VHD můžete použít [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) .

**Návody stahovat data do počítače se systémem Linux z účtu služby Azure Storage nebo nahrávat data z počítače se systémem Linux?**

Můžete použít rozhraní příkazového řádku Azure.

- Stažení jednoho objektu BLOB:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Nahrát jeden objekt BLOB:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Návody migrujete objekty BLOB z jednoho účtu úložiště do jiného?**

 Můžete to provést pomocí našeho [skriptu pro migraci objektů BLOB](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migrace nebo zálohování

**Návody přesunout data z jednoho kontejneru úložiště do jiného?**

Postupujte následovně:

1.  Vytvořte kontejner (složka) v cílovém objektu BLOB.

2.  Pomocí [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) zkopírujte obsah z původního kontejneru objektů blob do jiného kontejneru objektů BLOB.

**Návody vytvořit PowerShellový skript pro přesun dat z jedné sdílené složky Azure do druhé v Azure Storage?**

Pomocí AzCopy můžete přesunout data z jedné sdílené složky Azure do druhé v Azure Storage. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Návody do Azure Storage nahrávat velké soubory. csv?**

Použijte AzCopy k nahrání velkých souborů. CSV do Azure Storage. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Je potřeba, abyste každý den přesunuli protokoly z jednotky D na můj účet úložiště Azure. Návody automatizaci?**

Můžete použít AzCopy a vytvořit úlohu v Plánovač úloh. Nahrajte soubory do účtu služby Azure Storage pomocí dávkového skriptu AzCopy. Další informace najdete v tématu [Konfigurace a spouštění úloh po spuštění pro cloudovou službu](../../cloud-services/cloud-services-startup-tasks.md).

**Návody přesunete svůj účet úložiště mezi předplatnými?**

Pomocí AzCopy můžete přesunout svůj účet úložiště mezi předplatnými. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Jak můžu přesunout asi 10 TB dat do úložiště v jiné oblasti?**

Data přesuňte pomocí AzCopy. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Jak můžu kopírovat data z místního prostředí do Azure Storage?**

Data zkopírujte pomocí AzCopy. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Jak můžu přesunout data z místního prostředí do služby soubory Azure?**

Přesun dat pomocí AzCopy. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Návody přesunout spravované disky do jiného účtu úložiště?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Postupujte následovně:

1.  Zastavte virtuální počítač, ke kterému je připojen spravovaný disk.

2.  Zkopírujte virtuální pevný disk spravovaného disku z jedné oblasti do druhé spuštěním následujícího skriptu Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Pomocí souboru VHD v jiné oblasti, do které jste zkopírovali VHD, vytvořte spravovaný disk. Chcete-li to provést, spusťte následující skript Azure PowerShell:  

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

Další informace o tom, jak nasadit virtuální počítač ze spravovaného disku, najdete v tématu [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Návody přesunout nebo stáhnout data z účtu úložiště?**

Pomocí AzCopy si stáhněte data. Další informace najdete v tématech [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Návody přesunete z účtu Premium Storage na účet úložiště úrovně Standard?**

Postupujte následovně:

1.  Vytvořte účet úložiště úrovně Standard. (Nebo použijte existující účet standardního úložiště v předplatném.)

2.  Stáhněte si AzCopy. Spusťte jeden z následujících příkazů AzCopy.

    Kopírování celých disků v účtu úložiště:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Chcete-li kopírovat pouze jeden disk, zadejte název disku ve **vzoru**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

Dokončení operace může trvat několik hodin.

Pokud se chcete ujistit, že se přenos úspěšně dokončil, prověřte cílový kontejner účtu úložiště v Azure Portal. Po zkopírování disků do standardního účtu úložiště je můžete připojit k virtuálnímu počítači jako stávající disk. Další informace najdete v tématu [Postup připojení spravovaného datového disku k virtuálnímu počítači s Windows v Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Návody přesunete z klasického účtu úložiště na účet úložiště Azure Resource Manager?**

Můžete použít rutinu **Move-AzureStorageAccount** . Tato rutina má více kroků (ověření, příprava, potvrzení). Přesun můžete ověřit před tím, než ho uděláte.

Pokud máte virtuální počítače, musíte před migrací dat účtu úložiště provést další kroky. Další informace najdete v tématu [migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Návody zálohovat celý účet úložiště na jiný účet úložiště?**

Neexistuje možnost zálohovat celý účet úložiště přímo. Kontejner v tomto účtu úložiště ale můžete ručně přesunout do jiného účtu pomocí AzCopy nebo Průzkumník služby Storage. Následující kroky ukazují, jak pomocí AzCopy přesunout kontejner:  

1.  Nainstalujte nástroj příkazového řádku [AzCopy](storage-use-azcopy.md) . Tento nástroj vám pomůže přesunout soubor VHD mezi účty úložiště.

2.  Po instalaci AzCopy ve Windows pomocí instalačního programu otevřete okno příkazového řádku a přejděte do složky pro instalaci AzCopy na vašem počítači. Ve výchozím nastavení je AzCopy nainstalován do **% ProgramFiles (x86)% \ Microsoft SDKs\Azure\AzCopy** nebo **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Spusťte následující příkaz pro přesunutí kontejneru. Je nutné nahradit text skutečnými hodnotami.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Zadejte identifikátor URI pro zdrojový účet úložiště (až do kontejneru).  
    - `/Dest`: Zadejte identifikátor URI pro cílový účet úložiště (až do kontejneru).  
    - `/SourceKey`: Zadejte primární klíč pro zdrojový účet úložiště. Tento klíč můžete zkopírovat z Azure Portal tím, že vyberete účet úložiště.  
    - `/DestKey`: Zadejte primární klíč pro cílový účet úložiště. Tento klíč můžete zkopírovat z portálu tak, že vyberete účet úložiště.

Po spuštění tohoto příkazu se soubory kontejneru přesunou do cílového účtu úložiště.

> [!NOTE]
> AzCopy CLI při kopírování z jednoho objektu blob Azure do jiného nefunguje společně s přepínačem **vzoru** .
>
> Můžete přímo zkopírovat a upravit příkaz AzCopy a křížové kontroly, abyste se ujistili, že **vzorek** odpovídá zdroji. Také se ujistěte, že jsou použity zástupné znaky **/s** . Další informace najdete v tématu [AzCopy Parameters](storage-use-azcopy.md).

**Návody zálohovat službu Azure File Storage?**

Neexistuje žádné řešení zálohování. Soubory Azure ale také podporují asynchronní kopírování. Můžete tedy zkopírovat soubory:

- Ze sdílené složky do jiné sdílené složky v rámci účtu úložiště nebo do jiného účtu úložiště.

- Ze sdílené složky do kontejneru objektů BLOB v rámci účtu úložiště nebo do jiného účtu úložiště.

Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md).

## <a name="configuration"></a>Konfigurace

**Návody změnit sekundární umístění na oblast Evropa pro účet úložiště?**

Při vytváření účtu úložiště vyberete primární oblast pro daný účet. Výběr sekundární oblasti je založen na primární oblasti a nelze ji změnit. Další informace najdete v tématu [geograficky redundantní úložiště (GRS): replikace mezi různými oblastmi pro Azure Storage](storage-redundancy.md).

**Kde mohu získat další informace o šifrování Azure Storage?**  

Viz následující články:

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Použití klíčů spravovaných zákazníkem se Azure Key Vault ke správě šifrování Azure Storage](encryption-customer-managed-keys.md)
- [Zadání šifrovacího klíče pro požadavek na úložiště objektů BLOB (Preview)](encryption-customer-provided-keys.md)

**Návody šifrovat data v účtu úložiště?**

Po povolení šifrování v účtu úložiště nebudou existující data zašifrovaná. Chcete-li zašifrovat existující data, je nutné ji znovu odeslat do účtu úložiště.

Pomocí AzCopy zkopírujte data do jiného účtu úložiště a přemístěte data zpátky. Můžete také použít [šifrování v klidovém](storage-service-encryption.md)provozu.

**Existují nějaké předpoklady pro změnu replikace účtu úložiště z geograficky redundantního úložiště na místně redundantní úložiště?**

Ne.

**Návody převést na Premium Storage Azure pro sdílenou složku?**

Premium Storage se ve sdílené složce Azure nepovoluje.

**Návody upgradovat z účtu služby Storage úrovně Standard na účet Premium Storage? Návody downgrade z účtu Premium Storage na účet úložiště úrovně Standard?**

Musíte vytvořit cílový účet úložiště, zkopírovat data ze zdrojového účtu do cílového účtu a pak zdrojový účet odstranit. K kopírování dat můžete použít nástroj, jako je AzCopy.

Pokud máte virtuální počítače, musíte před migrací dat účtu úložiště provést další kroky. Další informace najdete v tématu [migrace do Azure Premium Storage (nespravované disky)](storage-migration-to-premium-storage.md).

**Jak můžu ostatním lidem umožnit přístup k prostředkům úložiště?**

Poskytnutí přístupu k prostředkům úložiště ostatním lidem:

- K poskytnutí přístupu k prostředku použijte token sdíleného přístupového podpisu (SAS).

- Zadejte uživatele s primárním nebo sekundárním klíčem pro účet úložiště. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](storage-account-keys-manage.md).

- Změňte zásady přístupu tak, aby povolovaly anonymní přístup. Další informace najdete v tématu [udělení oprávnění anonymních uživatelů k kontejnerům a](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)objektům blob.

**Kde je nainstalováno AzCopy?**

- Pokud k AzCopy přistupujete z příkazového řádku Microsoft Azure Storage zadejte **AzCopy**. Příkazový řádek se nainstaluje společně s AzCopy.

- Pokud jste nainstalovali 32 verzi, najdete ji tady: **% ProgramFiles (x86)% \\ Microsoft SDK \\ Azure \\ AzCopy**.

- Pokud jste nainstalovali 64 verzi, najdete ji tady: **% ProgramFiles% \\ Microsoft SDK \\ Azure \\ AzCopy**.

**Návody použít vlastní doménu HTTPS s účtem úložiště? Například, jak se dá "https: \/ /mystorageaccountname.blob.core.windows.net/images/image.gif" zobrazovat jako "https: \/ /www.contoso.com/images/image.gif"?**

TLS/SSL se v současné době nepodporuje u účtů úložiště s vlastními doménami.
Můžete ale použít vlastní domény bez HTTPS. Další informace najdete v tématu [Konfigurace vlastního názvu domény pro koncový bod služby Blob Storage](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Přístup k úložišti

**Návody namapovat na virtuálním počítači složku kontejneru?**

Použijte sdílenou složku Azure.

**Návody přistupovat k redundantnímu úložišti Azure Files?**

Pro přístup k redundantnímu úložišti se vyžaduje geograficky redundantní úložiště s přístupem pro čtení. Soubory Azure ale podporují jenom místně redundantní úložiště a standardní geograficky redundantní úložiště, které nepovoluje přístup jen pro čtení.

**Jak se dá získat přístup k datům uloženým v sekundární oblasti pro replikovaný účet úložiště (například úložiště redundantní zóny, geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení)?**

- Pokud používáte redundantní úložiště zóny nebo geograficky redundantní úložiště, nemůžete získat přístup k datům ze sekundární oblasti, pokud neinicializujete převzetí služeb při selhání této oblasti. Další informace o procesu převzetí služeb při selhání najdete v tématu [zotavení po havárii a převzetí služeb při selhání účtu úložiště](storage-disaster-recovery-guidance.md).

- Pokud používáte geograficky redundantní úložiště s přístupem pro čtení, můžete kdykoli získat přístup k datům ze sekundární oblasti. Použijte jednu z následujících metod:  

  - **AzCopy**: pro přístup k sekundárnímu koncovému bodu se připojí k názvu účtu úložiště v adrese URL **sekundární** . Příklad:  

      `https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd`

  - **Token SAS**: pro přístup k datům z koncového bodu použijte token SAS. Další informace najdete v tématu [použití sdílených přístupových podpisů](storage-sas-overview.md).

**Návody k přístupu k datům v účtu úložiště použít FTP?**

Neexistuje žádný způsob, jak získat přístup k účtu úložiště přímo pomocí FTP. Můžete ale nastavit virtuální počítač Azure a pak na virtuální počítač nainstalovat server FTP. Server FTP můžete mít uložený ve sdílené složce služby soubory Azure nebo na datovém disku, který je k dispozici pro virtuální počítač.

Pokud chcete jenom stahovat data, aniž byste museli používat Průzkumník služby Storage nebo podobnou aplikaci, možná budete moct použít token SAS. Další informace najdete v tématu [použití sdílených přístupových podpisů](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
