---
title: Úlohy a úlohy v Azure Batch
description: Přečtěte si o úlohách a úlohách a způsobu jejich použití v Azure Batchovém pracovním postupu z hlediska vývoje.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95808594"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Úlohy a úlohy v Azure Batch

V Azure Batch *úkol* představuje jednotku výpočtu. *Úloha* je kolekcí těchto úkolů. Další informace o úlohách a úlohách a způsobu jejich použití v Azure Batchovém pracovním postupu jsou popsané dál.

## <a name="jobs"></a>Úlohy

Úloha je kolekce úkolů. Řídí, jak se provádí výpočet pomocí jejích úkolů na výpočetních uzlech ve fondu.

Úloha Určuje [fond](nodes-and-pools.md#pools) , ve kterém má být práce spuštěna. Můžete vytvořit nový fond pro každou úlohu nebo použít jeden fond pro mnoho úloh. Můžete vytvořit fond pro každou úlohu, která je přidružená k [plánu úlohy](#scheduled-jobs), nebo jeden fond pro všechny úlohy, které jsou přidružené k plánu úlohy.

### <a name="job-priority"></a>Priorita úloh

Úlohám, které vytvoříte, můžete přiřadit volitelnou prioritu úlohy. Služba Batch používá hodnotu priority úlohy k určení pořadí plánování (pro všechny úlohy v rámci úlohy) wtihin každý fond.

Chcete-li aktualizovat prioritu úlohy, zavolejte [aktualizaci vlastností operace úlohy](/rest/api/batchservice/job/update) (Batch REST) nebo upravte [vlastnosti cloudjob. priority](/dotnet/api/microsoft.azure.batch.cloudjob) (Batch .NET). Hodnoty priority jsou v rozsahu od-1000 (nejnižší priorita) do 1000 (nejvyšší priorita).

V rámci stejného fondu mají úlohy s vyšší prioritou přednost plánování před úlohami s nižší prioritou. Úlohy v úlohách s nižší prioritou, které jsou již spuštěny, nebudou přerušeny úlohami v úloze s vyšší prioritou. Úlohy se stejnou úrovní priority mají stejnou pravděpodobnost, že se naplánuje, a pořadí provádění úkolů není definované.

Úloha s hodnotou s vysokou prioritou spuštěnou v jednom fondu nebude mít vliv na plánování úloh spuštěných v samostatném fondu nebo v jiném účtu Batch. Priorita úlohy se nevztahuje na [autofondy](nodes-and-pools.md#autopools), které jsou vytvořeny při odeslání úlohy.

### <a name="job-constraints"></a>Omezení úlohy

Můžete použít omezení úlohy k zadání určitých omezení pro úlohy.

- Můžete nastavit **maximální uplynulý čas**, takže pokud některá úloha běží po dobu delší než určený maximální uplynulý čas, úloha a všechny její úkoly budou ukončeny.
- Jako omezení můžete zadat **maximální počet opakovaných pokusů o úlohu** , včetně toho, jestli se má úloha vždycky opakovat, nebo se nikdy neopakuje. Opakování úkolu znamená, že pokud úloha selže, bude znovu zařazen do fronty, aby ji bylo možné znovu spustit.

### <a name="job-manager-tasks-and-automatic-termination"></a>Úlohy Správce úloh a automatické ukončení

Klientská aplikace si může přidat úkoly do úlohy, nebo můžete zadat [úkol správce úloh](#job-manager-task). Úkol správce úloh obsahuje informace potřebné k vytvoření požadovaných úkolů pro úlohu, přičemž úkol správce úloh běží na jednom výpočetním uzlu v rámci fondu. Úkol správce úloh je zpracováván konkrétně dávkou. je zařazená do fronty, jakmile se úloha vytvoří a restartuje se, pokud se nezdařila. Úkol správce úloh je vyžadován pro úlohy, které jsou vytvořeny [plánem úlohy](#scheduled-jobs), protože se jedná o jediný způsob, jak definovat úkoly před vytvořením instance úlohy.

Ve výchozím nastavení zůstanou úlohy v aktivním stavu po dokončení všech úkolů v rámci úlohy. Toto chování můžete změnit tak, aby se úlohy automaticky ukončily po dokončení všech úkolů v úloze. Nastavte vlastnost **onAllTasksComplete** úlohy ([onAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) v dávce .NET) na `terminatejob` * pro automatické ukončení úlohy, když jsou všechny její úkoly v dokončeném stavu.

Služba Batch posuzuje úlohu, která nemá *žádné* úkoly na dokončení všech jejích úkolů. Tato možnost se proto nejčastěji používá pro [úkoly správce úloh](#job-manager-task). Pokud chcete použít automatické ukončení úlohy bez Správce úloh, měli byste nejprve nastavit vlastnost **onAllTasksComplete** nové úlohy na hodnotu `noaction` a potom ji nastavit na hodnotu `terminatejob` * až po dokončení přidávání úkolů do úlohy.

### <a name="scheduled-jobs"></a>Naplánované úlohy

[Plány úloh](/rest/api/batchservice/jobschedule) umožňují vytvořit opakované úlohy v rámci služby Batch. Plán úloh určuje, kdy spustit úlohy a obsahuje specifikace pro úlohy, které mají být spuštěny. Můžete zadat dobu trvání plánu (jak dlouho a kdy je plán v platnosti) a jak často se úlohy vytvářejí během naplánovaného období.

## <a name="tasks"></a>Úlohy

Úkol je jednotka výpočtu, která je přidružena k úloze. Běží na uzlu. Úkoly jsou přiřazeny k uzlu pro provádění nebo jsou zařazeny do fronty, dokud se uzel neuvolní. Jednoduše řečeno, úkol spustí na výpočetním uzlu jeden nebo více programů nebo skriptů k provedení potřebné práce.

Při vytvoření úkolu můžete zadat:

- **Příkazový řádek** pro úkol. Toto je příkazový řádek, kterým se na výpočetním uzlu spouští vaše aplikace nebo skript.

    Je důležité si uvědomit, že příkazový řádek se v prostředí nespustí. Proto nemůže nativně využívat funkce shellu, jako například rozšíření [proměnné prostředí](#environment-settings-for-tasks) (sem patří i `PATH`). Chcete-li využít výhod těchto funkcí, je nutné vyvolat prostředí z příkazového řádku, například spuštěním `cmd.exe` v uzlech systému Windows nebo `/bin/sh` na systému Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Pokud vaše úkoly potřebují spustit aplikaci nebo skript, které nejsou v `PATH` uzlu nebo v referenčních proměnných prostředí, vyvolejte shell explicitně v příkazovém řádku úkolu.
- **Soubory prostředků** obsahující data, která mají být zpracována. Tyto soubory se před provedením příkazového řádku úkolu automaticky zkopírují do uzlu z úložiště objektů blob v účtu Azure Storage. Další informace najdete v tématu [spuštění úlohy](#start-task) a [souborů a adresářů](files-and-directories.md).
- **Proměnné prostředí**, které jsou požadovány příslušnou aplikací. Další informace najdete v tématu [nastavení prostředí pro úlohy](#environment-settings-for-tasks).
- **Omezení**, za kterých by měl být proveden úkol. Mezi omezení patří například: maximální doba, po kterou smí úkol běžet, maximální počet pokusů o opakování neúspěšného úkolu a maximální doba, po kterou jsou zachovány soubory v pracovním adresáři úkolu.
- **Balíčky aplikací** pro nasazení do výpočetního uzlu, na kterém je naplánováno spuštění úkolu. [Balíčky aplikací](batch-application-packages.md) poskytují zjednodušené nasazení a správu verzí aplikací, které vaše úkoly spouštějí. Balíčky aplikací na úrovni úkolů jsou zvláště užitečné v prostředích sdíleného fondu, kde se různé úlohy spouštějí v jednom fondu a kde se fond po dokončení úlohy neodstraňuje. Pokud má vaše úloha méně úkolů, než je uzlů ve fondu, balíčky aplikací úkolů můžou omezit přenosy dat, protože se aplikace může nasadit jen na uzly, které úkoly budou skutečně provádět.
- Odkaz na **image kontejneru** v Docker Hubu nebo privátním registru a další nastavení pro vytvoření kontejneru Dockeru, ve kterém se bude spouštět úloha na uzlu. Tyto údaje zadáváte pouze v případě, že je ve fondu nastavená konfigurace kontejneru.

> [!NOTE]
> Maximální doba života úkolu, od kdy se přidá do úlohy po jeho dokončení, je 180 dní. Dokončené úlohy trvají po dobu 7 dnů. data pro úlohy, které nejsou dokončená v rámci maximální doby života, nejsou dostupná.

Kromě úloh, které definujete pro provádění výpočtů na uzlu, služba Batch taky poskytuje několik zvláštních úloh:

- [Spouštěcí úkol](#start-task)
- [Úkol správce úloh](#job-manager-task)
- [Úkoly přípravy a uvolnění úloh](#job-preparation-and-release-tasks)
- [Úkoly s více instancemi](#multi-instance-task)
- [Závislosti úkolů](#task-dependencies)

### <a name="start-task"></a>Spouštěcí úkol

Přidružením spouštěcího úkolu k fondu můžete připravit provozní prostředí jeho uzlů. Můžete například provádět akce, jako je instalace aplikací, které vaše úkoly spouštějí, nebo spouštění procesů na pozadí. Spouštěcí úkol se spustí při každém spuštění uzlu, a to tak dlouho, dokud zůstává ve fondu. To zahrnuje, kdy se uzel poprvé přidá do fondu a když se restartuje nebo obnoví z image.

Hlavní výhodou spouštěcího úkolu je, že obsahuje všechny informace potřebné ke konfiguraci výpočetního uzlu a instalaci aplikací nezbytných k provádění úkolu. Proto je navýšení počtu uzlů ve fondu pouze otázkou zadání nového cílového počtu uzlů. Spouštěcí úkol poskytuje informace potřebné ke konfiguraci nových uzlů služby Batch a jejich přípravě na přijetí úkolů.

Stejně jako u každého úkolu služby Azure Batch můžete také zadat seznam souborů prostředků ve službě [Azure Storage](../storage/index.yml) (vedle příkazového řádku, který se má provést). Služba Batch nejprve zkopíruje soubory prostředků ze služby Azure Storage do uzlu a pak spustí příkazový řádek. U spouštěcího úkolu fondu obsahuje seznam souborů obvykle aplikaci úkolu a její závislosti.

Spouštěcí úkol ale může obsahovat také referenční data, která budou k dispozici k použití ve všech úkolech spouštěných na výpočetním uzlu. Například příkazový řádek spouštěcího úkolu může provést `robocopy` operaci kopírování souborů aplikace (které byly zadány jako soubory prostředků a staženy do uzlu) z [pracovního adresáře](files-and-directories.md) spouštěcího úkolu do **sdílené** složky a potom spustit soubor MSI nebo `setup.exe` .

Obvykle je žádoucí, aby služba Batch čekala na dokončení spouštěcího úkolu předtím, než bude uzel považovat za připravený k přiřazení úkolů, ale tuto možnost lze nakonfigurovat.

Pokud na výpočetním uzlu selže spouštěcí úkol, je stav tohoto uzlu aktualizován, aby odrážel selhání, a uzlu se nepřiřazují žádné úlohy. Spouštěcí úkol může selhat, jestliže se vyskytl problém s kopírováním jeho souborů prostředků z úložiště nebo pokud proces spuštěný pomocí jeho příkazového řádku vrátí nenulový ukončovací kód.

Pokud přidáváte nebo aktualizujete spouštěcí úkol pro už existující fond, musíte restartovat jeho výpočetní uzly, aby se na nich spouštěcí úkol provedl.

>[!NOTE]
> Batch omezuje celkovou velikost spouštěcího úkolu, což zahrnuje soubory prostředků i proměnné prostředí. Pokud potřebujete zmenšit velikost spouštěcího úkolu, můžete použít jednu ze dvou následujících metod:
>
> 1. K distribuci aplikací nebo dat napříč všemi uzly ve fondu Batch můžete použít balíčky aplikací. Další informace o balíčcích aplikací najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](batch-application-packages.md).
> 2. Můžete ručně vytvořit komprimovaný archiv obsahující vaše soubory aplikací. Tento komprimovaný archiv potom uložte do služby Azure Storage jako objekt blob. Zadejte tento komprimovaný archiv jako soubor prostředků pro spouštěcí úkol. Před spuštěním příkazového řádku pro spouštěcí úkol rozbalte archiv z příkazového řádku. 
>
>    K rozbalení archivu můžete použít archivační nástroj podle vašeho výběru. Nástroj, který použijete k rozbalení archivu, budete muset zahrnout jako soubor prostředků pro tento spouštěcí úkol.

### <a name="job-manager-task"></a>Úkol správce úloh

Úkol správce úloh se obvykle používá k řízení nebo monitorování provádění úlohy. Například úlohy Správce úloh se často používají k vytvoření a odeslání úkolů pro úlohu, určení dalších úkolů ke spuštění a určení, kdy se práce dokončila.

Úkol správce úloh však není omezen na tyto aktivity. Jedná se o úplný podrobnějším úkol, který může provádět všechny akce, které jsou pro úlohu nutné. Úkol správce úloh může například stáhnout soubor zadaný jako parametr, analyzovat obsah tohoto souboru a odeslat další úkoly na základě těchto obsahů.

Úkol správce úloh je spuštěn před všemi ostatními úkoly. Má následující funkce:

- Je službou Batch automaticky odeslán jako úkol při vytvoření úlohy.
- Je naplánován ke spuštění před dalšími úkoly v rámci úlohy.
- Jeho přidružený uzel je poslední, který se odebere z fondu, když je fond zmenšován.
- Jeho ukončení může být vázáno na ukončení všech úkolů v úloze.
- Úkolu správce úloh je přiřazena nejvyšší priorita, když je nutné jej restartovat. Pokud není k dispozici nečinný uzel, může služba Batch ukončit jeden z ostatních spuštěných úkolů ve fondu, aby uvolnila prostor pro spuštění úkolu správce úloh.
- Úkol správce úloh v jedné úloze nemá přednost před úkoly jiných úloh. Mezi úlohami jsou dodržovány pouze priority s úrovní úlohy.

### <a name="job-preparation-and-release-tasks"></a>Úkoly přípravy a uvolnění úloh

Batch poskytuje úlohy přípravy úloh pro nastavení spuštění před úlohou a úlohy uvolnění úloh pro údržbu nebo čištění po úloze.

Úkol přípravy úlohy se spustí na všech výpočetních uzlech, které mají naplánované spouštění úkolů, před provedením jakékoli jiné úlohy úlohy. Úkol přípravy úlohy můžete například použít ke zkopírování dat, která jsou sdílena všemi úkoly, ale jsou jedinečná pro úlohu.

Po dokončení úlohy se na každém uzlu ve fondu, který spustil alespoň jeden úkol, spustí úkol uvolnění úlohy. Například úkol uvolnění úlohy může odstranit data, která byla zkopírována úlohou přípravy úlohy nebo může komprimovat a odeslat data protokolu diagnostiky.

Úkoly přípravy i uvolnění úloh umožňují zadat příkazový řádek, který se provede, když je úkol vyvolán. Nabízejí funkce, jako například stahování souborů, provádění se zvýšenými oprávněními, vlastní proměnné prostředí, maximální dobu provádění, počet opakování a dobu uchovávání souboru.

Další informace ohledně úkolů přípravy a uvolnění úloh najdete v části [Spouštění úkolů přípravy a dokončení úlohy na výpočetních uzlech Azure Batch](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Úkoly s více instancemi

[Úkol s více instancemi](batch-mpi.md) je úkol, který je nakonfigurován, aby byl současně spuštěn na více než jednom výpočetním uzlu. Pomocí úkolů s více instancemi můžete povolit vysoce výkonné výpočetní scénáře, které vyžadují skupinu výpočetních uzlů, které jsou přiděleny společně pro zpracování jedné úlohy, jako je například rozhraní předávání zpráv (MPI).

Podrobné informace o spouštění úloh MPI ve službě Batch pomocí knihovny Batch .NET najdete v článku [Použití úkolů s více instancemi ke spouštění aplikací rozhraní MPI (Message Passing Interface) v Azure Batch](batch-mpi.md).

### <a name="task-dependencies"></a>Závislosti úkolů

[Závislosti úkolů](batch-task-dependencies.md), jak již název napovídá, umožňují určit, jestli úkol závisí před svým spuštěním na dokončení jiných úkolů. Tato funkce poskytuje podporu pro situace, ve kterých "podřízený" úkol spotřebovává výstup "nadřazeného" úkolu nebo když nadřazený úkol provádí inicializaci, která je požadovaná podřízeným úkolem.

Chcete-li použít tuto funkci, musíte nejprve [Povolit závislosti úkolů](batch-task-dependencies.md#enable-task-dependencies
) na úloze služby Batch. Pak pro každý úkol, který závisí na jiném (nebo mnoha jiných), zadáte úkoly, na kterých tento úkol závisí.

Pomocí závislosti úkolů lze nakonfigurovat například následující scénáře:

- *úkolb* závisí na *úloze* a (*úkolb* se nespustí, dokud se nedokončí *úkol* ).
- *Úkol C* závisí na *úkolu A* i *úkolu B*.
- *úkol* závisí na řadě úkolů, například na úkolech *1* až *10*, než se spustí.

Další podrobnosti najdete v tématu [závislosti úloh v Azure Batch](batch-task-dependencies.md) a ukázka kódu [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) v úložišti GitHub [Azure-Batch-Samples](https://github.com/Azure-Samples/azure-batch-samples) .

### <a name="environment-settings-for-tasks"></a>Nastavení prostředí pro úlohy

Každý úkol prováděný službou Batch má přístup k proměnným prostředí, které nastaví na výpočetních uzlech. To zahrnuje proměnné prostředí[definované službou Batch (a](./batch-compute-node-environment-variables.md) vlastní proměnné prostředí, které můžete definovat pro vaše úkoly). Aplikace a skripty zpracovávané vašimi úkoly mají během zpracování přístup k těmto proměnným prostředí.

Vlastní proměnné prostředí můžete nastavit na úrovni úkolů či úloh tím, že vyplníte vlastnost *nastavení prostředí* pro tyto entity. Další podrobnosti najdete v tématu věnovaném operaci [Přidání úlohy do úlohy](/rest/api/batchservice/task/add?)] (Batch REST API), nebo vlastností [CloudTask. EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) a [vlastnosti cloudjob. CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) v dávce .NET.

Vaše klientská aplikace nebo služba může získat službou i uživatelem definované proměnné prostředí úkolu pomocí operace [Získat informace o úkolu](/rest/api/batchservice/task/get) (Batch REST) nebo přístupem k vlastnosti [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (Batch .NET). Procesy prováděné na výpočetním uzlu mohou také přistupovat k těmto i ostatním proměnným prostředí na uzlu například pomocí známé syntaxe `%VARIABLE_NAME%` (Windows) nebo `$VARIABLE_NAME` (Linux).

Úplný seznam všech proměnných prostředí definovaných službou najdete v článku [Proměnné prostředí výpočetního uzlu](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Další kroky

- Seznamte [se se soubory a adresáři](files-and-directories.md).
