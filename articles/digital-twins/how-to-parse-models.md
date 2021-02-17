---
title: Parsování a ověření modelů
titleSuffix: Azure Digital Twins
description: Naučte se používat knihovnu analyzátorů k analýze DTDL modelů.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 155566a125485fda326f9f5e02d4aead0ffe30e3
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560751"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Analýza a ověření modelů pomocí knihovny analyzátoru DTDL

[Modely](concepts-models.md) v digitálních prostředníkech Azure se definují pomocí jazyka DTDL (Digital nenáročné na digitální vlákna JSON). **Před odesláním do instance digitálního vlákna Azure doporučujeme vaše modely ověřit offline.**

Abychom to mohli udělat, je k dispozici knihovna pro analýzu DTDL na straně klienta .NET na webu NuGet: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Knihovnu analyzátoru můžete použít přímo v kódu C# nebo pomocí ukázkového projektu Language-nezávislá Code, který je založen na knihovně analyzátoru: [**DTDL validátor**](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Použití ukázky validátoru DTDL

[**Validátor DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator) je ukázkový projekt, který umožňuje ověřit modelové dokumenty, aby bylo zajištěno, že DTDL je platný. Je postavená na knihovně analyzátoru .NET a je to Language-nezávislá. Můžete ji získat pomocí tlačítka *Stáhnout ZIP* na ukázkovém odkazu.

Zdrojový kód ukazuje příklady použití knihovny analyzátoru. Můžete použít ukázku validátoru jako nástroj příkazového řádku k ověření stromu adresářových souborů DTDL. Poskytuje také interaktivní režim.

Informace o tom, jak balíček ukázek do samostatného spustitelného souboru zabalit, najdete ve složce DTDL validátoru pro ověřování v souboru *Readme.MD* .

Po vytvoření samostatného balíčku a přidání spustitelného souboru do vaší cesty můžete validátor spustit pomocí tohoto příkazu v konzole na počítači:

```cmd/sh
DTDLValidator
```

V případě výchozích možností bude ukázka hledat `*.json` soubory v aktuálním adresáři a všech podadresářích. Následující možnost můžete také přidat, chcete-li ukázkové hledání v označeném adresáři a všech podadresářích pro soubory s příponou *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Můžete přidat `-i` možnost pro ukázku zadání interaktivního režimu:

```cmd/sh
DTDLValidator -i
```

Další informace o této ukázce naleznete v tématu zdrojový kód nebo spustit `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>Použití knihovny analyzátoru .NET 

Knihovna [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) poskytuje modelový přístup k definicím DTDL, který v podstatě funguje jako ekvivalent pro reflexi jazyka C# pro DTDL. Tato knihovna se dá použít nezávisle na jakékoli [sadě SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md), zejména pro ověřování DTDL ve vizuálním nebo textovém editoru. Je vhodné zajistit, aby byly soubory definice modelu platné, než se pokusíte je odeslat do služby.

Chcete-li použít knihovnu analyzátoru, zadejte ji se sadou dokumentů DTDL. Obvykle byste tyto modelové dokumenty načetli ze služby, ale můžete je také zpřístupnit místně, pokud byl váš klient zodpovědný za jejich nahrání do služby na prvním místě. 

Tady je obecný pracovní postup pro použití analyzátoru:
1. Načte některé nebo všechny dokumenty DTDL ze služby.
2. Předejte vracené dokumenty DTDL v paměti do analyzátoru.
3. Analyzátor ověří sadu předaných dokumentů a vrátí podrobné informace o chybě. Tato možnost je užitečná ve scénářích editoru.
4. Použijte rozhraní API analyzátoru pro pokračování v analýze modelů obsažených v sadě dokumentů. 

Mezi schopnosti analyzátoru patří:
* Získat všechna implementovaná rozhraní modelu (obsah `extends` oddílu rozhraní).
* Získá všechny vlastnosti, telemetrie, příkazy, komponenty a vztahy deklarované v modelu. Tento příkaz také získá všechna metadata zahrnutá v těchto definicích a bere v `extends` úvahu dědičnost (oddíly).
* Získat všechny definice komplexního modelu
* Určete, zda lze model přiřadit z jiného modelu.

> [!NOTE]
> Zařízení [IoT technologie Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) používají k popisu jejich funkce malou variantu syntaxe. Tato varianta syntaxe je sémanticky kompatibilní podmnožinou DTDL, která se používá v digitálních Vlákenách Azure. Při použití knihovny analyzátoru nemusíte znát, která varianta syntaxe se použila k vytvoření DTDL pro digitální vlákna. Analyzátor bude ve výchozím nastavení vždycky vracet stejný model pro syntaxi digitálního vlákna PnP a Azure.

### <a name="code-with-the-parser-library"></a>Kód pomocí knihovny analyzátoru

Knihovnu analyzátoru můžete použít přímo pro věci, jako je ověřování modelů ve vaší vlastní aplikaci nebo pro generování dynamických, modelem řízených uživatelských rozhraní, řídicích panelů a sestav.

Pro podporu níže uvedeného příkladu kódu analyzátoru zvažte několik modelů definovaných v instanci digitálních vláken Azure:

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

Následující kód ukazuje příklad použití knihovny analyzátoru pro reflektování těchto definic v jazyce C#:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>Další kroky

Až dokončíte psaní modelů, přečtěte si téma jak je nahrát (a provést jiné operace správy) pomocí rozhraní DigitalTwinsModels API:
* [*Postupy: Správa modelů DTDL*](how-to-manage-model.md)