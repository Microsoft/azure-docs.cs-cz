---
title: Sdílená složka Azure pro fondy Azure Batch | Dokumenty společnosti Microsoft
description: Jak připojit sdílené složky Azure Files z výpočetních uzlů ve fondu Linux nebo Windows v Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: 156dad25af5abd1b4d5db32569faf09a23fadfb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022507"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Použití sdílené složky Azure s fondem dávek

[Azure Files](../storage/files/storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím protokolu Server Message Block (SMB). Tento článek obsahuje informace a příklady kódu pro připojení a použití sdílené složky Azure na výpočetních uzlech fondu. Příklady kódu používají sady Batch .NET a Python SDK, ale podobné operace můžete provádět pomocí jiných sad Batch SDK a nástrojů.

Batch poskytuje nativní podporu rozhraní API pro použití objektů BLOB služby Azure Storage ke čtení a zápisu dat. V některých případech však můžete chtít získat přístup ke sdílené složce Azure ze výpočetních uzlů fondu. Například máte starší úlohy, které závisí na sdílené složce SMB, nebo vaše úkoly potřebují přístup ke sdíleným datům nebo vytvoření sdíleného výstupu. 

## <a name="considerations-for-use-with-batch"></a>Důležité informace pro použití s batch

* Zvažte použití sdílené složky Azure, pokud máte fondy, které běží relativně nízký počet paralelních úloh. Zkontrolujte [cíle výkonu a škálování](../storage/files/storage-files-scale-targets.md) a zjistěte, jestli se mají používat soubory Azure (který používá účet Azure Storage) vzhledem k očekávané velikosti fondu a počtu souborů prostředků. 

* Sdílené složky Azure jsou [nákladově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a lze je nakonfigurovat s replikací dat do jiné oblasti, takže jsou globálně redundantní. 

* Sdílenou složku Azure můžete připojit souběžně z místního počítače.

* Viz také obecné [aspekty plánování](../storage/files/storage-files-planning.md) pro sdílené složky Azure.


## <a name="create-a-file-share"></a>Vytvoření sdílené složky

[Vytvořte sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v účtu úložiště, který je propojený s vaším dávkovým účtem, nebo v samostatném účtu úložiště.

## <a name="mount-a-share-on-a-windows-pool"></a>Připojení sdílené složky ve fondu Windows

Tato část obsahuje kroky a příklady kódu pro připojení a použití sdílené složky Azure ve fondu uzlů Windows. Další informace najdete v [dokumentaci](../storage/files/storage-how-to-use-files-windows.md) k připojení sdílené složky Azure v systému Windows. 

V aplikaci Batch je třeba připojit sdílenou složku při každém spuštění úlohy v uzlu systému Windows. V současné době není možné zachovat síťové připojení mezi úlohami v uzlech systému Windows.

Můžete například `net use` zahrnout příkaz pro připojení sdílené složky jako součást každého příkazového řádku úkolu. Chcete-li připojit sdílenou složku, jsou potřeba následující pověření:

* **Uživatelské jméno**\\\<: název\>účtu úložiště\\AZURE , například název*mystorageaccount* azure
* **Heslo** \<: StorageAccountKeyWhichEnds in==>, například *XXXXXXXXXXXXXXXXXXXXX==*

Následující příkaz připojí sdílení souborů *myfileshare* v účtu úložiště *mystorageaccountname* jako *Jednotka S:*

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Pro jednoduchost zde uvedené příklady předat pověření přímo v textu. V praxi důrazně doporučujeme spravovat přihlašovací údaje pomocí proměnných prostředí, certifikátů nebo řešení, jako je například Azure Key Vault.

Chcete-li zjednodušit operaci připojení, volitelně zachovat pověření na uzlech. Potom můžete připojit sdílenou složku bez pověření. Proveďte následující dva kroky:

1. Spusťte nástroj `cmdkey` příkazového řádku pomocí úlohy spuštění v konfiguraci fondu. To zachová pověření v každém uzlu systému Windows. Příkazový řádek úlohy zahájení je podobný:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Připojte sdílenou složku na každý uzel `net use`jako součást každého úkolu pomocí . Například následující příkazový řádek úkolu připojí sdílenou složku jako jednotku *S:.* Následuje příkaz nebo skript, který odkazuje na sdílenou složku. Pověření uložená v mezipaměti `net use`se používají při volání . Tento krok předpokládá, že používáte stejnou identitu uživatele pro úkoly, které jste použili při zahájení úkolu ve fondu, což není vhodné pro všechny scénáře.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Příklad jazyka C#
Následující příklad jazyka C# ukazuje, jak zachovat pověření ve fondu systému Windows pomocí úlohy spuštění. Název služby souboru úložiště a pověření úložiště jsou předány jako definované konstanty. Zde je počáteční úloha spuštěna pod standardním (neadministrátorem) účet automatického uživatele s rozsahem fondu.

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

Po uložení pověření použijte příkazové řádky úlohy k připojení sdílené složky a odkazování na sdílenou složku v operacích čtení nebo zápisu. Jako základní příklad příkazový řádek úkolu v následujícím fragmentu příkazu `dir` uvádí seznam souborů ve sdílené složce. Ujistěte se, že spustit každou úlohu úlohy pomocí stejné [identity uživatele,](batch-user-accounts.md) kterou jste použili ke spuštění úlohy spuštění ve fondu. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Připojení sdílené položky do fondu Linuxu

Sdílené složky Azure lze připojit v linuxových distribucích pomocí [klienta jádra CIFS](https://wiki.samba.org/index.php/LinuxCIFS). Následující příklad ukazuje, jak připojit sdílenou složku do fondu výpočetních uzlů Ubuntu 16.04 LTS. Pokud používáte jinou distribuci Linuxu, obecné kroky jsou podobné, ale použijte správce balíčků vhodný pro distribuci. Podrobnosti a další příklady najdete [v tématu použití souborů Azure s Linuxem](../storage/files/storage-how-to-use-files-linux.md).

Nejprve pod identitou uživatele správce `cifs-utils` nainstalujte balíček a vytvořte přípojný bod (například */mnt/MyAzureFileShare*) v místním souborovém systému. Složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale `/mnt` je běžné konvence k vytvoření ve složce. Ujistěte se, že nevytváříte přípojný bod přímo na `/mnt` (na Ubuntu) nebo `/mnt/resource` (na jiných distribucích).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Potom spusťte `mount` příkaz pro připojení sdílené složky a zadejte tato pověření:

* **Uživatelské**jméno \<:\>název_účtu storageaccount , například *mystoragename*
* **Heslo** \<: StorageAccountKeyWhichEnds in==>, například *XXXXXXXXXXXXXXXXXXXXX==*

Následující příkaz připojí sdílení souborů *myfileshare* v účtu úložiště *mystorageaccountname* na */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Pro jednoduchost zde uvedené příklady předat pověření přímo v textu. V praxi důrazně doporučujeme spravovat přihlašovací údaje pomocí proměnných prostředí, certifikátů nebo řešení, jako je například Azure Key Vault.

Ve fondu Linuxu můžete všechny tyto kroky kombinovat v jedné počáteční úloze nebo je spustit ve skriptu. Spusťte počáteční úlohu jako správce uživatele ve fondu. Nastavte počáteční úlohu čekat na úspěšné dokončení před spuštěním další úkoly ve fondu, které odkazují na sdílenou složku.

### <a name="python-example"></a>Příklad Pythonu

Následující příklad Pythonu ukazuje, jak nakonfigurovat fond Ubuntu pro připojení sdílené složky v počáteční úloze. Přípojkový bod, koncový bod sdílené složky a pověření úložiště jsou předávány jako definované konstanty. Počáteční úloha je spuštěna pod účtem automatického uživatele správce s rozsahem fondu.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Po nasazení sdílené složky a definování úlohy použijte sdílenou složku v řádcích příkazů úkolu. Následující základní příkaz například `ls` používá k vyseznamu souborů ve sdílené složce.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Další kroky

* Další možnosti čtení a zápisu dat v aplikaci Batch naleznete v [tématech Přehled dávkových funkcí](batch-api-basics.md) a [Zachovat úlohu a výstup úlohy](batch-task-output.md).

* Viz také sada nástrojů [Batch Loděnice,](https://github.com/Azure/batch-shipyard) která obsahuje [recepty loděnice](https://github.com/Azure/batch-shipyard/tree/master/recipes) pro nasazení souborových systémů pro úlohy kontejnerů Batch.
