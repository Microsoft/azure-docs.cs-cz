---
title: Spouštění úloh v rámci uživatelských účtů – Azure Batch | Dokumentace Microsoftu
description: Nakonfigurujte uživatelské účty pro spouštění úloh ve službě Azure Batch
services: batch
author: laurenhughes
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
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: ba64ecc334d93c8ff973345cfd10ed12436d3fb6
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813904"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Spouštění úloh v rámci uživatelských účtů ve službě Batch

Úlohu ve službě Azure Batch vždy běží pod účtem uživatele. Ve výchozím nastavení se úkoly spustí v rámci standardní uživatelské účty bez oprávnění správce. Tato výchozí nastavení účtu uživatele je obvykle dostatečné. Pro určité scénáře je však užitečné mít možnost při konfiguraci uživatelského účtu, pod kterou se má úloha spustit. Tento článek popisuje typy uživatelských účtů a způsob jejich konfigurace pro váš scénář.

## <a name="types-of-user-accounts"></a>Typy uživatelských účtů

Azure Batch poskytuje dva typy uživatelských účtů pro spouštění úloh:

- **Automatické uživatelské účty.** Automatické uživatelské účty jsou předdefinované uživatelské účty, které jsou automaticky vytvořeny pomocí služby Batch. Ve výchozím nastavení spustí úlohy pod účtem uživatele automaticky. Můžete nakonfigurovat uživatele automaticky specifikaci úlohou a oznamovala, které automaticky uživatelského účtu by měl spustit úlohu. Specifikace uživatele automaticky vám umožní určit úroveň ke zvýšení úrovně oprávnění a rozsah automaticky uživatelský účet, který se spustí úloha. 

- **Pojmenované uživatelský účet.** Při vytváření fondu můžete určit jeden nebo více účtů pojmenovaného uživatele pro fond. Na každý uzel ve fondu se vytvoří každý uživatelský účet. Kromě názvu účtu zadejte heslo k uživatelskému účtu, zvýšení úrovně a pro fondy Linux, privátní klíč SSH. Při přidání úlohy, můžete zadat pojmenované uživatelský účet, pod kterým se tento úkol spouštět.

> [!IMPORTANT] 
> Verze služby Batch 2017-01-01.4.0 zavádí narušující změně je nutné aktualizovat váš kód volat tuto verzi. Pokud se migrace kódu ze starší verze služby Batch, Všimněte si, že **runElevated** vlastnost je již nejsou podporovány v klientské knihovny rozhraní REST API nebo služby Batch. Pomocí nové **userIdentity** vlastnosti úlohy k určení úrovně zvýšení oprávnění. V části s názvem [aktualizujte svůj kód do nejnovější verze klientské knihovny Batch](#update-your-code-to-the-latest-batch-client-library) pro rychlé pokyny pro aktualizaci kódu Batch, pokud použijete jednu z knihoven klienta.
>
>

> [!NOTE] 
> Uživatelské účty popisovaných v tomto článku nepodporují protokol RDP (Remote Desktop) nebo Secure Shell (SSH), z bezpečnostních důvodů. 
>
> Připojení k uzlu se systémem Linux konfigurace virtuálního počítače přes SSH najdete v tématu [použití vzdálené plochy k virtuálnímu počítači s Linuxem v Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Připojení k uzlů se systémem Windows pomocí protokolu RDP najdete v tématu [připojit k virtuálnímu počítači s Windows serverem](../virtual-machines/windows/connect-logon.md).<br /><br />
> Připojení k uzlu prostřednictvím protokolu RDP s konfigurací cloudové služby najdete v tématu [povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Uživatelskému účtu přístup do souborů a adresářů

Automaticky uživatelský účet a účet uživatele s názvem mají přístup pro čtení a zápis do pracovního adresáře úkolu, sdílený adresář a adresáře úkoly s více instancemi. Oba typy účtů mají přístup pro čtení k adresářům přípravě spouštěcí a úloha.

Pokud úloha běží pod stejným účtem, který byl použit ke spuštění spouštěcího úkolu, úloha má přístup pro čtení a zápis do adresáře úkolu spuštění. Podobně pokud úlohy běží pod stejným účtem, který byl použit pro spouštění úkol přípravy úlohy, úloha má přístup pro čtení a zápis do adresáře úkolu přípravy úlohy. Pokud úloha běží pod jiným účtem než spouštěcí úkol nebo úkol přípravy úlohy, úloha má přístup jenom pro čtení do příslušného adresáře.

Další informace o přístupu k souborům a adresářům z úlohy najdete v tématu [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Přístup se zvýšeným oprávněním pro úlohy 

Uživatelský účet zvýšení úrovně označuje, zda je spuštěna úloha s přístup se zvýšeným oprávněním. Automatické uživatelský účet a účet s názvem uživatele můžete spustit pomocí přístup se zvýšeným oprávněním. Jsou dvě možnosti pro zvýšení úrovně:

- **Text NonAdmin:** Úloha běží jako standardní uživatel bez přístup se zvýšeným oprávněním. Výchozí úroveň zvýšení oprávnění pro uživatelský účet Batch je vždy **text NonAdmin**.
- **Správce:** Úloha běží jako uživatel s přístupem se zvýšenými oprávněními a funguje s úplnými oprávněními správce. 

## <a name="auto-user-accounts"></a>Automatické uživatelské účty

Ve výchozím nastavení úlohy spuštěny ve službě Batch v rámci automatického uživatelský účet jako standardní uživatel bez přístupu se zvýšenými oprávněními a s rozsahem úloh. Pokud specifikaci uživatele automaticky je nakonfigurovaná pro obor úloh, služba Batch vytvoří automaticky uživatelský účet pro tuto úlohu pouze.

Alternativa k úkolu oboru je rozsah fondu. Když uživatele automaticky specifikace pro úlohy je nakonfigurována pro rozsah fondu, je úloha spuštěna automaticky – uživatelský účet, který je k dispozici pro všechny úlohy ve fondu. Další informace o rozsahu fondu najdete v části s názvem spustit úkol jako uživatel automaticky s rozsahem fondu.   

Výchozí obor se liší na uzlech Windows a Linux:

- Na uzlech Windows úlohy spuštěny v rámci oboru úloha ve výchozím nastavení.
- Uzly s Linuxem, které jsou vždy spouštěny v rámci rozsahu fondu.

Existují čtyři možné konfigurace pro specifikaci uživatelů automaticky, každá z nich odpovídá jedinečný automaticky uživatelský účet:

- Přístup bez oprávnění správce s oborem úkolů (specifikace uživatele automaticky výchozí)
- Přístup správce (vyšší) s rozsahem úloh
- Přístup bez oprávnění správce s oborem fondu
- Přístup správce k oboru fondu

> [!IMPORTANT] 
> Úkoly spuštěné v oboru úlohy nemají de facto stane přístup jiných úloh na uzlu. Však uživatel s přístupem k účtu se zlými úmysly může toto omezení obejít, odešlete úlohu, která spustí s oprávněními správce a budou k dispozici další adresáře úkolu. Uživatel se zlými úmysly může také použití RDP nebo SSH k připojení k uzlu. Je důležité chránit přístup k vaší klíče účtu Batch, aby se zabránilo takové situaci. Pokud máte podezření, že váš účet možná ohrožené, nezapomeňte znovu vygenerovat klíče.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Spuštění úlohy jako uživatel automaticky s přístup se zvýšeným oprávněním

Pokud potřebujete spustit úlohu s přístup se zvýšeným oprávněním, můžete nakonfigurovat specifikaci automaticky uživateli pro oprávnění správce. Spouštěcí úkol může například potřebovat přístup se zvýšeným oprávněním k instalaci softwaru na uzlu.

> [!NOTE] 
> Obecně je nejvhodnější použít přístup se zvýšeným oprávněním pouze v případě potřeby. Doporučeným postupem udělení minimálních oprávnění potřebná k dosažení požadovaného výsledku. Například pokud spouštěcí úkol nainstaluje software pro aktuálního uživatele, ne pro všechny uživatele, bude pravděpodobně možné do Vyhněte se udělování přístupu se zvýšenými oprávněními na úlohy. Můžete nakonfigurovat uživatele automaticky specifikace oboru, bez oprávnění správce přístup pro všechny úlohy, které potřebujete ke spuštění pod stejným účtem, včetně spouštěcího úkolu fondu. 
>
>

Následující fragmenty kódu ukazují, jak nakonfigurovat uživatele automaticky specifikace. Příklady nastavení zvýšení úrovně na `Admin` a rozsah na `Task`. Úloha oboru je výchozí nastavení, ale je zde uveden příklad pro účely.

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Spuštění úlohy jako uživatel automaticky s rozsahem fondu

Při zřízení uzel dvě celého fondu automaticky uživatelské účty jsou vytvořeny na každém uzlu ve fondu, s přístup se zvýšeným oprávněním a druhou bez přístup se zvýšeným oprávněním. Nastavení oboru uživatele automaticky do fondu oboru pro daný úkol spustí úlohu v jednom z těchto dvou celého fondu automaticky uživatelských účtů. 

Pokud zadáte rozsah fondu pro automatické – uživatel, všechny úlohy, které běží s přístupem správce spustit pod stejným účtem uživatele automaticky celý fond. Podobně úkoly, které běží bez oprávnění správce také spouštět jedné úrovni fondu automaticky uživatelského účtu. 

> [!NOTE] 
> Jsou dva celého fondu automaticky uživatelské účty, samostatné účty. Úkoly spuštěné pod účtem správce fondu celou nemůžou sdílet data s úkoly spuštěné pod účtem, na standard a naopak. 
>
>

Běží pod stejným účtem uživatele automaticky výhodou je, že úlohy budou moct sdílet data s ostatními úlohami běžícími na stejném uzlu.

Tajné klíče mezi úkoly pro sdílení obsahu je jeden scénář, kde je užitečné spouštění úloh v jednom ze dvou celého fondu automaticky uživatelské účty. Předpokládejme například, že spouštěcí úkol je potřeba zřídit tajného kódu do uzlu, který můžete použít další úkoly. Můžete použít Windows Data Protection API (DPAPI), ale vyžaduje oprávnění správce. Místo toho můžete chránit tajný klíč na úrovni uživatele. Úlohy běžící pod stejným uživatelským účtem můžete přistupovat tajný kód bez přístup se zvýšeným oprávněním.

Jiný scénář, kde můžete chtít spustit úlohy v části Automatické uživatelský účet s rozsahem fondu je rozhraní MPI (Message Passing Interface) sdílené složky. Sdílenou složku MPI je užitečná při uzly, úlohy MPI potřebují spolupracovat na stejná data souboru. Hlavní uzel vytvoří sdílenou složku, která podřízené uzly můžete přistupovat, pokud jsou spuštěné pod stejným účtem uživatele automaticky. 

Následující fragment kódu nastaví oboru uživatele automaticky rozsah fondu pro úlohu v Batch .NET. Zvýšení úrovně je vynechán, takže spuštění pomocí standardní úrovni fondu automaticky uživatelského účtu.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Pojmenované uživatelské účty

Při vytváření fondu můžete definovat pojmenované uživatelské účty. Pojmenované uživatelský účet má název a heslo, které zadáte. Můžete zadat úroveň zvýšení oprávnění pro pojmenovaného uživatele účtu. Pro uzly s Linuxem můžete také zadat privátní klíč SSH.

Pojmenované uživatelský účet existuje na všech uzlech ve fondu a je k dispozici na všech úkolech běžících na těchto uzlech. Můžete definovat libovolný počet uživatelů s názvem pro fond. Při přidání úlohy nebo kolekci úloh, můžete zadat, že je úloha spuštěna v rámci jednoho pojmenované uživatelské účty definované ve fondu.

Pojmenované uživatelský účet je užitečné, pokud chcete spouštět všechny úlohy v rámci úlohy pomocí stejného uživatelského účtu, ale izolovaná od úkoly spuštěné v jiné úlohy ve stejnou dobu. Můžete například vytvořit pojmenovaného uživatele pro každou úlohu a spuštění každé úlohy úkoly pod tímto účtem pojmenovaného uživatele. Každá úloha pak můžete sdílet tajným kódem pomocí vlastní úlohy, ale ne s úkoly spuštěné v jiné úlohy.

Můžete také použít účet pojmenovanému uživateli spustit úlohu, která nastaví oprávnění, na externím prostředkům, jako jsou sdílené složky. S účtem pojmenovanému uživateli řídit identitu uživatele a tuto identitu uživatele můžete použít k nastavení oprávnění.  

Účty pojmenovanému uživateli povolit SSH bez hesla mezi uzly s Linuxem. Můžete použít účet pojmenovaného uživatele s Linuxovými uzly, které potřebujete pro vedení úkoly s více instancemi. Každý uzel ve fondu můžete spouštět úlohy pod účtem uživatele, který je definovaný na celý fond. Další informace o úkoly s více instancemi, naleznete v tématu [použít více\-instancí úlohy ke spouštění aplikací MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Vytvořit pojmenovaný uživatelských účtů

K vytvoření pojmenovaného uživatelských účtů ve službě Batch, přidejte kolekce uživatelských účtů do fondu. Následující fragmenty kódu ukazují, jak vytvořit pojmenovaný uživatelské účty v .NET, Java nebo Python. Tyto fragmenty kódu ukazují, jak vytvořit správce i bez oprávnění správce s názvem účty ve fondu. Příklady vytvoření fondů v konfiguraci cloudové služby, ale můžete použít stejný přístup při vytváření fondu Windows nebo Linux pomocí konfigurace virtuálního počítače.

#### <a name="batch-net-example-windows"></a>Příklad rozhraní .NET služby Batch (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
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

#### <a name="batch-net-example-linux"></a>Příklad rozhraní .NET služby Batch (Linux)

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


#### <a name="batch-java-example"></a>Příklad batch Java

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

#### <a name="batch-python-example"></a>Příklad v Pythonu služby batch

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Spustit úlohu s názvem uživatelského účtu s přístupem se zvýšenými oprávněními

Spustit úlohu jako se zvýšenými oprávněními uživatele, nastavte typ úkolu **UserIdentity** vlastnost s názvem uživatelský účet, který byl vytvořen s jeho **ElevationLevel** nastavenou na `Admin`.

Tento fragment kódu určuje, že má být úloha spuštěna pod účtem pojmenovaného uživatele. Tento pojmenovaný uživatelský účet byl definován ve fondu při vytváření fondu. V tomto případě byl vytvořen pojmenovaný uživatelský účet s oprávněními správce:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualizujte svůj kód do nejnovější verze knihovny klienta služby Batch

Verze služby Batch 2017-01-01.4.0 zavádí k zásadní změně nahrazení **runElevated** vlastnost k dispozici v dřívějších verzích s **userIdentity** vlastnost. Následující tabulka obsahuje jednoduché mapování, které můžete použít k aktualizaci vašeho kódu z předchozích verzí klientských knihoven.

### <a name="batch-net"></a>Batch .NET

| Pokud váš kód používá...                  | Aktualizujte ji, aby...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Nezadáno | Vyžaduje se žádné aktualizace                                                                                               |

### <a name="batch-java"></a>Batch Java

| Pokud váš kód používá...                      | Aktualizujte ji, aby...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Nezadáno | Vyžaduje se žádné aktualizace                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Pokud váš kód používá...                      | Aktualizujte ji, aby...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated` Nezadáno | Vyžaduje se žádné aktualizace                                                                                                                                  |


## <a name="next-steps"></a>Další postup

* Podrobný přehled služby Batch, najdete v části [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
