---
title: Vysvětlení konceptů úložiště modelů zařízení | Microsoft Docs
description: Jako vývojář řešení nebo odborníky v IT se dozvíte o základních konceptech úložiště modelů zařízení.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfdf22ac9b97ff7187bc360efe07cfe16249bd6b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042877"
---
# <a name="device-model-repository"></a>Úložiště modelů zařízení

Úložiště modelů zařízení (DMR) umožňuje tvůrcům zařízení spravovat a sdílet modely zařízení IoT technologie Plug and Play. Modely zařízení jsou definice dokumentů JSON LD, které jsou definované pomocí [DTDL (Digital vlákna Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Zařízení DMR definuje vzor pro ukládání rozhraní DTDL ve struktuře složek na základě identifikátoru vlákna (DTMI) zařízení. Rozhraní ve službě DMR můžete vyhledat tak, že převedete DTMI na relativní cestu. Například `dtmi:com:example:Thermostat;1` DTMI překládá na `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Úložiště modelů veřejných zařízení

Společnost Microsoft je hostitelem veřejné služby DMR s těmito charakteristikami:

- Spravované modely. Microsoft zkontroluje a schválí všechna dostupná rozhraní pomocí pracovního postupu pro ověření žádosti o přijetí změn (GitHub).
- Neměnnosti.  Po publikování se rozhraní nedá aktualizovat.
- Technologie Hyper-Scale. Společnost Microsoft poskytuje veškerou potřebnou infrastrukturu pro vytvoření zabezpečeného a vysoce škálovatelného koncového bodu.

## <a name="custom-device-model-repository"></a>Vlastní úložiště modelů zařízení

Pokud chcete vytvořit vlastní soubor DMR, můžete použít stejný vzor protokolu DMR na jakémkoli úložném médiu, jako je místní systém souborů nebo vlastní webové servery HTTP. Můžete načíst modely z vlastního nástroje DMR stejným způsobem jako z veřejné správy DRM pouhým změnou základní adresy URL používané pro přístup ke službě DMR.

> [!NOTE]
> Nástroje, které slouží k ověření modelů ve veřejném úložišti DMR, se dají znovu použít ve vlastních úložištích.

## <a name="public-models"></a>Veřejné modely

Veřejné digitální zdvojené modely uložené v úložišti modelu jsou k dispozici všem uživatelům, kteří mají využívat a integrovat do svých aplikací. Veřejné modely umožňují otevřenému systému pro sestavovatele zařízení a vývojářům řešení sdílet a znovu používat svoje modely zařízení technologie Plug and Play IoT.

V části [publikování modelu](#publish-a-model) najdete pokyny k publikování modelu v úložišti modelu, aby bylo možné ho zveřejnit.

Uživatelé můžou procházet, vyhledávat a zobrazovat veřejná rozhraní z oficiálního [úložiště GitHub](https://github.com/Azure/iot-plugandplay-models).

Všechna rozhraní ve `dtmi` složkách jsou dostupná taky z veřejného koncového bodu. [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Řešení modelů

Chcete-li programově získat přístup k těmto rozhraním, je nutné převést dtmi na relativní cestu, kterou můžete použít k dotazování veřejného koncového bodu. Následující ukázka kódu ukazuje, jak to provést:

Chcete-li převést DTMI na absolutní cestu, používáme `DtmiToPath` funkci s `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Pomocí výsledné cesty a základní adresy URL pro úložiště můžeme rozhraní získat:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Publikování modelu

> [!Important]
> Abyste mohli odesílat modely do veřejného DMR, musíte mít účet GitHub.

1. Rozvětvení veřejného úložiště GitHub: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Naklonujte rozvětvené úložiště. Volitelně můžete vytvořit novou větev, abyste zachovali změny izolované od `main` větve.
1. Přidejte nová rozhraní do `dtmi` složky pomocí konvence složka/filename. Prohlédněte si nástroj [Add-model](#add-model) .
1. Ověřte modely místně pomocí oddílu [skripty k ověření změn](#validate-files) .
1. Potvrďte změny místně a vložte je do rozvětvení.
1. Z větve vytvořte žádost o přijetí změn, která cílí na `main` větev. Viz téma [vytvoření problému nebo dokumentace žádostí o](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) přijetí změn.
1. Zkontrolujte [požadavky](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)žádosti o přijetí změn.

Žádost o přijetí změn spustí řadu akcí GitHubu, které ověří nová odeslaná rozhraní a zajistěte, aby vaše žádost o přijetí změn splňovala všechny kontroly.

Microsoft odpoví na žádost o přijetí změn se všemi kontrolami za tři pracovní dny.

### <a name="add-model"></a>přidat model

Následující kroky ukazují, jak add-model.js skript vám pomůže přidat nové rozhraní. Tento skript vyžaduje spuštění Node.js:

1. Z příkazového řádku přejděte do místního úložiště Git.
1. Spuštěním příkazu `npm install`
1. Spuštěním příkazu `npm run add-model <path-to-file-to-add>`

Podívejte se na výstup konzoly pro všechny chybové zprávy.

### <a name="local-validation"></a>Místní ověřování

Stejné kontroly ověřování můžete spustit místně před odesláním žádosti o přijetí změn, abyste mohli pomáhat s diagnostikou problémů předem.

#### <a name="validate-files"></a>ověřit – soubory

`npm run validate-files <file1.json> <file2.json>` kontroluje, zda cesta k souboru odpovídá očekávané složce a názvům souborů.

#### <a name="validate-ids"></a>ověřit – ID

`npm run validate-ids <file1.json> <file2.json>` kontroluje, že všechna ID definovaná v dokumentu používají stejný kořen jako hlavní ID.

#### <a name="validate-deps"></a>ověřit – DEPS

`npm run validate-deps <file1.json> <file2.json>` zkontroluje, jestli jsou všechny závislosti dostupné ve `dtmi` složce.

#### <a name="validate-models"></a>ověřit – modely

Můžete spustit [ukázku ověřování DTDL](https://github.com/Azure-Samples/DTDL-Validator) k místnímu ověření vašich modelů.

## <a name="next-steps"></a>Další kroky

Doporučeným dalším krokem je kontrola [architektury IoT technologie Plug and Play](concepts-architecture.md).