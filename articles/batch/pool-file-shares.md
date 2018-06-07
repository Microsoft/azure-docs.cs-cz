---
title: Sdílenou složku Azure pro fondy Azure Batch | Microsoft Docs
description: Postup připojení Azure Files složky z výpočetních uzlů ve fondu Linux nebo Windows v Azure Batch.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 88d7c0d033d7b517a396df27468de8be7ae20be9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811725"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Použijte sdílenou složku Azure k fondu služby Batch

[Soubory Azure](../storage/files/storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes protokol Server Message Block (SMB). Tento článek obsahuje informace a ukázky kódu pro připojení a používá sdílenou složku Azure na výpočetních uzlech fondu. Příklady kódu pomocí rozhraní Batch .NET a Python SDK, ale můžete provádět pomocí jiných nástrojů a SDK služby Batch podobnými operacemi.

Batch poskytuje nativní podporu rozhraní API pro použití ke čtení a zápis dat objektů BLOB služby Azure Storage. V některých případech můžete chtít přístup Azure sdílené složky z fondu výpočetních uzlů. Například máte starší verze pracovních úloh, které závisí na sdílené složce SMB nebo úlohami potřeba přistupovat k sdíleným datům nebo vytvoření sdíleného výstupu. 

## <a name="considerations-for-use-with-batch"></a>Důležité informace pro použití se službou Batch

* Zvažte použití sdílenou složku Azure, když máte fondy, které spustit relativně nízký počet paralelních úloh. Zkontrolujte [výkonu a možností škálování cíle](../storage/files/storage-files-scale-targets.md) k určení, pokud by měl použít Azure soubory (které používá účet úložiště Azure), zadaná velikost očekávané fondu a počet souborů asset. 

* Sdílené složky Azure jsou [cenově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a může být nakonfigurovaná s daty na jiné oblasti replikace proto jsou globálně redundantní. 

* Sdílenou složku Azure současně můžete připojit z místního počítače.

* Viz také Obecné [plánování](../storage/files/storage-files-planning.md) pro Azure sdílené složky.


## <a name="create-a-file-share"></a>Vytvoření sdílené složky

[Vytvoření sdílené složky](../storage/files/storage-how-to-create-file-share.md) v účtu úložiště, který je propojený s vaším účtem Batch, nebo účet samostatného úložiště.

## <a name="mount-a-share-on-a-windows-pool"></a>Připojit sdílenou složku ve fondu Windows

Tato část obsahuje kroky a příklady kódu připojit a používat soubor Azure sdílenou složkou na fond uzlů Windows. Další informace najdete v článku [dokumentace](../storage/files/storage-how-to-use-files-windows.md) pro připojení Azure sdílení souborů v systému Windows. 

Ve službě Batch budete muset připojit sdílenou složku pokaždé, když úloha běží na uzlu systému Windows. V současné době není možné uchovávat síťové připojení mezi úlohy v uzlech systému Windows.

Příklady `net use` příkazu připojit sdílenou složku v rámci jednotlivých úloh příkazového řádku. Připojení sdílené složky, jsou potřeba následující pověření:

* **Uživatelské jméno**: AZURE\\\<storageaccountname\>, například AZURE\\*mystorageaccountname*
* **Heslo**: < StorageAccountKeyWhichEnds v == >, například *XXXXXXXXXXXXXXXXXXXXX ==*

Tento příkaz připojí sdílenou *myfileshare* v účtu úložiště *mystorageaccountname* jako *S:* jednotky:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Pro jednoduchost předejte příklady zde pověření přímo v textu. V praxi důrazně doporučujeme Správa pověření pomocí proměnné prostředí, certifikáty nebo řešení, jako je Azure Key Vault.

Pro zjednodušení operace připojení, volitelně zachovat přihlašovací údaje na uzlech. Pak můžete připojit sdílenou složku bez přihlašovacích údajů. Proveďte následující dva kroky:

1. Spustit `cmdkey` nástroj příkazového řádku pomocí spouštěcí úkol v konfiguraci fondu. To trvá přihlašovací údaje v každém uzlu systému Windows. Je podobná spuštění úloh příkazového řádku:

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. Připojit sdílenou složku na každém uzlu v rámci jednotlivých úloh pomocí `net use`. Například následující příkazový řádek úkolu připojí jako sdílené složky *S:* jednotky. To by pak příkaz či skript, který odkazuje na sdílenou složku. Přihlašovací údaje v mezipaměti se používají ve volání `net use`. Tento krok předpokládá, že používáte stejnou identitu uživatele pro úlohy, které jste použili v úloze pro spuštění ve fondu, která není vhodná pro všechny scénáře.

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>Příklad jazyka C#
Následující příklad jazyka C# ukazuje, jak k uchování přihlašovacích údajů ve Windows fondu pomocí spouštěcí úkol. Název souboru služby úložiště a přihlašovací údaje úložiště jsou předány jako definované konstanty. Zde spouštěcí úkol běží pod účtem standardní auto uživatel (bez oprávnění správce) s oborem fondu.

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

Po uložení pověření, pomocí vaší příkazové řádky úkolu připojit sdílenou složku a odkaz sdílenou složku v pro čtení nebo operací zápisu. Jako základní příklad používá úlohu příkazového řádku v následující fragment kódu `dir` příkaz pro vytvoření seznamu souborů ve sdílené složce. Zajistěte, aby běžela každý úkol používající stejný [identitu uživatele](batch-user-accounts.md) použitý ke spuštění spouštěcího úkolu ve fondu. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Připojit sdílenou složku ve fondu Linux

Sdílené složky Azure může být připojen v distribucí Linux pomocí [CIFS jádra klienta](https://wiki.samba.org/index.php/LinuxCIFS). Následující příklad ukazuje, jak připojit sdílenou složku ve fondu Ubuntu 16.04 LTS výpočetních uzlů. Pokud chcete použít jiný distribuční Linux, obecné kroky jsou podobné, ale pomocí vhodné pro distribuci balíčku správce. Podrobnosti a další příklady najdete v tématu [soubory pomocí Azure s Linuxem](../storage/files/storage-how-to-use-files-linux.md).

Nejdřív pod identitou správce uživatele, nainstalujte `cifs-utils` balíčků a vytvoření přípojného bodu (například */mnt/MyAzureFileShare*) v místním systému souborů. Složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je běžné konvence na vytvořte ji v části `/mnt` složky. Ujistěte se, není k vytvoření přípojného bodu přímo na `/mnt` (na Ubuntu) nebo `/mnt/resource` (na jiných distribuce).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Potom spusťte `mount` příkaz pro připojení sdílené složky, poskytuje tyto přihlašovací údaje:

* **Uživatelské jméno**: \<storageaccountname\>, například *mystorageaccountname*
* **Heslo**: < StorageAccountKeyWhichEnds v == >, například *XXXXXXXXXXXXXXXXXXXXX ==*

Tento příkaz připojí sdílenou *myfileshare* v účtu úložiště *mystorageaccountname* v */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Pro jednoduchost předejte příklady zde pověření přímo v textu. V praxi důrazně doporučujeme Správa pověření pomocí proměnné prostředí, certifikáty nebo řešení, jako je Azure Key Vault.

Linux fondu můžete sloučit všechny tyto kroky v jednom spouštěcí úkol nebo spustit ve skriptu. Spusťte úlohu spustit jako uživatel s oprávněním správce ve fondu. Nastavte počáteční úlohu čekání na dokončení úspěšně před spuštěním další úkoly ve fondu, které odkazují na sdílenou složku.

### <a name="python-example"></a>Příklad Python

Následující příklad Python ukazuje postup konfigurace fondu Ubuntu připojit sdílenou složku v spouštěcí úkol. Přípojný bod, koncový bod souborové sdílené složky a přihlašovací údaje úložiště jsou předány jako definované konstanty. Spouštěcí úkol běží pod účtem uživatele automaticky správce s oborem fondu.

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

Po připojení sdílené složky a definování úlohu, použijte sdílenou složku ve vašem příkazové řádky úkolu. Například následující příkaz základní používá `ls` pro vytvoření seznamu souborů ve sdílené složce.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Další postup

* Další možnosti pro čtení a zápis dat ve službě Batch najdete v tématu [přehled funkcí Batch](batch-api-basics.md) a [zachovat výstup úlohy a úkolů](batch-task-output.md).

* Viz také [loděnice Batch](https://github.com/Azure/batch-shipyard) nástrojů, která zahrnuje [loděnice recepty](https://github.com/Azure/batch-shipyard/tree/master/recipes) nasadit systémy souborů pro kontejner úloh služby Batch.