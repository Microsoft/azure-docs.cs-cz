---
title: Instalovat balíčky aplikací na výpočetní uzly - Azure Batch | Microsoft Docs
description: Použijte funkci balíčků aplikací Azure Batch snadno spravovat více aplikací a verzí pro instalaci na Batch výpočetních uzlů.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 440f7eba99e5fa02a597ae62d5d14329f5e50af7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Nasazení aplikací na výpočetní uzly pomocí balíčků aplikací Batch

Funkci balíčků aplikací Azure Batch poskytuje snadnou správu úloh aplikací a jejich nasazení na výpočetní uzly ve fondu. Pomocí balíčků aplikací můžete odeslat a spravovat více verzí aplikací, které vaše úkoly spouštět, včetně jejich podpůrné soubory. Můžete pak automaticky nasadit jednu nebo více těchto aplikací na výpočetní uzly ve fondu.

V tomto článku se dozvíte, jak odesílat a spravovat balíčky aplikací na portálu Azure. Potom se dozvíte, jak k instalaci na výpočetní uzly fondu s [Batch .NET] [ api_net] knihovny.

> [!NOTE]
> 
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací.
>
> Rozhraní API pro vytváření a správě balíčků aplikací jsou součástí [rozhraní Batch Management .NET] [[api_net_mgmt]] knihovny. Rozhraní API pro instalaci balíčků aplikací na výpočetním uzlu jsou součástí [Batch .NET] [ api_net] knihovny.  
>
> Funkci balíčků aplikací v rámci popsaného nahrazuje funkci aplikace Batch k dispozici v předchozích verzích služby.
> 
> 

## <a name="application-package-requirements"></a>Požadavky na balíček aplikace
Chcete-li použít balíčky aplikací, je potřeba [propojení účtu Azure Storage](#link-a-storage-account) k účtu Batch.

Tato funkce byla zavedena v [Batch REST API] [ api_rest] verze 2015-12-01.2.2 a odpovídající [Batch .NET] [ api_net] knihovní verze 3.1.0. Doporučujeme vám, že vždy používáte nejnovější verzi rozhraní API při práci se službou Batch.

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací.
>
>

## <a name="about-applications-and-application-packages"></a>O aplikacích a balíčky aplikací
V rámci Azure Batch *aplikace* odkazuje na sadu verzí binární soubory, které lze automaticky staženy na výpočetní uzly ve fondu. *Balíčku aplikace* odkazuje *konkrétní sadu* těchto binárních souborů a představuje daný *verze* aplikace.

![Vysokoúrovňový diagram aplikace a balíčky aplikací][1]

### <a name="applications"></a>Aplikace
Aplikace ve službě Batch obsahuje jeden nebo více aplikací, balíčků a určuje možnosti konfigurace pro aplikaci. Aplikace můžete například zadat výchozí verze balíčku aplikace nainstalovat na výpočetní uzly a zda může být jeho balíčky aktualizovat ani odstranit.

### <a name="application-packages"></a>Balíčky aplikací
Balíček aplikace je soubor .zip, který obsahuje binární soubory aplikace a podpůrné soubory, které jsou požadovány pro vaše úkoly a spusťte aplikaci. Každý balíček aplikace představuje určitou verzi aplikace.

Můžete zadat balíčky aplikací na úrovni fondu a úloh. Nejméně jeden z těchto balíčků a (volitelně) verzi můžete zadat při vytváření fondu nebo úloh.

* **Fond aplikací balíčky** nasazených *každých* uzlu ve fondu. Jestliže se uzel připojí fondu, a, pokud je restartovat nebo obnovit z Image se aplikace nasadí.
  
    Balíčky fondu aplikací jsou vhodné, když všechny uzly v rámci fondu spuštění úlohy. Jeden nebo více balíčků aplikací můžete určit, když vytvoříte fond, a můžete přidat nebo aktualizovat existující fond balíčky. Pokud aktualizujete balíčky existující fond aplikací, je nutné restartovat jeho uzly k instalaci nového balíčku.
* **Úloha balíčky aplikací** se nasadit jenom na výpočetním uzlu naplánované spuštění úlohy, právě před spuštěním příkazového řádku úkolu. Pokud zadaný balíček aplikace a verze je již v uzlu, není znovu nasazena a používá existující balíček.
  
    Balíčky aplikací úloh jsou užitečné v sdílený fond prostředí, kde různé úlohy se spouštějí na jeden fond, a fondu se neodstraní po dokončení úlohy. Pokud má vaše úloha méně úkolů, než je uzlů ve fondu, balíčky aplikací úkolů můžou omezit přenosy dat, protože se aplikace může nasadit jen na uzly, které úkoly budou skutečně provádět.
  
    Další scénáře, které můžete využít balíčky aplikací úloh jsou úlohy, které spouštějí rozsáhlé aplikace, ale pouze několik úkolů. Předběžné zpracování fáze nebo sloučení úlohy, kde je aplikace předběžné zpracování nebo sloučená těžký, může například těžit z pomocí balíčků aplikací úloh.

> [!IMPORTANT]
> Existují omezení počtu aplikace a balíčky aplikací v rámci účtu Batch a na velikosti balíčku maximální aplikace. V tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) podrobnosti o těchto omezeních.
> 
> 

### <a name="benefits-of-application-packages"></a>Výhody balíčky aplikací
Balíčky aplikací, můžete zjednodušit kód v řešení pro Batch a snížení režie potřebná ke správě aplikací, které vaše úkoly spouštět.

Pomocí balíčků aplikací váš fond spouštěcí úkol nemá k určení dlouhý seznam jednotlivé zdrojové soubory k instalaci na uzlech. Nemusíte ručně spravovat více verzí soubory aplikace v Azure Storage nebo na uzly. A nemusíte si dělat starosti o generování [adresy URL SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) k poskytování přístupu k souborům ve vašem účtu úložiště. Služba batch pracuje na pozadí s Azure Storage a ukládat balíčky aplikací, je nasadit na výpočetních uzlech.

> [!NOTE] 
> Celková velikost spouštěcího úkolu nesmí přesahovat 32768 znaků, včetně souborů prostředků a proměnných prostředí. Pokud spouštěcí úkol překračuje tento limit, pak pomocí balíčků aplikací je jinou možnost. Můžete také vytvořit komprimované archivu obsahující soubory prostředků, nahrajte ho jako objekt blob do služby Azure Storage a potom rozbalte ho z příkazového řádku úkolu spuštění. 
>
>

## <a name="upload-and-manage-applications"></a>Odesílat a spravovat aplikace
Můžete použít [portál Azure] [ portal] nebo [rozhraní Batch Management .NET](batch-management-dotnet.md) knihovny ke správě balíčků aplikací v účtu Batch. V následujících částech několik nejprve ukážeme, jak propojit účet úložiště a pak zabývat přidáním aplikací a balíčků a jejich pomocí portálu pro správu.

### <a name="link-a-storage-account"></a>Odkaz účet úložiště
Pokud chcete používat balíčky aplikací, je nutné nejprve propojit účtu Azure Storage k účtu Batch. Pokud ještě nemáte nakonfigurovaný účet úložiště, portálu Azure zobrazí upozornění poprvé kliknete **aplikace** dlaždice v nástroji **účet Batch** okno.

> [!IMPORTANT]
> Batch aktuálně podporuje *pouze* **pro obecné účely** typ účtu úložiště, jak je popsáno v kroku 5, [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account)v [účty Azure storage](../storage/common/storage-create-storage-account.md). Při propojení účtu Azure Storage k účtu Batch, propojte *pouze* **pro obecné účely** účet úložiště.
> 
> 

![Upozornění: nakonfigurován žádný účet úložiště, na portálu Azure][9]

Služba Batch používá přidružený účet úložiště pro uložení balíčků vaší aplikace. Po připojení dva účty Batch můžete automaticky nasadit balíčky uložených v propojeném účtu úložiště pro výpočetní uzly. Odkaz účet úložiště k účtu Batch, klikněte na tlačítko **nastavení účtu úložiště** na **upozornění** okna a pak klikněte na tlačítko **účet úložiště** na **účet úložiště** okno.

![Zvolte okně účtu úložiště na portálu Azure][10]

Doporučujeme vytvořit účet úložiště *konkrétně* pro použití s vaším účtem Batch a vyberte ho sem. Podrobnosti o tom, jak vytvořit účet úložiště, najdete v části "Vytvoření účtu úložiště" v [účty Azure Storage](../storage/common/storage-create-storage-account.md). Po vytvoření účtu úložiště, můžete pak propojit se vašeho účtu Batch pomocí **účet úložiště** okno.

> [!WARNING]
> Služba Batch používá Azure Storage k ukládání balíčky aplikací jako objekty BLOB bloku. Jste [účtován jako normální] [ storage_pricing] pro data objektů blob bloku. Je nutné vzít v úvahu velikost a počet balíčky aplikací a pravidelně odstraňuje zastaralá balíčky, chcete-li minimalizovat náklady.
> 
> 

### <a name="view-current-applications"></a>Zobrazit aktuální aplikace
Chcete-li zobrazit aplikace v účtu Batch, klikněte na tlačítko **aplikace** položky nabídky v levé nabídce při prohlížení **účet Batch** okno.

![Dlaždice aplikace][2]

Výběrem této možnosti nabídky otevře **aplikace** okno:

![Seznam aplikací][3]

**Aplikace** ID každé aplikace zobrazuje v účtu a následující vlastnosti:

* **Balíčky**: číslo verze přidružené k této aplikaci.
* **Výchozí verze**: verze aplikace nainstalovat, pokud neuvedete na verzi, když zadáte aplikací pro fond. Toto nastavení je volitelné.
* **Povolit aktualizace**: hodnota, která určuje, zda balíček aktualizace, odstranění a přidání jsou povoleny. Pokud je nastavena v **ne**, balíček aktualizace a odstranění jsou zakázány pro aplikaci. Můžete přidat pouze nové verze balíčku aplikace. Výchozí hodnota je **Yes** (Ano).

### <a name="view-application-details"></a>Zobrazení podrobností o aplikaci
Otevřete okno, které zahrnuje podrobné informace pro aplikaci, vyberte aplikaci v **aplikace** okno.

![Podrobnosti o aplikaci][4]

V okně podrobností aplikace můžete nakonfigurovat následující nastavení pro vaši aplikaci.

* **Povolit aktualizace**: Určete, zda jeho balíčky aplikací můžete aktualizovat nebo odstranit. Později v tomto článku najdete v části "Aktualizace nebo odstranění balíčku aplikace".
* **Výchozí verze**: Zadejte výchozí balíček aplikace pro nasazení na výpočetní uzly.
* **Zobrazovaný název**: Zadejte popisný název, který vaše řešení Batch můžete použít při zobrazuje informace o aplikaci, například v uživatelském rozhraní služby, která vám umožní vašim zákazníkům prostřednictvím Batch poskytovat.

### <a name="add-a-new-application"></a>Přidejte novou aplikaci
Chcete-li vytvořit novou aplikaci, přidejte balíček aplikace a zadejte ID aplikace nové, jedinečné. První balíček aplikace, který přidáte s novým ID aplikace také vytvoří novou aplikaci.

Klikněte na tlačítko **přidat** na **aplikace** otevřete **novou aplikaci** okno.

![Nové okno aplikace na portálu Azure][5]

**Novou aplikaci** okno obsahuje následující pole k zadání nastavení pro novou aplikaci a balíček aplikace.

**Id aplikace**

Toto pole určuje ID novou aplikaci, která je předmětem standardní pravidla ověřování ID dávky Azure. Pravidla pro zajištění ID aplikací jsou následující:

* Na Windows se ID může obsahovat libovolnou kombinaci alfanumerických znaků, pomlčky a podtržítka. Na uzlech Linux jsou povoleny pouze alfanumerické znaky a podtržítka.
* Nesmí obsahovat víc než 64 znaků.
* Musí být jedinečný v rámci účtu Batch.
* Je zachována a velká a malá písmena.

**Verze**

Toto pole určuje verzi balíčku aplikace, které odesíláte. Řetězce verzi se vztahují následující pravidla ověřování:

* Na uzlech Windows řetězec verze může obsahovat libovolnou kombinaci alfanumerických znaků, pomlčky, podtržítka a tečky. Na uzlech Linux řetězec verze může obsahovat pouze alfanumerické znaky a podtržítka.
* Nesmí obsahovat víc než 64 znaků.
* Musí být jedinečný v rámci aplikace.
* Jsou zachována a velká a malá písmena.

**Balíček aplikace**

Toto pole určuje soubor .zip, který obsahuje binární soubory aplikace a podpůrné soubory, které jsou nutné ke spuštění aplikace. Klikněte **vyberte soubor** pole nebo na ikonu složky a vyhledejte a vyberte soubor .zip, který obsahuje soubory aplikace.

Jakmile vyberete soubor, klikněte na tlačítko **OK** zahájíte nahrávání do úložiště Azure. Po dokončení operace odesílání na portálu zobrazí oznámení a zavře okno. V závislosti na rychlosti síťového připojení a velikost souboru, který odesíláte může tato operace chvíli trvat.

> [!WARNING]
> Nezavírejte okno **novou aplikaci** okno před dokončením operace odesílání. Díky tomu bude zastavení procesu nahrávání.
> 
> 

### <a name="add-a-new-application-package"></a>Přidat nový balíček aplikace
Přidat novou verzi balíčku aplikace pro existující aplikace, vyberte aplikaci v **aplikace** okně klikněte na tlačítko **balíčky**, pak klikněte na tlačítko **přidat** otevřete **přidat balíček** okno.

![Přidejte balíček okna aplikací na portálu Azure][8]

Jak vidíte, se pole shodují těch, které **novou aplikaci** okno, ale **id aplikace** pole je zakázána. Stejně jako u nové aplikace, zadejte **verze** pro nový balíček, přejděte do vaší **balíčku aplikace** .zip souboru a pak klikněte na **OK** pro nahrání balíčku.

### <a name="update-or-delete-an-application-package"></a>Aktualizace nebo odstranění balíčku aplikace
Aktualizovat nebo odstranit existující balíček aplikace, otevřete okno Podrobnosti pro aplikace, klikněte na tlačítko **balíčky** otevřete **balíčky** okně klikněte na tlačítko **třemi tečkami** v řádku balíček aplikace, který chcete upravit a vyberte akci, kterou chcete provést.

![Aktualizace nebo odstranění balíčku na portálu Azure][7]

**Aktualizace**

Když kliknete na tlačítko **aktualizace**, *balíček aktualizace* zobrazí se okno. Toto okno je podobná *nový balíček aplikace* okno, ale pouze pole výběr balíčku je povoleno, umožní vám to zadat nový soubor ZIP k odeslání.

![Okno balíček aktualizace na portálu Azure][11]

**Odstranění**

Když kliknete na tlačítko **odstranit**, se zobrazí výzva k potvrzení odstranění verze balíčku a Batch odstraní balíček z úložiště Azure. Pokud odstraníte výchozí verze aplikace, **výchozí verze** nastavení se odebere pro aplikaci.

![Odstranit aplikaci ][12]

## <a name="install-applications-on-compute-nodes"></a>Instalace aplikací na výpočetní uzly
Teď, když jste se naučili jak spravovat balíčky aplikací pomocí portálu Azure, můžete pojednává o je nasadit na výpočetní uzly a jejich spuštění s úkoly služby Batch.

### <a name="install-pool-application-packages"></a>Instalovat balíčky fondu aplikací
Balíček aplikace nainstalovat na všechny výpočetní uzly ve fondu, zadejte balíček aplikace jeden nebo více *odkazy* pro fond. Balíčky aplikací, které zadáte pro fond jsou nainstalované na každém výpočetním uzlu, pokud tento uzel připojí k fondu a pokud je uzel restartován nebo obnovit z Image.

V Batch .NET, zadejte jednu nebo více [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] při vytvoření nového fondu, nebo pro existující fond. [ApplicationPackageReference] [ net_pkgref] třída určuje ID aplikace a verzi, instalace na vytvoření fondu výpočetních uzlů.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Pokud z nějakého důvodu selže nasazení balíček aplikace, služba Batch označí uzlu [nepoužitelná][net_nodestate], a žádné úlohy jsou naplánovány pro spuštění v tomto uzlu. V takovém případě byste měli **restartujte** uzel k nasazení balíčku provést znovu. Restartování uzlu taky umožňuje znovu plánování úkolů na uzlu.
> 
> 

### <a name="install-task-application-packages"></a>Instalovat balíčky aplikace úkolů
Podobně jako u fondu, zadejte balíček aplikace *odkazy* pro úlohu. Když je naplánován ke spuštění na uzel, balíčku stažena a extrahována těsně před příkazový řádek úkolu je spustí. Pokud zadaného balíčku a verze je už nainstalovaný na uzlu, není-li stáhnout balíček a se používá existující balíček.

Chcete-li nainstalovat balíček aplikace úkolů, nakonfigurujte úkolu [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] vlastnost:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Spuštění nainstalovaných aplikací
Balíčky, které jste určili pro fond nebo úloh jsou stažené a rozbalené na adresář s názvem v rámci `AZ_BATCH_ROOT_DIR` uzlu. Batch vytvoří také proměnná prostředí, který obsahuje cestu k adresáři s názvem. Vaše příkazové řádky úkolu pomocí této proměnné prostředí při odkazování na aplikace na uzlu. 

V uzlech systému Windows je proměnná v následujícím formátu:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Na Linuxových uzlů formát se mírně liší. Tečky (.) a pomlčky (-) a tyto znaky (#), se sloučí na podtržené v proměnné prostředí. Všimněte si také, že se zachová, i v případě ID aplikace. Příklad:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` a `version` jsou hodnoty, které odpovídají verzi aplikací a balíčků, jste určili pro nasazení. Například pokud jste zadali, že verze 2.7 aplikace *digestoru* by měly být nainstalovány na uzlech Windows by vaše příkazové řádky úkolu pomocí této proměnné prostředí pro přístup k jeho soubory:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Na Linuxových uzlů zadejte proměnné prostředí v tomto formátu. Vyrovnání tečky (.) a pomlčky (-) a znaky čísla (#) na podtržítka a zachovat v případě ID aplikace:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Při nahrávání balíčku aplikace, můžete zadat výchozí verze pro nasazení na výpočetní uzly. Pokud jste zadali výchozí verze pro aplikaci, můžete vynechat přípony verze při odkazu na aplikaci. V portálu Azure, v okně aplikace můžete určit výchozí verze aplikace, jak je znázorněno v [odesílat a spravovat aplikace](#upload-and-manage-applications).

Například pokud nastavíte jako výchozí verze pro aplikace "2.7" *digestoru*a vaše úkoly odkazování následující proměnné prostředí, pak uzlů Windows, budou spuštěny verze 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Následující fragment kódu ukazuje příklad úloh příkazový řádek, který spustí výchozí verzi *digestoru* aplikace:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> V tématu [nastavení prostředí pro úkoly](batch-api-basics.md#environment-settings-for-tasks) v [přehled funkcí Batch](batch-api-basics.md) Další informace o nastavení prostředí výpočetního uzlu.
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu
Pokud se balíček aplikace již byla nakonfigurována existujícího fondu, můžete zadat nový balíček pro fond. Pokud zadáte nový odkaz na balíček pro fond následujících podmínek:

* Služba Batch nainstaluje nově zadaný balíček na všechny nové uzly, které se připojí k fondu a na všechny existující uzlu, který je restartovat nebo obnovit z Image.
* Výpočetní uzly, které jsou již ve fondu, když aktualizujete balíček odkazuje automaticky neinstaluje nového balíčku aplikace. Tyto výpočetní uzly musí být restartovat nebo obnovit z Image pro příjem nového balíčku.
* Po nasazení nového balíčku projeví proměnné vytvořené prostředí balíček odkazuje nové aplikace.

V tomto příkladu má existující fond verzi 2.7 *digestoru* aplikace konfigurovaná jako jeden z jeho [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. Pokud chcete aktualizovat uzly fondu s verzí 2.76b, zadejte nový [ApplicationPackageReference] [ net_pkgref] s novou verzí a potvrzení změn.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Teď, když byl nakonfigurován na novou verzi, služba Batch nainstaluje do jakéhokoli verze 2.76b *nové* uzlu, který se připojí k fondu. Chcete-li nainstalovat 2.76b na uzly, které jsou *již* ve fondu, restartovat nebo obnovit z Image je. Všimněte si, že restartovaný uzly zachovat soubory z předchozích nasazení balíčku.

## <a name="list-the-applications-in-a-batch-account"></a>Zobrazí seznam aplikací v účtu Batch
Můžete vytvořit seznam aplikací a jejich balíčků v účtu Batch pomocí [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metoda.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Zabalení
Pomocí balíčků aplikací můžete pomoct vašim zákazníkům vyberte aplikace, pro svou práci a určete přesnou verze se má použít při zpracování úlohy se služby podporují službu Batch. Můžete zadat také možnost pro vaše zákazníky k odesílání a sledování vlastních aplikací ve službě.

## <a name="next-steps"></a>Další postup
* [Batch REST API] [ api_rest] taky poskytuje podporu pro práci s balíčky aplikací. Například najdete v článku [applicationPackageReferences] [ rest_add_pool_with_packages] element v [přidat fond na účet] [ rest_add_pool] informace o tom, jak zadat balíčků pro instalaci pomocí rozhraní REST API. V tématu [aplikace] [ rest_applications] podrobnosti o tom, jak získat informace o aplikaci pomocí rozhraní REST API služby Batch.
* Zjistěte, jak programově [spravovat účty Azure Batch a kvóty pomocí rozhraní Batch Management .NET](batch-management-dotnet.md). [Rozhraní Batch Management .NET][api_net_mgmt] knihovny můžete povolit funkce vytváření a odstraňování účtu Batch aplikace nebo služby.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Vysokoúrovňový diagram balíčky aplikací"
[2]: ./media/batch-application-packages/app_pkg_02.png "Dlaždice aplikace na portálu Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Okno aplikace na portálu Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Okno podrobností aplikace v portálu Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nové okno aplikace na portálu Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Aktualizace nebo odstranění balíčků rozevírací seznam v portálu Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Okno nový balíček aplikace na portálu Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Žádná propojené výstraha účtu úložiště"
[10]: ./media/batch-application-packages/app_pkg_10.png "Zvolte okně účtu úložiště na portálu Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Okno balíček aktualizace na portálu Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Odstranit balíček dialogové okno potvrzení na portálu Azure"
