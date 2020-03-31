---
title: Spouštění úloh v rámci uživatelských účtů – Azure Batch
description: Je užitečné, abyste mohli nakonfigurovat uživatelský účet, pod kterým chcete úlohu spustit. Seznamte se s typy uživatelských účtů a jejich konfigurací.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/18/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: fee3dc764d2052185160a4ba6b3d70854c54eeac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252269"
---
> [!NOTE] 
> Uživatelské účty popsané v tomto článku se liší od uživatelských účtů používaných pro protokol RDP (RDP) nebo zabezpečené prostředí (SSH) z bezpečnostních důvodů. 
>
> Pokud se chcete připojit k uzlu s konfigurací virtuálního počítače Linux přes SSH, přečtěte si informace [o použití vzdálené plochy k virtuálnímu počítači SM s Linuxem v Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Pokud se chcete připojit k uzlům se systémem Windows přes RDP, přečtěte si informace [o připojení k virtuálnímu virtuálnímu virtuálnímu virtuálnímu zařízení se systémem Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Pokud se chcete připojit k uzlu, na který běží konfigurace cloudové služby prostřednictvím protokolu RDP, přečtěte si informace [o povolení připojení ke vzdálené ploše pro roli v Cloudových službách Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Spuštění úloh v části uživatelské účty v části Dávka

Úloha v Azure Batch se vždy spouští pod uživatelským účtem. Ve výchozím nastavení jsou úlohy spuštěny pod standardními uživatelskými účty bez oprávnění správce. Tato výchozí nastavení uživatelského účtu jsou obvykle dostatečná. Pro určité scénáře je však užitečné konfigurovat uživatelský účet, pod kterým chcete úlohu spustit. Tento článek popisuje typy uživatelských účtů a jak je můžete nakonfigurovat pro váš scénář.

## <a name="types-of-user-accounts"></a>Typy uživatelských účtů

Azure Batch poskytuje dva typy uživatelských účtů pro spouštění úloh:

- **Účty automatického uživatele.** Účty automatického uživatele jsou předdefinované uživatelské účty, které jsou automaticky vytvářeny službou Batch. Ve výchozím nastavení jsou úlohy spuštěny pod účtem automatického uživatele. Můžete nakonfigurovat specifikaci automatického uživatele pro úlohu tak, aby označovala, pod jakým účtem automatického uživatele má úloha být spuštěna. Specifikace automatického uživatele umožňuje určit úroveň nadmořské výšky a rozsah účtu automatického uživatele, který bude úlohu spouštět. 

- **Pojmenovaný uživatelský účet.** Můžete zadat jeden nebo více pojmenovaných uživatelských účtů pro fond při vytváření fondu. Každý uživatelský účet je vytvořen na každém uzlu fondu. Kromě názvu účtu zadáte heslo uživatelského účtu, úroveň zvýšení oprávnění a pro fondy Linuxu soukromý klíč SSH. Při přidání úlohy můžete zadat pojmenovaný uživatelský účet, pod kterým by měla být tato úloha spuštěna.

> [!IMPORTANT] 
> Verze služby Batch 2017-01-01.4.0 zavádí narušující změnu, která vyžaduje aktualizaci kódu pro volání této verze. Pokud migrujete kód ze starší verze batch, všimněte si, že vlastnost **runElevated** již není podporována v knihovnách klienta REST API nebo Batch. K určení **výškové výšky** použijte vlastnost new userIdentity úkolu. V části s názvem [Aktualizace kódu do nejnovější knihovny klienta Batch](#update-your-code-to-the-latest-batch-client-library) naleznete rychlé pokyny pro aktualizaci dávkového kódu, pokud používáte některou z klientských knihoven.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Přístup k uživatelským účtům k souborům a adresářům

Účet automatického uživatele i pojmenovaný uživatelský účet mají přístup pro čtení a zápis do adresáře pracovního adresáře úkolu, sdíleného adresáře a úkolů s více instancemi. Oba typy účtů mají přístup pro čtení do adresářů pro spuštění a přípravu úloh.

Pokud je úloha spuštěna pod stejným účtem, který byl použit pro spuštění úlohy zahájení, má úloha přístup pro čtení a zápis do adresáře počáteční úlohy. Podobně pokud je úloha spuštěna pod stejným účtem, který byl použit pro spuštění úlohy přípravy úlohy, má úloha přístup pro čtení a zápis do adresáře úloh y přípravy úloh. Pokud je úloha spuštěna pod jiným účtem, než je počáteční úloha nebo úloha přípravy úlohy, má pouze přístup pro čtení do příslušného adresáře.

Další informace o přístupu k souborům a adresářům z úlohy naleznete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení pomocí aplikace Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Zvýšený přístup pro úkoly 

Úroveň zvýšení uživatelského účtu označuje, zda je úloha spuštěna se zvýšeným přístupem. Účet automatického uživatele i pojmenovaný uživatelský účet lze spustit se zvýšeným přístupem. Dvě možnosti výškové výšky jsou:

- **NonAdmin:** Úloha je spuštěna jako standardní uživatel bez přístupu se zvýšenými oprávněními. Výchozí úroveň zvýšení dávkového uživatelského účtu je vždy **NonAdmin**.
- **Správce:** Úloha běží jako uživatel se zvýšeným přístupem a pracuje s úplnými oprávněními správce. 

## <a name="auto-user-accounts"></a>Účty automatického uživatele

Ve výchozím nastavení se úlohy spouštějí v aplikaci Batch pod účtem automatického uživatele, jako standardní uživatel bez zvýšeného přístupu a s rozsahem úloh. Pokud je specifikace automatického uživatele nakonfigurována pro obor úlohy, služba Dávka vytvoří účet automatického uživatele pouze pro tuto úlohu.

Alternativou k oboru úkolu je rozsah fondu. Pokud je specifikace automatického uživatele pro úlohu nakonfigurována pro obor fondu, úloha je spuštěna pod účtem automatického uživatele, který je k dispozici pro všechny úkoly ve fondu. Další informace o oboru fondu naleznete v části s názvem Spustit úlohu jako automatického uživatele s rozsahem fondu.   

Výchozí obor se liší u uzlů Windows a Linux:

- V uzlech systému Windows jsou úlohy ve výchozím nastavení spuštěny v rámci oboru úloh.
- Linuxové uzly vždy běží pod rozsahem fondu.

Existují čtyři možné konfigurace pro specifikaci automatického uživatele, z nichž každá odpovídá jedinečnému účtu automatického uživatele:

- Přístup bez oprávnění správce s rozsahem úloh (výchozí specifikace automatického uživatele)
- Přístup správce (se zvýšenými oprávněními) s rozsahem úloh
- Přístup bez oprávnění správce s rozsahem fondu
- Přístup správce s rozsahem fondu

> [!IMPORTANT] 
> Úlohy spuštěné v oboru úloh nemají de facto přístup k jiným úkolům v uzlu. Uživatel se zlými úmysly s přístupem k účtu by však mohl toto omezení obejít odesláním úlohy, která je spuštěna s oprávněními správce a přistupuje k jiným adresářům úloh. Uživatel se zlými úmysly může také použít RDP nebo SSH pro připojení k uzlu. Je důležité chránit přístup ke klíčům účtu Batch, abyste takovému scénáři zabránili. Pokud máte podezření, že váš účet mohl být napaden, nezapomeňte klíče znovu vygenerovat.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Spuštění úlohy jako automatického uživatele se zvýšeným přístupem

Specifikaci automatického uživatele pro oprávnění správce můžete nakonfigurovat, když potřebujete spustit úlohu se zvýšeným přístupem. Například počáteční úloha může vyžadovat zvýšený přístup k instalaci softwaru v uzlu.

> [!NOTE] 
> Obecně je nejlepší použít zvýšený přístup pouze v případě potřeby. Doporučené postupy doporučují udělit minimální oprávnění nezbytná k dosažení požadovaného výsledku. Pokud například počáteční úloha nainstaluje software pro aktuálního uživatele, nikoli pro všechny uživatele, můžete se vyhnout udělení zvýšeného přístupu k úkolům. Můžete nakonfigurovat specifikaci automatického uživatele pro rozsah fondu a přístup bez správce pro všechny úkoly, které je třeba spustit pod stejným účtem, včetně počáteční úlohy. 
>
>

Následující fragmenty kódu ukazují, jak nakonfigurovat specifikaci automatického uživatele. Příklady nastavují výšku `Admin` výšky `Task`a obor na . Obor úlohy je výchozí nastavení, ale je zde zahrnuta z důvodu příkladu.

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Spuštění úlohy jako automatického uživatele s rozsahem fondu

Při zřízení uzlu jsou vytvořeny dva účty automatického uživatelského připojení v celém fondu na každém uzlu ve fondu, jeden se zvýšeným přístupem a jeden bez zvýšeného přístupu. Nastavení oboru automatického uživatele do fondu oboru pro danou úlohu spustí úlohu pod jedním z těchto dvou účtů automatického uživatele celého fondu. 

Pokud zadáte obor fondu pro automatického uživatele, všechny úlohy, které běží s přístupem správce, budou spuštěny pod stejným účtem automatického uživatele v rámci celého fondu. Podobně úlohy, které běží bez oprávnění správce také spustit pod jeden fond široký účet automatického uživatele. 

> [!NOTE] 
> Dva účty automatického uživatele v celém fondu jsou samostatné účty. Úlohy spuštěné v rámci účtu pro správu v rámci celého fondu nemohou sdílet data s úlohami spuštěnými v rámci standardního účtu a naopak. 
>
>

Výhodou spuštění pod stejným účtem automatického uživatele je, že úlohy jsou schopny sdílet data s jinými úlohami spuštěnými na stejném uzlu.

Sdílení tajných kódů mezi úkoly je jeden scénář, kde je užitečné spuštění úloh v rámci jednoho ze dvou účtů automatického uživatele celého fondu. Předpokládejme například, že počáteční úloha musí zřídit tajný klíč do uzlu, který mohou použít jiné úkoly. Můžete použít rozhraní Windows Data Protection API (DPAPI), ale vyžaduje oprávnění správce. Místo toho můžete chránit tajný klíč na úrovni uživatele. Úlohy spuštěné pod stejným uživatelským účtem mají přístup k tajnému klíči bez zvýšeného přístupu.

Dalším scénářem, kde můžete chtít spustit úlohy v rámci účtu automatického uživatele s rozsahem fondu je sdílené složky rozhraní MPI (Message Passing Interface). Sdílená složka MPI je užitečná, když uzly v úloze MPI musí pracovat na stejných datech souboru. Hlavní uzel vytvoří sdílenou složku, ke které mají podřízené uzly přístup, pokud jsou spuštěny pod stejným účtem automatického uživatele. 

Následující fragment kódu nastaví obor automatického uživatele tak, aby dofondul obor úlohy v aplikaci Batch .NET. Úroveň nadmořské výšky je vynechána, takže úloha je spuštěna pod standardním účtem automatického uživatele v celém fondu.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Pojmenované uživatelské účty

Při vytváření fondu můžete definovat pojmenované uživatelské účty. Pojmenovaný uživatelský účet má jméno a heslo, které zadáte. Můžete určit úroveň zvýšení pro pojmenovaný uživatelský účet. Pro linuxové uzly můžete také zadat soukromý klíč SSH.

Pojmenovaný uživatelský účet existuje ve všech uzlech ve fondu a je k dispozici pro všechny úlohy spuštěné na těchto uzlech. Můžete definovat libovolný počet pojmenovaných uživatelů fondu. Když přidáte kolekci úkolů nebo úkolů, můžete určit, že úloha bude spuštěna pod jedním z pojmenovaných uživatelských účtů definovaných ve fondu.

Pojmenovaný uživatelský účet je užitečný, pokud chcete spustit všechny úlohy v úloze pod stejným uživatelským účtem, ale izolovat je od úloh spuštěných v jiných úlohách současně. Můžete například vytvořit pojmenovaného uživatele pro každou úlohu a spustit úlohy každé úlohy pod tímto pojmenovaným uživatelským účtem. Každá úloha pak může sdílet tajný klíč s vlastními úkoly, ale ne s úlohami spuštěnými v jiných úlohách.

Pomocí pojmenovaného uživatelského účtu můžete také spustit úlohu, která nastavuje oprávnění pro externí prostředky, jako jsou sdílené složky. S pojmenovaným uživatelským účtem můžete řídit identitu uživatele a můžete tuto identitu uživatele použít k nastavení oprávnění.  

Pojmenované uživatelské účty umožňují ssh bez hesla mezi uzly Linuxu. Můžete použít pojmenovaný uživatelský účet s uzly Linuxu, které je třeba spustit úlohy s více instancemi. Každý uzel ve fondu můžete spouštět úlohy v rámci uživatelského účtu definovaného v celém fondu. Další informace o úlohách s více instancemi naleznete v [tématu Použití úloh s více\-instancemi ke spuštění aplikací MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Vytvoření pojmenovaných uživatelských účtů

Chcete-li vytvořit pojmenované uživatelské účty v části Batch, přidejte do fondu kolekci uživatelských účtů. Následující fragmenty kódu ukazují, jak vytvořit pojmenované uživatelské účty v rozhraní .NET, Java a Pythonu. Tyto fragmenty kódu ukazují, jak vytvořit účty s názvem správce i nesprávce ve fondu. Příklady vytvářejí fondy pomocí konfigurace cloudové služby, ale stejný přístup použijete při vytváření fondu Windows nebo Linux u konfigurace virtuálního počítače.

#### <a name="batch-net-example-windows"></a>Příklad dávky .NET (Windows)

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

#### <a name="batch-net-example-linux"></a>Příklad dávky .NET (Linux)

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


#### <a name="batch-java-example"></a>Příklad Java dávky

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

#### <a name="batch-python-example"></a>Příklad dávkového Pythonu

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Spuštění úlohy pod pořízeným uživatelským účtem se zvýšeným přístupem

Chcete-li spustit úlohu jako uživatele se zvýšenými oprávněními, nastavte vlastnost **UserIdentity úlohy** na `Admin`pojmenovaný uživatelský účet, který byl vytvořen s **vlastností ElevationLevel** nastavenou na .

Tento fragment kódu určuje, že úloha by měla být spuštěna pod pojmenovaným uživatelským účtem. Tento pojmenovaný uživatelský účet byl definován ve fondu při vytvoření fondu. V tomto případě byl pojmenovaný uživatelský účet vytvořen s oprávněními správce:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualizace kódu na nejnovější knihovnu klienta Batch

Verze služby Batch 2017-01-01.4.0 zavádí narušující změnu, která nahradí vlastnost **runElevated** dostupnou v dřívějších verzích vlastností **userIdentity.** Následující tabulky poskytují jednoduché mapování, které můžete použít k aktualizaci kódu z dřívějších verzí klientských knihoven.

### <a name="batch-net"></a>Batch .NET

| Pokud váš kód používá...                  | Aktualizujte jej na....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`není zadáno | Není nutná žádná aktualizace.                                                                                               |

### <a name="batch-java"></a>Batch Java

| Pokud váš kód používá...                      | Aktualizujte jej na....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`není zadáno | Není nutná žádná aktualizace.                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Pokud váš kód používá...                      | Aktualizujte jej na....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated`není zadáno | Není nutná žádná aktualizace.                                                                                                                                  |


## <a name="next-steps"></a>Další kroky

* Podrobný přehled batch, najdete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení s Batch](batch-api-basics.md).
