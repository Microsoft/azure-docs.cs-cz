---
title: Připojení virtuálního systému souborů ve fondu
description: Přečtěte si, jak připojit virtuální systém souborů ve fondu Batch.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89145480"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Připojení virtuálního systému souborů ve fondu Batch

Azure Batch teď podporuje připojení cloudového úložiště nebo externího systému souborů ve výpočetních uzlech se systémem Windows nebo Linux ve fondech služby Batch. Když je výpočetní uzel připojen k fondu, je virtuální systém souborů připojen a je v tomto uzlu považován za místní disk. Můžete připojit souborové systémy, jako jsou soubory Azure, Azure Blob Storage, systém souborů NFS (Network File System), včetně [mezipaměti avere vFXT cache](../avere-vfxt/avere-vfxt-overview.md)nebo protokolu CIFS (Common Internet File System).

V tomto článku se dozvíte, jak připojit virtuální systém souborů ve fondu výpočetních uzlů pomocí [knihovny pro správu služby Batch pro .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Připojení virtuálního systému souborů je podporováno v fondech Batch vytvořených v nebo po 2019-08-19. Fondy dávek vytvořené před 2019-08-19 tuto funkci nepodporují.
> 
> Rozhraní API pro připojování systémů souborů na výpočetním uzlu jsou součástí knihovny [Batch .NET](/dotnet/api/microsoft.azure.batch) .

## <a name="benefits-of-mounting-on-a-pool"></a>Výhody připojení ve fondu

Systém souborů se připojuje ke fondu. místo toho, aby úkoly načetly vlastní data z velké sady dat, usnadňuje a zefektivňuje pro úlohy přístup k potřebným datům.

Vezměte v úvahu scénář s více úlohami, které vyžadují přístup ke společné sadě dat, jako je například vykreslování filmu. Každý úkol vykreslí jeden nebo více snímků v čase ze souborů scény. Připojením jednotky, která obsahuje soubory scény, je snazší u výpočetních uzlů získat přístup ke sdíleným datům. V závislosti na výkonu a škálování (propustnost a IOPS) vyžadovaných počtem výpočetních uzlů, které jsou souběžně přistupují k datům, je navíc možné zvolit a škálovat základní systém souborů. Například distribuovanou mezipaměť [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) lze použít k podpoře velkého měřítka obrázků s využitím tisíců souběžných uzlů vykreslování a přístup ke zdrojovým datům, která se nacházejí v místním prostředí. Alternativně můžete pro data, která se nacházejí v cloudovém úložišti objektů blob, použít [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) k připojení těchto dat jako místní systém souborů. Blobfuse je k dispozici pouze na uzlech systému Linux, ale služby [soubory Azure](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) poskytují podobný pracovní postup a jsou k dispozici v systémech Windows i Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Připojení virtuálního systému souborů ve fondu  

Připojení virtuálního systému souborů ve fondu způsobí, že je systém souborů dostupný pro každý výpočetní uzel ve fondu. Systém souborů je nakonfigurován buď v případě, kdy se výpočetní uzel připojí k fondu, nebo když se uzel restartuje nebo obnoví z image.

Chcete-li připojit systém souborů ve fondu, vytvořte `MountConfiguration` objekt. Vyberte objekt, který odpovídá vašemu virtuálnímu systému souborů: `AzureBlobFileSystemConfiguration` , `AzureFileShareConfiguration` , `NfsMountConfiguration` nebo `CifsMountConfiguration` .

Všechny konfigurační objekty připojení potřebují následující základní parametry. Některé konfigurace připojení mají parametry specifické pro používaný systém souborů, které jsou podrobněji popsány v příkladech kódu.

- **Název účtu nebo zdroj**: Pokud chcete připojit sdílenou složku virtuálního souboru, potřebujete název účtu úložiště nebo jeho zdroje.
- **Relativní cesta nebo zdroj připojení**: umístění systému souborů připojeného k výpočetnímu uzlu vzhledem ke standardnímu `fsmounts` adresáři přístupnému v uzlu prostřednictvím `AZ_BATCH_NODE_MOUNTS_DIR` . Přesné umístění se liší v závislosti na operačním systému, který se používá na uzlu. Například fyzické umístění na uzlu Ubuntu je namapováno na `mnt\batch\tasks\fsmounts` a na uzlu CentOS, na který je namapován `mnt\resources\batch\tasks\fsmounts` .
- Možnosti **připojení nebo možnosti blobfuse**: tyto možnosti popisují konkrétní parametry pro připojení systému souborů.

Jakmile `MountConfiguration` je objekt vytvořen, přiřaďte objekt k `MountConfigurationList` vlastnosti při vytváření fondu. Systém souborů je připojen buď když se uzel připojí k fondu, nebo když se uzel restartuje nebo obnoví z image.

Když je systém souborů připojen, je vytvořena proměnná prostředí, `AZ_BATCH_NODE_MOUNTS_DIR` která odkazuje na umístění připojených systémů souborů i soubory protokolů, které jsou užitečné při řešení potíží a ladění. Podrobnější vysvětlení souborů protokolu najdete v části [Diagnostika chyb připojení](#diagnose-mount-errors) .  

> [!IMPORTANT]
> Maximální počet připojených systémů souborů ve fondu je 10. Podrobnosti a další omezení najdete v tématu [kvóty služby Batch a omezení](batch-quota-limit.md#other-limits) .

## <a name="examples"></a>Příklady

Následující příklady kódu ukazují připojení nejrůznějších sdílených složek do fondu výpočetních uzlů.

### <a name="azure-files-share"></a>Sdílená složka Azure Files

Azure Files je standardní nabídka Azure cloudového systému souborů. Další informace o tom, jak získat libovolný parametr v ukázce konfiguračního kódu připojení, najdete v tématu [použití sdílené složky Azure Files](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Systém souborů Azure Blob

Další možností je používat úložiště objektů BLOB v Azure prostřednictvím [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Připojení systému souborů objektů BLOB vyžaduje `AccountKey` nebo `SasKey` pro váš účet úložiště. Informace o tom, jak tyto klíče získat, najdete v tématech [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md)nebo [použití sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md). Další informace o použití blobfuse najdete v tématu [věnovaném řešení potíží s](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)blobfuse. Pokud chcete získat výchozí přístup k připojenému adresáři blobfuse, spusťte úlohu jako **správce**. Blobfuse připojí adresář v uživatelském prostoru a při vytváření fondu je připojen jako kořenový adresář. V systému Linux jsou všechny úlohy **správce** kořenové. Všechny možnosti pro modul zapékací postupu jsou popsány na [referenční stránce zapékací stránky](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Kromě Průvodce odstraňováním potíží je v úložišti blobfuse užitečný způsob, jak kontrolovat aktuální problémy s blobfuse a jejich řešení. Další informace najdete v tématu [blobfuse problémy](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Systém souborů sítě

Systémy souborů NFS (Network File System) je možné připojit také k uzlům fondu, aby bylo možné k tradičním systémům souborů snadno přistupovat Azure Batch uzly. Může to být jeden server NFS nasazený v cloudu nebo místní server NFS, ke kterému se přistupoval přes virtuální síť. Alternativně můžete využít výhod řešení mezipaměťové mezipaměti [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) , které poskytuje bezproblémové připojení k místnímu úložišti, čtení dat na vyžádání do mezipaměti a poskytuje vysoký výkon a škálování na cloudové výpočetní uzly.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Common Internet File System

Společné soubory protokolu CIFS (Common Internet File System) je možné připojit také k uzlům fondu, aby bylo možné k tradičním systémům souborů snadno přicházet pomocí Azure Batchch uzlů. CIFS je protokol pro sdílení souborů, který poskytuje mechanismus pro otevření a více platforem pro vyžádání souborů a služeb síťového serveru. Protokol CIFS je založený na rozšířené verzi protokolu SMB (Server Message Block) společnosti Microsoft pro sdílení souborů v Internetu a intranetu a používá se k připojení externích systémů souborů v uzlech systému Windows. Další informace o protokolu SMB najdete v tématu [souborový server a SMB](/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnostika chyb připojení

Pokud konfigurace připojení selže, výpočetní uzel ve fondu selže a stav uzlu bude nepoužitelný. Chcete-li diagnostikovat selhání konfigurace připojení, prozkoumejte [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) vlastnost, kde najdete podrobnosti o chybě.

Chcete-li získat soubory protokolu pro ladění, použijte [OutputFiles](batch-task-output-files.md) k nahrání `*.log` souborů. `*.log`Soubory obsahují informace o připojení systému souborů v `AZ_BATCH_NODE_MOUNTS_DIR` umístění. Soubory protokolu připojení mají formát: `<type>-<mountDirOrDrive>.log` pro každé připojení. Například `cifs` připojení v adresáři pro připojení s názvem `test` bude mít soubor protokolu připojení s názvem: `cifs-test.log` .

## <a name="supported-skus"></a>Podporované SKU

| Publisher | Nabídka | SKU | Sdílená složka Azure Files | Blobfuse | Připojení systému souborů NFS | Připojení CIFS |
|---|---|---|---|---|---|---|
| dávka | vykreslování – centos73 | vykreslování | :heavy_check_mark: <br>Poznámka: kompatibilní s CentOS 7,7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16,04 – LTS, 18,04 – LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | znak | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-ADS | Linux – data-věda – virtuální počítač | linuxdsvm | :heavy_check_mark: <br>Poznámka: kompatibilní s CentOS 7,4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS – kontejner | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS-kontejner – RDMA | 7,4 | :heavy_check_mark: <br>Poznámka: podporuje úložiště A_8 nebo 9.</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | Ubuntu-Server-kontejner | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-dsvm | Linux – data-věda-VM-Ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS – HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | znak | znak | znak | znak |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | znak | znak | znak |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další podrobnosti o připojení sdílené složky služby soubory Azure se [systémem Windows](../storage/files/storage-how-to-use-files-windows.md) nebo [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Seznamte se s používáním a připojením virtuálních systémů souborů [blobfuse](https://github.com/Azure/azure-storage-fuse) .
- Informace o systému souborů NFS a jeho aplikacích najdete v tématu [Přehled systému souborů](/windows-server/storage/nfs/nfs-overview) NFS.
- Další informace o CIFS najdete v tématu [Přehled protokolu SMB a protokolu CIFS](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) .
