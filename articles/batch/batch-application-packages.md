---
title: Instalace balíčků aplikací na výpočetní uzly – Azure Batch | Dokumenty společnosti Microsoft
description: Pomocí funkce balíčky aplikací azure batch můžete snadno spravovat více aplikací a verzí pro instalaci na dávkových výpočetních uzlech.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30301832381bdc7b5f001eec2c449c571f9fd671
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086224"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Nasazení aplikací pro výpočetní uzly pomocí balíčků dávkových aplikací

Funkce balíčků aplikací Azure Batch poskytuje snadnou správu aplikací úloh a jejich nasazení do výpočetních uzlů ve vašem fondu. Pomocí balíčků aplikací můžete nahrát a spravovat více verzí aplikací, které vaše úlohy spouštějí, včetně jejich podpůrných souborů. Pak můžete automaticky nasadit jednu nebo více těchto aplikací do výpočetních uzlů ve vašem fondu.

V tomto článku se dozvíte, jak nahrát a spravovat balíčky aplikací na webu Azure Portal. Potom se dozvíte, jak je nainstalovat na výpočetní uzly fondu s knihovnou [Batch .NET.][api_net]

> [!NOTE]
> Balíčky aplikací jsou podporované ve všech fondech služby Batch vytvořených po 5. červenci 2017. Ve fondech služby Batch vytvořených mezi 10. březnem 2016 a 5. červencem 2017 jsou podporované, pouze pokud byl fond vytvořen pomocí konfigurace cloudové služby. Fondy služby Batch vytvořené před 10. březnem 2016 nepodporují balíčky aplikací.
>
> Rozhraní API pro vytváření a správu balíčků aplikací jsou součástí knihovny [Batch Management .NET.][api_net_mgmt] Rozhraní API pro instalaci balíčků aplikací na výpočetní uzel jsou součástí knihovny [Batch .NET.][api_net] Srovnatelné funkce jsou v dostupných dávkových rozhraních API pro jiné jazyky. 
>
> Zde popsaná funkce balíčků aplikací nahrazuje funkci Dávkové aplikace, která je k dispozici v předchozích verzích služby.

## <a name="application-package-requirements"></a>Požadavky na balíček aplikací
Chcete-li používat balíčky aplikací, musíte [propojit účet Azure Storage](#link-a-storage-account) s vaším dávkovým účtem.

## <a name="about-applications-and-application-packages"></a>O aplikacích a balíčcích aplikací
V rámci Azure Batch *aplikace* odkazuje na sadu binárních souborů s verzí, které lze automaticky stáhnout do výpočetních uzlů ve vašem fondu. *Balíček aplikace* odkazuje na *konkrétní sadu* těchto binárních souborů a představuje danou *verzi* aplikace.

![Diagram aplikací a balíčků aplikací na vysoké úrovni][1]

### <a name="applications"></a>Aplikace
Aplikace v batch obsahuje jeden nebo více balíčků aplikací a určuje možnosti konfigurace pro aplikaci. Aplikace může například zadat výchozí verzi balíčku aplikace, která se má nainstalovat do výpočetních uzlů, a zda lze aktualizovat nebo odstranit její balíčky.

### <a name="application-packages"></a>Balíčky aplikací
Balíček aplikace je soubor ZIP, který obsahuje binární soubory aplikace a podpůrné soubory, které jsou nutné pro úlohy ke spuštění aplikace. Každý balíček aplikace představuje konkrétní verzi aplikace.

Balíčky aplikací můžete zadat na úrovni fondu a úkolů. Můžete zadat jeden nebo více z těchto balíčků a (volitelně) verzi při vytváření fondu nebo úkolu.

* **Balíčky aplikací fondu** jsou nasazeny do *všech* uzlů ve fondu. Aplikace jsou nasazeny, když uzel připojí fond a když je restartován nebo reimaged.
  
    Balíčky aplikací fondu jsou vhodné, když všechny uzly ve fondu provádějí úlohy úlohy. Můžete zadat jeden nebo více balíčků aplikací při vytváření fondu a můžete přidat nebo aktualizovat balíčky existujícího fondu. Pokud aktualizujete balíčky aplikací existujícího fondu, je nutné restartovat jeho uzly a nainstalovat nový balíček.
* **Balíčky aplikací úloh** se nasazují pouze do výpočetního uzlu, který je naplánován na spuštění úlohy, těsně před spuštěním příkazového řádku úlohy. Pokud zadaný balíček aplikace a verze je již v uzlu, není znovu nasazena a existující balíček se používá.
  
    Balíčky aplikací úloh jsou užitečné v prostředích sdíleného fondu, kde jsou v jednom fondu spuštěny různé úlohy a fond není odstraněn po dokončení úlohy. Pokud má vaše úloha méně úkolů, než je uzlů ve fondu, balíčky aplikací úkolů můžou omezit přenosy dat, protože se aplikace může nasadit jen na uzly, které úkoly budou skutečně provádět.
  
    Další scénáře, které mohou využívat balíčky aplikací úloh, jsou úlohy, které spouštějí velké aplikace, ale pouze pro několik úloh. Například fáze předběžného zpracování nebo úloha sloučení, kde je předzpracování nebo sloučení aplikace těžká váha, může mít prospěch z použití balíčků aplikace úlohy.

> [!IMPORTANT]
> Existují omezení počtu aplikací a balíčků aplikací v rámci účtu Batch a maximální velikost balíčku aplikace. Podrobnosti o těchto limitech najdete [v tématu Kvóty a limity pro službu Azure Batch.](batch-quota-limit.md)
> 
> 

### <a name="benefits-of-application-packages"></a>Výhody aplikačních balíčků
Balíčky aplikací můžete zjednodušit kód v řešení Batch a snížit režii potřebnou ke správě aplikací, které vaše úlohy spustit.

U balíčků aplikací nemusí počáteční úloha vašeho fondu zadávat dlouhý seznam jednotlivých souborů prostředků, které se mají nainstalovat do uzlů. Není nutné ručně spravovat více verzí souborů aplikace ve službě Azure Storage nebo na uzlech. A nemusíte se starat o generování [adres URL SAS,](../storage/common/storage-dotnet-shared-access-signature-part-1.md) abyste poskytli přístup k souborům ve vašem účtu úložiště. Batch funguje na pozadí s Azure Storage pro ukládání balíčků aplikací a jejich nasazení do výpočetních uzlů.

> [!NOTE] 
> Celková velikost spouštěcího úkolu nesmí přesahovat 32768 znaků, včetně souborů prostředků a proměnných prostředí. Pokud počáteční úloha překročí tento limit, je použití balíčků aplikací další možností. Můžete také vytvořit archiv se zipem obsahující soubory prostředků, nahrát ho jako objekt blob do Služby Azure Storage a pak ho rozbalit z příkazového řádku úlohy zahájení. 
>
>

## <a name="upload-and-manage-applications"></a>Nahrávání a správa aplikací
Ke správě balíčků aplikací ve vašem dávkovém účtu můžete použít [portál Azure][portal] nebo api pro správu dávek. V několika dalších částech nejprve ukážeme, jak propojit účet úložiště, pak diskutovat o přidávání aplikací a balíčků a jejich správu s portálem.

### <a name="link-a-storage-account"></a>Propojení účtu úložiště
Chcete-li používat balíčky aplikací, musíte nejprve propojit [účet Azure Storage](batch-api-basics.md#azure-storage-account) s vaším dávkovým účtem. Pokud jste ještě nenakonfigurovali účet úložiště, na webu Azure portal se zobrazí upozornění při prvním kliknutí na **položku Aplikace** ve vašem dávkovém účtu.



![Upozornění "Žádný účet úložiště nawebuje" na webu Azure Portal][9]

Služba Batch používá přidružený účet úložiště k ukládání balíčků aplikací. Po propojení dvou účtů batch může automaticky nasadit balíčky uložené v propojeném účtu úložiště do vašich výpočetních uzlů. Pokud chcete propojit účet úložiště s dávkovým účtem, klikněte v okně **Upozornění** na **Účet úložiště** a potom znovu klikněte na **Účet úložiště.**

![Výběr okna účtu úložiště na webu Azure Portal][10]

Doporučujeme vytvořit účet úložiště *speciálně* pro použití s dávkovým účtem a vybrat ho zde. Po vytvoření účtu úložiště ho pak můžete propojit se svým dávkovým účtem pomocí okna **Účet úložiště.**

> [!IMPORTANT] 
> - V současné době nelze použít balíčky aplikací s účtem Azure Storage, který je nakonfigurovaný s [pravidly brány firewall](../storage/common/storage-network-security.md).
> - Účet úložiště Azure s **hierarchickým oborem názvů** nastaveným na **Povoleno** nelze použít pro balíčky aplikací.

Služba Batch používá Azure Storage k ukládání balíčků aplikací jako objektů BLOB bloku. Pro data objektu blob bloku se [vám budou účtovat jako obvykle][storage_pricing] a velikost každého balíčku nesmí překročit maximální velikost objektu blob bloku. Další informace najdete v tématu [Škálovatelnost úložiště Azure a cíle výkonu pro účty úložiště](../storage/blobs/scalability-targets.md). Nezapomeňte zvážit velikost a počet balíčků aplikací a pravidelně odebírat zastaralé balíčky, abyste minimalizovali náklady.

### <a name="view-current-applications"></a>Zobrazit aktuální aplikace
Chcete-li zobrazit aplikace v účtu Batch, klepněte při zobrazení **dávkového účtu**na položku nabídky **Aplikace** v levé nabídce .

![Dlaždice Aplikace][2]

Výběrem této možnosti nabídky se otevře okno **Aplikace:**

![Výpis aplikací][3]

V tomto okně se zobrazí ID každé aplikace ve vašem účtu a následující vlastnosti:

* **Balíčky**: Počet verzí přidružených k této aplikaci.
* **Výchozí verze**: Verze aplikace nainstalovaná, pokud neurčíte verzi při zadávání aplikace pro fond. Toto nastavení je volitelné.
* **Povolit aktualizace**: Hodnota, která určuje, zda jsou povoleny aktualizace balíčku, odstranění a dodatky. Pokud je tato možnost nastavena na **ne**, budou aktualizace a odstranění balíčku pro aplikaci zakázány. Lze přidat pouze nové verze balíčků aplikace. Výchozí hodnota je **Yes** (Ano).

Pokud chcete zobrazit strukturu souborů balíčku aplikace na výpočetním uzlu, přejděte na portálu na účet Batch. V účtu Batch přejděte do **seznamu Fondy**. Vyberte fond, který obsahuje výpočetní uzla, která vás zajímají.

![Uzly ve fondu][13]

Po výběru fondu přejděte na výpočetní uzel, na který je nainstalován balíček aplikace. Odtud jsou podrobnosti o balíčku aplikace umístěny ve složce **aplikace.** Další složky na výpočetním uzlu obsahují další soubory, jako jsou úlohy spuštění, výstupní soubory, výstup chyb atd.

![Soubory na uzlu][14]

### <a name="view-application-details"></a>Zobrazit podrobnosti o aplikaci
Chcete-li zobrazit podrobnosti o aplikaci, vyberte aplikaci v okně **Aplikace.**

![Podrobnosti o aplikaci][4]

V podrobnostech aplikace můžete nakonfigurovat následující nastavení pro vaši aplikaci.

* **Povolit aktualizace**: Určete, zda lze aktualizovat nebo odstranit balíčky aplikací. Viz "Aktualizovat nebo odstranit balíček aplikace" dále v tomto článku.
* **Výchozí verze**: Zadejte výchozí balíček aplikace pro nasazení do výpočetních uzlů.
* **Zobrazovaný název**: Zadejte popisný název, který může řešení Batch použít, když zobrazuje informace o aplikaci, například v uživatelském uživatelském prostředí služby, kterou poskytujete zákazníkům prostřednictvím dávky.

### <a name="add-a-new-application"></a>Přidání nové aplikace
Chcete-li vytvořit novou aplikaci, přidejte balíček aplikace a zadejte nové, jedinečné ID aplikace. První balíček aplikace, který přidáte s novým ID aplikace také vytvoří novou aplikaci.

Klepněte na **položku Aplikace** > **přidat**.

![Nové okno aplikace na webu Azure Portal][5]

Okno **Nová aplikace** obsahuje následující pole pro určení nastavení nové aplikace a balíčku aplikace.

**ID aplikace**

Toto pole určuje ID nové aplikace, která podléhá standardním pravidlům ověření Azure Batch ID. Pravidla pro poskytování ID aplikace jsou následující:

* V uzlech systému Windows může ID obsahovat libovolnou kombinaci alfanumerických znaků, spojovníků a podtržítek. V linuxových uzlech jsou povoleny pouze alfanumerické znaky a podtržítka.
* Nesmí obsahovat více než 64 znaků.
* Musí být jedinečný v rámci účtu Batch.
* Je malá a velká písmena zachování a malá a malá a malá a malá a malá a malá a malá a malá a malá a malá a malá a malá a malá a malá

**Verze**

Toto pole určuje verzi balíčku aplikace, který nahráváte. Řetězce verzí podléhají následujícím ověřovacím pravidlům:

* V uzlech systému Windows může řetězec verze obsahovat libovolnou kombinaci alfanumerických znaků, spojovníků, podtržítk a období. V uzlech Linux uzly verze řetězec může obsahovat pouze alfanumerické znaky a podtržítka.
* Nesmí obsahovat více než 64 znaků.
* Musí být jedinečný v rámci aplikace.
* Jsou malá a velká písmena a malá a velká písmena.

**Balíček aplikace**

Toto pole určuje soubor ZIP, který obsahuje binární soubory aplikace a podpůrné soubory, které jsou nutné ke spuštění aplikace. Klepněte na pole **Vybrat soubor** nebo ikonu složky, na kterou chcete procházet, a vyberte soubor ZIP, který obsahuje soubory aplikace.

Po výběru souboru klikněte na **OK** a začněte nahrávání do Služby Azure Storage. Po dokončení operace nahrávání se na portálu zobrazí oznámení. V závislosti na velikosti nahrávaného souboru a rychlosti síťového připojení může tato operace nějakou dobu trvat.

> [!WARNING]
> Nezavírejte okno **Nová aplikace** před dokončením operace nahrávání. Tím se zastaví proces nahrávání.
> 
> 

### <a name="add-a-new-application-package"></a>Přidání nového balíčku aplikace
Chcete-li přidat verzi balíčku aplikace pro existující aplikaci, vyberte aplikaci v oknech **Aplikace** a klepněte na **tlačítko Balíčky** > **přidat**.

![Přidání okna balíčků aplikací na portálAzure Portal][8]

Jak můžete vidět, pole odpovídají polím **okna Nová aplikace,** ale pole **ID aplikace** je zakázáno. Stejně jako u nové aplikace zadejte **verzi** nového balíčku, přejděte do souboru zip **balíčku aplikace** a kliknutím na **OK** balíček nahrajte.

### <a name="update-or-delete-an-application-package"></a>Aktualizace nebo odstranění balíčku aplikace
Chcete-li aktualizovat nebo odstranit existující balíček aplikace, otevřete podrobnosti o aplikaci, klepněte na **položku Balíčky**, klepněte na **tři tečky** v řádku balíčku aplikace, který chcete upravit, a vyberte akci, kterou chcete provést.

![Aktualizace nebo odstranění balíčku na webu Azure Portal][7]

**Aktualizace**

Po klepnutí na tlačítko **Aktualizovat**se zobrazí okna **balíčku aktualizace.** Toto okno je podobné oknu **Nový balíček aplikace,** ale je povoleno pouze pole výběru balíčku, což umožňuje zadat nový soubor ZIP k nahrání.

![Aktualizovat okno balíčku na webu Azure Portal][11]

**Odstranit**

Po klepnutí na tlačítko **Odstranit**budete vyzváni k potvrzení odstranění verze balíčku a Batch odstraní balíček z Azure Storage. Pokud odstraníte výchozí verzi aplikace, bude pro aplikaci odebráno výchozí nastavení **verze.**

![Odstranit aplikaci][12]

## <a name="install-applications-on-compute-nodes"></a>Instalace aplikací na výpočetní uzly
Teď, když jste se naučili spravovat balíčky aplikací pomocí portálu Azure, můžeme diskutovat o tom, jak je nasadit do výpočetních uzlů a spustit je pomocí dávkových úloh.

### <a name="install-pool-application-packages"></a>Instalace balíčků aplikací fondu
Chcete-li nainstalovat balíček aplikace na všechny výpočetní uzly ve fondu, zadejte jeden nebo více *odkazů na balíček* aplikace pro fond. Balíčky aplikací, které zadáte pro fond jsou nainstalovány na každém výpočetním uzlu, když se tento uzel připojí k fondu a když je uzel restartován nebo reimaged.

V poli Batch .NET zadejte jeden nebo více [cloudových fondu][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] při vytváření nového fondu nebo pro existující fond. Třída [ApplicationPackageReference][net_pkgref] určuje ID aplikace a verzi, kterou chcete nainstalovat do výpočetních uzlů fondu.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

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
> Pokud se nasazení balíčku aplikace z nějakého důvodu nezdaří, služba Batch označí uzel [jako nepoužitelný][net_nodestate]a v tomto uzlu nejsou naplánovány žádné úlohy. V takovém případě byste měli **restartovat** uzel znovu zahájit nasazení balíčku. Restartování uzlu také umožňuje plánování úkolů znovu na uzlu.
> 
> 

### <a name="install-task-application-packages"></a>Instalace balíčků aplikací úloh
Podobně jako fond, zadáte *odkazy na balíček* aplikace pro úkol. Pokud je naplánováno spuštění úlohy v uzlu, balíček je stažen a extrahován těsně před spuštěním příkazového řádku úlohy. Pokud je v uzlu již nainstalován zadaný balíček a verze, balíček se nestáhne a použije se existující balíček.

Chcete-li nainstalovat balíček aplikace úlohy, nakonfigurujte [cloudtask][net_cloudtask]úlohy . [ApplicationPackageReferences,][net_cloudtask_pkgref] vlastnost:

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
Balíčky, které jste zadali pro fond nebo úkol, jsou staženy `AZ_BATCH_ROOT_DIR` a extrahovány do pojmenovaného adresáře v rámci uzlu. Batch také vytvoří proměnnou prostředí, která obsahuje cestu k pojmenovanému adresáři. Příkazové řádky úlohy používají tuto proměnnou prostředí při odkazování na aplikaci v uzlu. 

V uzlech systému Windows je proměnná v následujícím formátu:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Na linuxových uzlech je formát mírně odlišný. Tečky (.), pomlčky (-) a číselné znaky (#) jsou sloučí na podtržítka v proměnné prostředí. Všimněte si také, že případ ID aplikace je zachována. Například:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`a `version` jsou hodnoty, které odpovídají verzi aplikace a balíčku, které jste zadali pro nasazení. Pokud jste například zadali, že verze 2.7 aplikačního *blenderu* by měla být nainstalována v uzlech systému Windows, příkazové řádky úlohy by používaly tuto proměnnou prostředí pro přístup k jejím souborům:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

V uzlech Linux uzly, zadejte proměnnou prostředí v tomto formátu. Sloučí tečky (.), pomlčky (-) a číselné znaky (#) tak, aby podtržítka a zachovat případ ID aplikace:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Když nahrajete balíček aplikace, můžete zadat výchozí verzi pro nasazení do výpočetních uzlů. Pokud jste zadali výchozí verzi aplikace, můžete vynechat příponu verze při odkazování na aplikaci. Výchozí verzi aplikace můžete zadat na webu Azure Portal v okně **Aplikace,** jak je znázorněno v [části Nahrávání a správa aplikací](#upload-and-manage-applications).

Pokud například nastavíte "2.7" jako výchozí verzi pro aplikační *blender*a vaše úkoly budou odkazovat na následující proměnnou prostředí, pak uzly systému Windows spustí verzi 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Následující fragment kódu ukazuje ukázkový příkazový řádek úlohy, který spouští výchozí verzi aplikace *blenderu:*

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Další informace o nastavení prostředí výpočetních uzlů najdete v tématu [Nastavení prostředí pro úlohy](batch-api-basics.md#environment-settings-for-tasks) v [přehledu dávkových funkcí.](batch-api-basics.md)
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu
Pokud již byl existující fond nakonfigurován s balíčkem aplikace, můžete zadat nový balíček pro fond. Pokud zadáte nový odkaz na balíček pro fond, platí následující:

* Služba Batch nainstaluje nově zadaný balíček na všechny nové uzly, které se připojují ke fondu, a na všechny existující uzly, které jsou restartovány nebo znovu zobrazeny.
* Výpočetní uzly, které jsou již ve fondu při aktualizaci odkazů na balíček, automaticky neinstalují nový balíček aplikace. Tyto výpočetní uzly musí být restartován nebo reimaged přijímat nový balíček.
* Při nasazení nového balíčku, vytvořené proměnné prostředí odrážejí nové odkazy na balíček aplikace.

V tomto příkladu má stávající fond verzi 2.7 aplikace *blenderu* nakonfigurovanou jako jeden ze svých [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Chcete-li aktualizovat uzly fondu verzí 2.76b, zadejte novou [applicationPackageReference][net_pkgref] s novou verzí a potvrďte změnu.

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

Nyní, když byla nakonfigurována nová verze, služba Batch nainstaluje verzi 2.76b do *libovolného nového* uzlu, který se připojí ke fondu. Chcete-li nainstalovat 2.76b na uzly, které jsou *již* ve fondu, restartujte nebo reimage je. Všimněte si, že restartované uzly zachovat soubory z předchozích nasazení balíčku.

## <a name="list-the-applications-in-a-batch-account"></a>Seznam aplikací v účtu Batch
Aplikace a jejich balíčky můžete uvést v účtu Batch pomocí [applicationoperations][net_appops]. [ListApplicationSummaries][net_appops_listappsummaries] metoda.

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

## <a name="wrap-up"></a>Zabalit
Pomocí balíčků aplikací můžete zákazníkům pomoci vybrat aplikace pro jejich úlohy a určit přesnou verzi, která se má použít při zpracování úloh pomocí služby Batch. Zákazníkům můžete také poskytnout možnost nahrávat a sledovat své vlastní aplikace ve vaší službě.

## <a name="next-steps"></a>Další kroky
* Rozhraní [API dávky REST][api_rest] také poskytuje podporu pro práci s balíčky aplikací. Například naleznete [v applicationPackageReferences][rest_add_pool_with_packages] element v [Přidat fondu do účtu][rest_add_pool] informace o tom, jak určit balíčky k instalaci pomocí rozhraní REST API. Podrobnosti o tom, jak získat informace o aplikaci pomocí rozhraní API dávky REST, najdete v tématu [Aplikace.][rest_applications]
* Zjistěte, jak programově [spravovat účty a kvóty Azure pomocí správy dávek .NET](batch-management-dotnet.md). Knihovna [Služby správa dávek .NET][api_net_mgmt] může povolit funkce pro vytváření a odstraňování účtů pro dávkovou aplikaci nebo službu.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram balíčků aplikací na vysoké úrovni"
[2]: ./media/batch-application-packages/app_pkg_02.png "Dlaždice Aplikace na webu Azure Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Okno Aplikace na webu Azure Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Okno Podrobnosti o aplikaci na webu Azure Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nové okno aplikace na webu Azure Portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Aktualizace nebo odstranění balíčků na webu Azure Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Okno nového balíčku aplikace na webu Azure Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Žádné upozornění na účet propojeného úložiště"
[10]: ./media/batch-application-packages/app_pkg_10.png "Výběr okna účtu úložiště na webu Azure Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Aktualizovat okno balíčku na webu Azure Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Dialogové okno Potvrzení odstranění balíčku na webu Azure Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Informace o výpočetních uzlách na Webu Azure Portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Soubory na výpočetním uzlu zobrazené matem na webu Azure Portal"
