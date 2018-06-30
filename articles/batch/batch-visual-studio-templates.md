---
title: Sestavení řešení Batch pomocí šablony sady Visual Studio – Azure | Microsoft Docs
description: Zjistěte, jak pomocí šablony projektů Visual Studio můžete implementovat a spouštět úlohy náročné na Azure Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 98a5af1c0b321b7f9acf2bfd936a16d22088babf
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128856"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Sestavování řešení Batch pomocí šablony projektů Visual Studio

**Správce úloh** a **šablony sady Visual Studio úloh procesoru** pro dávku zadejte kód, abyste pro implementaci a spouštění výpočetně náročných úloh na dávce s minimem úsilí. Tento dokument popisuje tyto šablony a poskytuje pokyny, jak je používat.

> [!IMPORTANT]
> Tento článek popisuje pouze informace pro tyto dvě šablony a předpokládá, že jste obeznámeni se službou Batch a s ním souvisejí klíčové koncepty: fondy, výpočetní uzly, úlohy a úlohy, úkolech Správce úloh, proměnné prostředí a další relevantní informace. Můžete najít další informace v [základy Azure Batch](batch-technical-overview.md), [přehled funkcí Batch pro vývojáře](batch-api-basics.md), a [Začínáme s knihovnou Azure Batch pro .NET](batch-dotnet-get-started.md).
> 
> 

## <a name="high-level-overview"></a>Podrobný přehled
Správce úloh a úloh procesoru šablony lze vytvořit užitečné dvě součásti:

* Úkolu Správce úloh, který implementuje rozdělovače úlohy, který můžete rozdělit úlohu na více úkolů, které můžete spustit samostatně, paralelně.
* Procesor úloh, které lze použít k provádění předem zpracování a po zpracování kolem příkazového řádku s aplikací.

Například v případě vykreslování film by rozdělovače úlohy zapnout úlohu jeden film do stovkami nebo tisíci samostatné úkoly, které by jednotlivé snímky zpracovávají odděleně. Odpovídajícím způsobem procesoru úloh by měl vyvolat vykreslování aplikace a všechny závislé procesy, které jsou potřebné k vykreslení každý rámečku, jakož i provádět žádné další akce (například kopírování vykreslené rámečku do umístění úložiště).

> [!NOTE]
> Správce úloh a úloh procesoru šablony jsou nezávisle na sobě navzájem, takže můžete použít obě, nebo jenom jeden z nich, v závislosti na požadavcích vaší výpočetní úlohy a vaše předvolby.
> 
> 

Jak je znázorněno v následujícím diagramu, bude výpočetní úlohu, která používá tyto šablony projít tří fází:

1. Kód klienta (např. aplikace, webové služby atd.) odešle úlohu pro službu Batch v Azure, a určení jako jeho Správce úloh úkolů programu Správce úloh.
2. Služba Batch spustí úkol správce na výpočetním uzlu a rozdělovače úloha spustí na zadaný počet úloh procesoru úlohy, jako mnoho výpočetní uzly podle potřeby, na základě specifikací v kódu rozdělovače úlohy a parametry.
3. Procesor úkoly úloh spustit samostatně, paralelní zpracování vstupních dat a vygenerovat výstupní data.

![Diagram znázorňující, jak kód klienta komunikuje se službou Batch][diagram01]

## <a name="prerequisites"></a>Požadavky
Použití šablon Batch, budete potřebovat následující:

* Počítač s nainstalovaná sada Visual Studio 2015. Šablony batch jsou aktuálně podporovány pouze pro Visual Studio 2015.
* Batch šablony, které jsou k dispozici na [Galerie sady Visual Studio] [ vs_gallery] jako rozšíření Visual Studia. Existují dva způsoby, jak získat šablony:
  
  * Instalace šablony pomocí **rozšíření a aktualizace** dialogové okno v sadě Visual Studio (Další informace najdete v tématu [hledání a používání rozšíření Visual Studia][vs_find_use_ext]). V **rozšíření a aktualizace** dialogové okno pole, vyhledávání a stáhněte si následující dvě rozšíření:
    
    * Správce úloh Azure Batch pomocí rozdělovače úlohy
    * Procesor úlohy Azure Batch
  * Stáhnout šablony z online Galerie pro sadu Visual Studio: [projektu šablony aplikace Microsoft Azure Batch][vs_gallery_templates]
* Pokud budete chtít použít [balíčky aplikací](batch-application-packages.md) funkci k nasazení Správce úloh a úloh procesor dávky výpočetní uzly, budete muset propojit účet úložiště k účtu Batch.

## <a name="preparation"></a>Příprava
Doporučujeme vytvářet řešení, které může obsahovat váš správce úloh a také vaše úloha procesoru, protože to může být usnadňuje sdílení kódu mezi Správce úloh a úloh procesoru programy. Pokud chcete vytvořit toto řešení, postupujte takto:

1. Otevřete Visual Studio a vyberte **soubor** > **nový** > **projektu**.
2. V části **šablony**, rozbalte položku **jiné typy projektů**, klikněte na tlačítko **řešení sady Visual Studio**a potom vyberte **prázdného řešení**.
3. Zadejte název, který popisuje vaší aplikace a účel tohoto řešení (například "LitwareBatchTaskPrograms").
4. Chcete-li vytvořit nové řešení, klikněte na tlačítko **OK**.

## <a name="job-manager-template"></a>Správce úloh šablony
Správce úloh šablony pomáhá při implementaci úkolu Správce úloh, které můžete provádět následující akce:

* Úlohu rozdělte do několika úloh.
* Odešlete tyto úlohy ke spuštění v dávce.

> [!NOTE]
> Další informace o úkolech Správce úloh najdete v tématu [přehled funkcí Batch pro vývojáře](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Vytvoření správce úloh, pomocí šablony
Chcete-li přidat správce úloh na řešení, které jste vytvořili dříve, postupujte takto:

1. Otevřete existující řešení v sadě Visual Studio.
2. V Průzkumníku řešení klikněte pravým tlačítkem na řešení, klikněte na tlačítko **přidat** > **nový projekt**.
3. V části **Visual C#**, klikněte na tlačítko **cloudu**a potom klikněte na **Správce úloh Azure Batch pomocí úlohy rozdělovače**.
4. Zadejte název, který popisuje vaší aplikace a identifikuje tohoto projektu jako správce úloh (např.) "LitwareJobManager").
5. Chcete-li vytvořit projekt, klikněte na tlačítko **OK**.
6. Nakonec sestavte projekt a vynutit sadě Visual Studio načíst všechny odkazované balíčky NuGet a ověřte, zda je projekt platný před zahájením provádění úprav.

### <a name="job-manager-template-files-and-their-purpose"></a>Soubory šablon Správce úloh a jejich účel
Když vytvoříte projekt pomocí šablony Správce úloh, generuje tři skupiny soubory kódu:

* Hlavní programový soubor (Program.cs). Tato položka obsahuje program Vstupní bod a nejvyšší úrovně výjimek. Neměli byste potřebovat obvykle tuto možnost lze upravit.
* Do složky Framework. Tato položka obsahuje soubory zodpovědná za práci, často používaný' pomocí programu Správce úloh – rozbalování parametrů, přidávání úkolů do úlohy Batch atd. Neměli byste potřebovat normálně k úpravě těchto souborů.
* Soubor rozdělovače úlohy (JobSplitter.cs). Toto je, kam jste umístili specifické pro aplikaci logiky k rozdělení úlohu do úlohy.

Samozřejmě můžete přidat další soubory jako nezbytný pro podporu kódu rozdělovače úlohy, v závislosti na složitosti úlohu, rozdělování logiku.

Šablona generuje také standardní soubory rozhraní .NET projektu, například souboru .csproj, app.config, souboru packages.config, atd.

Zbývající část tohoto oddílu popisuje různých souborů a jejich struktura kódu a vysvětluje, jaké jsou jednotlivé třídy.

![Průzkumník řešení Visual Studio, které jsou zobrazující Správce úloh šablony řešení][solution_explorer01]

**Soubory Framework**

* `Configuration.cs`: Zapouzdří načítání úlohy konfigurační data, jako je například Podrobnosti účtu Batch, přihlašovací údaje účtu propojené úložiště, úlohy a informace o úloze a parametry úlohy. Také poskytuje přístup k proměnné prostředí definované Batch (viz nastavení prostředí pro úkoly v dokumentaci k Batch) prostřednictvím třídy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrahuje implementace třídy konfigurace, aby bylo možné testování částí rozdělovače vaše úlohy pomocí falešných nebo imitované konfiguraci objektu.
* `JobManager.cs`: Orchestruje součástí programu Správce úloh. Zodpovídá za inicializaci rozdělovače úlohy, vyvolání rozdělovače úlohy a odeslání úlohy vrácený rozdělovače úlohy k odesílatel úlohy.
* `JobManagerException.cs`: Představuje chybu, která vyžaduje Správce úloh ukončit. JobManagerException slouží k zabalení 'očekávaný: chyby, kde lze zadat konkrétní diagnostické informace jako součást ukončení.
* `TaskSubmitter.cs`: Tato třída je zodpovědný přidávání úkolů vrácený rozdělovače úlohy pro dávkové úlohy. Agreguje třída JobManager pořadí úloh do dávek pro efektivní, ale včas přidání do úlohy, potom volá TaskSubmitter.SubmitTasks vlákna na pozadí pro každou dávku.

**Úloha rozdělovače**

`JobSplitter.cs`: Tato třída obsahuje konkrétní aplikace logiky k rozdělení úlohu do úlohy. Rozhraní framework vyvolá metodu JobSplitter.Split získat sekvenci úlohy, které přidá do úlohy, jako je metoda vrátí. Toto je třída, kde bude vložit logiku úlohy. Implementujte metodu rozdělení vrátit posloupnost instancí CloudTask představující úlohy, do kterých chcete oddílu úlohy.

**Standardní soubory projektu příkazového řádku .NET**

* `App.config`: Standardní soubor konfigurace aplikace .NET.
* `Packages.config`: Standardní soubor závislost balíčku NuGet.
* `Program.cs`: Obsahuje program Vstupní bod a nejvyšší úrovně výjimek.

### <a name="implementing-the-job-splitter"></a>Implementace rozdělovače úlohy
Když otevřete Správce úloh šablony projektu, projekt bude mít JobSplitter.cs soubor otevřete ve výchozím nastavení. Rozdělení logiku pro úlohy můžete implementovat v vaše úlohy pomocí zobrazení metoda Split() níže:

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
> Části s poznámkami v `Split()` metoda je části pouze kód šablony Správce úloh, který je určena pro vás upravit přidáním logiku rozdělení úloh na různé úlohy. Pokud chcete změnit různé části šablony, zkontrolujte, zda jsou familiarized s fungování Batch a vyzkoušejte několik [ukázky kódu služby Batch][github_samples].
> 
> 

Implementace Split() má přístup k:

* Parametry úlohy prostřednictvím `_parameters` pole.
* CloudJob objekt představující úlohu, prostřednictvím `_job` pole.
* Objektu CloudTask představující úkolu Správce úloh, pomocí `_jobManagerTask` pole.

Vaše `Split()` implementace nemusí přímo přidání úkolů do úlohy. Místo toho kódu by měla vrátit pořadí objektů CloudTask a tyto do úlohy automaticky přidá framework třídy, které vyvolají rozdělovače úlohy. Je běžné použití jazyka C# na iterator (`yield return`) funkce pro implementaci příčky úlohy, jako to umožňuje úlohy spusťte nejdříve systémem než čekání na všechny úlohy se vypočítá.

**Selhání úlohy rozdělovače**

Pokud vaše úlohy rozdělovače dojde k chybě, měli buď:

* Ukončit pořadí pomocí jazyka C# `yield break` příkaz, ve kterém bude případ Správce úloh jednal jako úspěšné; nebo
* Způsobí výjimku, ve kterém případě, kdy správce úloh bude považována za se nezdařilo a může být opakována v závislosti na tom, jak klient má nakonfigurované ji).

V obou případech bude vhodné spouštět všechny úlohy už vrácený rozdělovače úlohy a přidány do úlohy Batch. Pokud nechcete, aby k tomu dojít, pak vám může:

* Ukončit úlohu před návratem od rozdělovače úlohy
* Formulovali celý úkol kolekcí před jejím vrácením (tedy vrátit `ICollection<CloudTask>` nebo `IList<CloudTask>` místo implementace rozdělovače vaše úlohy pomocí iterator C#)
* Ujistěte se, všechny úlohy, které závisí na úspěšné dokončení Správce úloh pomocí závislosti úkolů

**Opakování úlohy správce**

Pokud správce úloh selže, může pokus o službou Batch v závislosti na nastavení klienta opakování. Obecně platí to je bezpečné, protože při rozhraní přidá úkolů do úlohy, je ignorováno jakékoli úlohy, které již existují. Ale pokud výpočet úlohy je náročná, nemusí chcete zpoplatněná náklady přepočítání úlohy, které již byly přidány do úlohy; Naopak pokud ke generování ID pro stejnou úlohu spusťte znovu není zaručena pak chování 'ignorovat duplicity' nebude nové. V těchto případech měli navrhnout vaše úlohy rozdělovače ke zjištění práci, kterou již bylo provedeno a neopakuje, například provedením CloudJob.ListTasks předtím, než začnete yield úlohy.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kódy ukončení a výjimky v šabloně Správce úloh
Kódy ukončení a výjimky poskytují mechanismus určit výsledek spuštění programu, a může pomoct zjistit případné problémy s spuštění programu. Správce úloh šablony implementuje ukončovacích kódů a výjimek popsaných v této části.

Úkolu Správce úloh, která je implementována pomocí šablony Správce úloh se můžete vrátit tři možné ukončovací kód:

| Kód | Popis |
| --- | --- |
| 0 |Správce úloh byla úspěšně dokončena. Rozdělovače kódu úlohy byl dokončen a všechny úlohy byly přidány do úlohy. |
| 1 |Úkolu Správce úloh se nezdařilo s výjimkou v 'očekávané' součástí programu. Výjimka byla převedeny na JobManagerException s diagnostické informace a, kde je to možné, návrhy na řešení selhání. |
| 2 |Úkolu Správce úloh se nezdařilo s výjimkou 'neočekávané'. Protokolu byla zaznamenána výjimka standardním výstupu, ale Správci úloh se nepodařilo přidat žádné další diagnostiky nebo nápravnou informace. |

V případě selhání úlohy správce úloh některé úlohy mohou stále jsou přidané do služby předtím, než se stala chyba. Tyto úlohy se spustí normálně. Najdete v části "Selhání úlohy rozdělovače" nad diskuzi o tuto cestu kódu.

Všechny informace vrácené výjimky jsou zapsána do stdout.txt a stderr.txt soubory. Další informace najdete v tématu [zpracování chyb](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Důležité informace o klientech
Tato část popisuje některé požadavky na implementaci klienta při vyvolání Správce úloh, na základě této šablony. V tématu [jak předat parametry a proměnné prostředí z klientského kódu](#pass-environment-settings) podrobnosti o předání parametry a nastavení prostředí.

**Povinné přihlašovací údaje**

Chcete-li přidání úkolů do úlohy Azure Batch, úkolu Správce úloh vyžaduje adresa URL účtu Azure Batch a klíč. Je nutné předat v proměnné prostředí s názvem YOUR_BATCH_URL a YOUR_BATCH_KEY. Můžete nastavit tyto ve Správci úloh nastavení prostředí úloh. Například v C# klienta:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Přihlašovací údaje úložiště**

Klient obvykle není nutné poskytnout pověření k účtu propojené úložiště na úkolu Správce úloh, protože není nutné explicitně přístup propojený účet úložiště (a) většina Správci úloh a (b) propojený účet úložiště se často poskytuje na všechny úlohy, jako Běžné nastavení prostředí pro úlohu. Pokud nejsou poskytuje propojený účet úložiště pomocí běžných nastavení prostředí a Správce úloh vyžaduje přístup k propojené úložiště, pak by měl zadáte přihlašovacích údajů propojené úložiště následujícím způsobem:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Nastavení úloh Správce úloh**

Klient musí nastavit správce úloh *killJobOnCompletion* příznak, který **false**.

Obvykle je bezpečné pro klienta k nastavení *runExclusive* k **false**.

Klient musí použít *resourceFiles* nebo *applicationPackageReferences* kolekce se má úloha manager spustitelného souboru (a její požadované knihovny DLL) nasazené na výpočetním uzlu.

Ve výchozím nastavení nebude Správce úloh zopakován, pokud se nezdaří. V závislosti na logice úlohy správce klienta chtít povolit opakování prostřednictvím *omezení*/*maxTaskRetryCount*.

**Nastavení úloh**

Pokud úloha rozdělovače vysílá úlohy se závislostmi, klient musí nastavit usesTaskDependencies úlohy na hodnotu true.

V modelu rozdělovače úlohy neobvyklé, že klientům chcete přidání úkolů do úlohy nad rámec jaké úlohu rozdělovače vytvoří. Klient má proto za normálních okolností nastavit úlohy *onAllTasksComplete* k **terminatejob**.

## <a name="task-processor-template"></a>Šablony úloh procesoru
Šablony úloh procesoru pomáhá při implementaci procesor úloh, které můžete provádět následující akce:

* Nastavte informace vyžadované každý úkol Batch ke spuštění.
* Spusťte všechny akce, které vyžaduje každý úkol Batch.
* Uložte výstupy úlohy do trvalého úložiště.

Procesor úloh není potřeba spouštět úlohy Batch, klíče výhodou použití procesor úloh sice poskytuje obálka pro implementaci všechny akce provádění úkolů na jednom místě. Například pokud potřebujete spustit více aplikacemi v rámci každé úlohy, nebo pokud je nutné zkopírovat data do trvalého úložiště po dokončení každé úlohy.

Akce prováděné procesorem úloha může být jako jednoduché nebo komplexní a jako mnoho nebo jako několika podle požadavků vaší zatížení. Kromě toho implementací všechny akce úkolů do jednoho procesoru úloh můžete snadno aktualizovat nebo přidávat akce založené na změny požadavků na zatížení nebo aplikace. Ale v některých případech procesor úloh nemusí být optimální řešení týkající se vaší implementace jako ji můžete přidat nepotřebné složitost, například při spuštění úlohy, které lze rychle spustit z příkazového řádku jednoduché.

### <a name="create-a-task-processor-using-the-template"></a>Vytvoření úloh procesor pomocí šablony
Chcete-li přidat úloha procesor na řešení, které jste vytvořili dříve, postupujte takto:

1. Otevřete existující řešení v sadě Visual Studio.
2. V Průzkumníku řešení klikněte pravým tlačítkem na řešení, klikněte na tlačítko **přidat**a potom klikněte na **nový projekt**.
3. V části **Visual C#**, klikněte na tlačítko **cloudu**a potom klikněte na **Azure Batch úloh procesoru**.
4. Zadejte název, který popisuje vaší aplikace a identifikuje tohoto projektu jako úloha procesoru (např.) "LitwareTaskProcessor").
5. Chcete-li vytvořit projekt, klikněte na tlačítko **OK**.
6. Nakonec sestavte projekt a vynutit sadě Visual Studio načíst všechny odkazované balíčky NuGet a ověřte, zda je projekt platný před zahájením provádění úprav.

### <a name="task-processor-template-files-and-their-purpose"></a>Soubory šablon procesoru úloh a jejich účel
Při vytváření projektu pomocí šablony úloh procesoru generuje tři skupiny soubory kódu:

* Hlavní programový soubor (Program.cs). Tato položka obsahuje program Vstupní bod a nejvyšší úrovně výjimek. Neměli byste potřebovat obvykle tuto možnost lze upravit.
* Do složky Framework. Tato položka obsahuje soubory zodpovědná za práci, často používaný' pomocí programu Správce úloh – rozbalování parametrů, přidávání úkolů do úlohy Batch atd. Neměli byste potřebovat normálně k úpravě těchto souborů.
* Soubor procesoru úloh (TaskProcessor.cs). Toto je, kam jste umístili logika specifické pro aplikaci pro provádění úlohy (obvykle ve volají existující spustitelný soubor). Před a po zpracování kódu, třeba stahování další data nebo nahrávání souborů výsledek také zde bude.

Samozřejmě můžete přidat další soubory jako nezbytný pro podporu kódu procesoru úloh, v závislosti na složitosti úlohu, rozdělování logiku.

Šablona generuje také standardní soubory rozhraní .NET projektu, například souboru .csproj, app.config, souboru packages.config, atd.

Zbývající část tohoto oddílu popisuje různých souborů a jejich struktura kódu a vysvětluje, jaké jsou jednotlivé třídy.

![Visual Studio Průzkumník řešení zobrazující řešení šablony úloh procesoru][solution_explorer02]

**Soubory Framework**

* `Configuration.cs`: Zapouzdří načítání úlohy konfigurační data, jako je například Podrobnosti účtu Batch, přihlašovací údaje účtu propojené úložiště, úlohy a informace o úloze a parametry úlohy. Také poskytuje přístup k proměnné prostředí definované Batch (viz nastavení prostředí pro úkoly v dokumentaci k Batch) prostřednictvím třídy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrahuje implementace třídy konfigurace, aby bylo možné testování částí rozdělovače vaše úlohy pomocí falešných nebo imitované konfiguraci objektu.
* `TaskProcessorException.cs`: Představuje chybu, která vyžaduje Správce úloh ukončit. TaskProcessorException slouží k zabalení 'očekávaný: chyby, kde lze zadat konkrétní diagnostické informace jako součást ukončení.

**Úloha procesoru**

* `TaskProcessor.cs`: Spustí úlohu. Rozhraní framework vyvolá metodu TaskProcessor.Run. Toto je třída, kde bude vložit specifické pro aplikaci logiky vaše úlohy. Implementujte metodu spustit, abyste:
  * Analýza a ověření žádné parametry úlohy
  * Napište příkazového řádku pro jakýkoli externí program, který má být vyvolán
  * Přihlaste se další diagnostické informace, které můžete potřebovat pro účely ladění
  * Spustit proces, který používá tento příkazový řádek
  * Počkejte na ukončení procesu
  * Zaznamenat kód ukončení procesu k určení, zda je byla úspěšná nebo neúspěšná
  * Uložit výstupní soubory, které chcete zachovat do trvalého úložiště

**Standardní soubory projektu příkazového řádku .NET**

* `App.config`: Standardní soubor konfigurace aplikace .NET.
* `Packages.config`: Standardní soubor závislost balíčku NuGet.
* `Program.cs`: Obsahuje program Vstupní bod a nejvyšší úrovně výjimek.

## <a name="implementing-the-task-processor"></a>Implementace úkolů procesoru
Když otevřete projekt šablony úloh procesoru, bude mít projektu TaskProcessor.cs soubor otevřete ve výchozím nastavení. Spuštění logiku pro úlohy můžete implementovat v vaše úlohy pomocí metodu Run() vidíte níže:

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
> Části s poznámkami v metodu Run() je pouze část kód šablony úloh procesoru, který je určena pro vás upravit přidáním spuštění logiku pro úlohy v vaše úlohy. Pokud chcete změnit různé části šablony, prosím nejdřív Seznamte se s fungování Batch přečtení dokumentace Batch a vyzkoušení několik ukázky kódu Batch.
> 
> 

Metodu Run() je zodpovědná za spuštění příkazového řádku, od jednoho nebo více procesů čekání na dokončení, všechny procesu ukládání výsledků a nakonec vrácení s ukončovacím kódem. Metodu Run() je, kde můžete implementovat logiku zpracování pro vaše úkoly. Rozhraní framework procesoru úloh vyvolá metodu Run() pro vás; není nutné volat sami.

Implementace Run() má přístup k:

* Parametry úlohy prostřednictvím `_parameters` pole.
* ID úlohy a úkolů prostřednictvím `_jobId` a `_taskId` pole.
* Konfigurace úlohy prostřednictvím `_configuration` pole.

**Selhání úlohy**

V případě selhání můžete k ukončení metodu Run() došlo k výjimce, ale zůstane obslužná rutina výjimky nejvyšší úrovně v prvku kód ukončení úkolů. Pokud potřebujete řídit ukončovací kód, aby mohl rozlišit různé typy selhání, například k diagnostickým účelům nebo protože některé selhání režimy musí ukončit úlohu a ostatní neměli, pak by měl ukončení metodu Run() vrácením nenulovou hodnotou ukončovací kód. To se stane kód ukončení úkolů.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kódy ukončení a výjimky v šabloně úlohy procesoru
Kódy ukončení a výjimky poskytují mechanismus určit výsledek spuštění programu, a můžou pomoct zjistit případné problémy s spuštění programu. Šablony úloh procesoru implementuje ukončovacích kódů a výjimek popsaných v této části.

Úloha procesoru úloh, která je implementována pomocí šablony úloh procesoru vrátit tři možné ukončovací kód:

| Kód | Popis |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Úloha procesor byl dokončen. Všimněte si, že tento program můžete vyvolat úspěšného – pouze neznamená, že procesor úkolů úspěšně volat a provést jakékoli po zpracování bez výjimek. Význam ukončovací kód závisí na vyvolaný program – obvykle ukončovací kód 0 znamená program úspěšná a jiný kód ukončení znamená program selhal. |
| 1 |Procesor úlohy se nezdařilo s výjimkou v 'očekávané' součástí programu. Výjimka byl přeložen na `TaskProcessorException` s diagnostické informace a, kde je to možné, návrhy na řešení selhání. |
| 2 |Procesor úlohy se nezdařilo s 'neočekávané' výjimky. Protokolu byla zaznamenána výjimka standardním výstupu, ale procesoru úloh se nepodařilo přidat další diagnostiky nebo nápravnou informace. |

> [!NOTE]
> Pokud program, který je vyvolán používá k označení režimy konkrétní chyby ukončovací kód 1 a 2, pak pomocí ukončovací kód 1 a 2 pro procesoru chybám úloh je nejednoznačný. Kódy chyb procesoru těchto úloh můžete změnit na rozlišovací ukončovací kód úpravou případů výjimka v souboru Program.cs.
> 
> 

Všechny informace vrácené výjimky jsou zapsána do stdout.txt a stderr.txt soubory. Další informace najdete v tématu zpracování chyb v dokumentaci k Batch.

### <a name="client-considerations"></a>Důležité informace o klientech
**Přihlašovací údaje úložiště**

Pokud procesor úloh používá úložiště objektů blob v Azure k uchování výstupů, například pomocí souboru konvence pomocné knihovny, pak potřebuje přístup k *buď* údaje k účtu úložiště cloudu *nebo* objektu blob Adresa URL kontejneru, která zahrnuje sdílený přístupový podpis (SAS). Šablona zahrnuje podporu poskytování pověření prostřednictvím společné proměnné prostředí. Váš klient může předat přihlašovacích údajů úložiště následujícím způsobem:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Účet úložiště se pak dostupná ve třídě TaskProcessor prostřednictvím `_configuration.StorageAccount` vlastnost.

Pokud byste radši chtěli použít adresu URL kontejneru s tokenem SAS, můžete to taky předat prostřednictvím nastavení prostředí běžné úlohy, ale procesor šablony úloh aktuálně nezahrnuje integrovanou podporu pro tento.

**Instalační program úložiště**

Doporučuje se, že Správce úloh klienta nebo úloha vytvořit žádné kontejnery požadované úlohami před přidání úkolů do úlohy. Toto je povinné, že pokud použijete adresu URL kontejneru s tokenem SAS, jako například adresu URL nezahrnuje oprávnění k vytvoření kontejneru. I v případě, že předáváte přihlašovacích údajů účtu úložiště, doporučujeme jako ukládá každý úkol, museli volání CloudBlobContainer.CreateIfNotExistsAsync na kontejneru.

## <a name="pass-parameters-and-environment-variables"></a>Předávání parametrů a proměnných prostředí
### <a name="pass-environment-settings"></a>Předat nastavení prostředí
Informace o úkolu Správce úloh ve formě nastavení prostředí můžete předat klienta. Tyto informace pak může být použit úkol správce při generování úlohy procesoru úloh, které se spustí jako součást výpočetní úlohy. Příklady informace, které můžete předat jako nastavení prostředí:

* Klíče pro účet a název účtu úložiště
* Adresa URL účtu batch
* Klíč účtu batch

Služba Batch je jednoduchý mechanismus pro nastavení prostředí předat úkolu Správce úloh pomocí `EnvironmentSettings` vlastnost [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Chcete-li například získat `BatchClient` instance pro účet Batch můžete předat jako proměnné prostředí z klienta code adresu URL a pověření sdíleného klíče pro účet Batch. Stejně tak přístup k účtu úložiště, který je spojen s účtem Batch, můžete předat název účtu úložiště a klíč účtu úložiště jako proměnné prostředí.

### <a name="pass-parameters-to-the-job-manager-template"></a>Předat parametry do šablony Správce úloh
V mnoha případech je vhodné předat parametry na úlohu úkolu Správce úloh, řídit úlohu, rozdělování procesu nebo konfigurovat úlohy pro úlohu. To provedete tím, že nahrajete soubor JSON s názvem parameters.JSON tímto kódem jako soubor prostředků pro spuštění úkolu Správce úloh. Parametry se pak můžou stát k dispozici v `JobSplitter._parameters` pole v šabloně Správce úloh.

> [!NOTE]
> Předdefinované parametr obslužná rutina podporuje pouze slovník řetězec řetězec. Pokud chcete předat komplexní JSON hodnoty jako hodnoty parametrů, budete muset předat jako řetězce a analyzovat je ve rozdělovače úlohy nebo upravit rozhraní framework `Configuration.GetJobParameters` metoda.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Předat parametry do šablony úloh procesoru
Můžete také předat parametry jednotlivé úlohy implementovaná pomocí šablony úloh procesoru. Stejně jako pomocí šablony Správce úloh, procesor šablony úloh hledá soubor prostředků s názvem

Parameters.JSON tímto kódem a pokud zjistila, že ho načte jako slovník parametrů. Existuje několik možností pro jak předat parametry úlohy procesoru úloh:

* Znovu použít parametry úlohy JSON. To funguje dobře v případě, že pouze parametry jsou ty úlohy celou (pro příklad, vykreslení výška a šířka). K tomuto účelu, při vytváření CloudTask v rozdělovače úlohy Přidat odkaz na objekt souboru parameters.JSON tímto kódem prostředku z úkolu Správce úloh ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) do kolekce ResourceFiles CloudTask.
* Generování a nahrávání dokumentu Parameters.JSON tímto kódem specifických úkolů při provádění úlohy rozdělovače a odkazovat na tento objekt blob v kolekci souborů prostředků úkolu. To je nezbytné, pokud jiné úlohy s odlišnými parametry. Příkladem může být 3D vykreslování scénář, kde je index rámečku předaný úlohu jako parametr.

> [!NOTE]
> Předdefinované parametr obslužná rutina podporuje pouze slovník řetězec řetězec. Pokud chcete předat komplexní JSON hodnoty jako hodnoty parametrů, budete muset předat jako řetězce a analyzovat je procesor úlohy nebo upravit rozhraní framework `Configuration.GetTaskParameters` metoda.
> 
> 

## <a name="next-steps"></a>Další postup
### <a name="persist-job-and-task-output-to-azure-storage"></a>Zachovat výstup úlohy a úkolů do úložiště Azure.
Další užitečné nástroje při vývoji řešení Batch je [Azure Batch souboru konvence][nuget_package]. Tato knihovna tříd rozhraní .NET (momentálně ve verzi preview) v aplikacích Batch .NET pomocí snadno ukládání a načítání úloh výstupy do a z Azure Storage. [Zachovat výstup úlohy a úlohy Azure Batch](batch-task-output.md) obsahuje úplnou diskusi o knihovně a jeho použití.


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
