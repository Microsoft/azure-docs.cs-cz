---
title: Vytváření vlastních jazykových sad SDK pomocí automatického REST
titleSuffix: Azure Digital Twins
description: Naučte se, jak pomocí funkce AutoRest generovat sady SDK pro vlastní jazyk, a to pro psaní kódu Azure Digital revláken v jiných jazycích, které nemají publikované sady SDK.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751107"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Vytváření vlastních jazykových sad SDK pro digitální vlákna Azure pomocí automatického REST

Pokud potřebujete pracovat s digitálními úkoly Azure pomocí jazyka, který nemá [publikována sada Azure Digital](how-to-use-apis-sdks.md)Workers SDK, v tomto článku se dozvíte, jak pomocí funkce AutoRest vygenerovat vlastní sadu SDK v jazyce podle vašeho výběru. 

Příklady v tomto článku ukazují vytvoření [sady SDK datové roviny](how-to-use-apis-sdks.md#overview-data-plane-apis), ale tento proces bude fungovat také pro VYGENEROVÁNÍ  [sady SDK řídicích rovin](how-to-use-apis-sdks.md#overview-control-plane-apis) .

## <a name="prerequisites"></a>Požadavky

Aby bylo možné vygenerovat sadu SDK, budete nejprve muset na svém místním počítači dokončit následující instalaci:
* Instalace [**AutoRest**](https://github.com/Azure/autorest), verze 2.0.4413 (verze 3 není aktuálně podporovaná)
* Instalace [**Node.js**](https://nodejs.org), což je předpokladem pro použití automatického REST
* Nainstalovat [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Stáhněte si nejnovější soubor Swagger (openapi) **roviny dat** pro Azure, který se nachází ve [složce Swagger datové roviny](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins), spolu s doprovodnou složkou příkladů. Soubor Swagger je ten s názvem *digitaltwins.jsv*.

>[!TIP]
> Pokud chcete vytvořit **sadu SDK řídicí plochy** , proveďte kroky v tomto článku pomocí nejnovějšího souboru **Swagger** (openapi) roviny ovládacího prvku () ze [složky Swagger řídicí roviny](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) místo z roviny dat.

Jakmile je počítač vybavený vše ze seznamu výše, jste připraveni použít k vytvoření sady SDK sadu AutoRest.

## <a name="create-the-sdk-using-autorest"></a>Vytvoření sady SDK pomocí programu AutoRest 

Jakmile Node.js nainstalujete, můžete spustit tento příkaz, abyste se ujistili, že máte nainstalovanou požadovanou verzi AutoRest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Pokud chcete spustit AutoRest proti souboru Swagger digitálních vláken Azure, použijte následující postup:
1. Zkopírujte soubor Swagger digitálních vláken Azure a jeho doprovodnou složku příkladů do pracovního adresáře.
2. Pomocí okna příkazového řádku přepněte do tohoto pracovního adresáře.
3. Spusťte AutoRest pomocí následujícího příkazu. `<language>`Zástupný text nahraďte vaším jazykem podle vlastního výběru: `python` , `java` , `go` a tak dále. (Můžete najít úplný seznam možností v [souboru Readme pro AutoRest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

V důsledku toho se v pracovním adresáři zobrazí nová složka s názvem *DigitalTwinsApi* . Vygenerované soubory sady SDK budou mít obor názvů *DigitalTwinsApi*. Tento obor názvů budete používat i v ostatních příkladech použití v tomto článku.

AutoRest podporuje široké spektrum generátorů kódů jazyka.

## <a name="make-the-sdk-into-a-class-library"></a>Vytvoření sady SDK do knihovny tříd

Můžete zahrnout soubory generované AutoRest přímo do řešení .NET. Je však možné, že budete chtít zahrnout sadu SDK digitálních vláken Azure do několika samostatných projektů (klientské aplikace, Azure Functions aplikace a další). Z tohoto důvodu může být užitečné sestavit samostatný projekt (knihovnu tříd .NET) ze generovaných souborů. Pak můžete zahrnout tento projekt knihovny tříd do několika řešení jako odkaz na projekt.

V této části jsou uvedeny pokyny k sestavení sady SDK jako knihovny tříd, což je vlastní projekt a lze je zahrnout do jiných projektů. Tyto kroky jsou závislé na **aplikaci Visual Studio**.

Tady je postup:

1. Vytvoření nového řešení sady Visual Studio pro knihovnu tříd
2. Jako název projektu použijte *DigitalTwinsApi*
3. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt *DigitalTwinsApi* vygenerovaného řešení a zvolte *Přidat > existující položku...*
4. Najděte složku, do které jste sadu SDK vygenerovali, a vyberte soubory na kořenové úrovni.
5. Stiskněte OK
6. Přidejte do projektu složku (kliknutím pravým tlačítkem vyberte projekt v Průzkumník řešení a zvolte *přidat > nová složka*).
7. Pojmenování *modelů* složek
8. V Průzkumník řešení klikněte pravým tlačítkem na složku *modely* a vyberte *Přidat > existující položku...*
9. Vyberte soubory ve složce *modely* VYGENEROVANÉ sady SDK a stiskněte OK.

K úspěšnému sestavení sady SDK bude projekt potřebovat tyto odkazy:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Pokud je chcete přidat, otevřete *nástroje > správce balíčků nuget > spravovat balíčky NuGet pro řešení...*.

1. Na panelu se ujistěte, že je vybraná karta *Procházet* .
2. Hledání *Microsoft. REST*
3. Vyberte `ClientRuntime` balíčky a a `ClientRuntime.Azure` přidejte je do svého řešení.

Nyní můžete sestavit projekt a zahrnout ho jako odkaz na projekt v libovolné aplikaci digitálního vlákna v Azure, kterou zapisujete.

## <a name="tips-for-using-the-sdk"></a>Tipy pro používání sady SDK

Tato část obsahuje obecné informace a pokyny pro používání vygenerované sady SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Synchronní a asynchronní volání

Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

### <a name="typed-and-untyped-data"></a>Zadaná a netypové data

REST API volání obecně vracejí objekty silného typu. Protože ale digitální vlákna Azure umožňuje uživatelům definovat vlastní typy pro vlákna, neexistuje žádný způsob, jak předem definovat statická návratová data pro mnoho volání digitálních vláken Azure. Místo toho rozhraní API vrátí typy obálky silného typu tam, kde je to možné, a vlastní data typu jsou v objektech Json.NET (používají se všude, kde se v podpisech rozhraní API zobrazuje datový typ "Object"). Tyto objekty lze v kódu vhodně přetypovat.

### <a name="error-handling"></a>Zpracování chyb

Pokaždé, když v sadě SDK dojde k chybě (včetně chyb HTTP, jako je 404), SDK vyvolá výjimku. Z tohoto důvodu je důležité zapouzdřit všechna volání rozhraní API v blocích try/catch.

Zde je fragment kódu, který se pokusí přidat dvojitou hodnotu a zachytí všechny chyby v tomto procesu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Stránkování

AutoRest generuje dva typy vzorů stránkování pro sadu SDK:
* Jedna pro všechna rozhraní API s výjimkou rozhraní API pro dotazy
* Jedno pro rozhraní API pro dotazování

V případě vzoru stránkování bez dotazu je zde ukázka ukázková metoda, která ukazuje, jak načíst stránkovaný seznam odchozích vztahů z digitálních vláken Azure:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Druhý vzor se generuje jenom pro rozhraní API dotazů. Používá `continuationToken` explicitně.

>[!TIP]
> Hlavním důvodem pro získání stránek je výpočet [poplatků za jednotky dotazu](concepts-query-units.md) pro volání rozhraní API dotazů.

Tady je příklad s tímto vzorem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Další kroky

Projděte si postup vytvoření klientské aplikace, kde můžete použít sadu SDK:
* [*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)
