---
title: Migrace do Azure Storage – nejčastější dotazy | Dokumentace Microsoftu
description: Odpovědi na běžné dotazy týkající se migrace Azure Storage
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.component: common
ms.openlocfilehash: ecc5948c84f7659e950c360c3b19d1985b0ab81c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521485"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Nejčastější dotazy ohledně migrace úložiště Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se migrace úložiště Azure. 

## <a name="faq"></a>Nejčastější dotazy

**Jak vytvořit skript pro kopírování souborů z jednoho kontejneru do jiného?**

Kopírování souborů mezi kontejnery, můžete pomocí nástroje AzCopy. Podívejte se na následující příklad:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

Využívá nástroj AzCopy [kopírování objektů Blob rozhraní API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) kopírování všech souborů v kontejneru.  
  
Můžete použít libovolný virtuální počítač nebo místním počítači, který má přístup k Internetu ke spuštění nástroje AzCopy. Plánování služby Azure Batch můžete také použít k tomu automaticky, ale má složitější.  
  
Automatizační skript je určená pro nasazení Azure Resource Manageru namísto zpracování obsahu úložiště. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Je k dispozici žádné poplatky pro kopírování dat mezi dvěma sdílenými složkami souborů v rámci stejného účtu úložiště ve stejné oblasti?**

Ne. Neplatí žádné poplatky pro tento proces.

**Jak můžu zálohovat Moje celý účet úložiště do jiného účtu úložiště?**

Neexistuje žádná možnost zálohovat přímo celý účet úložiště. Ale můžete ručně přesunout kontejneru v účtu úložiště do jiného účtu s použitím AzCopy nebo Průzkumníka služby Storage. Následující kroky ukazují, jak pomocí nástroje AzCopy můžete přesunout kontejneru:  
 

1.  Nainstalujte [AzCopy](storage-use-azcopy.md) nástroj příkazového řádku. Tento nástroj umožňuje přesunout soubor virtuálního pevného disku mezi účty úložiště.

2.  Po instalaci nástroje AzCopy ve Windows s použitím instalačního programu, otevřete okno příkazového řádku a přejděte do instalační složky nástroje AzCopy ve vašem počítači. Ve výchozím nastavení je nástroj AzCopy nainstalovaný na **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** nebo **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Spusťte následující příkaz pro přesun kontejneru. Text musíte nahradit skutečnými hodnotami.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Zadejte identifikátor URI pro zdrojového účtu úložiště (až do kontejneru).  
    - `/Dest`: Zadejte identifikátor URI pro cílový účet úložiště (až do kontejneru).  
    - `/SourceKey`: Obsahuje primární klíč zdrojového účtu úložiště. Tento klíč můžete zkopírovat z portálu Azure portal výběrem účtu úložiště.  
    - `/DestKey`: Obsahuje primární klíč pro cílový účet úložiště. Tento klíč můžete z portálu zkopírujte výběrem účtu úložiště.

Po spuštění tohoto příkazu se přesouvají souborů kontejneru na cílový účet úložiště.

> [!NOTE]
> Rozhraní příkazového řádku AzCopy nefunguje společně s **vzor** přepnout při kopírování z jednoho Azure blob do jiné.
>
> Můžete přímo zkopírovat a upravit příkazu AzCopy a zkontrolovat že **vzor** odpovídá zdroji. Také zajistěte, aby **/S** zástupné znaky jsou aktivní. Další informace najdete v tématu [AzCopy parametry](storage-use-azcopy.md).

**Jak přesunu dat z jednoho kontejneru úložiště do jiného?**

Postupujte následovně:

1.  Vytvoření kontejneru (složka) v cílové objektů blob.

2.  Použití [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) chcete zkopírovat obsah z původní kontejneru objektů blob do kontejneru objektů blob v jiné.

**Jak vytvořit skript Powershellu pro přesun dat z jedné sdílené složky Azure do jiné ve službě Azure Storage?**

Pomocí nástroje AzCopy pro přesun dat z jedné sdílené složky Azure do jiné ve službě Azure Storage. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak nahrát velké soubory do služby Azure Storage?**

Pomocí AzCopy můžete nahrát CSV velkých souborů do služby Azure Storage. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Budu muset přesunout protokoly z jednotky D ke svému účtu úložiště Azure každý den. Jak to mohu automatizovat?**

Můžete pomocí nástroje AzCopy a vytvoří úkol v Plánovači úloh. Nahrání souborů do účtu služby Azure storage pomocí AzCopy dávky skriptu. Další informace najdete v tématu [jak nakonfigurovat a spustit úlohy po spuštění pro cloudovou službu](../../cloud-services/cloud-services-startup-tasks.md).

**Jak můžu přesunout svůj účet úložiště mezi předplatnými?**

Pomocí AzCopy můžete přesunout svůj účet úložiště mezi předplatnými. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak můžu přesunout 10 TB dat do úložiště v jiné oblasti?**

Pro přesun dat pomocí nástroje AzCopy. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak může kopírovat data z místních do služby Azure Storage?**

Pomocí AzCopy můžete kopírovat data. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak můžu přesunout data z místních do služby soubory Azure?**

Pro přesun dat pomocí nástroje AzCopy. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Jak se mapují složce kontejneru na virtuálním počítači?**

Používejte sdílenou složku Azure.

**Jak můžu zálohovat Azure file storage?**

Neexistuje žádné řešení zálohování. Soubory Azure však také podporuje asynchronní kopírování. Ano můžete zkopírovat soubory:

- Ze sdílené složky do jiné sdílené složky v rámci účtu úložiště nebo na jiný účet úložiště.

- Ze sdílené složky na kontejner objektů blob v rámci účtu úložiště nebo na jiný účet úložiště.

Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md).

**Jak můžu přesunout spravovaných disků do jiného účtu úložiště?**

Postupujte následovně:

1.  Zastavte virtuální počítač, který je spravovaný disk připojen k.

2.  Zkopírování spravovaného disku virtuální pevný disk z jedné oblasti do jiného spuštěním následujícího skriptu Azure Powershellu:

    ```
    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Vytvoření spravovaného disku s použitím souboru virtuálního pevného disku v jiné oblasti, do které jste zkopírovali virtuální pevný disk. Chcete-li to provést, spusťte následující skript Azure Powershellu:  

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

Další informace o tom, jak nasadit virtuální počítač ze spravovaného disku najdete v tématu [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Jak lze stáhnout 1 – 2 TB dat na webu Azure Portal?**

Pomocí AzCopy můžete stáhnout data. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).

**Změna tohoto sekundárního umístění k oblasti Evropa pro účet úložiště**

Při vytváření účtu úložiště vyberte primární oblast pro účet. Výběr sekundární oblasti vychází z primární oblasti a nejde změnit. Další informace najdete v tématu [geograficky redundantní úložiště (GRS): replikace mezi zónami pro službu Azure Storage](storage-redundancy.md).

**Kde získám další informace o Azure Storage Service Encryption (SSE)?**  
  
Viz následující články:

-  [Příručka zabezpečení Azure Storage](storage-security-guide.md)

-  [Šifrování služby Azure Storage pro neaktivní uložená Data](storage-service-encryption.md)

**Jak přesunout nebo stáhnout data z účtu úložiště?**

Pomocí AzCopy můžete stáhnout data. Další informace najdete v tématu [přenos dat pomocí AzCopy ve Windows](storage-use-azcopy.md) a [přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md).


**Jak se šifrují data v účtu úložiště?**

Po povolení šifrování účtu úložiště se existující data nejsou šifrována. K šifrování existující data, musíte nahrát ji znovu do účtu úložiště.

Pomocí AzCopy můžete kopírovat data na jiný účet úložiště a pak přesunout data zpět. Můžete také použít [šifrování v klidovém stavu](storage-service-encryption.md).

**Jak lze stáhnout virtuální pevný disk do místního počítače, jiné než pomocí možnosti stahování na portálu?**

Můžete použít [Průzkumníka služby Storage](https://azure.microsoft.com/features/storage-explorer/) stáhnout virtuální pevný disk.

**Existují nějaké požadavky změny replikace účtu úložiště z geograficky redundantní úložiště na místně redundantní úložiště?**

Ne. 

**Jak získám přístup k Azure Files storage redundantní?**

Geograficky redundantní úložiště jen pro čtení vyžaduje přístup k redundantním úložištěm. Soubory Azure však podporuje pouze místně redundantní úložiště a standardní geograficky redundantní úložiště, který neumožňuje přístup jen pro čtení. 

**Jak přesunu z účtu služby premium storage na účet úložiště úrovně standard?**

Postupujte následovně:

1.  Vytvořte účet úložiště úrovně standard. (Nebo použijte existující účet úložiště úrovně standard ve vašem předplatném.)

2.  Stáhněte si nástroj AzCopy. Spusťte jeden z následujících příkazů AzCopy.
      
    Zkopírujte celý disků v účtu úložiště:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Pokud chcete zkopírovat jenom jeden disk, zadejte název disku v **vzor**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Operace může trvat několik hodin.

Pokud chcete mít jistotu, že přenos dokončena úspěšně, zkontrolujte cílový kontejner účtu úložiště na webu Azure Portal. Po zkopírování disky do účtu úložiště úrovně standard je můžete připojit k virtuálnímu počítači jako stávající disk. Další informace najdete v tématu [postup připojení spravovaného datového disku k virtuálnímu počítači Windows na webu Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Jak převést na Azure Premium Storage pro sdílenou složku?**

Premium Storage není povolená u sdílené složky Azure.

**Jak můžu přejít z účtu úložiště úrovně standard účtu služby premium storage? Jak se downgrade z účtu služby premium storage na účet úložiště úrovně standard?**

Musíte vytvořit cílový účet úložiště, kopírování dat z účtu zdroje do cílového účtu a pak odstraňte účet zdrojové. Nástroje, jako je AzCopy můžete kopírovat data.

Pokud máte virtuální počítače, je nutné provést další kroky předtím, než zahájíte migraci dat účtu úložiště. Další informace najdete v tématu [migrace na Azure Premium Storage (nespravovaných discích)](storage-migration-to-premium-storage.md).

**Jak přesouvat klasický účet úložiště do účtu úložiště Azure Resource Manageru?**

Můžete použít **Move-AzureStorageAccount** rutiny. Tato rutina má několik kroků (ověření, Příprava, potvrzení). Přesunutí můžete ověřit, před jeho provedením.

Pokud máte virtuální počítače, je nutné provést další kroky předtím, než zahájíte migraci dat účtu úložiště. Další informace najdete v tématu [migrace prostředků IaaS z modelu nasazení classic do Azure Resource Manageru pomocí prostředí Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Jak můžu stáhnout data na počítači s Linuxem z účtu služby Azure storage, nebo nahrávání dat z počítače s Linuxem?**

Můžete použít rozhraní příkazového řádku Azure.

- Stáhněte si jeden objekt blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Nahrání jednoho objektu blob: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Jak můžete mi poskytnout ostatním přístup ke Moje prostředky úložiště?**

Chcete-li poskytnout ostatním přístup k prostředkům úložiště:

-   Použijte token sdíleného přístupového podpisu (SAS) k poskytnutí přístupu k prostředku. 

-   Zadejte uživatele s primární nebo sekundární klíč pro účet úložiště. Další informace najdete v tématu [Správa účtu úložiště](storage-create-storage-account.md#manage-your-storage-account).

-   Změňte zásady přístupu k povolení anonymního přístupu. Další informace najdete v tématu [anonymním uživatelům uděluje oprávnění ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Kde je nástroj AzCopy nainstalovaný?**

-   Pokud přistupujete AzCopy z příkazového řádku Microsoft Azure Storage, zadejte **AzCopy**. Příkazový řádek se instaluje společně s AzCopy.

-   Pokud jste nainstalovali 32bitovou verzi, je tady: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy**.

-   Pokud jste nainstalovali 64bitovou verzi, je tady: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**Pro účet replikované úložiště (například zónově redundantní úložiště, geograficky redundantní úložiště nebo geograficky redundantní úložiště jen pro čtení) jak lze získat data, která je uložena v sekundární oblasti?**

-   Pokud používáte zónově redundantní úložiště nebo geograficky redundantní úložiště, můžete nelze přistupovat k datům ze sekundární oblasti pokud dojde k selhání. Další informace o procesu převzetí služeb při selhání najdete v tématu [co očekávat, pokud dojde k selhání úložiště](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Pokud používáte geografické redundantní úložiště jen pro čtení, můžou k datům ze sekundární oblasti v každém okamžiku. Použijte jednu z následujících metod:  
      
    - **AzCopy**: připojit **– sekundární** k názvu účtu úložiště v adrese URL pro přístup k sekundární koncový bod. Příklad:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS token**: použijte SAS token pro přístup k datům z koncového bodu. Další informace najdete v tématu [použití sdílených přístupových podpisů](storage-dotnet-shared-access-signature-part-1.md).

**Použití vlastní domény HTTPS pomocí účtu úložiště Například jak zajistím, aby "https://mystorageaccountname.blob.core.windows.net/images/image.gif"se zobrazí jako"https://www.contoso.com/images/image.gif"?**

V účtech úložiště s použitím vlastních domén se momentálně nepodporuje SSL.
Ale můžete použít bez HTTPS vlastní domény. Další informace najdete v tématu [konfigurace vlastního názvu domény pro koncový bod služby Blob storage](../blobs/storage-custom-domain-name.md).

**Použití FTP pro přístup k datům, která je v účtu úložiště**

Neexistuje žádný způsob, jak získat přístup k účtu úložiště přímo pomocí FTP. Můžete však nastavení virtuálního počítače Azure a pak nainstalujte FTP server na virtuálním počítači. Server FTP ukládat soubory ve sdílené složce služby soubory Azure nebo na datový disk, který je k dispozici pro virtuální počítač může mít.

Pokud chcete pouze ke stahování dat bez nutnosti použití Průzkumníka služby Storage nebo podobné aplikace, je možné použít SAS token. Další informace najdete v tématu [použití sdílených přístupových podpisů](storage-dotnet-shared-access-signature-part-1.md).

**Jak můžu migrovat objekty BLOB z jednoho účtu úložiště do jiného?**

 Můžete provést pomocí naší [Blob skript migrace](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
