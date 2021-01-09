---
title: 'Kurz: vytvoření kódu klientské aplikace'
titleSuffix: Azure Digital Twins
description: Kurz pro zápis minimálního kódu pro klientskou aplikaci pomocí sady .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: fd958c09a14334d8230e52413c590febb2148851
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048945"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Kurz: kódování pomocí rozhraní API digitálních vláken Azure

Pro vývojáře, kteří pracují s digitálními podmnožinami Azure, je běžné, že napíší klientskou aplikaci pro komunikaci s její instancí služby Azure Digital Working. Tento kurz zaměřený na vývojáře poskytuje Úvod do programování služby Azure Digital prokážely pomocí [sady Azure Digital prokážed SDK pro .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Provede vás vytvořením kroku klientské aplikace konzoly C# od začátku.

> [!div class="checklist"]
> * Nastavit projekt
> * Začínáme s kódem projektu   
> * Ukázka kompletního kódu
> * Vyčištění prostředků
> * Další kroky

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se používá příkazový řádek pro nastavení a práci v projektu. Proto můžete použít libovolný editor kódu k procházení cvičení.

Co je potřeba začít:
* Jakýkoli Editor kódu
* **.NET Core 3,1** na vašem vývojovém počítači. Tuto verzi .NET Core SDK můžete stáhnout pro více platforem od [stažení .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Příprava instance digitálních vláken Azure

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Nastavit projekt

Až budete připraveni přejít na instanci digitálního vlákna Azure, začněte nastavovat projekt klientské aplikace. 

V počítači otevřete příkazový řádek nebo jiné okno konzoly a vytvořte prázdný adresář projektu, do kterého chcete během tohoto kurzu uložit práci. Pojmenujte adresář libovolným způsobem, který chcete (například *DigitalTwinsCodeTutorial*).

Přejděte do nového adresáře.

Jednou v adresáři projektu **vytvořte prázdný projekt konzolové aplikace .NET**. V okně příkazového řádku můžete spuštěním následujícího příkazu vytvořit projekt minimálního C# pro konzolu:

```cmd/sh
dotnet new console
```

Tím se vytvoří několik souborů v adresáři, včetně jednoho s názvem *program.cs* , kde budete psát většinu kódu.

Nechejte příkazové okno otevřené, jak ho budete dál používat v průběhu tohoto kurzu.

V dalším kroku **přidejte do projektu dvě závislosti** , které budete potřebovat pro práci s digitálními úkoly Azure pomocí digitálních vláken. Pomocí níže uvedených odkazů můžete přejít k balíčkům v NuGet, kde můžete najít příkazy konzoly (včetně rozhraní .NET CLI) a přidat do projektu nejnovější verzi.
* [**Azure. DigitalTwins. Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Toto je balíček pro [sadu Azure Digital Revlákens SDK pro .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
* [**Azure. identity**](https://www.nuget.org/packages/Azure.Identity). Tato knihovna poskytuje nástroje, které vám pomůžou s ověřováním v Azure.

## <a name="get-started-with-project-code"></a>Začínáme s kódem projektu

V této části začnete psát kód pro nový projekt aplikace, abyste mohli pracovat s digitálními úkoly Azure. Mezi zahrnuté akce patří:
* Ověřování proti službě
* Nahrává se model.
* Zachycení chyb
* Vytváření digitálních vláken
* Vytváření relací
* Dotazování na digitální vlákna

K dispozici je také část zobrazující kompletní kód na konci tohoto kurzu. Tuto možnost můžete použít jako referenci ke kontrole vašeho programu.

Začněte tím, že otevřete soubor *program.cs* v editoru kódu. Zobrazí se minimální šablona kódu, která vypadá přibližně takto:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Fragment kódu vzorku. Existuje jeden příkaz using System;, obor názvů s názvem DigitalTwinsCodeTutorial; Třída v oboru názvů s názvem program; a metoda Main ve třídě s standardním podpisem &quot;static void Main (String [] args)&quot;. Metoda Main obsahuje příkaz Hello World Print." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nejprve přidejte `using` do horní části kódu některé řádky, které budou vyžádané v nezbytných závislostech.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Dále do tohoto souboru přidáte kód, který vyplní některé funkce. 

### <a name="authenticate-against-the-service"></a>Ověřování proti službě

První věc, kterou bude vaše aplikace muset udělat, je ověřování vůči službě Azure Digital prokážed. Pak můžete vytvořit třídu klienta služby pro přístup k funkcím sady SDK.

Aby bylo možné ověřit, potřebujete *název hostitele* instance digitálního vlákna Azure.

Do *program.cs* vložte následující kód pod text "Hello, World!". čára tisku v `Main` metodě Nastavte hodnotu `adtInstanceUrl` na *název hostitele* instance digitálního vlákna Azure.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Soubor uložte. 

V příkazovém okně spusťte kód pomocí tohoto příkazu: 

```cmd/sh
dotnet run
```

Tato akce obnoví závislosti při prvním spuštění a pak program spustí. 
* Pokud nedojde k žádné chybě, program vytiskne *klienta služby, který je vytvořený a připravený k přechodu*.
* Vzhledem k tomu, že v tomto projektu není žádná manipulace s chybami, zobrazí se výjimka vyvolaná kódem.

### <a name="upload-a-model"></a>Nahrání modelu

Digitální vlákna Azure nemá žádný vnitřní slovník domény. Typy prvků v prostředí, které můžete reprezentovat v rámci digitálních vláken Azure, jsou definovány pomocí **modelů**. [Modely](concepts-models.md) jsou podobné třídám v objektově orientovaném programovacím jazyce; poskytují uživatelsky definované šablony pro [digitální vlákna](concepts-twins-graph.md) , které budou následovat a vytvoří se později. Jsou napsané v jazyce podobném formátu JSON, který se nazývá **Digital DTDL (digitální vlákna Definition Language)**.

Prvním krokem při vytváření řešení digitálních vláken Azure je definování alespoň jednoho modelu v souboru DTDL.

V adresáři, ve kterém jste vytvořili projekt, vytvořte nový soubor *. JSON* s názvem *SampleModel.jsv*. Vložte do následujícího textu souboru: 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Pokud pro tento kurz používáte sadu Visual Studio, možná budete chtít vybrat nově vytvořený soubor JSON a nastavit vlastnost *Kopírovat do výstupního adresáře* v inspektoru vlastností na hodnotu *Kopírovat, pokud* je vždycky novější nebo *Kopírovat*. To umožní aplikaci Visual Studio najít soubor JSON s výchozí cestou při spuštění programu s klávesou **F5** během zbývající části kurzu.

> [!TIP] 
> Je k dispozici [Ukázka validátoru](/samples/azure-samples/dtdl-validator/dtdl-validator) jazyka nezávislá DTDL, kterou můžete použít ke kontrole modelu dokumentů, abyste měli jistotu, že je DTDL platný. Je postaven na knihovně analyzátoru DTDL, kterou si můžete přečíst v tématu [*Postupy: analýza a ověření modelů*](how-to-parse-models.md).

Dále přidejte další kód do *program.cs* , abyste nahráli model, který jste právě vytvořili, do instance digitálního vlákna Azure.

Nejprve přidejte `using` do horní části souboru několik příkazů:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Dále připravte na použití asynchronních metod v sadě SDK služby C# tak, že změníte `Main` signaturu metody tak, aby povolovala asynchronní spuštění. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> Použití `async` není bezpodmínečně nutné, protože sada SDK také poskytuje synchronní verze všech volání. V tomto kurzu se používají postupy `async` .

Další součástí je první bit kódu, který komunikuje se službou Azure Digital revlákens. Tento kód načte soubor DTDL, který jste vytvořili z disku, a pak ho nahraje do instance služby digitálního vláken Azure. 

Vložte následující kód v rámci autorizačního kódu, který jste přidali dříve.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

V příkazovém okně spusťte program s tímto příkazem: 

```cmd/sh
dotnet run
```
"Nahrání modelu" bude vytištěno ve výstupu, což znamená, že byl tento kód dosažen, ale ještě neexistuje žádný výstup k označení, zda bylo nahrávání úspěšné.

Chcete-li přidat příkaz Print se zobrazením všech modelů, které byly úspěšně nahrány do instance, přidejte po předchozím oddílu následující kód:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Předtím, než program znovu spustíte pro otestování tohoto nového kódu**, zajistěte, aby jste svůj model nahráli již při posledním spuštění programu. Digitální vlákna Azure vám neumožní nahrát stejný model dvakrát, takže pokud se pokusíte znovu nahrát stejný model, program by měl vyvolat výjimku.

V takovém případě spusťte program znovu s tímto příkazem v příkazovém okně:

```cmd/sh
dotnet run
```

Program by měl vyvolat výjimku. Při pokusu o nahrání modelu, který již byl nahrán, služba vrátí chybu "špatný požadavek" prostřednictvím REST API. Výsledkem je, že klientská sada SDK pro Azure Digital rejímka vyvolá výjimku pro každou službu, která vrací jiný kód než úspěch. 

V další části se dozvíte o výjimkách, jako jsou tyto výjimky a jak je zpracovat v kódu.

### <a name="catch-errors"></a>Zachytit chyby

Chcete-li zabránit selhání programu, můžete přidat kód výjimky kolem kódu nahrávání modelu. Zabalte existující volání klienta `await client.CreateModelsAsync(typeList)` v obslužné rutině try/catch, například takto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Když teď spustíte program `dotnet run` ve vašem příkazovém okně, uvidíte, že se vám vrátí kód chyby. Výstup kódu vytváření modelů zobrazuje tuto chybu:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Výstup programu zobrazuje zprávu s oznámením, že žádost o službu se nezdařila. Stav: 409 (konflikt). ', následuje odkaz na chybu oznamující, že dtmi: Příklad: SampleModel; 1 již existuje.":::

Od tohoto okamžiku se v kurzu zabalí všechna volání metod služby v obslužných rutinách try/catch.

### <a name="create-digital-twins"></a>Vytváření digitálních vláken

Teď, když jste nahráli model do digitálních vláken Azure, můžete k vytváření **digitálních vláken** použít tuto definici modelu. [Digitální vlákna](concepts-twins-graph.md) jsou instance modelu a představují entity v rámci vašeho podnikového prostředí – například senzory ve farmě, místnosti v budově nebo světla v kleci. Tato část vytvoří několik digitálních vláken na základě modelu, který jste nahráli dříve.

Na konec metody přidejte následující kód, `Main` který vytvoří a inicializuje tři digitální vlákna na základě tohoto modelu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

V příkazovém okně spusťte program pomocí příkazu `dotnet run` . Ve výstupu vyhledejte tiskové zprávy, které byly vytvořeny z *sampleTwin-0*, *sampleTwin-1* a *sampleTwin-2* . 

Pak program spusťte znovu. 

Všimněte si, že není vyvolána žádná chyba při druhém vytvoření vlákna, a to i v případě, že vlákna již existují po prvním spuštění. Na rozdíl od vytváření modelů je vytvoření vlákna na úrovni REST, volání *Put* se sémantikou *Upsert* . To znamená, že pokud již existuje vlákna, pokus o vytvoření stejné vlákna znovu nahradí původní vlákna. Není vyvolána žádná chyba.

### <a name="create-relationships"></a>Vytvoření relací

V dalším kroku můžete vytvořit **relace** mezi dvojitými vytvořenými podmnožinami a propojit je s **dvojitým grafem**. K reprezentaci celého prostředí se používají [Dvojitá grafu](concepts-twins-graph.md) .

Přidejte do třídy **novou statickou metodu** `Program` pod `Main` metodou (kód má nyní dvě metody):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Dále přidejte následující kód na konec `Main` metody, pro volání `CreateRelationship` metody a použijte kód, který jste právě napsali:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

V příkazovém okně spusťte program pomocí příkazu `dotnet run` . Ve výstupu vyhledejte příkazy Print oznamující, že se tyto dvě relace úspěšně vytvořily.

Všimněte si, že digitální vlákna Azure vám neumožní vytvořit relaci, pokud už existuje jiná relace se stejným ID, takže pokud program spustíte několikrát, při vytváření relace se zobrazí výjimky. Tento kód zachytí výjimky a ignoruje je. 

### <a name="list-relationships"></a>Výpis relací

Další kód, který přidáte, vám umožní zobrazit seznam relací, které jste vytvořili.

Do třídy přidejte následující **novou metodu** `Program` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Pak přidejte následující kód na konec `Main` metody pro volání `ListRelationships` kódu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

V příkazovém okně spusťte program pomocí příkazu `dotnet run` . Měl by se zobrazit seznam všech relací, které jste vytvořili v příkazu OUTPUT, který vypadá takto:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Výstup programu zobrazující zprávu s názvem &quot;zdvojená sampleTwin-0&quot; je připojená k: obsahuje->sampleTwin-1,-Contains->sampleTwin-2" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Dotazování na digitální vlákna

Hlavní funkcí digitálních vláken Azure je schopnost snadno a efektivně [dotazovat](concepts-query-language.md) se na váš graf, aby odpovídal na dotazy týkající se vašeho prostředí.

Poslední část kódu, který se má přidat v tomto kurzu, spustí dotaz na instanci digitálních vláken Azure. Dotaz použitý v tomto příkladu vrátí všechny digitální vlákna v instanci.

Přidejte tento `using` příkaz, který umožňuje použití `JsonSerializer` třídy k prezentaci informací o digitálním typu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Pak na konec metody přidejte následující kód `Main` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

V příkazovém okně spusťte program pomocí příkazu `dotnet run` . Ve výstupu by se měly zobrazit všechny digitální vlákna v této instanci.

## <a name="complete-code-example"></a>Příklad kompletního kódu

V tomto okamžiku v tomto kurzu máte úplnou klientskou aplikaci, která umožňuje provádět základní akce proti digitálním vazbám Azure. Pro referenci je úplný kód programu v *program.cs* uveden níže:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Vyčištění prostředků
 
Instance použitá v tomto kurzu se dá znovu použít v dalším kurzu. [*kurz: Prozkoumejte základy pomocí ukázkové klientské aplikace*](tutorial-command-line-app.md). Pokud máte v úmyslu pokračovat k dalšímu kurzu, můžete ponechat instanci digitálních vláken Azure, kterou jste nastavili tady.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Nakonec odstraňte složku projektu, kterou jste vytvořili na místním počítači.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili klientskou aplikaci konzoly .NET od nuly. Napsali jste kód pro tuto klientskou aplikaci k provádění základních akcí v instanci digitálních vláken Azure.

Pokračujte k dalšímu kurzu, abyste prozkoumali věci, které můžete s touto ukázkovou klientskou aplikací dělat: 

> [!div class="nextstepaction"]
> [*Kurz: zkoumání základních informací pomocí ukázkové klientské aplikace*](tutorial-command-line-app.md)
