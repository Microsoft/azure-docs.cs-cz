---
title: Sestavujte řešení s využitím šablony sady Visual Studio – služby Azure Batch | Dokumentace Microsoftu
description: Zjistěte, jak pomocí šablony projektů Visual Studio můžete implementovat a spouštět úlohy náročné na výpočetní prostředky v Azure Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 085bfa582b676f34a02e4c1c5ae7e69c49e5cb4e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538119"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Rychlé zprovoznění řešení Batch pomocí šablony projektů Visual Studio

**Správce úloh** a **šablony sady Visual Studio úlohy procesoru** pro službu Batch poskytují kódu, který pomáhá implementovat a spouštět úlohy náročné na výpočetní prostředky ve službě Batch adresou minimální množství úsilí. Tento dokument popisuje tyto šablony a obsahuje pokyny, jak je používat.

> [!IMPORTANT]
> Tento článek popisuje pouze informace vztahující se na tyto dvě šablony a předpokládá, že máte zkušenosti se službou Batch a klíčové koncepty týkající se k němu: fondy, výpočetní uzly, úlohy a úkoly, úkolech Správce úloh, proměnné prostředí a další relevantní informace. Můžete najít další informace najdete v [Základy služby Azure Batch](batch-technical-overview.md) a [přehled funkcí Batch pro vývojáře](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Podrobný přehled
Šablony Správce úloh a úkolů procesoru umožňuje vytvořit dvě užitečné součásti:

* Úkol Správce úloh, která implementuje rozdělovač úlohy, které můžete rozdělit úlohy na více úkolů, které můžou běžet nezávisle na sobě paralelně.
* Úloha procesor, který slouží k provádění předběžného zpracování a následné zpracování kolem aplikace příkazového řádku.

Například ve scénáři vykreslování film rozdělovač úlohy by proměnit úlohu jednoho film stovky nebo tisíce samostatné úlohy, které bude zpracovávat jednotlivé snímky samostatně. Odpovídajícím způsobem procesor úloh by měl vyvolat vykreslování aplikace a všechny závislé procesy, které jsou potřebné pro vykreslení každého snímku, jakož i provádět žádné další akce (například kopírování vykreslený snímek do úložiště).

> [!NOTE]
> Šablony Správce úloh a úkolů procesoru jsou nezávisle na sobě navzájem, tak můžete použít oba, nebo pouze jeden z nich, v závislosti na požadavcích vaší výpočetní úlohy a na vašich předvolbách.
> 
> 

Jak je znázorněno v následujícím diagramu, výpočetní úlohu, která používá tyto šablony budou procházet tři fáze:

1. Klientský kód (např. aplikace, webová služba atd.) odešle úlohu do služby Batch v Azure, určení jako jeho Správce úloh, úkolů programu Správce úloh.
2. Služba Batch spustí úkol Správce úloh na výpočetních uzlech a rozdělovač úloha spustí zadaný počet úkolů úlohy procesoru, na jako mnoho výpočetních uzlů podle potřeby, na základě specifikací v kódu rozdělovač úlohy a parametry.
3. Procesor úkoly úloh spustit nezávisle, paralelní zpracování vstupních dat a vygenerovat výstupní data.

![Diagram znázorňující, jak kód klienta komunikuje se službou Batch][diagram01]

## <a name="prerequisites"></a>Požadavky
Použití šablon služby Batch, budete potřebovat následující:

* Počítač s nainstalovanou sadu Visual Studio 2015. Šablony batch jsou v tuto chvíli podporuje jenom pro Visual Studio 2015.
* Šablon služby Batch, které jsou k dispozici na [Galerie sady Visual Studio] [ vs_gallery] jako rozšíření sady Visual Studio. Existují dva způsoby, jak získat šablony:
  
  * Instalace šablony pomocí **rozšíření a aktualizace** dialogové okno v sadě Visual Studio (Další informace najdete v tématu [hledání a používání rozšíření sady Visual Studio][vs_find_use_ext]). V **rozšíření a aktualizace** dialogové okno pole, vyhledávání a stáhněte si následující dvě rozšíření:
    
    * Správce úloh Azure Batch s rozdělovačem úlohy
    * Procesor úloh služby Azure Batch
  * Stáhněte šablony z online Galerie pro sadu Visual Studio: [Šablony projektů Microsoft Azure Batch][vs_gallery_templates]
* Pokud budete chtít použít [balíčky aplikací](batch-application-packages.md) funkce pro nasazení Správce úloh a procesor úloh do služby Batch výpočetní uzly, budete muset propojit účet úložiště do svého účtu Batch.

## <a name="preparation"></a>Příprava
Doporučujeme vytvořit řešení, které mohou obsahovat váš správce úloh, stejně jako úloha procesor, protože to může usnadňují sdílení kódu mezi Správce úloh a úkolů procesoru programy. Pokud chcete vytvořit toto řešení, postupujte takto:

1. Otevřít Visual Studio a vyberte **souboru** > **nový** > **projektu**.
2. V části **šablony**, rozbalte **ostatní typy projektů**, klikněte na tlačítko **řešení sady Visual Studio**a pak vyberte **prázdné řešení**.
3. Zadejte název, který popisuje vaše aplikace a účel tohoto řešení (například "LitwareBatchTaskPrograms").
4. Chcete-li vytvořit nové řešení, klikněte na tlačítko **OK**.

## <a name="job-manager-template"></a>Šablona Správce úloh
Šablona Správce úloh umožňuje implementovat úkol Správce úloh, které můžete provádět následující akce:

* Úloha rozdělují na více úkolů.
* Odeslání těchto úkolů ke spuštění v Batch.

> [!NOTE]
> Další informace o úkolech Správce úloh, naleznete v tématu [přehled funkcí Batch pro vývojáře](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Vytvoření správce úloh pomocí šablony
Chcete-li přidat správce úloh do řešení, které jste vytvořili dříve, postupujte takto:

1. Otevřete existující řešení v sadě Visual Studio.
2. V Průzkumníku řešení klikněte pravým tlačítkem na řešení, klikněte na tlačítko **přidat** > **nový projekt**.
3. V části **Visual C#**, klikněte na tlačítko **cloudu**a potom klikněte na tlačítko **Správce úloh Azure Batch s rozdělovačem úlohy**.
4. Zadejte název, který popíše aplikaci a identifikuje tento projekt jako správce úloh (např.) "LitwareJobManager").
5. Chcete-li vytvořit projekt, klikněte na tlačítko **OK**.
6. Nakonec sestavte projekt tak, aby platnost sady Visual Studio se načíst všechny odkazované balíčky NuGet a ověřte, že projekt je platné, než začnete, její změny.

### <a name="job-manager-template-files-and-their-purpose"></a>Soubory šablon Správce úloh a jejich účel
Při vytváření projektu pomocí šablony Správce úloh vytvoří tři skupiny souborů s kódem:

* Hlavní program souboru (Program.cs). Tato položka obsahuje vstupní bod programu a zpracování výjimky nejvyšší úrovně. Neměli byste potřebovat obvykle ji měnit.
* Adresáře. Tato položka obsahuje soubory za "standardní" práce dokončené pomocí programu Správce úloh – při rozbalování parametry, přidávání úkolů do úlohy služby Batch, atd. Neměli byste potřebovat obvykle k úpravě těchto souborů.
* Soubor rozdělovač úlohy (JobSplitter.cs). Toto je místo, kam bude dáte logiky specifické pro aplikaci pro rozdělení úlohu do úlohy.

Samozřejmě můžete přidat další soubory jako nezbytný pro podporu úloh rozdělovač kódu, v závislosti na složitosti úlohy rozdělení logiku.

Tato šablona také vygeneruje standardní soubory projektu .NET například soubor .csproj, app.config, soubor packages.config, atd.

Zbývající část popisuje různé soubory a jejich strukturu kódu a vysvětluje, co dělá jednotlivé třídy.

![Průzkumníku řešení Visual Studio zobrazuje Správce úloh šablony řešení][solution_explorer01]

**Soubory Framework**

* `Configuration.cs`: Zapouzdřuje načítání úlohy konfiguračních dat, jako je například Podrobnosti o účtu Batch, přihlašovací údaje účtu propojené úložiště, úlohy a informace o úkolu a parametry úlohy. Poskytuje také přístup k proměnným prostředí definované Batch (viz nastavení prostředí pro úkoly v dokumentaci ke službě Batch) prostřednictvím Configuration.EnvironmentVariable třídy.
* `IConfiguration.cs`: Abstrahuje provádění třídu konfigurace, abyste mohli rozdělovač vaší úlohy pomocí konfigurace falešné nebo mock objektu testu jednotek.
* `JobManager.cs`: Orchestruje součásti programu Správce úloh. Zodpovídá za inicializaci rozdělovač úlohy, volání rozdělovač úlohy a odesílání úloh vrácenou, rozdělovač úlohy na odesílatel úlohy.
* `JobManagerException.cs`: Představuje chybu, který vyžaduje Správce úloh ukončete. JobManagerException slouží k zabalení "očekávané" chyby, kde konkrétní diagnostické informace lze zadat jako součást ukončení.
* `TaskSubmitter.cs`: Tato třída je zodpovědný přidávání úkolů vrácený rozdělovač úlohy pro úlohy služby Batch. Třída agreguje JobManager pořadí úloh do dávek pro efektivní, ale včas přidání do úlohy, pak zavolá TaskSubmitter.SubmitTasks ve vlákně na pozadí pro jednotlivé dávky.

**Rozdělovač úlohy**

`JobSplitter.cs`: Tato třída obsahuje specifická pro aplikaci logiky pro rozdělení úlohy do úlohy. Rozhraní volá metodu JobSplitter.Split získat sekvenci úloh, které přidá do úlohy, jako je metoda vrátí. Toto je třída, ve kterém vloží logiky vaší úlohy. Implementujte metodu rozdělení k vrácení sekvence instancí CloudTask představující úlohy, do kterých chcete rozdělit úlohy.

**Standardní soubory projektu .NET příkazového řádku**

* `App.config`: Standardní konfigurační soubor aplikace .NET.
* `Packages.config`: Standardní soubor závislostí balíčku NuGet.
* `Program.cs`: Obsahuje vstupní bod programu a zpracování výjimky nejvyšší úrovně.

### <a name="implementing-the-job-splitter"></a>Implementace rozdělovače úlohy
Když otevřete Správce úloh šablony projektu, bude mít projekt JobSplitter.cs soubor otevřete ve výchozím nastavení. Můžete implementovat logiku rozdělení pro úkoly ve vašich úloh pomocí metody zobrazit Split() níže:

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
> V části s poznámkami `Split()` metoda je pouze část kód šablony Správce úloh, který se má si můžete upravit tak, že přidáte logiku rozdělit úlohy na různé úlohy. Pokud chcete změnit různé části šablony, zkontrolujte, zda jsou familiarized s tím, jak funguje služby Batch a možnost vyzkoušet si pár [ukázky kódu Batch][github_samples].
> 
> 

Implementace Split() má přístup k:

* Parametry úlohy prostřednictvím `_parameters` pole.
* CloudJob objekt představující úlohu prostřednictvím `_job` pole.
* CloudTask objekt představující úkol Správce úloh prostřednictvím `_jobManagerTask` pole.

Vaše `Split()` implementace není potřeba přímo do úlohy přidat úkoly. Místo toho kód by měl vrátit pouze sekvenci objektů CloudTask a ty se přidají do úlohy automaticky v rámci framework tříd, které vyvolají rozdělovač úlohy. Je běžné použití jazyka C# na iterátor (`yield return`) funkce pro provádění úlohy příčky to umožní úlohám spustit systémem co nejdříve, spíše než čekání na vypočítat všech úloh.

**Selhání úlohy rozdělovač**

Pokud vaše úloha rozdělovač dojde k chybě, by měl buď:

* Ukončení sekvence jazyka C# `yield break` příkazu, ve kterém případ Správce úloh se zpracuje jako úspěšně dokončený; nebo
* Vyvolejte výjimku, ve kterém případ, který správce úloh, bude zacházeno jako se nezdařila a může opakovat v závislosti na tom, jak má klient nakonfigurovaný se).

V obou případech budou všechny úlohy už vrácené úlohy rozdělovač a přidány do úlohy služby Batch oprávnění ke spuštění. Pokud nechcete, aby k tomu dojde, pak můžete:

* Ukončit úlohu před návratem z úlohy rozdělovač
* Formulování celá úloha kolekcí před jejím vrácením (to znamená, vrátit `ICollection<CloudTask>` nebo `IList<CloudTask>` namísto implementace rozdělovače vaší úlohy pomocí iterátoru C#)
* Pomocí závislosti úkolů provádět všechny úlohy, které závisí na úspěšném dokončení úlohy správce

**Opakování úlohy správce**

Pokud správce úloh selže, může pokus o pomocí služby Batch v závislosti na nastavení klienta zkuste to znovu. Obecně platí totiž bezpečné, pokud rozhraní přidá úkoly do úlohy, ignoruje všechny úkoly, které už existují. Nicméně pokud výpočet úlohy je nákladná, nemusí chcete vám být naúčtovány náklady přepočítání úlohy, které již byly přidány do úlohy; Naopak pokud znovu spusťte není zaručeno, že ke generování ID stejné úlohy potom chování 'ignorovat duplicity' nebude spouští. V těchto případech byste navrhnout vaše úlohy rozdělovač ke zjištění práce, která již byla provedena a opakovat, například tím, že provádí CloudJob.ListTasks před zahájením pozastavit úlohy.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Ukončovací kódy a výjimky v šabloně Správce úloh
Ukončovací kódy a výjimky poskytují mechanismus pro určení výsledky spuštění programu a může pomoci zjistit případné problémy s provádění programu. Šablona Správce úloh implementuje ukončovací kódy a výjimek, které jsou popsané v této části.

Úkol Správce úloh, která je implementována pomocí šablony Správce úloh může vrátit tří možných ukončovacích kódů:

| Kód | Popis |
| --- | --- |
| 0 |Správce úloh bylo úspěšně dokončeno. Rozdělovač kódu úloha dokončena a všechny úkoly byly přidány do úlohy. |
| 1 |Úkol Správce úloh se nezdařilo s výjimkou v "očekávané" část programu. Výjimka byl přeložen do JobManagerException s diagnostickými informacemi a kde je to možné, návrhy pro vyřešení chyby. |
| 2 |Úkol Správce úloh se nezdařilo s výjimkou "neočekávaný". Výjimky protokolu byla zaznamenána do standardního výstupu, ale Správci úloh se nepodařilo přidat žádné další informace diagnostiky nebo odstraňování problémů. |

V případě selhání úkolu Správce úloh některé úlohy mohou stále byly přidány do služby dříve, než došlo k chybě. Tyto úlohy se spustí normálně. Naleznete v tématu "Selhání úlohy rozdělovač" nad diskuzi o tuto cestu ke kódu.

Všechny informace vrácené výjimky je zapsán do stdout.txt a stderr.txt souborů. Další informace najdete v tématu [zpracování chyb](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Důležité informace o klientech
Tato část popisuje některé požadavky klienta provádění při vyvolání Správce úloh na základě této šablony. Zobrazit [jak předávat parametry a proměnné prostředí z klientského kódu](#pass-environment-settings) podrobné informace o předávání parametrů a nastavení prostředí.

**Povinné přihlašovacích údajů**

Chcete-li přidat úkoly do úlohy služby Azure Batch, úkol Správce úloh vyžaduje adresu URL účtu Azure Batch a klíč. Tyto proměnné prostředí s názvem YOUR_BATCH_URL a YOUR_BATCH_KEY musíte předat. Můžete nastavit tyto ve Správci úloh úloh prostředí. Například v jazyce C# klienta:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Přihlašovací údaje úložiště**

Obvykle není nutné zadat přihlašovací údaje účtu propojené úložiště pro úkol Správce úloh, protože není potřeba explicitně přístup propojeném účtu úložiště (a) většina Správce úloh a (b) propojený účet úložiště je často k dispozici všechny úkoly, jako klient společné nastavení prostředí pro konkrétní úlohu. Pokud nejsou poskytuje propojeném účtu úložiště pomocí běžných nastavení prostředí a Správce úloh vyžaduje přístup k propojené úložiště, pak by měl zadáte přihlašovací údaje propojené úložiště následujícím způsobem:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Nastavení úkolu Správce úloh**

Klient musí nastavit správce úloh *killJobOnCompletion* příznak **false**.

Obvykle je bezpečné pro klienta pro nastavení *runExclusive* k **false**.

Klient musí použít *resourceFiles* nebo *applicationPackageReferences* kolekce se má úloha správce spustitelného souboru (a jeho požadovaných knihoven DLL) nasazení do výpočetního uzlu.

Ve výchozím nastavení Správce úloh se zopakuje, pokud selže. V závislosti na logice úlohy správce klienta chtít povolit opakované pokusy prostřednictvím *omezení*/*maxTaskRetryCount*.

**Nastavení úlohy**

Pokud úlohy rozdělovač vysílá úlohy se závislostmi, klient musí nastavit usesTaskDependencies úlohy na hodnotu true.

V modelu s rozdělovačem úlohy neobvyklé, že u klientů, aby k přidání úkolů do úlohy daleko nad rámec toho, co úlohy rozdělovače vytvoří. Klient by proto obvykle nastaveno úlohy *onAllTasksComplete* k **terminatejob**.

## <a name="task-processor-template"></a>Šablona procesoru úkolu
Úloha procesoru šablony vám umožní implementovat procesor úkolu, který lze provádět následující akce:

* Nastavte informace požadované ke spuštění každého úkolu služby Batch.
* Spusťte všechny akce vyžadované každého úkolu služby Batch.
* Uložte výstupy úloh do trvalého úložiště.

I když to úloha procesoru není nutné ke spouštění úloh v Batch, hlavní výhodou použití úkolů procesoru je, že poskytuje obálku provádět všechny akce zpracování úkolů na jednom místě. Například pokud je potřeba spustit několik aplikací v kontextu jednotlivých úloh, nebo pokud potřebujete ke zkopírování dat do trvalého úložiště po dokončení jednotlivých úkolů.

Akce prováděné úlohy procesoru může být jednoduché nebo složité a tolik nebo jako pár podle požadavků vašich úloh. Kromě toho implementací všechny akce úkolů na jeden procesor úkol můžete snadno aktualizovat nebo přidat akce na základě změn aplikací nebo požadavků na zatížení. Ale v některých případech procesor úloh nemusí být optimální řešení pro implementaci jak ho můžete přidat zbytečné složitosti, například při spuštění úlohy, které lze rychle spustit z jednoduchého příkazového řádku.

### <a name="create-a-task-processor-using-the-template"></a>Vytvoření úkolů procesoru pomocí šablony
Přidat úkol procesor do řešení, které jste vytvořili dříve, postupujte takto:

1. Otevřete existující řešení v sadě Visual Studio.
2. V Průzkumníku řešení klikněte pravým tlačítkem na řešení, klikněte na tlačítko **přidat**a potom klikněte na tlačítko **nový projekt**.
3. V části **Visual C#**, klikněte na tlačítko **cloudu**a potom klikněte na tlačítko **Azure Batch úkol procesoru**.
4. Zadejte název, který popíše aplikaci a identifikuje tento projekt jako úloha procesor (např.) "LitwareTaskProcessor").
5. Chcete-li vytvořit projekt, klikněte na tlačítko **OK**.
6. Nakonec sestavte projekt tak, aby platnost sady Visual Studio se načíst všechny odkazované balíčky NuGet a ověřte, že projekt je platné, než začnete, její změny.

### <a name="task-processor-template-files-and-their-purpose"></a>Soubory šablony procesoru úloh a jejich účel
Při vytváření projektu pomocí šablony úloh procesoru generuje tři skupiny souborů s kódem:

* Hlavní program souboru (Program.cs). Tato položka obsahuje vstupní bod programu a zpracování výjimky nejvyšší úrovně. Neměli byste potřebovat obvykle ji měnit.
* Adresáře. Tato položka obsahuje soubory za "standardní" práce dokončené pomocí programu Správce úloh – při rozbalování parametry, přidávání úkolů do úlohy služby Batch, atd. Neměli byste potřebovat obvykle k úpravě těchto souborů.
* Soubor procesoru úkolů (TaskProcessor.cs). Toto je místo, kam bude dáte logiky specifické pro aplikaci pro provedení úkolu (obvykle ve volání do existujícího spustitelného souboru). Provedení před instrumentací a následného zpracování kódu, jako je například stahování dalších dat a odesílání soubory s výsledky, také zde bude.

Samozřejmě můžete přidat další soubory jako nezbytný pro podporu úloh procesor kódu, v závislosti na složitosti úlohy rozdělení logiku.

Tato šablona také vygeneruje standardní soubory projektu .NET například soubor .csproj, app.config, soubor packages.config, atd.

Zbývající část popisuje různé soubory a jejich strukturu kódu a vysvětluje, co dělá jednotlivé třídy.

![Visual Studio Průzkumník řešení zobrazující šablony řešení úloh procesoru][solution_explorer02]

**Soubory Framework**

* `Configuration.cs`: Zapouzdřuje načítání úlohy konfiguračních dat, jako je například Podrobnosti o účtu Batch, přihlašovací údaje účtu propojené úložiště, úlohy a informace o úkolu a parametry úlohy. Poskytuje také přístup k proměnným prostředí definované Batch (viz nastavení prostředí pro úkoly v dokumentaci ke službě Batch) prostřednictvím Configuration.EnvironmentVariable třídy.
* `IConfiguration.cs`: Abstrahuje provádění třídu konfigurace, abyste mohli rozdělovač vaší úlohy pomocí konfigurace falešné nebo mock objektu testu jednotek.
* `TaskProcessorException.cs`: Představuje chybu, který vyžaduje Správce úloh ukončete. TaskProcessorException slouží k zabalení "očekávané" chyby, kde konkrétní diagnostické informace lze zadat jako součást ukončení.

**Úloha procesoru**

* `TaskProcessor.cs`: Spustí úlohu. Rozhraní volá metodu TaskProcessor.Run. Toto je třída, ve kterém vloží specifické pro aplikaci logiky vaší úlohy. Implementujte metodu spustit:
  * Provádění analýzy a ověření žádné parametry úlohy
  * Vytváření příkazového řádku pro všechny externí program, který chcete vyvolat
  * Protokolovat diagnostické informace, které můžou vyžadovat pro účely ladění
  * Spustit proces, který používá tento příkazový řádek
  * Čekání na ukončení procesu
  * Zachycení ukončovací kód procesu k určení, zda bylo úspěšné nebo neúspěšné
  * Uložit všechny výstupní soubory, které chcete sledovat do trvalého úložiště

**Standardní soubory projektu .NET příkazového řádku**

* `App.config`: Standardní konfigurační soubor aplikace .NET.
* `Packages.config`: Standardní soubor závislostí balíčku NuGet.
* `Program.cs`: Obsahuje vstupní bod programu a zpracování výjimky nejvyšší úrovně.

## <a name="implementing-the-task-processor"></a>Implementace úloh procesoru
Při otevření projektu šablony úloh procesoru, bude mít projekt TaskProcessor.cs soubor otevřete ve výchozím nastavení. Spustit logiku pro úlohy v úlohy můžete implementovat pomocí metodu Run(), je uvedeno níže:

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
> V části s poznámkami v metoda Run() je pouze část kód šablony úloh procesoru, která je určená pro úpravy přidáním spuštění logiku pro úkoly ve vašich úloh. Pokud chcete změnit různé části šablony, prosím nejdříve seznámíte s jak služba Batch pracuje tak, že zkontrolujete dokumentace ke službě Batch a vyzkoušet si pár ukázek kódu služby Batch.
> 
> 

Metoda Run() je zodpovědná za spuštění příkazového řádku, od jednoho nebo více procesů, čekání na dokončení všech procesu ukládání výsledků a nakonec vrácení s ukončovacím kódem. Metoda Run() je, kde je implementovat logiku zpracování pro své úkoly. Rozhraní procesoru úkolu vyvolá metodu Run() pro vás. není nutné volat sami.

Implementace Run() má přístup k:

* Parametry úlohy prostřednictvím `_parameters` pole.
* ID úloh a úkolů pomocí `_jobId` a `_taskId` pole.
* Konfigurace úlohy prostřednictvím `_configuration` pole.

**Selhání úlohy**

V případě selhání můžete práci ukončit metoda Run() došlo k výjimce. to ale znamená obslužná rutina výjimky nejvyšší úrovně v prvku ukončovací kód úkolu. Pokud je potřeba, aby mohl rozlišit různé typy selhání, třeba k diagnostickým účelům nebo protože některé režimů selhání by měla ukončit úlohy, zatímco jiné ne kontroly ukončovací kód, pak by měl ukončení metoda Run() vrácením nenulové ukončovací kód. To se stane ukončovací kód úkolu.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Ukončovací kódy a výjimky v šabloně úlohy procesoru
Ukončovací kódy a výjimky poskytují mechanismus pro určení výsledky spuštění programu a mohou pomoci identifikovat případné potíže s provádění programu. Šablony úloh procesoru implementuje ukončovací kódy a výjimek, které jsou popsané v této části.

Úkol procesoru, která je implementována pomocí šablony úloh procesor může vrátit tří možných ukončovacích kódů:

| Kód | Popis |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Procesor úloha dokončena. Všimněte si, že to, že program, kdybyste vyvolali bylo úspěšné – pouze neznamená, že úloha procesoru úspěšně volat a provést jakékoli následné zpracování bez výjimky. Význam ukončovací kód závisí na vyvolaný program – obvykle ukončovací kód 0 znamená, že program proběhlo úspěšně a všechny ukončovací kód znamená, že program selhal. |
| 1 |Procesor úlohy selhalo s výjimkou v "očekávané" část programu. Výjimka byl přeložen na `TaskProcessorException` s diagnostickými informacemi a kde je to možné, návrhy pro vyřešení chyby. |
| 2 |Procesor úlohy selhalo s výjimkou "neočekávaný". Výjimky protokolu byla zaznamenána do standardního výstupu, ale procesoru úloh se nepodařilo přidat žádné další informace diagnostiky nebo odstraňování problémů. |

> [!NOTE]
> Pokud program, který je zapotřebí vyvolat používá k označení selhání režimy ukončovací kód 1 a 2, pak pomocí ukončovací kód 1 a 2 pro chyby procesoru úkolů je nejednoznačný. Tyto kódy chyb procesoru úkolů výrazný, ale ukončovací kódy můžete změnit úpravou případy výjimky v souboru Program.cs.
> 
> 

Všechny informace vrácené výjimky je zapsán do stdout.txt a stderr.txt souborů. Další informace najdete v tématu zpracování chyb v dokumentaci ke službě Batch.

### <a name="client-considerations"></a>Důležité informace o klientech
**Přihlašovací údaje úložiště**

Pokud vaše úloha procesoru používá úložiště objektů blob v Azure k uchování výstupy, například při použití pomocné knihovny pro vytváření názvů souborů, pak potřebuje přístup k *buď* přihlašovací údaje účtu úložiště cloudu *nebo* objektu blob Adresa URL kontejneru, obsahující sdílený přístupový podpis (SAS). Šablona obsahuje podporu pro zadání přihlašovacích údajů prostřednictvím společné proměnné prostředí. Klienta můžete předávat přihlašovací údaje úložiště následujícím způsobem:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Účet úložiště je pak k dispozici ve třídě TaskProcessor prostřednictvím `_configuration.StorageAccount` vlastnost.

Pokud chcete pomocí SAS adresa URL kontejneru, to můžete také předat prostřednictvím úlohy společné nastavení prostředí, ale procesoru šablony úloh aktuálně neobsahuje integrovanou podporu pro tento.

**Nastavení úložiště**

Doporučuje se, že úkol správce klienta nebo úloha vytvořit všechny kontejnery, vyžadují úkoly před přidáním úkolů do úlohy. Toto je povinný, že pokud používáte adresu URL kontejneru se SAS, jako například adresy URL nezahrnuje oprávnění k vytvoření kontejneru. I v případě, že předáte přihlašovací údaje účtu úložiště, se doporučuje jako uloží každou úlohu volat CloudBlobContainer.CreateIfNotExistsAsync v kontejneru.

## <a name="pass-parameters-and-environment-variables"></a>Předávání parametrů a proměnných prostředí
### <a name="pass-environment-settings"></a>Předejte nastavení prostředí
Informace o úkolu Správce úloh ve formě nastavení prostředí můžete předat klienta. Tyto informace pak může být použit úkol Správce úloh při generování úloh procesoru úkoly, které se spustí výpočetní úlohy v rámci. Mezi informace, které lze předat jako nastavení prostředí patří:

* Název a účet klíče účtu úložiště
* Adresa URL účtu batch
* Klíč účtu batch

Služba Batch má jednoduchý mechanismus pro nastavení prostředí předat úkol Správce úloh s použitím `EnvironmentSettings` vlastnost [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Například, chcete-li získat `BatchClient` instance pro účet Batch, můžete předat jako proměnné prostředí z klientského kódu adresy URL a přihlašovací údaje sdíleného klíče pro účet Batch. Podobně pro přístup k účtu úložiště, který je propojený s účtem Batch, lze předat název účtu úložiště a klíč účtu úložiště jako proměnné prostředí.

### <a name="pass-parameters-to-the-job-manager-template"></a>Předání parametrů do šablony Správce úloh
V mnoha případech je užitečné pro předávání parametrů za úlohu úkol Správce úloh, k řízení úlohy rozdělení procesu nebo nakonfigurovat úlohy pro konkrétní úlohu. Můžete to provést tak, že nahrajete soubor JSON s názvem parameters.json jako soubor prostředků pro úkol Správce úloh. Parametry se pak mohou stát součástí `JobSplitter._parameters` pole v šabloně Správce úloh.

> [!NOTE]
> Obslužná rutina integrované parametru podporuje pouze řetězec řetězec slovníky. Pokud chcete předat komplexních hodnot JSON jako hodnoty parametrů, je potřeba je Předávejte jako řetězce a analyzovat je v rozdělovač úlohy nebo upravit rozhraní framework `Configuration.GetJobParameters` metody.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Předání parametrů do šablony úlohy procesoru
Můžete také předat parametry do jednotlivých kroků, které jsou implementované pomocí šablony úloh procesoru. Stejně jako pomocí šablony Správce úloh, šablona procesoru úkolu hledá soubor prostředků s názvem

Parameters.JSON a pokud zjistila, že načte jako slovník parametrů. Máte několik možností jak předávat parametry úlohy procesoru úloh:

* Opětovné použití parametrů úlohy JSON. Tento postup funguje, pokud jsou pouze parametry ty úlohy (pro příklad, vykreslování výška a šířka). K tomuto účelu, při vytváření CloudTask v rozdělovač úlohy Přidat odkaz na objekt souboru parameters.json prostředku z ResourceFiles úkol Správce úloh (`JobSplitter._jobManagerTask.ResourceFiles`) do kolekce ResourceFiles CloudTask společnosti.
* Generování a nahrání dokumentu parameters.json specifické úkoly jako součást provádění úlohy rozdělovač a odkazují na tento objekt blob v kolekci soubory prostředků úkolu. To je nezbytné, pokud máte jiné parametry různých úloh. Příkladem mohou být 3D vykreslování scénář, kde index snímku je předán úkolu jako parametr.

> [!NOTE]
> Obslužná rutina integrované parametru podporuje pouze řetězec řetězec slovníky. Pokud chcete předat komplexních hodnot JSON jako hodnoty parametrů, je potřeba je Předávejte jako řetězce a analyzovat v procesoru úlohy nebo upravit rozhraní framework `Configuration.GetTaskParameters` metody.
> 
> 

## <a name="next-steps"></a>Další postup
### <a name="persist-job-and-task-output-to-azure-storage"></a>Trvalý výstup úloh a ke službě Azure Storage
Další užitečné nástroje při vývoji řešení Batch je [Azure Batch File Conventions][nuget_package]. Tato knihovna tříd rozhraní .NET (aktuálně ve verzi preview) v aplikacích .NET služby Batch můžete snadno ukládat a načítat výstupy úloh do a z Azure Storage. [Trvalý výstup úloh a úkolů služby Azure Batch](batch-task-output.md) obsahuje úplnou diskusi o knihovně a její používání.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
