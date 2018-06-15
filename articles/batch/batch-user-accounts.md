---
title: Spuštění úlohy v části uživatelské účty ve službě Azure Batch | Microsoft Docs
description: Konfigurace uživatelských účtů pro spuštění úloh v Azure Batch
services: batch
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.openlocfilehash: 1b9c0514e93fa89f8776d830ef242fc4963a6f7b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316466"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Spuštění úlohy v části uživatelské účty ve službě Batch

Úloha v Azure Batch je vždy spuštěna pod uživatelským účtem. Ve výchozím nastavení úlohy spouštěny pod standardní uživatelské účty, bez oprávnění správce. Tato výchozí nastavení účtu uživatele je obvykle dostatečné. Pro určité scénáře je však vhodné, když ji moct konfigurovat uživatelský účet, pod kterou chcete spustit úlohu. Tento článek popisuje typy uživatelských účtů a způsob jejich konfigurace pro váš scénář.

## <a name="types-of-user-accounts"></a>Typy uživatelských účtů

Azure Batch poskytuje dva typy uživatelských účtů pro spuštění úlohy:

- **Auto uživatelské účty.** Auto uživatelské účty jsou předdefinované uživatelské účty, které jsou automaticky vytvořené službou Batch. Ve výchozím nastavení úlohy spouštěny pod účtem uživatele automaticky. Specifikace automaticky uživatele pro úlohu k označení, pod které automaticky uživatelský účet bude úloha spuštěna, můžete nakonfigurovat. Specifikace uživatele automaticky vám umožní určit úroveň zvýšení oprávnění a rozsah automaticky uživatelský účet, který se spustí úlohu. 

- **Pojmenované uživatelský účet.** Při vytváření fondu můžete určit jeden nebo více účtů pojmenovanému uživateli pro fond. Každý uživatelský účet je vytvořen na všech uzlech fondu. Kromě název účtu zadejte heslo k uživatelskému účtu, zvýšení úrovně a pro Linux fondy, privátní klíč SSH. Když přidáte úlohu, můžete s názvem uživatelský účet, pod kterou by měla spouštět tuto úlohu.

> [!IMPORTANT] 
> Verze služby Batch 2017-01-01.4.0 zavádí narušující změně, která je nutné aktualizovat na volání této verze. Pokud se migrace kódu ze starší verze služby Batch, Všimněte si, že **runElevated** vlastnost již není podporována v knihovny klienta REST API nebo dávce. Použít novou **userIdentity** vlastnosti úlohy k určení zvýšení úrovně. Najdete v části s názvem [kódu aktualizovat na nejnovější klientské knihovny Batch](#update-your-code-to-the-latest-batch-client-library) pro rychlé pokyny pro aktualizaci kódu dávky, pokud použijete jednu z knihovny klienta.
>
>

> [!NOTE] 
> Uživatelské účty popsané v tomto článku nepodporují protokol RDP (Remote Desktop) nebo Secure Shell (SSH), z bezpečnostních důvodů. 
>
> Chcete-li připojit k uzlu se systémem Linux konfigurace virtuálního počítače pomocí protokolu SSH, přečtěte si téma [pomocí vzdálené plochy pro virtuální počítač s Linuxem v Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Pro připojení k uzly s Windows pomocí protokolu RDP, najdete v části [připojit k virtuální počítač Windows serveru](../virtual-machines/windows/connect-logon.md).<br /><br />
> Chcete-li připojit k uzlu se systémem konfigurace cloudové služby prostřednictvím protokolu RDP, přečtěte si téma [povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Uživatelskému účtu přístup do souborů a adresářů

Auto uživatelský účet a s názvem uživatelského účtu mají přístup pro čtení a zápis pracovního adresáře úkolu, sdíleného adresáře a adresář úlohy s více instancemi. Oba typy účtů mají přístup pro čtení k adresáři pro spuštění a úlohy přípravy.

Pokud úloha běží pod stejným účtem, který byl použit ke spuštění spouštěcí úkol, úloha má přístup pro čtení a zápis do adresáře úkolu spuštění. Podobně pokud úlohy běží pod stejným účtem, který byl použit pro spuštění úkol přípravy úlohy, úlohy má přístup pro čtení a zápis do adresáře úkolu přípravy úlohy. Pokud úloha běží pod jiným účtem než spouštěcí úkol nebo úkol přípravy úlohy, úlohy má přístup jenom pro čtení do příslušného adresáře.

Další informace o přístup k souborů a adresářů z úlohy najdete v tématu [rozsáhlé paralelní vývoj výpočetní řešení pomocí služby Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Přístup se zvýšeným oprávněním pro úlohy 

Uživatelský účet zvýšení úrovně určuje, zda úloha běží se zvýšenými oprávněními přístup. Auto uživatelský účet a s názvem uživatelského účtu můžete spustit s oprávněním vyšší úrovně přístupu. Jsou dvě možnosti pro zvýšení úrovně:

- **NonAdmin:** tato úloha se spustí jako standardní uživatel bez přístup se zvýšeným oprávněním. Výchozí úroveň zvýšení oprávnění pro uživatelský účet Batch je vždy **NonAdmin**.
- **Správce:** úloha běží jako uživatel s oprávněním vyšší úrovně přístupu a funguje s úplnými oprávněními správce. 

## <a name="auto-user-accounts"></a>Auto uživatelské účty

Ve výchozím nastavení úlohy spuštěny v dávce v rámci automatického uživatelský účet, jako standardní uživatel bez přístup se zvýšeným oprávněním a s oborem úloh. V případě specifikace uživatele automaticky nastavena pro obor úloh, služba Batch vytvoří automaticky uživatelský účet pro tuto úlohu jenom.

Alternativa k oboru úloh je fond oboru. Při automatické uživatele specifikace pro úlohy je nakonfigurován pro obor fondu, je úloha spuštěna automaticky uživatelský účet, který je k dispozici pro všechny úlohy ve fondu. Další informace o fondu oboru, najdete v části s názvem [spustit úlohu jako uživatel automaticky s oborem fondu](#run-a-task-as-the-autouser-with-pool-scope).   

Výchozí obor se liší v uzlech systému Windows a Linux:

- Na uzlech Windows úlohy spuštěny v rámci oboru úloha ve výchozím nastavení.
- Uzly Linux vždy spuštěný fondu oboru.

Existují čtyři možné konfigurace pro uživatele automaticky specifikace, každý z nich odpovídá jedinečný auto uživatelský účet:

- Přístup bez oprávnění správce s oborem úloh (specifikace uživatele automaticky výchozí)
- Přístup správce (zvýšenými) s oborem úloh
- Přístup bez oprávnění správce s oborem fondu
- Přístup správce k oboru fondu

> [!IMPORTANT] 
> Úkoly spuštěné v rámci oboru úloh nemají fakticky přístup k jiné úlohy na uzlu. Ale uživatel se zlými úmysly s přístupem k účtu může toto omezení obejít tím, že odešlete úlohu, která spustí s oprávněními správce a přistupuje k jiných adresářích úkolu. Uživatel se zlými úmysly může také pomocí protokolu RDP nebo SSH připojit k uzlu. Je důležité chránit přístup k klíče účtu Batch, aby se zabránilo takové situaci. Pokud se domníváte, že váš účet ohrožený, nezapomeňte znovu vygenerovat klíče.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Spustit úlohu jako auto uživatel s oprávněním vyšší úrovně přístupu

Když potřebujete spuštění úlohy se zvýšenými oprávněními přístupu můžete nakonfigurovat specifikace automaticky uživatel oprávnění správce. Spouštěcí úkol může například potřebovat přístup se zvýšeným oprávněním k instalaci softwaru na uzlu.

> [!NOTE] 
> Obecně platí je nejvhodnější použít přístup se zvýšeným oprávněním pouze v případě potřeby. Doporučeným udělení minimální oprávnění potřebná k dosažení požadovaném výsledku. Například pokud spouštěcí úkol nainstaluje software pro aktuálního uživatele, ne pro všechny uživatele, možná nebudete moct Vyhněte se udělování zvýšenými přístup k úlohám. Můžete nakonfigurovat specifikace automaticky uživatele pro přístup fondu oboru a bez oprávnění správce pro všechny úlohy, které je třeba spustit pod stejným účtem, včetně spouštěcího úkolu. 
>
>

Následující fragmenty kódu ukazují, jak nakonfigurovat specifikace uživatele automaticky. Příklady nastavit zvýšení oprávnění na úrovni `Admin` a obor pro `Task`. Úloha obor je výchozí nastavení, ale je zde uveden z důvodu příklad.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Spustit úlohu jako uživatelé automaticky s oborem fondu

Pokud uzel je zřízený, dvě celou fondu automaticky uživatelské účty jsou vytvořeny na každém uzlu ve fondu, s oprávněním vyšší úrovně přístupu a jeden bez přístup se zvýšeným oprávněním. Nastavení oboru uživatele automaticky do fondu rozsahu pro danou úlohu spustí úlohu v rámci jednoho z těchto dvou celou fondu automaticky uživatelských účtů. 

Pokud zadáte rozsah fondu pro všechny úlohy, které spustí s přístupem správce spustit pod stejným účtem auto uživatele v celé fondu automaticky uživatel. Podobně úlohy, které běží bez oprávnění správce také spouštět pod účtem jednoho fondu celou auto uživatele. 

> [!NOTE] 
> Dva účty auto uživatele v celé fondu jsou samostatné účty. Úlohy spuštěné v rámci účtu správce fondu celou nelze sdílet data s úkoly spuštěné pod účtem, standard a naopak. 
>
>

Výhoda spuštění pod stejnou auto uživatelský účet je moci sdílet data s další úkoly spuštěné na stejném uzlu úlohy.

Jeden scénář, kde spuštěné úkoly v jednom ze dvou celou fondu automaticky uživatelské účty je užitečná sdílení tajných klíčů mezi úlohy je. Předpokládejme například, že spouštěcí úkol potřebuje ke zřízení tajný klíč do uzlu, který můžete použít jiné úlohy. Můžete použít rozhraní Windows Data Protection API (DPAPI), ale vyžaduje oprávnění správce. Místo toho můžete chránit tajný klíč na úrovni uživatele. Úkoly spuštěné pod stejným účtem uživatele mají přístup k tajný klíč bez přístup se zvýšeným oprávněním.

Další možností místo spouštět úlohy v části Automatické uživatelský účet s oborem fondu je rozhraní MPI (Message Passing) sdílené složky. MPI sdílené složky je užitečné, když uzlů, úlohy MPI potřebují spolupracovat na stejné datové soubory. Z hlavního uzlu vytvoří složku, která podřízených uzlů může získat přístup, pokud běží pod stejným účtem uživatele automaticky. 

Následující fragment kódu oboru uživatele automaticky nastaví na rozsah fondu pro úlohu v Batch .NET. Zvýšení úrovně je vynechán, proto je úloha spuštěna standardní úrovni fondu automaticky uživatelský účet.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>S názvem uživatelské účty

Při vytváření fondu můžete definovat pojmenované uživatelské účty. Pojmenované uživatelský účet má název a heslo, které zadáte. Můžete zadat úroveň zvýšení oprávnění pro účet s názvem uživatele. Pro Linux uzly můžete zadat taky privátní klíč SSH.

Pojmenované uživatelský účet existuje na všech uzlech ve fondu a je k dispozici pro všechny úkoly spuštěné na těchto uzlech. Můžete definovat libovolný počet jmenovaní uživatelé pro fond. Když přidáte úkolu nebo kolekce úloh, můžete zadat, že tato úloha se spustí v rámci jednoho z pojmenované uživatelské účty definované ve fondu.

S názvem uživatelského účtu je užitečné, když chcete spustit všechny úlohy pro úlohu pod stejným účtem uživatele, ale je izolovat z úloh spuštěných ve jiné úlohy ve stejnou dobu. Můžete například vytvořit pojmenovanému uživateli pro každou úlohu a spouštět úlohy každou úlohu v části s názvem uživatelského účtu. Každá úloha sdílet tajný klíč s její vlastní úkoly, ale nikoli s úkoly spuštěné v jiné úlohy.

S názvem uživatelského účtu můžete taky spustit úlohu, která nastaví oprávnění na externím prostředkům, jako jsou sdílené složky. Pomocí pojmenovaného uživatelského účtu řídit identity uživatele a můžete použít tuto identitu uživatele a nastavit oprávnění.  

S názvem uživatelské účty umožňují bez heslo SSH mezi uzly Linux. Můžete vytvořit s názvem uživatelského účtu s Linux uzly, které je potřeba spustit úkoly s více instancemi. Každý uzel ve fondu můžete spouštět úlohy pod uživatelským účtem definované na celý fond. Další informace o úkoly s více instancemi najdete v tématu [použít více\-instance úlohy ke spuštění aplikací MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Vytvořit s názvem uživatelské účty

Pokud chcete vytvořit s názvem uživatelské účty ve službě Batch, přidejte do fondu kolekce uživatelských účtů. Následující fragmenty kódu ukazují, jak můžete vytvořit s názvem uživatelské účty v rozhraní .NET, Java a Python. Tyto fragmenty kódu ukazují, jak vytvořit správce i bez oprávnění správce. s názvem účty ve fondu. Příklady vytvořit fondy pomocí konfigurace cloudové služby, ale můžete použít ve stejný přístup při vytváření fondu systému Windows nebo Linux pomocí konfigurace virtuálního počítače.

#### <a name="batch-net-example-windows"></a>Příklad batch .NET (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                                         
    virtualMachineSize: "small",                                                
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Příklad batch .NET (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Příklad Java batch

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Příklad batch Python

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Spustit úlohu v části s názvem uživatelského účtu s oprávněním vyšší úrovně přístupu

Chcete-li spustit úlohu jako uživatel s oprávněním vyšší úrovně, nastavte úkolu **identity uživatele** vlastnost s názvem uživatelského účtu, který byl vytvořen s jeho **ElevationLevel** vlastnost nastavena na hodnotu `Admin`.

Tento fragment kódu určuje, zda má být úloha spuštěna pod účtem s názvem uživatele. Tento účet pojmenovanému uživateli definované ve fondu při vytvoření fondu. V takovém případě byl vytvořen s názvem uživatelský účet s oprávněními správce:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualizovat na nejnovější klientské knihovny Batch kódu

Verze služby Batch 2017-01-01.4.0 zavádí narušující změně nahrazení **runElevated** vlastnost k dispozici v dřívějších verzích se **userIdentity** vlastnost. V následujících tabulkách jsou jednoduché mapování, která můžete použít k aktualizaci kódu z dřívějších verzí knihovny klienta.

### <a name="batch-net"></a>Batch .NET

| Pokud váš kód používá...                  | Aktualizujte, aby...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Není zadaná. | Bez aktualizace                                                                                               |

### <a name="batch-java"></a>Batch Java

| Pokud váš kód používá...                      | Aktualizujte, aby...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Není zadaná. | Bez aktualizace                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Pokud váš kód používá...                      | Aktualizujte, aby...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated` Není zadaná. | Bez aktualizace                                                                                                                                  |


## <a name="next-steps"></a>Další postup

### <a name="batch-forum"></a>Fórum batch

[Fóru služby Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch) na webu MSDN je skvělým místem popisují Batch a klást otázky týkající se služby. HEAD na přes pro užitečné definovaného příspěvky a při jejich vzniku při sestavování řešení Batch zveřejněte svoje otázky.