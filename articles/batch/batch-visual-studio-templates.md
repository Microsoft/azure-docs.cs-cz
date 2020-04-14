---
title: Vytváření řešení pomocí šablon Visual Studia – Azure Batch | Dokumenty společnosti Microsoft
description: Zjistěte, jak vám šablony projektů Visual Studia můžou pomoct implementovat a spouštět úlohy náročné na výpočetní výkon v Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: e42917237f3b114881655d88a017c2c4366612b3
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254559"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Použití šablon projektů sady Visual Studio k nastartování dávkových řešení

**Šablony správce úloh** a **procesorúloh Visual Studio** pro Batch poskytují kód, který vám pomůže implementovat a spustit úlohy náročné na výpočetní výkon v batch s nejmenším úsilím. Tento dokument popisuje tyto šablony a poskytuje pokyny pro jejich použití.

> [!IMPORTANT]
> Tento článek popisuje pouze informace vztahující se k těmto dvěma šablonám a předpokládá, že jste obeznámeni se službou Batch a klíčovými koncepty, které s ní souvisejí: fondy, výpočetní uzly, úlohy a úkoly, úlohy správce úloh, proměnné prostředí a další relevantní informace. Další informace najdete v [přehledu základních](batch-technical-overview.md) funkcí Azure Batch a [Batch pro vývojáře](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Podrobný přehled
Šablony Správce úloh a Procesor úloh lze použít k vytvoření dvou užitečných součástí:

* Úloha správce úloh, která implementuje rozdělovač úloh, který může rozdělit úlohu na více úloh, které lze spustit nezávisle, paralelně.
* Procesor úloh, který lze použít k provedení předběžného zpracování a následného zpracování kolem příkazového řádku aplikace.

Například ve scénáři vykreslování filmu by rozdělovač úloh přeměnil jednu úlohu filmu na stovky nebo tisíce samostatných úloh, které by zpracovaly jednotlivé snímky samostatně. Odpovídajícím způsobem by procesor úloh vyvolat vykreslovací aplikace a všechny závislé procesy, které jsou potřebné k vykreslení každého rámce, stejně jako provádět všechny další akce (například kopírování vykresleného rámce do umístění úložiště).

> [!NOTE]
> Šablony Job Managera a task processoru jsou na sobě nezávislé, takže můžete použít obojí nebo jenom jednu z nich v závislosti na požadavcích vaší výpočetní úlohy a vašich preferencích.
> 
> 

Jak je znázorněno na obrázku níže, výpočetní úloha, která používá tyto šablony, projde třemi fázemi:

1. Kód klienta (např. aplikace, webová služba atd.) odešle úlohu službě Batch v Azure a jako úlohu správce úloh určí program správce úloh.
2. Služba Batch spustí úlohu správce úloh na výpočetním uzlu a rozdělovač úloh spustí zadaný počet úloh procesoru úloh na tolika výpočetních uzlech, kolik je požadováno, na základě parametrů a specifikací v kódu rozdělovače úloh.
3. Úlohy procesoru úloh spustit nezávisle, paralelně, ke zpracování vstupních dat a generování výstupních dat.

![Diagram znázorňující interakci klientského kódu se službou Batch][diagram01]

## <a name="prerequisites"></a>Požadavky
Chcete-li použít šablony Batch, budete potřebovat následující:

* Počítač s nainstalovaným Visual Studio 2015. Dávkové šablony jsou aktuálně podporované pouze pro Visual Studio 2015.
* Předlohy Batch, které jsou k dispozici v [Galerii Visual Studia][vs_gallery] jako rozšíření sady Visual Studio. Existují dva způsoby, jak získat šablony:
  
  * Nainstalujte šablony pomocí dialogového okna **Rozšíření a aktualizace** v sadě Visual Studio (další informace naleznete v [tématu Hledání a používání rozšíření sady Visual Studio][vs_find_use_ext]). V dialogovém okně **Rozšíření a aktualizace** vyhledejte a stáhněte následující dvě rozšíření:
    
    * Správce dávkových úloh Azure s rozdělovačem úloh
    * Procesor dávek Azure
  * Stažení šablon z online galerie pro Visual Studio: [Šablony dávkových projektů Microsoft Azure][vs_gallery_templates]
* Pokud plánujete použít funkci [Balíčky aplikací](batch-application-packages.md) k nasazení správce úloh a procesoru úloh do výpočetních uzlů Batch, musíte propojit účet úložiště s účtem Batch.

## <a name="preparation"></a>Příprava
Doporučujeme vytvořit řešení, které může obsahovat správce úloh i procesor úloh, protože to může usnadnit sdílení kódu mezi programem správce úloh a programů procesoru úloh. Chcete-li vytvořit toto řešení, postupujte takto:

1. Otevřete Visual Studio a vyberte **Soubor** > **nový** > **projekt**.
2. V **části Šablony**rozbalte **položku Další typy projektů**, klepněte na položku Řešení sady Visual **Studio**a vyberte možnost **Prázdné řešení**.
3. Zadejte název, který popisuje vaši aplikaci a účel tohoto řešení (například "LitwareBatchTaskPrograms").
4. Chcete-li vytvořit nové řešení, klepněte na tlačítko **OK**.

## <a name="job-manager-template"></a>Šablona Správce úloh
Šablona Správce úloh vám pomůže implementovat úlohu správce úloh, která může provádět následující akce:

* Rozdělení úlohy na více úkolů.
* Odešlete tyto úlohy ke spuštění na Batch.

> [!NOTE]
> Další informace o úlohách správce úloh naleznete v [tématu Přehled dávkových funkcí pro vývojáře](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Vytvoření správce úloh pomocí šablony
Chcete-li přidat správce úloh do řešení, které jste vytvořili dříve, postupujte takto:

1. Otevřete stávající řešení v sadě Visual Studio.
2. V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a klikněte na **Přidat** > **nový projekt**.
3. V **části Visual C#** klikněte na **Cloud**a potom klikněte na Azure Batch Job Manager with **Job Splitter**.
4. Zadejte název, který popisuje vaši aplikaci a identifikuje tento projekt jako správce úloh (např.
5. Chcete-li vytvořit projekt, klepněte na tlačítko **OK**.
6. Nakonec vytvořte projekt vynutit Visual Studio načíst všechny odkazované balíčky NuGet a ověřit, že projekt je platný před zahájením jeho úpravy.

### <a name="job-manager-template-files-and-their-purpose"></a>Soubory šablon Správce úloh a jejich účel
Když vytvoříte projekt pomocí šablony Správce úloh, generuje tři skupiny souborů kódu:

* Hlavní soubor programu (Program.cs). Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně. Za normálních okolností byste to neměli měnit.
* Adresář rozhraní Framework. Obsahuje soubory odpovědné za "často používaný text" práce prováděné programem správce úloh – rozbalení parametrů, přidání úkolů do dávkové úlohy atd. Za normálních okolností byste neměli tyto soubory upravovat.
* Soubor rozdělovače úloh (JobSplitter.cs). Toto je místo, kde budete umístit logiku specifické pro aplikaci pro rozdělení úlohy do úkolů.

Samozřejmě můžete přidat další soubory podle potřeby pro podporu kódu rozdělovač úlohy, v závislosti na složitosti logiky rozdělení úlohy.

Šablona také generuje standardní soubory projektu .NET, jako je soubor .csproj, app.config, packages.config atd.

Zbývající část této části popisuje různé soubory a jejich strukturu kódu a vysvětluje, co jednotlivé třídy dělá.

![Průzkumník řešení Visual Studia zobrazující řešení šablony Správce úloh][solution_explorer01]

**Soubory frameworku**

* `Configuration.cs`: Zapouzdřuje načítání dat konfigurace úlohy, jako jsou podrobnosti o účtu dávky, pověření propojeného účtu úložiště, informace o úloze a úloze a parametry úlohy. Poskytuje také přístup k dávkově definovaným proměnným prostředí (viz nastavení prostředí pro úkoly v dokumentaci batch) prostřednictvím třídy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrahuje implementaci třídy Configuration, takže můžete otestovat rozdělovač úloh pomocí falešného nebo falešného konfiguračního objektu.
* `JobManager.cs`: Orchestruje součásti programu správce úloh. Je zodpovědný za inicializaci rozdělovačúloh, vyvolání rozdělovačúloh a odeslání úkolů vrácených rozdělovačem úlohy do zadavatele.
* `JobManagerException.cs`: Představuje chybu, která vyžaduje ukončení správce úloh. JobManagerException se používá k zabalení "očekávané" chyby, kde konkrétní diagnostické informace mohou být poskytnuty jako součást ukončení.
* `TaskSubmitter.cs`: Tato třída je zodpovědná za přidání úkolů vrácených rozdělovačem úloh do dávkové úlohy. JobManager třída agreguje pořadí úkolů do dávek pro efektivní, ale včasné přidání do úlohy, pak volá TaskSubmitter.SubmitTasks na vlákno na pozadí pro každou dávku.

**Rozdělovač úlohy**

`JobSplitter.cs`: Tato třída obsahuje logiku specifickou pro aplikaci pro rozdělení úlohy na úkoly. Rozhraní framework vyvolá metodu JobSplitter.Split, aby získala posloupnost úkolů, které přidá do úlohy, když je metoda vrátí. Toto je třída, kde budete aplikovat logiku své práce. Implementujte Split metoda vrátit posloupnost CloudTask instance představující úkoly, do kterých chcete rozdělit úlohu.

**Standardní soubory projektu příkazového řádku rozhraní .NET**

* `App.config`: Standardní konfigurační soubor aplikace .NET.
* `Packages.config`: Standardní soubor závislostí balíčku NuGet.
* `Program.cs`: Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně.

### <a name="implementing-the-job-splitter"></a>Implementace rozdělovače úloh
Když otevřete projekt šablony Správce úloh, projekt bude mít ve výchozím nastavení otevřený JobSplitter.cs soubor. Můžete implementovat logiku rozdělení pro úlohy ve vaší pracovní zátěži pomocí Split() metoda zobrazit níže:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> Část s popisem v `Split()` metodě je jedinou částí kódu šablony Správce úloh, která je určena k úpravě přidáním logiky pro rozdělení úloh na různé úkoly. Pokud chcete upravit jinou část šablony, ujistěte se, že jste obeznámeni s tím, jak Batch funguje, a vyzkoušejte několik [ukázek dávkového kódu][github_samples].
> 
> 

Vaše implementace Split() má přístup k:

* Parametry úlohy prostřednictvím `_parameters` pole.
* Objekt CloudJob představující úlohu prostřednictvím `_job` pole.
* Objekt CloudTask představující úlohu správce úlohy prostřednictvím `_jobManagerTask` pole.

Implementace `Split()` nemusí přidávat úkoly přímo do úlohy. Místo toho váš kód by měl vrátit posloupnost CloudTask objekty a tyto budou přidány do úlohy automaticky framework třídy, které vyvolávají rozdělovač úloh. Je běžné používat c#'s iterator`yield return`( ) funkce implementovat rozdělovačúloh, protože to umožňuje úlohy spustit co nejdříve, spíše než čekat na všechny úkoly, které mají být vypočteny.

**Selhání rozdělovače úloh**

Pokud rozdělovač úlohy narazí na chybu, měl by buď:

* Ukončit pořadí pomocí `yield break` příkazu C#, v takovém případě bude správce úloh považován za úspěšný; Nebo
* Vyvolat výjimku, v takovém případě bude správce úloh považován za neúspěšný a může být opakován v závislosti na tom, jak jej klient nakonfiguroval).

V obou případech budou všechny úkoly, které již byly vráceny rozdělovačem úloh a přidány do dávkové úlohy, způsobilé ke spuštění. Pokud nechcete, aby se to stalo, pak byste mohli:

* Ukončení úlohy před návratem z rozdělovače úloh
* Formulovat celou kolekci úkolů před vrácením (to `IList<CloudTask>` znamená vrátit nebo `ICollection<CloudTask>` namísto implementace rozdělovačúloh pomocí iterátoru Jazyka C#)
* Použití závislostí mezi úkoly k tomu, aby všechny úkoly závisely na úspěšném dokončení správce úloh

**Opakování úlohy vedoucího**

Pokud správce úloh selže, může být opakován službou Batch v závislosti na nastavení opakování klienta. Obecně je to bezpečné, protože když rámec přidá úkoly do úlohy, ignoruje všechny úkoly, které již existují. Pokud je však výpočet úkolů nákladný, nemusíte chtít vynaložit náklady na přepočet úkolů, které již byly do projektu přidány. naopak, pokud není zaručeno, že opakované spuštění vygeneruje id stejné úlohy, nebude chování "ignorovat duplikáty". V těchto případech byste měli navrhnout rozdělovač úlohy, abyste zjistili práci, která již byla provedena, a neopakovat ji, například provedením CloudJob.ListTasks před zahájením výnosů úkolů.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Ukončovací kódy a výjimky v šabloně Správce úloh
Ukončovací kódy a výjimky poskytují mechanismus k určení výsledku spuštění programu a mohou pomoci identifikovat všechny problémy s prováděním programu. Šablona Job Manager implementuje ukončovací kódy a výjimky popsané v této části.

Úloha správce úloh, která je implementována pomocí šablony Správce úloh, může vrátit tři možné kódy ukončení:

| kód | Popis |
| --- | --- |
| 0 |Vedoucí úloh byl úspěšně dokončen. Kód rozdělovače úloh byl dokončen a všechny úkoly byly přidány do úlohy. |
| 1 |Úloha správce úloh se nezdařila s výjimkou v "očekávané" části programu. Výjimka byla přeložena do JobManagerException s diagnostickými informacemi a pokud je to možné, návrhy na řešení selhání. |
| 2 |Úloha správce úloh se nezdařila s neočekávanou výjimkou. Výjimka byla zaznamenána do standardního výstupu, ale správce úloh nemohl přidat žádné další diagnostické nebo nápravné informace. |

V případě selhání úlohy správce úloh y mohou být některé úkoly ještě přidány do služby před tím, než došlo k chybě. Tyto úlohy budou spuštěny jako normální. Viz "Selhání rozdělovač úlohy" výše pro diskusi o této cestě kódu.

Všechny informace vrácené výjimkami jsou zapsány do souborů stdout.txt a stderr.txt. Další informace naleznete v [tématu Zpracování chyb](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Aspekty klienta
Tato část popisuje některé požadavky implementace klienta při vyvolání správce úloh na základě této šablony. Podrobnosti o předávání parametrů a nastavení prostředí naleznete v tématu [Jak předat parametry a proměnné prostředí z klientského kódu.](#pass-environment-settings)

**Povinné pověření**

Aby bylo možné přidat úkoly do úlohy Azure Batch, úloha správce úloh vyžaduje adresu URL a klíč účtu Azure Batch. Je nutné předat tyto proměnné prostředí s názvem YOUR_BATCH_URL a YOUR_BATCH_KEY. Můžete je nastavit v nastavení prostředí úloh Správce úloh. Například v klientovi Jazyka C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Pověření úložiště**

Klient obvykle nemusí poskytovat pověření propojeného účtu úložiště k úloze správce úloh, protože (a) většina správců úloh nemusí explicitně přistupovat k propojenému účtu úložiště a (b) propojený účet úložiště je často k dispozici všem úkolům jako společné nastavení prostředí pro úlohu. Pokud neposkytujete účet propojeného úložiště prostřednictvím společného nastavení prostředí a správce úloh vyžaduje přístup k propojenému úložišti, měli byste zadat pověření propojeného úložiště následujícím způsobem:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Nastavení úloh správce úloh**

Klient by měl nastavit správce úloh *killJobOnCompletion* příznak **false**.

Obvykle je bezpečné pro klienta nastavit *runExclusive* na **false**.

Klient by měl použít *kolekci resourceFiles* nebo *applicationPackageReferences* k tomu, aby byl do výpočetního uzlu nasazen správce úloh (a jeho požadované knihovny DLL).

Ve výchozím nastavení nebude správce úloh opakován, pokud se nezdaří. V závislosti na logice správce úloh může klient chtít povolit opakování pomocí *omezení*/*maxTaskRetryCount*.

**Nastavení úlohy**

Pokud rozdělovač úloh y vydává úkoly se závislostmi, musí klient nastavit usesTaskDependencies úlohy na true.

V modelu rozdělovač úloh je neobvyklé, že klienti chtějí přidávat úkoly do úloh nad rámec toho, co vytváří rozdělovač úloh. Klient by proto měl obvykle nastavit úlohy *naAllTasksComplete* **ukončit úlohu**.

## <a name="task-processor-template"></a>Šablona procesoru úloh
Šablona procesoru úloh vám pomůže implementovat procesor úloh, který může provádět následující akce:

* Nastavte informace vyžadované jednotlivými úlohami dávky ke spuštění.
* Spusťte všechny akce vyžadované jednotlivými úlohami dávky.
* Uložte výstupy úloh do trvalého úložiště.

Přestože procesor úloh není nutné spustit úlohy na Batch, klíčovou výhodou použití procesoru úloh je, že poskytuje obálku k implementaci všech akcí provádění úloh v jednom umístění. Například pokud potřebujete spustit několik aplikací v kontextu každé úlohy, nebo pokud potřebujete zkopírovat data do trvalého úložiště po dokončení každého úkolu.

Akce prováděné procesorem úloh může být stejně jednoduché nebo složité a tolik nebo jako málo, jak to vyžaduje vaše úlohy. Navíc implementací všech akcí úloh do jednoho procesoru úloh můžete snadno aktualizovat nebo přidat akce na základě změn aplikací nebo požadavků na pracovní vytížení. V některých případech však procesor úloh nemusí být optimálním řešením pro implementaci, protože může přidat zbytečnou složitost, například při spuštění úloh, které lze rychle spustit z jednoduchého příkazového řádku.

### <a name="create-a-task-processor-using-the-template"></a>Vytvoření procesoru úloh pomocí šablony
Chcete-li přidat procesor úloh do řešení, které jste vytvořili dříve, postupujte takto:

1. Otevřete stávající řešení v sadě Visual Studio.
2. V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení, klikněte na **Přidat**a potom klikněte na **Nový projekt**.
3. V **části Visual C#** klikněte na **Cloud**a potom klikněte na Azure Batch **Task Processor**.
4. Zadejte název, který popisuje vaši aplikaci a identifikuje tento projekt jako procesor úloh (např.
5. Chcete-li vytvořit projekt, klepněte na tlačítko **OK**.
6. Nakonec vytvořte projekt vynutit Visual Studio načíst všechny odkazované balíčky NuGet a ověřit, že projekt je platný před zahájením jeho úpravy.

### <a name="task-processor-template-files-and-their-purpose"></a>Soubory šablon procesoru úloh a jejich účel
Když vytvoříte projekt pomocí šablony procesoru úloh, generuje tři skupiny souborů kódu:

* Hlavní soubor programu (Program.cs). Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně. Za normálních okolností byste to neměli měnit.
* Adresář rozhraní Framework. Obsahuje soubory odpovědné za "často používaný text" práce prováděné programem správce úloh – rozbalení parametrů, přidání úkolů do dávkové úlohy atd. Za normálních okolností byste neměli tyto soubory upravovat.
* Soubor procesoru úloh (TaskProcessor.cs). Toto je místo, kde budete umístit logiku specifické pro aplikaci pro provádění úlohy (obvykle voláním existující spustitelný soubor). Pre- a post-processing kód, jako je stahování dalších dat nebo nahrávání výsledných souborů, také jde sem.

Samozřejmě můžete přidat další soubory podle potřeby pro podporu kódu procesoru úloh, v závislosti na složitosti logiky rozdělení úlohy.

Šablona také generuje standardní soubory projektu .NET, jako je soubor .csproj, app.config, packages.config atd.

Zbývající část této části popisuje různé soubory a jejich strukturu kódu a vysvětluje, co jednotlivé třídy dělá.

![Průzkumník řešení Visual Studia zobrazující řešení šablony úlohového procesoru][solution_explorer02]

**Soubory frameworku**

* `Configuration.cs`: Zapouzdřuje načítání dat konfigurace úlohy, jako jsou podrobnosti o účtu dávky, pověření propojeného účtu úložiště, informace o úloze a úloze a parametry úlohy. Poskytuje také přístup k dávkově definovaným proměnným prostředí (viz nastavení prostředí pro úkoly v dokumentaci batch) prostřednictvím třídy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrahuje implementaci třídy Configuration, takže můžete otestovat rozdělovač úloh pomocí falešného nebo falešného konfiguračního objektu.
* `TaskProcessorException.cs`: Představuje chybu, která vyžaduje ukončení správce úloh. TaskProcessorException se používá k zabalení "očekávané" chyby, kde konkrétní diagnostické informace mohou být poskytnuty jako součást ukončení.

**Procesor úloh**

* `TaskProcessor.cs`: Spustí úlohu. Rozhraní framework vyvolá metodu TaskProcessor.Run. Toto je třída, kde vložíte logiku specifické pro aplikaci úlohy. Implementujte metodu Run takto:
  * Analyzovat a ověřit všechny parametry úlohy
  * Vytvořit příkazový řádek pro libovolný externí program, který chcete vyvolat.
  * Protokolovat všechny diagnostické informace, které můžete potřebovat pro účely ladění
  * Spuštění procesu pomocí tohoto příkazového řádku
  * Počkejte na ukončení procesu
  * Zachyťte ukončovací kód procesu a zjistěte, zda byl úspěšný nebo neúspěšný.
  * Uložení všech výstupních souborů, které chcete zachovat, na trvalé úložiště

**Standardní soubory projektu příkazového řádku rozhraní .NET**

* `App.config`: Standardní konfigurační soubor aplikace .NET.
* `Packages.config`: Standardní soubor závislostí balíčku NuGet.
* `Program.cs`: Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně.

## <a name="implementing-the-task-processor"></a>Implementace procesoru úloh
Při otevření projektu šablony procesoru úloh bude mít projekt ve výchozím nastavení otevřený TaskProcessor.cs soubor. Logiku spuštění pro úlohy ve vaší pracovní zátěži můžete implementovat pomocí metody Run() uvedené níže:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> Anotovaný oddíl v Run() metoda je jediná část kódu šablony task processor, který je určen pro úpravu přidáním logiky spuštění pro úlohy ve vaší pracovní zátěži. Pokud chcete upravit jinou část šablony, nejprve se seznamte s tím, jak Batch funguje, a to kontrolou dokumentace dávky a vyzkoušením několika ukázek dávkového kódu.
> 
> 

Run() Metoda je zodpovědná za spuštění příkazového řádku, spuštění jednoho nebo více procesů, čekání na dokončení celého procesu, uložení výsledků a nakonec vrácení s ukončovacíkód. Run() Metoda je, kde implementovat logiku zpracování pro vaše úkoly. Architektura procesoru úloh vyvolá Run() metoda pro vás; nemusíte volat sami.

Implementace Run() má přístup k:

* Parametry úkolu prostřednictvím `_parameters` pole.
* Id úlohy a úlohy `_jobId` `_taskId` prostřednictvím polí a.
* Konfigurace úlohy `_configuration` prostřednictvím pole.

**Selhání úlohy**

V případě selhání můžete ukončit Run() metoda vyvoláním výjimky, ale to ponechává obslužnou rutinu výjimky nejvyšší úrovně v řízení kódu ukončení úlohy. Pokud potřebujete řídit ukončovací kód, abyste mohli rozlišit různé typy selhání, například pro diagnostické účely nebo proto, že některé režimy selhání by měly ukončit úlohu a jiné by neměly, měli byste ukončit metodu Run() vrácením nenulového ukončovacího kódu. To se stane kód ukončení úlohy.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Ukončovací kódy a výjimky v šabloně Procesor úloh
Ukončovací kódy a výjimky poskytují mechanismus k určení výsledku spuštění programu a mohou pomoci identifikovat všechny problémy s prováděním programu. Šablona task processor implementuje ukončovací kódy a výjimky popsané v této části.

Úloha procesoru úloh, která je implementována pomocí šablony procesoru úloh, může vrátit tři možné ukončovací kódy:

| kód | Popis |
| --- | --- |
| [Proces.Ukončovací kód][process_exitcode] |Procesor úloh byl dokončen. Všimněte si, že to neznamená, že program, který jste vyvolali byl úspěšný – pouze, že procesor úloh y jej úspěšně vyvolat a provedl jakékoli následné zpracování bez výjimky. Význam ukončovacího kódu závisí na vyvolaném programu – obvykle ukončovací kód 0 znamená, že program byl úspěšný a jakýkoli jiný ukončovací kód znamená, že program selhal. |
| 1 |Procesor úloh se nezdařil s výjimkou v "očekávané" části programu. Výjimka byla přeložena `TaskProcessorException` do diagnostické informace a pokud možno návrhy na vyřešení selhání. |
| 2 |Procesor úloh se nezdařil s neočekávanou výjimkou. Výjimka byla zaznamenána do standardního výstupu, ale procesor úloh nemohl přidat žádné další diagnostické nebo nápravné informace. |

> [!NOTE]
> Pokud program, který vyvoláte, používá k označení určitých režimů selhání kódy ukončení 1 a 2, je použití kódů ukončení 1 a 2 pro chyby procesoru úloh nejednoznačné. Tyto kódy chyb procesoru úloh můžete změnit na charakteristické kódy ukončení úpravou případů výjimek v souboru Program.cs.
> 
> 

Všechny informace vrácené výjimkami jsou zapsány do souborů stdout.txt a stderr.txt. Další informace naleznete v tématu Zpracování chyb v dokumentaci dávky.

### <a name="client-considerations"></a>Aspekty klienta
**Pověření úložiště**

Pokud váš procesor úloh používá úložiště objektů blob Azure k uchování výstupů, například pomocí pomocné knihovny konvencí souborů, potřebuje přístup *k* přihlašovacím údajům účtu cloudového úložiště *nebo* k adrese URL kontejneru objektů blob, který obsahuje sdílený přístupový podpis (SAS). Šablona obsahuje podporu pro poskytování přihlašovacích údajů prostřednictvím běžných proměnných prostředí. Váš klient může předat pověření úložiště následujícím způsobem:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Účet úložiště je pak k dispozici ve `_configuration.StorageAccount` třídě TaskProcessor prostřednictvím vlastnosti.

Pokud dáváte přednost použití adresy URL kontejneru s SAS, můžete také předat prostřednictvím nastavení společného prostředí úlohy, ale šablona procesoru úloh aktuálně neobsahuje integrovanou podporu pro toto.

**Nastavení úložiště**

Před přidáním úkolů do úlohy se doporučuje, aby úloha klienta nebo správce úloh vytvořila všechny kontejnery vyžadované úkoly. To je povinné, pokud používáte adresu URL kontejneru s SAS, jako taková adresa URL neobsahuje oprávnění k vytvoření kontejneru. Doporučuje se i v případě, že předáte přihlašovací údaje účtu úložiště, protože ukládá všechny úlohy, které mají volat CloudBlobContainer.CreateIfNotExistsAsync v kontejneru.

## <a name="pass-parameters-and-environment-variables"></a>Předat parametry a proměnné prostředí
### <a name="pass-environment-settings"></a>Předat nastavení prostředí
Klient může předat informace úlohě správce úlohy ve formě nastavení prostředí. Tyto informace pak může úloha správce úloh použít při generování úloh procesoru úloh, které budou spuštěny jako součást výpočetní úlohy. Příklady informací, které můžete předat jako nastavení prostředí jsou:

* Název účtu úložiště a klíče účtu
* Adresa URL dávkového účtu
* Klíč dávkového účtu

Služba Batch má jednoduchý mechanismus pro předání nastavení prostředí `EnvironmentSettings` úloze správce úloh pomocí vlastnosti [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Chcete-li například `BatchClient` získat instanci pro účet Batch, můžete předat jako proměnné prostředí z kódu klienta adresu URL a pověření sdíleného klíče pro účet Batch. Podobně pro přístup k účtu úložiště, který je propojen s účtem Batch, můžete předat název účtu úložiště a klíč účtu úložiště jako proměnné prostředí.

### <a name="pass-parameters-to-the-job-manager-template"></a>Předat parametry šabloně Správce úloh
V mnoha případech je užitečné předat parametry na úlohu úlohy správce úlohy, a to buď pro řízení procesu rozdělení úlohy, nebo pro konfiguraci úloh pro úlohu. To lze provést nahráním souboru JSON s názvem parameters.json jako souboru prostředků pro úlohu správce úloh. Parametry pak mohou být `JobSplitter._parameters` k dispozici v poli v šabloně Správce úloh.

> [!NOTE]
> Vestavěná obslužná rutina parametru podporuje pouze slovníky string-to-string. Pokud chcete předat komplexní hodnoty JSON jako hodnoty parametrů, budete muset předat tyto řetězce a analyzovat je v `Configuration.GetJobParameters` rozdělovači úloh nebo upravit metodu rozhraní.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Předání parametrů šabloně procesoru úloh
Parametry můžete také předat jednotlivým úlohám implementovaným pomocí šablony Task Processor. Stejně jako u šablony správce úloh hledá šablona procesoru úloh soubor prostředků s názvem

parameters.json, a pokud byl nalezen, načte jej jako slovník parametrů. Existuje několik možností, jak předat parametry úlohprocesoru úloh:

* Znovu použít parametry úlohy JSON. To funguje dobře, pokud jsou jedinými parametry ty, které jsou celé úlohy (například výška a šířka vykreslení). Chcete-li to provést, při vytváření CloudTask v rozdělovač úlohy, přidejte odkaz na parameters.json`JobSplitter._jobManagerTask.ResourceFiles`objektu souboru prostředků z úlohy správce úlohy ResourceFiles ( ) do kolekce CloudTask ResourceFiles.
* Vygenerujte a nahrajte dokument parameters.json specificképro úlohu jako součást provádění rozdělovače úloh a odkazujte na tento objekt blob v kolekci souborů prostředků úlohy. To je nezbytné, pokud různé úkoly mají různé parametry. Příkladem může být scénář 3D vykreslování, kde je index rámce předán úloze jako parametr.

> [!NOTE]
> Vestavěná obslužná rutina parametru podporuje pouze slovníky string-to-string. Pokud chcete předat komplexní hodnoty JSON jako hodnoty parametrů, budete muset předat tyto řetězce a analyzovat je v `Configuration.GetTaskParameters` procesoru úloh nebo upravit metodu rozhraní.
> 
> 

## <a name="next-steps"></a>Další kroky
### <a name="persist-job-and-task-output-to-azure-storage"></a>Zachování úlohy a výstupu úloh do Úložiště Azure
Dalším užitečným nástrojem ve vývoji řešení Batch je [Azure Batch File Conventions][nuget_package]. Pomocí této knihovny tříd .NET (aktuálně ve verzi preview) v aplikacích Batch .NET můžete snadno ukládat a načítat výstupy úloh do a z Azure Storage. [Zachovat Azure Batch úlohy a výstup úlohy](batch-task-output.md) obsahuje úplnou diskusi o knihovně a jeho využití.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://github.com/Azure/batch-extension-templates
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
