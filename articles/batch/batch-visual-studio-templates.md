---
title: Vytváření řešení pomocí šablon sady Visual Studio – Azure Batch | Microsoft Docs
description: Přečtěte si, jak vám šablony projektů sady Visual Studio pomůžou implementovat a spouštět úlohy náročné na výpočetní výkon na Azure Batch.
ms.topic: article
ms.date: 02/27/2017
ms.custom: seodec18
ms.openlocfilehash: 8e8d5be4a9f0fb5482ba6c86a8766a25e5713c09
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117518"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Použití šablon projektů sady Visual Studio ke skoku na zahájení dávkového řešení

Šablony **úloh** a **procesorů sady Visual Studio** pro Batch poskytují kód, který vám umožňuje implementovat a spouštět úlohy náročné na výpočetní výkon v dávce s minimálním úsilím. Tento dokument popisuje tyto šablony a poskytuje pokyny k jejich použití.

> [!IMPORTANT]
> Tento článek popisuje pouze informace týkající se těchto dvou šablon a předpokládá, že jste obeznámeni se službou Batch a s klíčovými koncepty, které se týkají: fondy, výpočetní uzly, úlohy a úkoly, úlohy Správce úloh, proměnné prostředí a další relevantní informace. Další informace najdete v článku [základy Azure Batch](batch-technical-overview.md) a [Batch – přehled funkcí pro vývojáře](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Podrobný přehled
Šablony úlohy Správce úloh a procesoru úloh lze použít k vytvoření dvou užitečných komponent:

* Úkol správce úloh, který implementuje rozdělovač úlohy, který může úlohu rozdělit do několika úloh, které mohou běžet nezávisle, paralelně.
* Procesor úloh, který lze použít k provedení předběžného zpracování a následného zpracování kolem příkazového řádku aplikace.

Například ve scénáři vykreslování filmu by příčka úlohy provede jednu filmovou úlohu na stovky nebo tisíce samostatných úloh, které by zpracovaly jednotlivé snímky samostatně. V souladu s tím procesor úlohy vyvolal aplikaci vykreslování a všechny závislé procesy, které jsou potřebné pro vykreslení jednotlivých rámců, a také provádění dalších akcí (například zkopírování vykresleného snímku do umístění úložiště).

> [!NOTE]
> Šablony úloh a procesoru úloh jsou nezávisle na sobě navzájem nezávislé, takže se můžete rozhodnout použít obojí nebo pouze jeden z nich, v závislosti na požadavcích výpočetní úlohy a na vašich preferencích.
> 
> 

Jak je znázorněno v následujícím diagramu, výpočetní úloha, která používá tyto šablony, projde třemi fázemi:

1. Klientský kód (např. aplikace, Webová služba atd.) odesílá úlohu do služby Batch v Azure a jako úlohu správce úloh zadává program Správce úloh.
2. Služba Batch spustí úlohu správce úloh ve výpočetním uzlu a příčka úlohy spustí zadaný počet úloh procesoru úloh, a to na základě požadavků a specifikací v kódu rozdělovače úlohy.
3. Úlohy procesoru úloh se spouštějí samostatně, paralelně pro zpracování vstupních dat a generování výstupních dat.

![Diagram znázorňující, jak klientský kód komunikuje se službou Batch][diagram01]

## <a name="prerequisites"></a>Požadované součásti
Chcete-li použít šablony Batch, budete potřebovat následující:

* Počítač s nainstalovanou sadou Visual Studio 2015. Šablony služby Batch se aktuálně podporují jenom pro Visual Studio 2015.
* Šablony služby Batch, které jsou k dispozici z [Galerie sady Visual Studio][vs_gallery] jako rozšíření sady Visual Studio. Existují dva způsoby, jak získat šablony:
  
  * Instalace šablon pomocí dialogového okna **rozšíření a aktualizace** v aplikaci Visual Studio (Další informace najdete v tématu [vyhledání a používání rozšíření sady Visual Studio][vs_find_use_ext]). V dialogovém okně **rozšíření a aktualizace** vyhledejte a stáhněte následující dvě rozšíření:
    
    * Azure Batch Správce úloh s rozdělovačem úlohy
    * Procesor úloh Azure Batch
  * Stažení šablon z Online galerie pro Visual Studio: [Microsoft Azure Batch šablon projektů][vs_gallery_templates]
* Pokud plánujete použít funkci [balíčky aplikací](batch-application-packages.md) k nasazení Správce úloh a procesoru úloh do výpočetních uzlů služby Batch, musíte propojit účet úložiště s účtem Batch.

## <a name="preparation"></a>Příprava
Doporučujeme vytvořit řešení, které může obsahovat Správce úloh i procesor úkolů, protože díky tomu může být snazší sdílet kód mezi správcem úloh a programy procesoru úloh. Chcete-li vytvořit toto řešení, postupujte podle následujících kroků:

1. Otevřete Visual Studio a vyberte **soubor** > **Nový** > **projekt**.
2. V části **šablony**rozbalte **jiné typy projektů**, klikněte na **řešení sady Visual Studio**a pak vyberte **prázdné řešení**.
3. Zadejte název, který popisuje vaši aplikaci, a účel tohoto řešení (např. "LitwareBatchTaskPrograms").
4. Chcete-li vytvořit nové řešení, klikněte na tlačítko **OK**.

## <a name="job-manager-template"></a>Šablona správce úloh
Šablona správce úloh vám pomůže implementovat úkol správce úloh, který může provádět tyto akce:

* Rozdělit úlohu na více úkolů.
* Odešlete tyto úlohy ke spuštění v dávce.

> [!NOTE]
> Další informace o úlohách Správce úloh najdete v tématu [Přehled funkcí Batch pro vývojáře](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Vytvoření správce úloh pomocí šablony
Chcete-li do řešení, které jste vytvořili dříve, přidat správce úloh, postupujte podle následujících kroků:

1. Otevřete existující řešení v aplikaci Visual Studio.
2. V Průzkumník řešení klikněte pravým tlačítkem myši na řešení, klikněte na **Přidat** > **Nový projekt**.
3. V části **Visual C#** klikněte na **Cloud**a pak klikněte na **Azure Batch Správce úloh s rozdělovačem úlohy**.
4. Zadejte název, který popisuje vaši aplikaci, a tento projekt Identifikujte jako správce úloh (např. "LitwareJobManager").
5. Chcete-li vytvořit projekt, klikněte na tlačítko **OK**.
6. Nakonec sestavte projekt, aby aplikace Visual Studio načetla všechny odkazované balíčky NuGet a ověřila, že projekt je platný před tím, než začnete upravovat.

### <a name="job-manager-template-files-and-their-purpose"></a>Soubory šablon Správce úloh a jejich účel
Když vytvoříte projekt pomocí šablony Správce úloh, vygeneruje tři skupiny souborů s kódem:

* Hlavní soubor programu (Program.cs). Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně. Nemusíte to obvykle měnit.
* Adresář rozhraní. Obsahuje soubory zodpovědné za práci "často používaného programu Správce úloh" – rozbalení parametrů, přidání úkolů do úlohy služby Batch atd. Tyto soubory byste neměli normálně měnit.
* Soubor rozdělovače úlohy (JobSplitter.cs). Tady umístíte logiku specifickou pro aplikaci pro rozdělení úlohy do úkolů.

Samozřejmě můžete přidat další soubory podle potřeby pro podporu kódu rozdělovače úlohy v závislosti na složitosti logiky rozdělení úloh.

Šablona také generuje standardní soubory projektu .NET, jako je například soubor. csproj, App. config, Packages. config, atd.

Zbytek této části popisuje různé soubory a jejich strukturu kódu a vysvětluje, co jednotlivé třídy dělá.

![Visual Studio Průzkumník řešení znázorňující řešení šablony úlohy Správce úloh][solution_explorer01]

**Soubory architektury**

* `Configuration.cs`: Zapouzdřuje načítání dat konfigurace úlohy, jako jsou například podrobnosti účtu Batch, přihlašovací údaje k propojenému účtu úložiště, informace o úloze a úkolu a parametry úlohy. Poskytuje také přístup k proměnným prostředím definovaným v dávce (viz nastavení prostředí pro úlohy v dokumentaci ke službě Batch) prostřednictvím třídy Configuration. objekt EnvironmentVariable.
* `IConfiguration.cs`: Vyabstrakcí implementaci třídy konfigurace, takže můžete rozdělit rozdělovač úlohy pomocí falešného nebo objektového konfiguračního objektu.
* `JobManager.cs`: Orchestruje součásti programu Správce úloh. Zodpovídá za inicializaci rozdělovače úlohy, vyvolání rozdělovače úlohy a odeslání úloh vrácených rozdělovačem úlohy odesílateli úkolu.
* `JobManagerException.cs`: Představuje chybu, která vyžaduje ukončení Správce úloh. JobManagerException se používá k zabalení očekávaných chyb, kde konkrétní diagnostické informace mohou být k dispozici jako součást ukončení.
* `TaskSubmitter.cs`: Tato třída zodpovídá za přidání úkolů vrácených rozdělovačem úlohy do úlohy Batch. Třída JobManager agreguje sekvenci úloh do dávek pro efektivní, ale včas přičtenou úlohu, a pak zavolá TaskSubmitter. SubmitTasks ve vlákně na pozadí pro každou dávku.

**Rozdělovač úlohy**

`JobSplitter.cs`: Tato třída obsahuje logiku specifickou pro aplikaci pro rozdělení úlohy do úkolů. Rozhraní vyvolá metodu JobSplitter. Split, aby získala sekvenci úloh, která se přidá do úlohy, jak je metoda vrátí. Toto je třída, do které vložíte logiku vaší úlohy. Implementujte metodu Split pro vrácení posloupnosti CloudTask instancí představujících úkoly, do kterých chcete rozdělit úlohu.

**Standardní soubory projektu příkazového řádku .NET**

* `App.config`: Standardní konfigurační soubor aplikace .NET.
* `Packages.config`: Standardní soubor závislosti balíčku NuGet.
* `Program.cs`: Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně.

### <a name="implementing-the-job-splitter"></a>Implementace rozdělovače úlohy
Když otevřete projekt šablony Správce úloh, projekt bude mít ve výchozím nastavení otevřený soubor JobSplitter.cs. Můžete implementovat logiku rozdělení pro úlohy v úloze pomocí níže uvedené metody Split ():

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
> Oddíl s poznámkami v `Split()` metodě je jediným oddílem kódu šablony Správce úloh, který je určen pro úpravu přidáním logiky pro rozdělení úloh do různých úloh. Pokud chcete upravit jiný oddíl šablony, ujistěte se, že jste obeznámení s tím, jak funguje Batch, a vyzkoušejte několik [ukázek kódu Batch][github_samples].
> 
> 

Vaše implementace rozdělení () má přístup k:

* Parametry úlohy prostřednictvím `_parameters` pole.
* Objekt vlastnosti cloudjob reprezentující úlohu prostřednictvím `_job` pole.
* Objekt CloudTask, který představuje úkol správce úloh, prostřednictvím `_jobManagerTask` pole.

Vaše `Split()` implementace nepotřebuje přidávat do úlohy úkoly přímo. Místo toho by měl váš kód vracet sekvenci objektů CloudTask a tyto objekty budou automaticky přidány do úlohy pomocí tříd rozhraní, které vyvolávají rozdělovač úlohy. K implementaci rozdělovačů úloh je běžné použít funkci`yield return`iterátoru () jazyka C#, protože to umožňuje, aby se úlohy spouštěly co nejdříve a nikoli čekaly na výpočet všech úkolů.

**Selhání rozdělovače úlohy**

Pokud v průběhu úlohy dojde k chybě, mělo by to mít jednu z těchto akcí:

* Ukončete sekvenci pomocí příkazu jazyka `yield break` C#. v takovém případě bude Správce úloh považován za úspěšný; ani
* Vyvolejte výjimku. v takovém případě se Správce úloh bude považovat za neúspěšný a může se pokusit znovu v závislosti na tom, jak klient nakonfigurovali.

V obou případech bude možné spustit všechny úlohy, které už vrátil rozdělovač úlohy a přidaly do úlohy Batch. Pokud nechcete, aby k tomu docházelo, můžete:

* Ukončit úlohu před návratem z rozdělovače úlohy
* Před vrácením celé kolekce úloh je třeba vrátit `ICollection<CloudTask>` nebo `IList<CloudTask>` místo implementace rozdělovače úlohy pomocí iterátoru jazyka C#.
* Použití závislostí úkolů k provedení všech úkolů na úspěšném dokončení Správce úloh

**Opakování Správce úloh**

Pokud se Správce úloh nepovede, může ho služba Batch zkusit znovu v závislosti na nastaveních opakování klienta. Obecně je to bezpečné, protože když rozhraní přidává úlohy do úlohy, ignoruje všechny úlohy, které již existují. Pokud je ale výpočet úkolů nákladné, možná nebudete chtít počítat s náklady na přepočítání úloh, které už byly do úlohy přidané. Naopak, pokud není zaručeno, že opakované spuštění vygeneruje stejné ID úlohy, nebude chování ignorovat duplicit v provozu. V těchto případech byste měli navrhnout rozdělovač úlohy a zjistit práci, která už skončila, a neopakovat ji, například provedením vlastnosti cloudjob. ListTasks před tím, než začnete vracet úkoly.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Ukončovací kódy a výjimky v šabloně Správce úloh
Ukončovací kódy a výjimky poskytují mechanismus pro určení výsledku spuštění programu a mohou pomáhat identifikovat případné problémy s prováděním programu. Šablona správce úloh implementuje kódy ukončení a výjimky popsané v této části.

Úkol správce úloh implementovaný pomocí šablony Správce úloh může vracet tři možné ukončovací kódy:

| kód | Popis |
| --- | --- |
| 0 |Správce úloh byl úspěšně dokončen. Došlo k dokončení kódu rozdělovače úlohy a všechny úkoly byly přidány do úlohy. |
| 1 |Úloha správce úloh se nezdařila s výjimkou v části očekávaná v programu. Výjimka byla přeložena do JobManagerException s diagnostickými informacemi a tam, kde je to možné, návrhy pro vyřešení selhání. |
| 2 |Úloha správce úloh se nezdařila s výjimkou "neočekávaná" výjimka. Výjimka byla zaznamenána do standardního výstupu, ale Správce úloh nemohl přidat žádné další informace o diagnostice nebo nápravě. |

V případě selhání úlohy Správce úloh může být do služby ještě před tím, než došlo k chybě, stále přidány některé úlohy. Tyto úlohy budou spouštěny normálním způsobem. Projděte si část "selhání rozdělení úlohy" výše pro diskuzi o této cestě k kódu.

Všechny informace vrácené výjimkami se zapisují do souborů STDOUT. txt a stderr. txt. Další informace najdete v tématu [zpracování chyb](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Klientské požadavky
Tato část popisuje některé požadavky implementace klienta při vyvolání Správce úloh na základě této šablony. Podrobnosti o předávání parametrů a nastavení prostředí najdete v tématu [postup předání parametrů a proměnných prostředí z klientského kódu](#pass-environment-settings) .

**Povinné přihlašovací údaje**

Aby bylo možné přidat úlohy do úlohy Azure Batch, vyžaduje úkol správce úloh vaši adresu URL a klíč účtu služby Azure Batch. Tyto jsou nutné předat do proměnných prostředí s názvem YOUR_BATCH_URL a YOUR_BATCH_KEY. Můžete je nastavit v nastavení prostředí úloh Správce úloh. Například v klientovi C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Přihlašovací údaje úložiště**

Klient obvykle nemusí poskytnout přihlašovací údaje účtu úložiště s připojeným účtem služby Správce úloh, protože (a) Většina správců úloh nemusí explicitně přistupovat k účtu propojeného úložiště a (b) propojený účet úložiště je často k dispozici pro všechny úlohy jako společné nastavení prostředí pro danou úlohu. Pokud neposkytujete propojený účet úložiště prostřednictvím společného nastavení prostředí a Správce úloh vyžaduje přístup k propojenému úložišti, měli byste zadat přihlašovací údaje k odkazovanému úložišti následujícím způsobem:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Nastavení úkolu správce úloh**

Klient by měl nastavit příznak *killJobOnCompletion* Správce úloh na **hodnotu false (NEPRAVDA**).

Je obvykle bezpečné, aby klient nastavil *runExclusive* na **hodnotu false**.

Klient by měl pomocí kolekce *resourceFiles* nebo *applicationPackageReferences* mít na výpočetním uzlu nasazený spustitelný soubor Správce úloh (a jeho požadované knihovny DLL).

Ve výchozím nastavení se Správce úloh nebude opakovat, pokud selže. V závislosti na vaší logice Správce úloh může klient chtít povolit opakované pokusy prostřednictvím *omezení*/*maxTaskRetryCount*.

**Nastavení úlohy**

Pokud příčka úlohy vysílá úkoly se závislostmi, musí klient nastavit usesTaskDependencies úlohy na hodnotu true.

V modelu rozdělovače úlohy není neobvyklé, že klienti chtějí přidat úlohy do úloh a nad tím, co příčka úlohy vytvoří. Klient by proto měl obvykle nastavit *onAllTasksComplete* úlohy na **terminatejob**.

## <a name="task-processor-template"></a>Šablona procesoru úloh
Šablona procesoru úloh vám pomůže implementovat procesor úloh, který může provádět následující akce:

* Nastavte informace požadované jednotlivými úlohami Batch ke spuštění.
* Spustí všechny akce vyžadované každou dávkovou úlohou.
* Uložte výstupy úloh do trvalého úložiště.

I když procesor úloh není potřebný ke spouštění úloh v dávce, klíčovou výhodou použití procesoru úloh je, že poskytuje obálku pro implementaci všech akcí spuštění úloh na jednom místě. Například pokud potřebujete spustit několik aplikací v kontextu jednotlivých úloh nebo pokud potřebujete po dokončení jednotlivých úloh zkopírovat data do trvalého úložiště.

Akce prováděné procesorem úloh můžou být jednoduché nebo složité a tolik nebo víc, kolik vyžaduje vaše zatížení. Díky implementaci všech akcí úloh do jednoho procesoru úloh můžete snadno aktualizovat nebo přidat akce na základě změn aplikací nebo úloh. V některých případech však procesor úloh nemusí být optimálním řešením pro vaši implementaci, protože může přidat zbytečné složitosti, například při spuštění úloh, které lze rychle spustit z jednoduchého příkazového řádku.

### <a name="create-a-task-processor-using-the-template"></a>Vytvoření procesoru úkolů pomocí šablony
Chcete-li přidat procesor úkolů do řešení, které jste vytvořili dříve, postupujte takto:

1. Otevřete existující řešení v aplikaci Visual Studio.
2. V Průzkumník řešení klikněte pravým tlačítkem myši na řešení, klikněte na tlačítko **Přidat**a poté klikněte na možnost **Nový projekt**.
3. V části **Visual C#** klikněte na **Cloud**a pak klikněte na **Azure Batch procesor úloh**.
4. Zadejte název, který popisuje vaši aplikaci, a Identifikujte tento projekt jako procesor úloh (např. "LitwareTaskProcessor").
5. Chcete-li vytvořit projekt, klikněte na tlačítko **OK**.
6. Nakonec sestavte projekt, aby aplikace Visual Studio načetla všechny odkazované balíčky NuGet a ověřila, že projekt je platný před tím, než začnete upravovat.

### <a name="task-processor-template-files-and-their-purpose"></a>Soubory šablon procesoru úloh a jejich účel
Při vytváření projektu pomocí šablony procesoru úloh generuje tři skupiny souborů kódu:

* Hlavní soubor programu (Program.cs). Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně. Nemusíte to obvykle měnit.
* Adresář rozhraní. Obsahuje soubory zodpovědné za práci "často používaného programu Správce úloh" – rozbalení parametrů, přidání úkolů do úlohy služby Batch atd. Tyto soubory byste neměli normálně měnit.
* Soubor procesoru úloh (TaskProcessor.cs). Tady umístíte logiku specifickou pro konkrétní aplikaci pro spuštění úlohy (obvykle voláním na existující spustitelný soubor). Kód předběžného a následného zpracování, jako je například stahování dalších dat nebo odesílání výsledných souborů, zde také prochází.

Samozřejmě můžete přidat další soubory podle potřeby pro podporu kódu procesoru úloh v závislosti na složitosti rozdělené logiky úlohy.

Šablona také generuje standardní soubory projektu .NET, jako je například soubor. csproj, App. config, Packages. config, atd.

Zbytek této části popisuje různé soubory a jejich strukturu kódu a vysvětluje, co jednotlivé třídy dělá.

![Visual Studio Průzkumník řešení znázorňující řešení šablony procesoru úloh][solution_explorer02]

**Soubory architektury**

* `Configuration.cs`: Zapouzdřuje načítání dat konfigurace úlohy, jako jsou například podrobnosti účtu Batch, přihlašovací údaje k propojenému účtu úložiště, informace o úloze a úkolu a parametry úlohy. Poskytuje také přístup k proměnným prostředím definovaným v dávce (viz nastavení prostředí pro úlohy v dokumentaci ke službě Batch) prostřednictvím třídy Configuration. objekt EnvironmentVariable.
* `IConfiguration.cs`: Vyabstrakcí implementaci třídy konfigurace, takže můžete rozdělit rozdělovač úlohy pomocí falešného nebo objektového konfiguračního objektu.
* `TaskProcessorException.cs`: Představuje chybu, která vyžaduje ukončení Správce úloh. TaskProcessorException se používá k zabalení očekávaných chyb, kde konkrétní diagnostické informace mohou být k dispozici jako součást ukončení.

**Procesor úloh**

* `TaskProcessor.cs`: Spustí úlohu. Rozhraní vyvolá metodu TaskProcessor. Run. Toto je třída, do které zadáte logiku úlohy pro konkrétní aplikaci. Implementujte metodu Run na:
  * Analyzovat a ověřit všechny parametry úlohy
  * Vytvoření příkazového řádku pro libovolný externí program, který chcete vyvolat
  * Protokolování všech diagnostických informací, které mohou být požadovány pro účely ladění
  * Spuštění procesu pomocí tohoto příkazového řádku
  * Počkejte na ukončení procesu.
  * Zaznamenejte ukončovací kód procesu, abyste zjistili, zda bylo úspěšné nebo neúspěšné.
  * Uložte všechny výstupní soubory, které chcete uchovat do trvalého úložiště.

**Standardní soubory projektu příkazového řádku .NET**

* `App.config`: Standardní konfigurační soubor aplikace .NET.
* `Packages.config`: Standardní soubor závislosti balíčku NuGet.
* `Program.cs`: Obsahuje vstupní bod programu a zpracování výjimek nejvyšší úrovně.

## <a name="implementing-the-task-processor"></a>Implementace procesoru úloh
Když otevřete projekt šablony procesoru úloh, projekt bude mít ve výchozím nastavení otevřený soubor TaskProcessor.cs. Můžete implementovat logiku spuštění pro úlohy v úloze pomocí metody Run () uvedené níže:

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
> Oddíl s poznámkou v metodě Run () je jediným oddílem kódu šablony procesoru úloh, který je určen pro úpravu, přidáním logiky spuštění pro úkoly v rámci úlohy. Pokud chcete upravit jiný oddíl šablony, Seznamte se s tím, jak funguje služba Batch, v dokumentaci ke službě Batch a vyzkoušením několika ukázek kódu Batch.
> 
> 

Metoda Run () zodpovídá za spuštění příkazového řádku, spuštění jednoho nebo více procesů, čekání na dokončení všech procesů, uložení výsledků a nakonec návratu pomocí ukončovacího kódu. Metoda Run () je místo, kde implementujete logiku zpracování pro vaše úkoly. Rozhraní procesoru úloh vyvolá metodu Run () pro vás. nemusíte je volat sami.

Vaše implementace Run () má přístup k:

* Parametry úlohy prostřednictvím `_parameters` pole.
* ID úlohy a úlohy, a to prostřednictvím `_jobId` polí `_taskId` a.
* Konfigurace úkolu prostřednictvím `_configuration` pole

**Selhání úlohy**

V případě selhání můžete ukončit metodu Run () vyvoláním výjimky, ale to nechá obslužnou rutinu výjimky nejvyšší úrovně v ovládacím prvku ukončovacího kódu úlohy. Pokud potřebujete řídit ukončovací kód, abyste mohli odlišit různé typy selhání, například pro diagnostické účely nebo protože některé režimy selhání by měly úlohu ukončit a jiné by neměly, pak byste měli ukončit metodu Run () vrácením nenulového ukončovacího kódu. Tím se vytvoří ukončovací kód úlohy.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Ukončovací kódy a výjimky v šabloně procesoru úloh
Ukončovací kódy a výjimky poskytují mechanismus pro určení výsledku spuštění programu a mohou pomáhat identifikovat případné problémy s prováděním programu. Šablona procesoru úloh implementuje ukončovací kódy a výjimky popsané v této části.

Úloha procesoru úlohy, která je implementovaná pomocí šablony procesoru úloh, může vracet tři možné ukončovací kódy:

| kód | Popis |
| --- | --- |
| [Process. ExitCode][process_exitcode] |Procesor úkolů byl dokončen. Všimněte si, že to neznamená, že program, který jste vyvolali, byl úspěšný – pouze tento procesor úkol vyvolal úspěšně a provedl jakékoli následné zpracování bez výjimek. Význam ukončovacího kódu závisí na vyvolaném programu – obvykle ukončovací kód 0 znamená úspěšné dokončení programu a další ukončovací kód znamená, že se program nezdařil. |
| 1 |Procesor úloh se nezdařil s výjimkou v části očekávaná v programu. Výjimka byla přeložena na `TaskProcessorException` s diagnostickými informacemi a je-li to možné, návrhy pro vyřešení selhání. |
| 2 |Procesor úloh se nezdařil s výjimkou neočekávané výjimky. Výjimka byla zaznamenána do standardního výstupu, ale procesor úloh nemohl přidat žádné další informace o diagnostice nebo nápravě. |

> [!NOTE]
> Pokud program, který vyvoláte, používá ukončovací kódy 1 a 2 k označení konkrétních režimů selhání, pak použití ukončovacích kódů 1 a 2 pro chyby procesoru úloh je nejednoznačné. Tyto kódy chyb procesoru úkolů můžete změnit na kódy pro odlišené ukončení úpravou případů výjimek v souboru Program.cs.
> 
> 

Všechny informace vrácené výjimkami se zapisují do souborů STDOUT. txt a stderr. txt. Další informace najdete v dokumentaci ke službě Batch v tématu zpracování chyb.

### <a name="client-considerations"></a>Klientské požadavky
**Přihlašovací údaje úložiště**

Pokud váš procesor úloh používá úložiště objektů BLOB v Azure k uchování výstupů, například pomocí pomocné knihovny pro konvence souborů, pak potřebuje *přístup k* přihlašovacím údajům účtu cloudového úložiště *nebo* k adrese URL kontejneru objektů blob, která obsahuje sdílený přístupový podpis (SAS). Šablona zahrnuje podporu pro poskytování přihlašovacích údajů prostřednictvím běžných proměnných prostředí. Váš klient může přihlašovací údaje k úložišti předat následujícím způsobem:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Účet úložiště je pak k dispozici ve třídě TaskProcessor prostřednictvím `_configuration.StorageAccount` vlastnosti.

Pokud dáváte přednost použití adresy URL kontejneru s SAS, můžete to předat taky pomocí společného prostředí úlohy, ale šablona procesoru úloh v současné době neobsahuje integrovanou podporu pro tuto možnost.

**Nastavení úložiště**

Před přidáním úkolů do úlohy doporučujeme, aby úloha klienta nebo Správce úloh vytvořila kontejnery požadované úlohou. To je povinné, pokud použijete adresu URL kontejneru s SAS, protože taková adresa URL nezahrnuje oprávnění k vytvoření kontejneru. Doporučuje se i v případě, že přihlašovací údaje účtu úložiště zadáte, protože všechny úlohy s voláním CloudBlobContainer. CreateIfNotExistsAsync na kontejneru.

## <a name="pass-parameters-and-environment-variables"></a>Předání parametrů a proměnných prostředí
### <a name="pass-environment-settings"></a>Předat nastavení prostředí
Klient může předat informace do úlohy Správce úloh ve formě nastavení prostředí. Tyto informace může použít úkol správce úloh při generování úkolů procesoru úloh, které se spustí jako součást výpočetní úlohy. Příklady informací, které můžete předat jako nastavení prostředí:

* Název účtu úložiště a klíče účtu
* Adresa URL účtu Batch
* Klíč účtu Batch

Služba Batch má jednoduchý mechanismus pro předávání nastavení prostředí úkolu správce úloh pomocí `EnvironmentSettings` vlastnosti v [Microsoft. Azure. batch. JobManagerTask][net_jobmanagertask].

Například pro získání `BatchClient` instance účtu Batch můžete předat jako proměnné prostředí z kódu klienta přihlašovací údaje adresy URL a sdíleného klíče pro účet Batch. Podobně pro přístup k účtu úložiště, který je propojený s účtem Batch, můžete název účtu úložiště a klíč účtu úložiště předat jako proměnné prostředí.

### <a name="pass-parameters-to-the-job-manager-template"></a>Předání parametrů do šablony Správce úloh
V mnoha případech je vhodné předat parametry za úlohu do úlohy Správce úloh, a to buď k řízení rozděleného procesu úlohy, nebo ke konfiguraci úkolů pro úlohu. To můžete provést tak, že nahrajete soubor JSON s názvem Parameters. JSON jako soubor prostředků pro úkol správce úloh. Parametry pak mohou být k dispozici v `JobSplitter._parameters` poli v šabloně Správce úloh.

> [!NOTE]
> Vestavěný parametr obslužné rutiny podporuje pouze slovníky řetězců do řetězce. Pokud chcete předat komplexní hodnoty JSON jako hodnoty parametrů, budete je muset předat jako řetězce a analyzovat je v rozdělovači úlohy nebo upravit `Configuration.GetJobParameters` metodu rozhraní.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Předání parametrů do šablony procesoru úloh
Můžete také předat parametry jednotlivým úlohám implementovaným pomocí šablony procesoru úloh. Stejně jako u šablony Správce úloh vyhledává šablona procesoru úloh soubor prostředků s názvem.

Parameters. JSON, a pokud ho najde, načte ho jako slovník parametrů. Existuje několik možností, jak předat parametry úlohám procesoru úloh:

* Znovu použijte parametry úlohy JSON. To funguje dobře, pokud jsou jediné parametry v rámci úlohy (například výška a šířka vykreslování). Pokud to chcete provést při vytváření CloudTask v rozdělovače úlohy, přidejte odkaz na objekt souboru prostředků Parameters. JSON z úlohy Správce úloh ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) do kolekce ResourceFiles CloudTask.
* Vygenerujte a nahrajte dokument parametry konkrétního úkolu. JSON jako součást provádění rozdělovače úlohy a odkázat na tento objekt BLOB v kolekci souborů prostředků úlohy. To je nezbytné v případě, že různé úlohy mají různé parametry. Příkladem může být scénář prostorového vykreslování, ve kterém je index rámce předán úkolu jako parametr.

> [!NOTE]
> Vestavěný parametr obslužné rutiny podporuje pouze slovníky řetězců do řetězce. Pokud chcete předat komplexní hodnoty JSON jako hodnoty parametrů, budete je muset předat jako řetězce a analyzovat je v procesoru úloh nebo upravit `Configuration.GetTaskParameters` metodu rozhraní.
> 
> 

## <a name="next-steps"></a>Další kroky
### <a name="persist-job-and-task-output-to-azure-storage"></a>Zachovat výstup úlohy a úlohy do Azure Storage
Dalším užitečným nástrojem pro vývoj řešení Batch je [Azure Batch konvence souborů][nuget_package]. Pomocí této knihovny tříd .NET (v současnosti ve verzi Preview) můžete v aplikacích Batch .NET snadno ukládat a načítat výstupy úloh z Azure Storage. [Trvalý výstup úlohy a úlohy Azure Batch](batch-task-output.md) obsahuje úplnou diskuzi o knihovně a jejím využití.


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
