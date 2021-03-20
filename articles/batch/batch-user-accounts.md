---
title: Spouštění úloh v rámci uživatelských účtů
description: Přečtěte si o typech uživatelských účtů a o tom, jak je nakonfigurovat.
ms.topic: how-to
ms.date: 08/20/2020
ms.custom: seodec18
ms.openlocfilehash: cce374e7d7ffb513bed882b048ea54bcbad81b0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719355"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Spouštění úloh v rámci uživatelských účtů ve Batch

> [!NOTE]
> Uživatelské účty popsané v tomto článku se liší od uživatelských účtů používaných pro protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) z důvodů zabezpečení.
>
> Pokud se chcete připojit k uzlu s konfigurací virtuálního počítače se systémem Linux přes SSH, přečtěte si téma [použití vzdálené plochy k virtuálnímu počítači se systémem Linux v Azure](../virtual-machines/linux/use-remote-desktop.md). Informace o připojení k uzlům se systémem Windows prostřednictvím protokolu RDP najdete v tématu [připojení k virtuálnímu počítači s Windows serverem](../virtual-machines/windows/connect-logon.md).<br /><br />
> Pokud se chcete připojit k uzlu, na kterém je spuštěná konfigurace cloudové služby prostřednictvím protokolu RDP, přečtěte si téma [povolení připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).

Úkol v Azure Batch vždy běží v rámci uživatelského účtu. Ve výchozím nastavení se úlohy spouštějí pod standardními uživatelskými účty bez oprávnění správce. V některých scénářích můžete chtít nakonfigurovat uživatelský účet, pod kterým chcete úlohu spustit. Tento článek pojednává o typech uživatelských účtů a o tom, jak je nakonfigurovat pro váš scénář.

## <a name="types-of-user-accounts"></a>Typy uživatelských účtů

Azure Batch poskytuje dva typy uživatelských účtů pro spuštěné úlohy:

- **Účty automatických uživatelů.** Účty automatických uživatelů jsou předdefinované uživatelské účty, které služba Batch automaticky vytvoří. Ve výchozím nastavení se úlohy spouštějí pod účtem automatického uživatele. Můžete nakonfigurovat specifikaci automatického uživatele pro úkol, aby označoval, pod kterým účtem automatických uživatelů se má úloha spustit. Specifikace automatického uživatele umožňuje zadat úroveň zvýšení úrovně oprávnění a rozsah účtu automatického uživatele, který úlohu spustí.

- **Pojmenovaný uživatelský účet.** Při vytváření fondu můžete zadat jeden nebo více pojmenovaných uživatelských účtů pro fond. Každý uživatelský účet se vytvoří v každém uzlu fondu. Kromě názvu účtu zadejte heslo uživatelského účtu, úroveň zvýšení oprávnění a pro fondy Linux privátního klíče SSH. Když přidáte úlohu, můžete zadat pojmenovaný uživatelský účet, pod kterým se má úloha spustit.

> [!IMPORTANT]
> Služba Batch verze 2017 -01-01.4.0 zavádí zásadní změnu, která vyžaduje, abyste aktualizovali kód pro volání této verze. Pokud migrujete kód ze starší verze služby Batch, Všimněte si, že vlastnost **runElevated** již není podporována v klientských knihovnách REST API nebo Batch. Pomocí nové vlastnosti **userIdentity** úlohy můžete určit úroveň zvýšení úrovně oprávnění. Pokud používáte jednu z klientských knihoven, přečtěte si téma [aktualizace kódu na nejnovější klientskou knihovnu Batch](#update-your-code-to-the-latest-batch-client-library) pro rychlé pokyny pro aktualizaci kódu Batch.

## <a name="user-account-access-to-files-and-directories"></a>Přístup k souborům a adresářům uživatelského účtu

Účet automatického uživatele i pojmenovaný uživatelský účet mají přístup pro čtení a zápis do adresáře pracovních adresářů, sdílených adresářů a úloh s více instancemi úlohy. Oba typy účtů mají k adresářům pro spuštění a přípravu úlohy oprávnění ke čtení.

Pokud úloha běží pod stejným účtem, který se použil pro spuštění spouštěcího úkolu, má úloha přístup pro čtení a zápis do adresáře spouštěcího úkolu. Podobně platí, že pokud úloha běží pod stejným účtem, který se použil pro spuštění úlohy přípravy úlohy, má úloha přístup pro čtení i zápis do adresáře úkolů přípravy úlohy. Pokud je úloha spuštěna pod jiným účtem než úlohou spustit úlohu nebo úkol přípravy úlohy, má úloha přístup jen pro čtení k příslušnému adresáři.

Další informace o přístupu k souborům a adresářům z úlohy najdete v tématu [soubory a adresáře](files-and-directories.md).

## <a name="elevated-access-for-tasks"></a>Zvýšený přístup pro úlohy

Úroveň zvýšení úrovně uživatelského účtu indikuje, jestli se úloha spouští s vyšším přístupem. Se zvýšeným přístupem může běžet účet automatického uživatele i pojmenovaný uživatelský účet. Úroveň zvýšení úrovně oprávnění jsou tyto dvě možnosti:

- **Nesprávce:** Úloha se spouští jako standardní uživatel bez zvýšeného přístupu. Výchozí úroveň zvýšení oprávnění pro uživatelský účet Batch je vždy **nesprávce**.
- **Správce:** Úloha se spouští jako uživatel se zvýšeným přístupem a funguje s úplnými oprávněními správce.

## <a name="auto-user-accounts"></a>Účty automatických uživatelů

Ve výchozím nastavení se úlohy spouštějí v dávce pod účtem automatických uživatelů, jako standardní uživatel bez zvýšeného přístupu a s rozsahem fondu. Rozsah fondu znamená, že se úloha spouští pod účtem automatického uživatele, který je k dispozici pro libovolný úkol ve fondu. Další informace o rozsahu fondu najdete v tématu [spuštění úlohy jako automatický uživatel s rozsahem fondu](#run-a-task-as-an-auto-user-with-pool-scope).

Alternativou rozsahu fondu je rozsah úlohy. Pokud je specifikace automatického uživatele nakonfigurovaná pro obor úlohy, vytvoří služba Batch účet automatického uživatele jenom pro tuto úlohu.

Existují čtyři možné konfigurace pro specifikaci automatického uživatele, z nichž každý odpovídá jedinečnému účtu automatického uživatele:

- Přístup bez oprávnění správce s oborem úkolu
- Přístup správce (se zvýšenými oprávněními) s oborem úkolu
- Přístup bez oprávnění správce s oborem fondu
- Přístup správce k oboru fondu

> [!IMPORTANT]
> Úlohy spuštěné v oboru úlohy nemají oprávnění de facto přístup k jiným úlohám na uzlu. Uživatel se zlými úmysly, který má přístup k účtu, ale může toto omezení obejít tak, že odešle úkol, který se spustí s oprávněními správce a přistupuje k ostatním adresářům úkolů. Uživatel se zlými úmysly může k připojení k uzlu použít taky RDP nebo SSH. Je důležité chránit přístup k klíčům účtu Batch a zabránit tak takovému scénáři. Pokud máte podezření, že váš účet může být ohrožený, nezapomeňte znovu vygenerovat klíče.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Spustit úlohu jako automatický uživatel se zvýšeným přístupem

Pokud potřebujete spustit úlohu s vyšším přístupem, můžete nakonfigurovat specifikaci automatického uživatele pro oprávnění správce. Spouštěcí úkol může například potřebovat zvýšený přístup k instalaci softwaru na uzel.

> [!NOTE]
> V případě potřeby použijte vyšší úroveň přístupu. Osvědčené postupy doporučuje udělit minimální oprávnění nezbytnou k dosažení požadovaného výsledku. Například pokud spouštěcí úkol nainstaluje software pro aktuálního uživatele místo pro všechny uživatele, můžete se vyhnout udělení vyššího přístupu k úkolům. Pro všechny úlohy, které musí běžet pod stejným účtem, včetně spouštěcího úkolu, můžete nakonfigurovat specifikaci automatického uživatele pro rozsah fondu a přístup bez oprávnění správce.

Následující fragmenty kódu ukazují, jak nakonfigurovat specifikaci automatického uživatele. Příklady nastaví úroveň zvýšení úrovně na `Admin` a rozsah na `Task` .

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Spustit úlohu jako automatický uživatel s rozsahem fondu

Když se zřídí uzel, vytvoří se na každém uzlu ve fondu dva účty automatických uživatelů na úrovni fondu, jeden s vyšším přístupem a druhý bez zvýšeného přístupu. Nastavení oboru automatického uživatele na rozsah fondu pro danou úlohu spustí úlohu v rámci jednoho z těchto dvou účtů automatických uživatelů v rámci fondu.

Když pro automatického uživatele určíte rozsah fondu, všechny úlohy, které se spustí s přístupem správce, se spustí v rámci stejného účtu automatických uživatelských účtů pro celý fond. Podobně úlohy, které se spouštějí bez oprávnění správce, se spouštějí také v rámci jednoho účtu automatického uživatele v rámci fondu.

> [!NOTE]
> Účty automatických uživatelských účtů v rámci fondu jsou samostatné účty. Úlohy spuštěné pod účtem správce fondu nemůžou sdílet data s úlohami, které běží na standardním účtu, a naopak.

Výhodou spuštění pod stejným účtem automatického uživatele je, že úkoly můžou sdílet data s ostatními úlohami běžícími na stejném uzlu.

Sdílení tajných kódů mezi úkoly je jeden scénář, ve kterém jsou užitečné úlohy na jednom ze dvou účtů automatických uživatelů na úrovni fondu. Předpokládejme například, že počáteční úkol potřebuje zřídit tajný klíč na uzel, který mohou používat jiné úkoly. Můžete použít Windows Data Protection API (DPAPI), ale vyžaduje oprávnění správce. Místo toho můžete tajný klíč chránit na úrovni uživatele. Úlohy běžící pod stejným uživatelským účtem mají přístup ke tajnému kódu bez zvýšeného přístupu.

Dalším scénářem, kdy budete chtít spouštět úlohy pod účtem automatického uživatele s rozsahem fondu, je sdílená složka rozhraní MPI (Message Passing Interface). Sdílená složka MPI je užitečná v případě, že uzly v úloze MPI potřebují pracovat se stejnými daty souborů. Hlavní uzel vytvoří sdílenou složku, ke které mají podřízené uzly přístup, pokud jsou spuštěny pod stejným účtem automatického uživatele.

Následující fragment kódu nastaví obor automatického uživatele na rozsah fondu pro úkol v dávce .NET. Úroveň zvýšení úrovně se vynechá, takže úloha se spouští pod standardním účtem automatického uživatele v rámci fondu.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Pojmenované uživatelské účty

Pojmenované uživatelské účty můžete definovat při vytváření fondu. Pojmenovaný uživatelský účet má jméno a heslo, které zadáte. Úroveň zvýšení oprávnění můžete zadat pro pojmenovaný uživatelský účet. Pro uzly se systémem Linux můžete také zadat privátní klíč SSH.

Pojmenovaný uživatelský účet existuje na všech uzlech ve fondu a je k dispozici pro všechny úlohy běžící na těchto uzlech. Můžete definovat libovolný počet pojmenovaných uživatelů pro fond. Když přidáte úlohu nebo kolekci úkolů, můžete určit, že se úloha spouští pod jedním z pojmenovaných uživatelských účtů definovaných ve fondu.

Pojmenovaný uživatelský účet je užitečný v případě, že chcete spouštět všechny úlohy v úloze pod stejným uživatelským účtem, ale izolovat je od úloh spuštěných v jiných úlohách současně. Můžete například vytvořit pojmenovaného uživatele pro každou úlohu a spustit úkoly každé úlohy pod názvem uživatelský účet. Každá úloha potom může sdílet tajný klíč s vlastními úkoly, ale ne úlohy spuštěné v jiných úlohách.

Pomocí pojmenovaného uživatelského účtu můžete taky spustit úlohu, která nastaví oprávnění u externích prostředků, jako jsou sdílené složky. Pomocí pojmenovaného uživatelského účtu můžete řídit identitu uživatele a pomocí této identity uživatele nastavit oprávnění.  

Pojmenované uživatelské účty umožňují SSH bez hesla mezi uzly Linux. U uzlů se systémem Linux, které potřebují spouštět úlohy s více instancemi, můžete použít pojmenovaný uživatelský účet. Každý uzel ve fondu může spouštět úlohy pod uživatelským účtem definovaným v celém fondu. Další informace o úlohách s více instancemi najdete v tématu [použití \- úloh s více instancemi ke spouštění aplikací MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Vytvoření pojmenovaných uživatelských účtů

Pokud chcete ve službě Batch vytvořit pojmenované uživatelské účty, přidejte do fondu kolekci uživatelských účtů. Následující fragmenty kódu ukazují, jak vytvořit pojmenované uživatelské účty v jazycích .NET, Java a Python. Tyto fragmenty kódu ukazují, jak vytvořit účet správce i bez správce s názvem účty ve fondu. Příklady vytvářejí fondy pomocí konfigurace cloudové služby, ale stejný přístup použijete při vytváření fondu systému Windows nebo Linux pomocí konfigurace virtuálního počítače.

#### <a name="batch-net-example-windows"></a>Příklad dávkového rozhraní .NET (Windows)

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

#### <a name="batch-net-example-linux"></a>Příklad dávkového rozhraní .NET (Linux)

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

#### <a name="batch-java-example"></a>Příklad dávkového Java

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Spuštění úlohy pod názvem uživatelského účtu se zvýšeným přístupem

Chcete-li spustit úlohu jako uživatel se zvýšenými oprávněními, nastavte vlastnost **UserIdentity** úlohy na pojmenovaný uživatelský účet, který byl vytvořen s vlastností **ElevationLevel** nastavenou na `Admin` .

Tento fragment kódu určuje, že úloha by měla běžet pod jménem uživatelského účtu. Tento pojmenovaný uživatelský účet byl ve fondu definován při vytvoření fondu. V tomto případě byl vytvořen pojmenovaný uživatelský účet s oprávněními správce:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualizace kódu na nejnovější klientskou knihovnu služby Batch

Služba Batch verze 2017 -01-01.4.0 zavádí zásadní změnu a nahrazuje vlastnost **runElevated** , která je k dispozici v dřívějších verzích s vlastností **userIdentity** . Následující tabulky obsahují jednoduché mapování, které můžete použít k aktualizaci kódu z dřívějších verzí klientských knihoven.

### <a name="batch-net"></a>Batch .NET

| Pokud váš kód používá...                  | Aktualizovat na....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Neurčeno | Není nutná žádná aktualizace.                                                                                               |

### <a name="batch-java"></a>Batch Java

| Pokud váš kód používá...                      | Aktualizovat na....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Neurčeno | Není nutná žádná aktualizace.                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Pokud váš kód používá...                      | Aktualizovat na....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, kde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` Neurčeno | Není nutná žádná aktualizace.                                                                                                                                  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Seznamte se se [soubory a adresáři](files-and-directories.md) v Azure Batch.
