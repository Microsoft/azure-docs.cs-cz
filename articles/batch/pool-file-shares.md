---
title: Sdílená složka Azure pro fondy Azure Batch
description: Jak připojit sdílenou složku služby soubory Azure z výpočetních uzlů ve fondu Linux nebo Windows ve službě Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 0aa8c8f6dcf1114688e6abaf1a17f2e8af6fa4fe
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723489"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Použití sdílené složky Azure s fondem služby Batch

[Soubory Azure](../storage/files/storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím protokolu SMB (Server Message Block). Tento článek poskytuje informace a příklady kódu pro připojení a používání sdílené složky Azure ve fondech výpočetních uzlů. Příklady kódu používají sady SDK Batch .NET a Python, ale podobné operace můžete provádět pomocí dalších sad SDK a nástrojů sady Batch.

Batch poskytuje nativní podporu rozhraní API pro použití objektů blob Azure Storage ke čtení a zápisu dat. V některých případech ale můžete chtít získat přístup ke sdílené složce Azure z výpočetních uzlů fondu. Například máte starší verzi úlohy, která závisí na sdílené složce SMB, nebo vaše úkoly potřebují získat přístup ke sdíleným datům nebo vytvořit sdílený výstup. 

## <a name="considerations-for-use-with-batch"></a>Pokyny pro použití s Batch

* Pokud máte fondy, které spouštějí relativně nízký počet paralelních úloh, zvažte použití sdílené složky Azure. Zkontrolujte [cíle výkonu a škálování](../storage/files/storage-files-scale-targets.md) , abyste zjistili, jestli by se měly používat soubory Azure (které používají účet Azure Storage) s ohledem na očekávanou velikost fondu a počet souborů prostředků. 

* Sdílené složky Azure jsou [nákladově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a je možné je nakonfigurovat pomocí replikace dat do jiné oblasti, takže jsou globálně redundantní. 

* Sdílenou složku Azure můžete připojit souběžně z místního počítače.

* Viz také obecné [požadavky plánování](../storage/files/storage-files-planning.md) pro sdílené složky Azure.


## <a name="create-a-file-share"></a>Vytvoření sdílené složky

[Vytvořte sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v účtu úložiště, který je propojený s vaším účtem Batch, nebo v samostatném účtu úložiště.

## <a name="mount-a-share-on-a-windows-pool"></a>Připojení sdílené složky ve fondu Windows

V této části najdete postup a příklady kódu pro připojení a používání sdílené složky Azure ve fondu uzlů Windows. Další informace najdete v [dokumentaci](../storage/files/storage-how-to-use-files-windows.md) k připojení sdílené složky Azure ve Windows. 

V dávce je potřeba připojit sdílenou složku pokaždé, když je úloha spuštěná na uzlu Windows. V současné době není možné zachovat síťové připojení mezi úlohami v uzlech systému Windows.

Například zahrňte `net use` příkaz pro připojení sdílené složky jako součást každého příkazového řádku úkolu. Pro připojení sdílené složky jsou potřeba následující přihlašovací údaje:

* **Uživatelské jméno**: Azure \\ \< storageaccountname \> , například Azure \\ *mystorageaccountname*
* **Heslo**: \< StorageAccountKeyWhichEnds in = =>, například *XXXXXXXXXXXXXXXXXXXXX = =*

Následující příkaz připojí sdílenou složku *myfileshare* v účtu úložiště *mystorageaccountname* jako jednotku *S:* .

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

V případě jednoduchosti tyto příklady přihlašovací údaje předají přímo v textu. V praxi důrazně doporučujeme spravovat přihlašovací údaje pomocí proměnných prostředí, certifikátů nebo řešení, jako je Azure Key Vault.

Chcete-li zjednodušit operaci připojení, volitelně zachovejte přihlašovací údaje na uzlech. Pak můžete sdílenou složku připojit bez přihlašovacích údajů. Proveďte následující dva kroky:

1. Spusťte `cmdkey` Nástroj příkazového řádku pomocí spouštěcího úkolu v konfiguraci fondu. Tím se uchovávají přihlašovací údaje na jednotlivých uzlech systému Windows. Příkazový řádek spouštěcího úkolu je podobný následujícímu:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Připojte sdílenou složku na každý uzel jako součást každého úkolu pomocí `net use` . Například následující příkazový řádek úkolu připojí sdílenou složku jako jednotku *S:* . Za tímto by následoval příkaz nebo skript, který odkazuje na sdílenou složku. Přihlašovací údaje uložené v mezipaměti se používají při volání metody `net use` . Tento krok předpokládá, že používáte stejnou identitu uživatele pro úlohy, které jste použili v počátečním úkolu ve fondu, což není vhodné pro všechny scénáře.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Příklad jazyka C#
Následující příklad jazyka C# ukazuje, jak zachovat přihlašovací údaje ve fondu Windows pomocí spouštěcího úkolu. Název souborové služby úložiště a přihlašovací údaje úložiště se předávají jako definované konstanty. Tady se spustí spouštěcí úkol pod standardním uživatelským účtem (bez správce) s oborem fondu.

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

Po uložení přihlašovacích údajů použijte příkazový řádek úkolu pro připojení sdílené složky a odkazování sdílené složky při operacích čtení nebo zápisu. Základní příklad: příkazový řádek úlohy v následujícím fragmentu kódu používá `dir` příkaz k vypsání souborů ve sdílené složce. Ujistěte se, že jste spustili každou úlohu úlohy pomocí stejné [identity uživatele](batch-user-accounts.md) , kterou jste použili ke spuštění spouštěcího úkolu ve fondu. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Připojení sdílené složky v rámci fondu Linux

Sdílené složky Azure je možné připojit v rámci distribucí systému Linux pomocí [klienta jádra CIFS](https://wiki.samba.org/index.php/LinuxCIFS). Následující příklad ukazuje, jak připojit sdílenou složku ve fondu výpočetních uzlů Ubuntu 16,04 LTS. Pokud používáte jinou distribuci systému Linux, je obecný postup podobný, ale použijte Správce balíčků, který je vhodný pro distribuci. Podrobnosti a další příklady najdete v tématu [použití souborů Azure se systémem Linux](../storage/files/storage-how-to-use-files-linux.md).

Nejprve v rámci identity uživatele správce nainstalujte `cifs-utils` balíček a vytvořte přípojný bod (například */mnt/MyAzureFileShare*) v místním systému souborů. Složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je to obvyklá konvence, kterou je možné vytvořit do `/mnt` složky. Ujistěte se, že nechcete vytvořit přípojný bod přímo v umístění `/mnt` (na Ubuntu) nebo `/mnt/resource` (v jiných distribucích).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Pak spusťte `mount` příkaz pro připojení sdílené složky a zadejte tyto přihlašovací údaje:

* **Uživatelské jméno**: \< storageaccountname \> , například *mystorageaccountname*
* **Heslo**: \< StorageAccountKeyWhichEnds in = =>, například *XXXXXXXXXXXXXXXXXXXXX = =*

Následující příkaz připojí sdílení souborů *myfileshare* v účtu úložiště *mystorageaccountname* na adrese */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

V případě jednoduchosti tyto příklady přihlašovací údaje předají přímo v textu. V praxi důrazně doporučujeme spravovat přihlašovací údaje pomocí proměnných prostředí, certifikátů nebo řešení, jako je Azure Key Vault.

Ve fondu Linux můžete všechny tyto kroky zkombinovat do jednoho spouštěcího úkolu nebo je spouštět ve skriptu. Spusťte spouštěcí úkol jako uživatel s oprávněním správce ve fondu. Než spustíte další úlohy ve fondu, který odkazuje na sdílenou složku, nastavte svůj spouštěcí úkol na dokončit úspěšně.

### <a name="python-example"></a>Příklad Pythonu

Následující příklad Pythonu ukazuje, jak nakonfigurovat fond Ubuntu pro připojení sdílené složky v rámci spouštěcího úkolu. Jako definované konstanty se jako přípojný bod, koncový bod sdílení souborů a přihlašovací údaje úložiště předávají. Spouštěcí úkol běží pod účtem správce s oborem fondu.

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

Po připojení sdílené složky a definování úlohy použijte sdílenou složku v příkazovém řádku úkolu. Například následující základní příkaz používá `ls` k vypsání souborů ve sdílené složce soubor.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Další kroky

* Další možnosti čtení a zápisu dat ve službě Batch najdete v tématu [Přehled funkcí Batch](batch-api-basics.md) a [trvalý výstup úloh a úkolů](batch-task-output.md).

* Viz také sada [Batch pro dávkové](https://github.com/Azure/batch-shipyard) zpracování, která zahrnuje [recepty v loděnicích](https://github.com/Azure/batch-shipyard/tree/master/recipes) pro nasazení systémů souborů pro úlohy kontejnerů služby Batch.
