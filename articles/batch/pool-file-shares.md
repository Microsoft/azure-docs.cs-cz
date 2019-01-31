---
title: Sdílené složky Azure pro fondy Azure Batch | Dokumentace Microsoftu
description: Postup pro připojení sdílené složky služby soubory Azure z výpočetních uzlů ve fondu s Linuxem nebo Windows ve službě Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 13ed2caa5ae547747707c368246ea23486dbed72
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469562"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Sdílené složky Azure pomocí fondu služby Batch

[Služba soubory Azure](../storage/files/storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes protokol zprávy bloku SMB (Server). Tento článek obsahuje informace a příklady kódu pro připojení a použití sdílené složky Azure na fond výpočetních uzlů. Příklady kódu pomocí rozhraní Batch .NET a sady SDK pro Python, ale můžete provádět podobné operace pomocí jiných nástrojů a sad SDK služby Batch.

Batch poskytuje nativní podporu rozhraní API pro použití ke čtení a zápis dat objektů BLOB Azure Storage. V některých případech můžete chtít přístup ke sdílené složky Azure z fondu výpočetních uzlů. Například máte starší verzi úlohu, která závisí na sdílené složce SMB nebo vaše úkoly potřebují přistupovat k sdíleným datům nebo sdílené výstup. 

## <a name="considerations-for-use-with-batch"></a>Důležité informace týkající se použití se službou Batch

* Zvažte použití sdílené složky Azure v případě, že máte fondy, na kterých běží relativně nízký počet paralelních úloh. Zkontrolujte [cíle výkonu a škálování](../storage/files/storage-files-scale-targets.md) k určení, pokud by měl používat soubory Azure, (ta používá účet Azure Storage), očekávaný fondu velikost a počet souborů prostředků. 

* Jsou sdílené složky Azure [nákladově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a dá se s daty replikace do jiné oblasti tak jsou globálně redundantní. 

* Sdílené složky Azure současně můžete připojit z místního počítače.

* Viz také Obecné [aspekty plánování](../storage/files/storage-files-planning.md) Azure sdílené složky.


## <a name="create-a-file-share"></a>Vytvoření sdílené složky

[Vytvoření sdílené složky](../storage/files/storage-how-to-create-file-share.md) v účtu úložiště, který je propojený s vaším účtem Batch, nebo samostatný účet úložiště.

## <a name="mount-a-share-on-a-windows-pool"></a>Připojení sdílené složky ve fondu Windows

Tato část obsahuje kroky a příklady kódu pro připojení a používání Azure file sdílet na fond uzlů Windows. Další informace najdete v článku [dokumentaci](../storage/files/storage-how-to-use-files-windows.md) pro připojení Azure pro sdílení souborů na Windows. 

Ve službě Batch potřebujete připojit sdílenou složku pokaždé, když je úloha spuštěna na uzlu Windows. V současné době není možné zachovat síťové připojení mezi úkoly na uzlech Windows.

Zadejte například `net use` příkaz pro připojení sdílené složky jako součást příkazového řádku jednotlivých úkolů. Pro připojení sdílené složky, jsou potřeba následující přihlašovací údaje:

* **Uživatelské jméno**: AZURE\\\<storageaccountname\>, například AZURE\\*mystorageaccountname*
* **Heslo**: < StorageAccountKeyWhichEnds v == >, například *XXXXXXXXXXXXXXXXXXXXX ==*

Následující příkaz slouží pro připojení sdílené složky *myfileshare* v účtu úložiště *mystorageaccountname* jako *S:* jednotky:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Příklady v tomto článku pro zjednodušení předání přihlašovacích údajů přímo v textu. V praxi důrazně doporučujeme Správa pověření pomocí proměnné prostředí, certifikáty nebo řešení, jako je Azure Key Vault.

Pro zjednodušení operaci připojení, volitelně uchování přihlašovacích údajů na uzlech. Potom můžou připojovat sdílené bez přihlašovacích údajů. Proveďte následující kroky:

1. Spustit `cmdkey` nástroj příkazového řádku pomocí spouštěcího úkolu v konfiguraci fondu. To potrvá přihlašovací údaje na každém uzlu Windows. Se podobá se příkazový řádek úkolu spuštění:

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. Připojit sdílenou složku na každém uzlu v rámci každé úlohy pomocí `net use`. Například následující příkazový řádek úkolu připojí jako sdílené složky souborů *S:* jednotky. To by být následován znakem příkaz či skript, který odkazuje na sdílenou složku. Při volání funkce se používají přihlašovací údaje v mezipaměti `net use`. Tento krok předpokládá, že používáte stejnou identitu uživatele pro úkoly, které jste použili ve spouštěcím úkolu fondu, který není vhodná pro všechny scénáře.

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>Příklad jazyka C#
Následující C# příklad ukazuje, jak k uchování přihlašovacích údajů ve fondu Windows pomocí spouštěcího úkolu. Název služby file storage a přihlašovací údaje úložiště jsou předány jako definované konstanty. Tady na nich spouštěcí úkol běží pod standardní (bez oprávnění správce) automaticky – uživatelský účet s rozsahem fondu.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Po uložení přihlašovacích údajů pomocí příkazové řádky úkolu připojit sdílenou složku a odkazují na sdílenou složku v čtení nebo zápis. Jako základní příklad příkazového řádku úkolu následující fragment kódu používá `dir` příkaz pro vytvoření seznamu souborů ve sdílené složce. Ujistěte se, že ke spuštění každé úlohy pomocí stejných [identitu uživatele](batch-user-accounts.md) použít ke spuštění spouštěcího úkolu ve fondu. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Připojte sdílenou složku, ve fondu s Linuxem

Sdílené složky Azure je možné připojit v Linuxových distribucích pomocí [CIFS jádra klienta](https://wiki.samba.org/index.php/LinuxCIFS). Následující příklad ukazuje, jak připojit sdílenou složku ve fondu výpočetních uzlů se systémem Ubuntu 16.04 LTS. Pokud používáte jiné distribuce Linuxu, obecné kroky jsou podobné, ale pomocí Správce balíčků pro příslušnou distribuci. Podrobnosti a další příklady najdete v tématu [použít soubory Azure s Linuxem](../storage/files/storage-how-to-use-files-linux.md).

Nejdřív s identitou uživatele, správce, nainstalujte `cifs-utils` balíčků a vytvoření přípojného bodu (například */mnt/MyAzureFileShare*) v místním systému souborů. Složka pro bod připojení je vytvořit kdekoli v systému souborů, ale je běžné konvence k vytvoření tohoto pod `/mnt` složky. Nezapomeňte vytvořit bod připojení přímo na `/mnt` (na Ubuntu) nebo `/mnt/resource` (na jiné distribuce).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Potom spusťte `mount` příkaz pro připojení sdílené složky, použijte tyto přihlašovací údaje:

* **Uživatelské jméno**: \<storageaccountname\>, například *mystorageaccountname*
* **Heslo**: < StorageAccountKeyWhichEnds v == >, například *XXXXXXXXXXXXXXXXXXXXX ==*

Následující příkaz slouží pro připojení sdílené složky *myfileshare* v účtu úložiště *mystorageaccountname* na */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Příklady v tomto článku pro zjednodušení předání přihlašovacích údajů přímo v textu. V praxi důrazně doporučujeme Správa pověření pomocí proměnné prostředí, certifikáty nebo řešení, jako je Azure Key Vault.

Ve fondu s Linuxem můžete sloučit všechny tyto kroky v jedné spouštěcí úkol nebo spustit ve skriptu. Spuštění spouštěcího úkolu jako uživatel s oprávněním správce k fondu. Nastavte spouštěcí úkol čekání úspěšně dokončit před spuštěním další úlohy ve fondu, který odkazuje sdílenou složku.

### <a name="python-example"></a>Příklad v Pythonu

Následující příklad Python ukazuje postup při konfiguraci připojení sdílené složky ve spouštěcím úkolu fondu Ubuntu. Přípojný bod, koncový bod sdílené složky souboru a přihlašovací údaje úložiště jsou předány jako definované konstanty. Spouštěcí úkol běží pod automaticky uživatelský účet správce s oborem fondu.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Po připojení sdílené složky a definování úlohu, použijte sdílenou složku na příkazových řádcích úkolů. Například následující základní příkaz používá `ls` pro vytvoření seznamu souborů ve sdílené složce.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Další postup

* Další možnosti číst a zapisovat data ve službě Batch najdete v článku [přehled funkcí Batch](batch-api-basics.md) a [trvalý výstup úloh a úkolů](batch-task-output.md).

* Viz také [Batch loděnice](https://github.com/Azure/batch-shipyard) toolkit, která zahrnuje [loděnice recepty](https://github.com/Azure/batch-shipyard/tree/master/recipes) nasadit systémy souborů pro kontejner úloh služby Batch.