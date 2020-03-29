---
title: Připojení virtuálního systému souborů ve fondu – Azure Batch | Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit virtuální systém souborů do fondu dávek.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919001"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Připojení virtuálního systému souborů do fondu dávek

Azure Batch teď podporuje připojení cloudového úložiště nebo externího souborového systému ve výpočetních uzlech Windows nebo Linuxu ve vašich fondů batch. Když výpočetní uzel připojí fond, virtuální systém souborů je připojen a považován za místní jednotku na tomto uzlu. Můžete připojit systémy souborů, jako jsou soubory Azure, úložiště objektů blob Azure, síťový souborový systém (NFS) včetně [mezipaměti Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)nebo společný internetový souborový systém (CIFS).

V tomto článku se dozvíte, jak připojit virtuální systém souborů do fondu výpočetních uzlů pomocí [knihovny správy dávek pro rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> Připojení virtuálního systému souborů je podporováno u fondů dávek vytvořených v letech 2019-08-19 nebo po něm. Dávkové fondy vytvořené před 2019-08-19 tuto funkci nepodporují.
> 
> Rozhraní API pro připojení systémů souborů na výpočetní uzel jsou součástí [knihovny Batch .NET.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Výhody montáže na bazén

Připojení systému souborů do fondu, namísto umožnění úloh načíst vlastní data z velké sady dat, usnadňuje a zefektivňuje úkoly pro přístup k potřebným datům.

Zvažte scénář s více úkoly, které vyžadují přístup ke společné sadě dat, jako je vykreslování filmu. Každá úloha vykreslí jeden nebo více snímků najednou ze souborů scény. Připojením jednotky, která obsahuje soubory scény, je pro výpočetní uzly snazší přístup ke sdíleným datům. Kromě toho základní systém souborů lze zvolit a škálovat nezávisle na výkonu a škálování (propustnost a IOPS) vyžadované počtem výpočetních uzlů souběžně přístup k datům. Například [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) distribuované mezipaměti v paměti lze použít pro podporu velkých vykreslování v měřítku filmu s tisíci souběžných vykreslovacích uzlů, přístup ke zdrojovým datům, která jsou umístěna v místním prostředí. Případně pro data, která jsou již umístěna v cloudovém úložišti objektů [Blob, lze blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) použít k připojení těchto dat jako místního systému souborů. Blobfuse je k dispozici jenom na linuxových uzlech, ale [soubory Azure](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) poskytují podobný pracovní postup a jsou dostupné ve Windows i linuxu.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Připojení virtuálního systému souborů do fondu  

Připojení virtuálního systému souborů ve fondu zpřístupní systém souborů každému výpočetnímu uzlu ve fondu. Systém souborů je nakonfigurován buď při připojení výpočetního uzlu do fondu, nebo při restartování nebo reimaged uzlu.

Chcete-li připojit systém souborů do `MountConfiguration` fondu, vytvořte objekt. Zvolte objekt, který odpovídá `AzureBlobFileSystemConfiguration`virtuálnímu systému souborů: , `AzureFileShareConfiguration`, `NfsMountConfiguration`, nebo `CifsMountConfiguration`.

Všechny objekty konfigurace připojení potřebují následující základní parametry. Některé konfigurace připojení mají parametry specifické pro používaný systém souborů, které jsou podrobněji popsány v příkladech kódu.

- **Název účtu nebo zdroj**: Chcete-li připojit virtuální sdílenou složku, potřebujete název účtu úložiště nebo jeho zdroj.
- **Relativní cesta nebo zdroj**připojení : Umístění systému souborů připojeného `fsmounts` na výpočetním uzlu `AZ_BATCH_NODE_MOUNTS_DIR`vzhledem ke standardnímu adresáři přístupnému na uzlu přes . Přesné umístění se liší v závislosti na operačním systému používaném v uzlu. Například fyzické umístění v uzlu Ubuntu je `mnt\batch\tasks\fsmounts`mapováno na a na uzlu CentOS je mapováno na `mnt\resources\batch\tasks\fsmounts`.
- **Možnosti připojení nebo možnosti blobfuse**: Tyto možnosti popisují specifické parametry pro připojení systému souborů.

Po `MountConfiguration` vytvoření objektu přiřaďte `MountConfigurationList` objekt vlastnosti při vytváření fondu. Systém souborů je připojen buď při připojení uzlu do fondu nebo při restartování nebo reimaged uzlu.

Při montáži systému souborů `AZ_BATCH_NODE_MOUNTS_DIR` je vytvořena proměnná prostředí, která odkazuje na umístění připojených systémů souborů a také na soubory protokolu, které jsou užitečné pro řešení potíží a ladění. Soubory protokolu jsou podrobněji vysvětleny v části [Diagnostikovat chyby připojení.](#diagnose-mount-errors)  

> [!IMPORTANT]
> Maximální počet připojených systémů souborů ve fondu je 10. Podrobnosti a další omezení najdete v [tématu Kvóty a limity dávkových služeb.](batch-quota-limit.md#other-limits)

## <a name="examples"></a>Příklady

Následující příklady kódu ukazují připojení různých sdílených složek do fondu výpočetních uzlů.

### <a name="azure-files-share"></a>Sdílená složka Azure Files

Azure Files je standardní nabídka cloudového souborového systému Azure. Další informace o tom, jak získat některý z parametrů v ukázce konfiguračního kódu připojení, najdete v [tématu Použití sdílené složky Azure Files](../storage/files/storage-how-to-use-files-windows.md).

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
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Systém souborů Azure Blob

Další možností je použití úložiště objektů blob Azure přes [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Připojení systému souborů objektů `AccountKey` `SasKey` blob vyžaduje účet úložiště nebo pro váš účet úložiště. Informace o získání těchto klíčů naleznete v [tématu Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md)nebo [použití sdílených přístupových podpisů (SAS).](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Další informace o používání blobfuse naleznete v [nejčastějších dotazech k řešení problémů s](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)blobfuse . Chcete-li získat výchozí přístup k adresáři připojenému k blobfuse, spusťte úlohu jako **správce**. Blobfuse připojí adresář v uživatelském prostoru a při vytváření fondu je připojen jako root. V Linuxu jsou všechny úlohy **správce** root. Všechny možnosti modulu FUSE jsou popsány na [referenční stránce FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Kromě průvodce odstraňováním problémů jsou problémy GitHubu v úložišti blobfuse užitečným způsobem, jak zkontrolovat aktuální problémy s blobfuse a řešení. Další informace naleznete v tématu [blobfuse problémy](https://github.com/Azure/azure-storage-fuse/issues).

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

### <a name="network-file-system"></a>Síťový systém souborů

Síťové systémy souborů (NFS) lze také připojit k uzlům fondu, což umožňuje snadné přístup k tradičním systémům souborů pomocí uzlů Azure Batch. Může se jedná o jeden server nfs nasazený v cloudu nebo místní server systému zabezpečení sítě, ke který se přistupuje přes virtuální síť. Případně můžete využít řešení distribuované mezipaměti [Avere vFXT,](../avere-vfxt/avere-vfxt-overview.md) které poskytuje bezproblémové připojení k místnímu úložišti, čtení dat na vyžádání do mezipaměti a poskytuje vysoký výkon a škálování pro cloudové výpočetní uzly.

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

### <a name="common-internet-file-system"></a>Běžný systém souborů V Internetu

Běžné systémy souborů Internetu (CIFS) lze také připojit ke sudům fondu, což umožňuje snadné přístup k tradičním systémům souborů pomocí uzlů Azure Batch. CIFS je protokol pro sdílení souborů, který poskytuje otevřený a multiplatformní mechanismus pro vyžádání souborů a služeb síťového serveru. CIFS je založen na rozšířené verzi protokolu SMB (Server Message Block) společnosti Microsoft pro sdílení souborů v síti Internet a intranet a používá se k připojení externích systémů souborů na uzly systému Windows. Další informace o smb naleznete [v tématu Souborový server a SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

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

Pokud konfigurace připojení selže, výpočetní uzel ve fondu se nezdaří a stav uzlu se stane nepoužitelným. Chcete-li diagnostikovat selhání [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) konfigurace připojení, zkontrolujte vlastnost podrobnosti o chybě.

Chcete-li získat soubory protokolu pro ladění, použijte `*.log` [OutputFiles](batch-task-output-files.md) k nahrání souborů. Soubory `*.log` obsahují informace o připojení systému souborů v `AZ_BATCH_NODE_MOUNTS_DIR` umístění. Soubory protokolu připojení mají `<type>-<mountDirOrDrive>.log` formát: pro každé připojení. Například `cifs` připojení v adresáři `test` připojení s názvem bude `cifs-test.log`mít soubor protokolu připojení s názvem: .

## <a name="supported-skus"></a>Podporované skUs

| Vydavatel | Nabídka | Skladová jednotka (SKU) | Sdílení souborů Azure | Blůza | NFS připojit | Držák CIFS |
|---|---|---|---|---|---|---|
| dávka | rendering-centos73 | Vykreslování | :heavy_check_mark: <br>Poznámka: Kompatibilní s CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Kredativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Kredativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-reklamy | linux-data-věda-vm | linuxdsvm | :heavy_check_mark: <br>Poznámka: Kompatibilní s CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centtos-kontejner | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-kontejner-rdma | 7.4 | :heavy_check_mark: <br>Poznámka: Podporuje A_8 nebo 9 úložišť</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-kontejner | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-věda-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | Centos-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Společnost Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další podrobnosti o připojení sdílené položky Azure Files s [Windows](../storage/files/storage-how-to-use-files-windows.md) nebo [Linuxem](../storage/files/storage-how-to-use-files-linux.md).
- Další informace o používání a [montážblobfuse](https://github.com/Azure/azure-storage-fuse) virtuální souborové systémy.
- Informace o systému souborů NFS a jeho aplikacích najdete v tématu [Přehled síťového systému.](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
- Další informace o CIFS najdete v [tématu Protokol Microsoft SMB a přehled protokolu CIFS.](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)
