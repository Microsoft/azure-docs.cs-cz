---
title: Azure migrace úložiště – nejčastější dotazy | Microsoft Docs
description: Odpovědi na časté otázky týkající se migrace úložiště Azure
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 89d1a4767c240c7e4fedb9d7ac47d6d4fb0aa737
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Nejčastější dotazy o migraci úložiště Azure

Tento článek obsahuje odpovědi na časté otázky týkající se migrace úložiště Azure. 

## <a name="faq"></a>Nejčastější dotazy

**Vytvoření skriptu pro kopírování souborů z jednoho kontejneru typu do jiného**

Pokud chcete zkopírovat soubory mezi kontejnery, můžete pomocí nástroje AzCopy. Podívejte se na následující příklad:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

Používá AzCopy [kopírovat objekt Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) zkopírujte každý soubor v kontejneru.  
  
Můžete použít všechny virtuální počítače nebo místní počítač, který má přístup k Internetu ke spuštění nástroje AzCopy. Plánování služby Azure Batch můžete také použít k tomu automaticky, ale má složitěji.  
  
Skriptu pro automatizaci je určená pro nasazení Azure Resource Manager místo úložiště obsahu manipulaci. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Je k dispozici žádné zdarma ke kopírování dat mezi dvěma sdílenými složkami ve stejném účtu úložiště ve stejné oblasti?**

Ne. Není nijak zpoplatněn pro tento proces.

**Jak mohu zálohovat Moje celý účet úložiště na jiný účet úložiště?**

Neexistuje žádná možnost zálohovat přímo celý účet úložiště. Ale můžete ručně přesunout kontejneru v daném účtu úložiště na jiný účet pomocí AzCopy nebo Storage Explorer. Následující kroky ukazují, jak pomocí nástroje AzCopy pro přesun kontejneru:  
 

1.  Nainstalujte [AzCopy](storage-use-azcopy.md) nástroj příkazového řádku. Tento nástroj umožňuje přesunout soubor virtuálního pevného disku mezi účty úložiště.

2.  Po instalaci AzCopy v systému Windows s použitím instalační program, otevřete okno příkazového řádku a pak přejděte do složky instalace AzCopy ve vašem počítači. Ve výchozím nastavení, je nainstalován nástroj AzCopy k **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** nebo **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Spusťte následující příkaz pro přesun kontejneru. Text je potřeba nahradit skutečnými hodnotami.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Zadejte identifikátor URI pro účet úložiště zdroje (až kontejneru).  
    - `/Dest`: Zadejte identifikátor URI pro cílový účet úložiště (až kontejneru).  
    - `/SourceKey`: Obsahuje primární klíč pro účet úložiště zdroje. Tento klíč můžete zkopírovat z portálu Azure tak, že vyberete účet úložiště.  
    - `/DestKey`: Obsahuje primární klíč pro cílový účet úložiště. Tento klíč z portálu můžete zkopírovat tak, že vyberete účet úložiště.

Po spuštění tohoto příkazu kontejneru soubory přesunou do cílového účtu úložiště.

> [!NOTE]
> Rozhraní příkazového řádku AzCopy nefunguje společně s **vzor** přepínače při kopírování z jednoho Azure blob do jiného.
>
> Můžete přímo kopírovat a upravit příkaz AzCopy a zkontrolovat zda **vzor** odpovídá zdroji. Také zkontrolujte, zda **/S** zástupné znaky jsou platné. Další informace najdete v tématu [AzCopy parametry](storage-use-azcopy.md).

**Přesunutí dat z jednoho kontejneru úložiště do druhého?**

Postupujte následovně:

1.  Vytvoření kontejneru (složka) v cílovém objektu blob.

2.  Použití [AzCopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) zkopírovat obsah z původní kontejner objektů blob do kontejneru různých objektů blob.

**Jak vytvořit skript prostředí PowerShell pro přesun dat z jedné Azure sdílené složky do jiné ve službě Azure Storage?**

Pomocí nástroje AzCopy pro přesun dat z jednu sdílenou složku Azure do jiné ve službě Azure Storage. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Jak nahrát soubory .csv velké do služby Azure Storage?**

Pomocí nástroje AzCopy k nahrání souborů .csv velké do úložiště Azure. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Je nutné přesunout protokoly z jednotky D na můj účet úložiště Azure každý den. Jak to mohu automatizovat?**

Můžete pomocí nástroje AzCopy a vytvoření úlohy v Plánovači úloh. Nahrání souborů do účtu úložiště Azure pomocí skriptu batch AzCopy. Další informace najdete v tématu [postupy pro konfiguraci a spuštění úlohy spuštění pro cloudové služby](../../cloud-services/cloud-services-startup-tasks.md).

**Jak přesunout svůj účet úložiště mezi předplatnými?**

Pomocí nástroje AzCopy přesunout účtu úložiště mezi předplatnými. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Jak lze přesunout 10 TB dat do úložiště v jiné oblasti?**

Pomocí nástroje AzCopy přesouvat data. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Zkopírování dat z místního do služby Azure Storage?**

Pomocí AzCopy kopírovat data. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Jak lze přesunout data z místně do souborů Azure?**

Pro přesun dat pomocí nástroje AzCopy. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Mapování kontejneru složky na virtuálním počítači**

Použijte sdílenou složku Azure.

**Jak mohu zálohovat Azure file storage?**

Neexistuje žádné řešení zálohování. Soubory Azure však také podporuje asynchronní kopírování. Ano můžete zkopírovat soubory:

- Ze sdílené složky do jiné složky v rámci účtu úložiště nebo na jiný účet úložiště.

- Ze sdílené složky na kontejner objektů blob v rámci účtu úložiště nebo na jiný účet úložiště.

Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md).

**Přesunutí spravovaných disků na jiný účet úložiště**

Postupujte následovně:

1.  Zastavte virtuální počítač, který je spravovaný disk připojen k.

2.  Spravované disku VHD kopírovat z jedné oblasti do jiného spuštěním následujícího skriptu prostředí Azure PowerShell:

    ```
    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Vytvoření spravovaného disku pomocí souboru virtuálního pevného disku v jiné oblasti, do které jste zkopírovali virtuální pevný disk. Chcete-li to provést, spusťte následující skript prostředí Azure PowerShell:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Další informace o tom, jak nasadit virtuální počítač z se spravovaným diskem, najdete v části [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Jak můžete stáhnout 1 – 2 TB dat z portálu Azure?**

Chcete-li stáhnout data pomocí nástroje AzCopy. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).

**Změna sekundárního umístění pro Evropu oblast pro účet úložiště**

Pokud vytvoříte účet úložiště, vyberte primární oblasti pro účet. Výběr sekundární oblast je založený na primární oblasti a nelze ji změnit. Další informace najdete v tématu [geograficky redundantní úložiště (GRS): mezi místní replikace pro Azure Storage](storage-redundancy.md).

**Kde je můžete získat další informace o šifrování pro služby úložiště Azure (SSE)?**  
  
Viz následující články:

-  [Příručka zabezpečení Azure Storage](storage-security-guide.md)

-  [Šifrování služby úložiště Azure pro Data v klidovém stavu](storage-service-encryption.md)

**Jak přesunout nebo stahování dat z účtu úložiště?**

Chcete-li stáhnout data pomocí nástroje AzCopy. Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy v systému Windows](storage-use-azcopy.md) a [přenos dat pomocí nástroje AzCopy v systému Linux](storage-use-azcopy-linux.md).


**Jak šifrují data v účtu úložiště?**

Jakmile povolíte šifrování v účtu úložiště, stávající data nejsou šifrována. K šifrování stávající data, musíte nahrát ho znovu k účtu úložiště.

Pomocí nástroje AzCopy kopírovat data do jiný účet úložiště a poté přesuňte data zpět. Můžete také použít [šifrování v klidovém stavu](storage-service-encryption.md).

**Jak můžete stáhnout virtuální pevný disk na místním počítači, jiné než pomocí možnosti stahování na portálu?**

Můžete použít [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ke stažení virtuální pevný disk.

**Jsou všechny požadavky pro změnu replikace účtu úložiště z geograficky redundantní úložiště do místně redundantní úložiště?**

Ne. 

**Jak získám přístup k Azure Files redundantní úložiště?**

Geograficky redundantní úložiště s přístupem pro čtení, je nutné pro přístup k redundantní úložiště. Soubory Azure však podporuje pouze místně redundantního úložiště a standardní geograficky redundantní úložiště, který neumožňuje přístup jen pro čtení. 

**Přesunutí z prémiový účet úložiště pro standardní účet úložiště?**

Postupujte následovně:

1.  Vytvořte standardní účet úložiště. (Nebo použít stávající účet úložiště standard storage v rámci vašeho předplatného.)

2.  Stáhněte si nástroj AzCopy. Spusťte jeden z následujících příkazů AzCopy.
      
    Zkopírujte celou disky v účtu úložiště:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Pokud chcete zkopírovat jenom jeden disk, zadejte název disku **vzor**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Operace může trvat několik hodin.

Chcete-li mít jistotu, že přenos dokončena úspěšně, prohlédněte si cílový kontejner účet úložiště na portálu Azure. Po disky se zkopírují do účet standardního úložiště, můžete je připojit k virtuálnímu počítači jako existující disk. Další informace najdete v tématu [jak připojit spravované datový disk k virtuálnímu počítači Windows na portálu Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Jak převést na Azure Premium Storage pro sdílené složky?**

Storage úrovně Premium není povolena na sdílenou složku Azure.

**Jak se dá upgradovat z standardní účet úložiště na účet úložiště premium? Jak se ponížit z prémiový účet úložiště na standardní účet úložiště?**

Musíte vytvořit cílový účet úložiště, kopírování dat z účet zdrojového do cílového účtu a pak odstraňte účet zdrojové. Nástroje, jako je AzCopy můžete kopírovat data.

Pokud máte virtuální počítače, je nutné provést další kroky před migrací dat účet úložiště. Další informace najdete v tématu [migrace na Storage úrovně Premium (nespravované disky)](storage-migration-to-premium-storage.md).

**Přesunutí z účtu úložiště classic k účtu úložiště Azure Resource Manager?**

Můžete použít **přesunutí AzureStorageAccount** rutiny. Tato rutina má několik kroků (ověřit, Příprava, potvrzení). Přesunutí můžete ověřit, před jeho provedením.

Pokud máte virtuální počítače, je nutné provést další kroky před migrací dat účet úložiště. Další informace najdete v tématu [IaaS migraci prostředků z klasického do Azure Resource Manageru pomocí prostředí Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Jak stahování dat do počítače se systémem Linux z účtu úložiště Azure, a ukládat data z počítač s Linuxem?**

Pomocí rozhraní příkazového řádku Azure.

- Stáhněte si jediného objektu blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Odešlete jediného objektu blob: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Jak můžete I poskytnout ostatním přístup k Moje prostředky úložiště?**

Ostatní uživatelé poskytnout přístup k prostředkům úložiště:

-   Pomocí tokenu sdíleného přístupového podpisu (SAS) k poskytování přístupu k prostředku. 

-   Zadejte uživatele s primární nebo sekundární klíč pro účet úložiště. Další informace najdete v tématu [spravovat váš účet úložiště](storage-create-storage-account.md#manage-your-storage-account).

-   Změňte nastavení zásad přístupu k povolení anonymního přístupu. Další informace najdete v tématu [udělit anonymním uživatelům oprávnění ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Kde je nainstalován nástroj AzCopy?**

-   Pokud máte přístup k AzCopy z příkazového řádku Microsoft Azure Storage, zadejte **AzCopy**. Příkazový řádek je nainstalována spolu s AzCopy.

-   Pokud jste nainstalovali 32bitovou verzi, je tady: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy**.

-   Pokud jste nainstalovali 64bitovou verzi je tady: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**Jak se pro účet replikované úložiště (třeba zónově redundantní úložiště, geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení) přístup data, která je uložená v sekundární oblasti?**

-   Pokud používáte zónově redundantní úložiště nebo geograficky redundantní úložiště, můžete nelze přistupovat k datům ze sekundární oblasti pokud dojde k selhání. Další informace o procesu převzetí služeb při selhání najdete v tématu [co mají očekávat, pokud dojde k selhání úložiště](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Pokud používáte geograficky redundantní úložiště s přístupem pro čtení, můžete přístup k datům ze sekundární oblasti kdykoli. Použijte jednu z následujících metod:  
      
    - **AzCopy**: připojit **-sekundární** k názvu účtu úložiště v adrese URL pro přístup sekundárního koncového bodu. Příklad:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS token**: použití tokenu SAS pro přístup k datům z koncového bodu. Další informace najdete v tématu [pomocí sdílené přístupové podpisy](storage-dotnet-shared-access-signature-part-1.md).

**Jak používat vlastní doménu HTTPS s svůj účet úložiště? Příklad, jak lze vytvořit "https://mystorageaccountname.blob.core.windows.net/images/image.gif"jsou uvedeny jako"https://www.contoso.com/images/image.gif"?**

V účtech úložiště s vlastní domény není aktuálně podporován protokol SSL.
Ale můžete použít vlastní domény není HTTPS. Další informace najdete v tématu [konfigurace vlastního názvu doménu pro koncový bod služby Blob storage](../blobs/storage-custom-domain-name.md).

**Jak pomocí protokolu FTP přístup k datům, která je v účtu úložiště**

Neexistuje žádný způsob, jak získat přístup k účtu úložiště přímo pomocí protokolu FTP. Můžete však nastavit virtuální počítač Azure a pak nainstalujte serveru FTP na virtuálním počítači. Server FTP, ukládat soubory ve sdílené složce souborů Azure nebo na datový disk, který je k dispozici pro virtuální počítač může mít.

Pokud chcete pouze ke stahování dat bez nutnosti použití Průzkumníka úložiště nebo jiné aplikace, je možné použít tokenu SAS. Další informace najdete v tématu [pomocí sdílené přístupové podpisy](storage-dotnet-shared-access-signature-part-1.md).

**Jak provedu migraci objektů blob z jednoho účtu úložiště do druhého?**

 To provedete pomocí našich [objektu Blob skriptu migrace](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
