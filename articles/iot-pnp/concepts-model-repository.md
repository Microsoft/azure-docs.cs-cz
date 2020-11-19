---
title: Vysvětlení konceptů úložiště modelů zařízení | Microsoft Docs
description: Jako vývojář řešení nebo odborníky v IT se dozvíte o základních konceptech úložiště modelů zařízení.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b567efe2541bb33c905def73bb78398799b4ed69
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920538"
---
# <a name="device-model-repository"></a>Úložiště modelů zařízení

Úložiště modelů zařízení (DMR) umožňuje tvůrcům zařízení spravovat a sdílet modely zařízení IoT technologie Plug and Play. Modely zařízení jsou definice dokumentů JSON LD, které jsou definované pomocí [DTDL (Digital vlákna Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Zařízení DMR definuje vzor pro ukládání rozhraní DTDL ve struktuře složek na základě identifikátoru vlákna (DTMI) zařízení. Rozhraní ve službě DMR můžete vyhledat tak, že převedete DTMI na relativní cestu. Například `dtmi:com:example:Thermostat;1` DTMI překládá na `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Úložiště modelů veřejných zařízení

Společnost Microsoft je hostitelem veřejné služby DMR s těmito charakteristikami:

- Spravované modely. Microsoft zkontroluje a schválí všechna dostupná rozhraní pomocí pracovního postupu ověření žádosti o přijetí změn (PR) GitHubu.
- Neměnnosti.  Po publikování se rozhraní nedá aktualizovat.
- Technologie Hyper-Scale. Společnost Microsoft poskytuje požadovanou infrastrukturu pro vytvoření zabezpečeného a škálovatelného koncového bodu, kde můžete publikovat a využívat modely zařízení.

## <a name="custom-device-model-repository"></a>Vlastní úložiště modelů zařízení

Použijte stejný vzor protokolu DMR k vytvoření vlastního protokolu DMR na jakémkoli úložném médiu, jako je místní systém souborů nebo vlastní webové servery HTTP. Můžete načítat modely zařízení z vlastního zařízení DMR stejným způsobem jako z veřejného ovladače DMR změnou základní adresy URL používané pro přístup ke službě DMR.

> [!NOTE]
> Microsoft poskytuje nástroje pro ověření modelů zařízení ve veřejné službě DMR. Tyto nástroje můžete znovu použít ve vlastních úložištích.

## <a name="public-models"></a>Veřejné modely

Modely veřejných zařízení uložené v úložišti modelů jsou k dispozici všem uživatelům, kteří mají ve svých aplikacích využívat a integrovat. Modely veřejných zařízení umožňují otevřenému systému pro sestavovatele zařízení a vývojářům řešení sdílet a znovu používat svoje modely zařízení technologie Plug and Play IoT.

V části [publikování modelu](#publish-a-model) najdete pokyny k publikování modelu v úložišti modelu, aby bylo možné ho zveřejnit.

Uživatelé můžou procházet, vyhledávat a zobrazovat veřejná rozhraní z oficiálního [úložiště GitHub](https://github.com/Azure/iot-plugandplay-models).

Všechna rozhraní ve `dtmi` složkách jsou dostupná taky z veřejného koncového bodu. [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Řešení modelů

Chcete-li programově získat přístup k těmto rozhraním, je nutné převést DTMI na relativní cestu, kterou můžete použít k dotazování veřejného koncového bodu.

Chcete-li převést DTMI na absolutní cestu, použijte `DtmiToPath` funkci s `IsValidDtmi` :

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
1. Přidejte nová rozhraní do `dtmi` složky pomocí konvence složka/filename. Další informace najdete v tématu [Import modelu do `dtmi/` složky](#import-a-model-to-the-dtmi-folder).
1. Ověřte modely místně pomocí `dmr-client` nástroje. Další informace najdete v tématu [ověření modelů](#validate-models).
1. Potvrďte změny místně a vložte je do rozvětvení.
1. Z větve vytvořte žádost o přijetí změn, která cílí na `main` větev. Viz téma [vytvoření problému nebo dokumentace žádostí o](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) přijetí změn.
1. Zkontrolujte [požadavky žádosti o](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)přijetí změn.

Žádost o přijetí změn spustí sadu akcí GitHubu, které ověřují odeslaná rozhraní, a zajistí, že vaše žádost o přijetí změn splňuje všechny požadavky.

Microsoft bude reagovat na žádost o přijetí změn se všemi kontrolami tři pracovní dny.

### <a name="dmr-client-tools"></a>`dmr-client` nástroje

Nástroje, které slouží k ověřování modelů během kontrol žádosti o přijetí změn, je možné také použít k místnímu přidání a ověření rozhraní DTDL.

> [!NOTE]
> Tento nástroj vyžaduje [sadu .NET SDK](https://dotnet.microsoft.com/download) verze 3,1 nebo vyšší.

### <a name="install-dmr-client"></a>Znovu `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Import modelu do `dtmi/` složky

Pokud už máte model uložený v souborech JSON, můžete `dmr-client import` k jejich přidání do `dtmi/` složky se správnými názvy souborů použít příkaz:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> `--local-repo`K určení kořenové složky místního úložiště můžete použít argument.

### <a name="validate-models"></a>Ověřit modely

Pomocí příkazu můžete ověřit své modely `dmr-client validate` :

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> Ověřování používá nejnovější verzi analyzátoru DTDL k zajištění kompatibility všech rozhraní se specifikací jazyka DTDL.

Pokud chcete ověřit externí závislosti, musí existovat v místním úložišti. Chcete-li ověřit modely, použijte `--repo` možnost pro určení `local` `remote` složky nebo pro vyřešení závislostí:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Striktní ověřování

Požadavek DMR obsahuje další [požadavky](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md), pomocí `stict` příznaku ověřte, že je váš model k těmto požadavkům:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Podívejte se na výstup konzoly pro všechny chybové zprávy.

### <a name="export-models"></a>Export modelů

Modely lze z daného úložiště (místní nebo vzdálené) exportovat do jednoho souboru pomocí pole JSON:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Další kroky

Doporučeným dalším krokem je kontrola [architektury IoT technologie Plug and Play](concepts-architecture.md).
