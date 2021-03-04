---
title: Rychlý Start klientské knihovny JavaScriptu pro obličej
description: Pomocí klientské knihovny pro rozpoznávání tváře pro JavaScript detekuje obličeje, najděte podobné (vyhledávání obličejem podle obrázku), identifikujte plošky (hledání rozpoznávání obličeje) a migrujte data z obličeje.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 16797a5bdb5ef5f2b5660a33e1788b8824f1ad8f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722551"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Rychlý Start: Klientská knihovna pro tvář pro JavaScript

Začněte s rozpoznáváním obličeje pomocí klientské knihovny pro tvář pro JavaScript. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba Faceer poskytuje přístup k pokročilým algoritmům pro zjišťování a rozpoznávání lidských plošek na obrázcích.

Pomocí klientské knihovny obličeje pro JavaScript:

* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvořit skupinu osob](#create-a-person-group)
* [Identifikace obličeje](#identify-a-face)

[Referenční dokumentace](/javascript/api/@azure/cognitiveservices-face/?view=azure-node-latest)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face)  |  [Ukázky](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Nejnovější verze [Node.js](https://nodejs.org/en/)
* Jakmile budete mít předplatné Azure, vytvořte na Azure Portal [prostředek pro vytváření obličeje](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) a Získejte svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání tváře budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny 

Nainstalujte `ms-rest-azure` balíčky a `azure-cognitiveservices-face` npm:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

Vytvořte soubor s názvem `index.js` a importujte následující knihovny:

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](), který obsahuje příklady kódu v tomto rychlém startu.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. 

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek obličeje, který jste vytvořili v části **předpoklady** , nasadil úspěšně, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny rozhraní .NET pro obličej:

|Název|Popis|
|---|---|
|[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient?view=azure-node-latest) | Tato třída reprezentuje vaši autorizaci používat službu obličeje a potřebujete ji pro všechny funkce obličeje. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. |
|[Rozpoznávání tváře](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete provádět s lidskými obličejemi. |
|[DetectedFace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/detectedface?view=azure-node-latest)|Tato třída reprezentuje všechna data, která byla zjištěna z jedné plošky v obrázku. Můžete ji použít k získání podrobných informací o tváři.|
|[FaceList](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/facelist?view=azure-node-latest)|Tato třída spravuje **FaceList** konstrukce uložené v cloudu, které ukládají roztříděné sady ploch. |
|[PersonGroupPerson](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroupperson?view=azure-node-latest)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu plošek, které patří jedné osobě.|
|[PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu objektů pro řazení **osob** . |

## <a name="code-examples"></a>Příklady kódu

Níže uvedené fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro tvář pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvořit skupinu osob](#create-a-person-group)
* [Identifikace obličeje](#identify-a-face)

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), který obsahuje příklady kódu v tomto rychlém startu.

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest)** s klíčem a použijte ho u svého koncového bodu k vytvoření objektu **[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient?view=azure-node-latest)** .

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Deklarace globálních hodnot a pomocné funkce

Následující globální hodnoty jsou nutné pro několik operací tváře, které přidáte později.

Adresa URL odkazuje na složku ukázkových obrázků. Identifikátor UUID bude sloužit jako název i ID pro osobu, kterou vytvoříte.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Pomocí následující funkce můžete počkat na dokončení školení osoby ve službě Person.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

### <a name="get-detected-face-objects"></a>Získat zjištěné objekty Face

Vytvořte novou metodu pro detekci plošek. `DetectFaceExtract`Metoda zpracuje tři obrázky na dané adrese URL a vytvoří seznam objektů **[DetectedFace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/detectedface?view=azure-node-latest)** v paměti pro programy. Seznam hodnot **[FaceAttributeType](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceattributetype?view=azure-node-latest)** určuje, které funkce se mají extrahovat. 

`DetectFaceExtract`Metoda pak analyzuje a tiskne data atributů pro každou zjištěnou plochu. Každý atribut se musí zadat samostatně v původním volání rozhraní API pro detekci obličeje (v seznamu **[FaceAttributeType](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceattributetype?view=azure-node-latest)** ). Následující kód zpracuje každý atribut, ale bude pravděpodobně nutné použít pouze jeden nebo několik.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Můžete také detekovat plošky v místní imagi. Podívejte se na metody [obličeje](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest) , jako je [DetectWithStreamAsync](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód přebírá jednu zjištěnou plošku (zdroj) a hledá v sadě dalších tváře (cíle) k nalezení shody (hledání na základě obrázku). Pokud najde shodu, vytiskne ID spárovaného obličeje konzole.

### <a name="detect-faces-for-comparison"></a>Detekovat plošky pro porovnání

Nejdřív Definujte metodu detekce druhé plochy. Je potřeba detekovat plošky na obrázcích předtím, než je můžete porovnat, a tato metoda detekce je optimalizována pro operace porovnání. Neextrahuje podrobné atributy obličeje jako v části výše a používá jiný model rozpoznávání.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Najít shody

Následující metoda detekuje plošky v sadě cílových imagí a v jediné zdrojové imagi. Pak je porovná a vyhledá všechny cílové image, které jsou podobné zdrojové imagi. Nakonec vytiskne podrobnosti o shodě do konzoly.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Identifikace obličeje

Operace [Identifikace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) přebírá obrázek osoby (nebo více lidí) a hledá identitu každé plošky v imagi (vyhledávání rozpoznávání obličeje). Porovnává každou zjištěnou plochu s [osobou](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest), databázi různých objektů [osob](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/person?view=azure-node-latest) , jejichž funkce obličeje jsou známy. Aby bylo možné operaci identifikace provést, musíte nejprve vytvořit a naučit objekt [Person](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest).

### <a name="add-faces-to-person-group"></a>Přidat plošky do skupiny osob

Vytvořte následující funkci, která přidá obličeje do pole [Person](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>Počkat na školení skupiny osob

Vytvořte následující pomocnou funkci, která čeká na dokončení školení pro skupinu osob.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>Vytvořit skupinu osob

Následující kód:
- Vytvoří objekt [Person](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest) .
- Přidá obličeje do skupiny person voláním `AddFacesToPersonGroup` , které jste definovali dříve.
- Navlakuje skupinu osob.
- Identifikuje plošky ve skupině Person.

Tato skupina **uživatelů** a její přidružené objekty **osoby** jsou teď připravené k použití v operacích ověřování, identifikace nebo seskupení.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Můžete také vytvořit objekt **Person** z místních imagí. Podívejte se na metody [PersonGroupPerson](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroupperson?view=azure-node-latest) , jako je [AddFaceFromStream](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroupperson?view=azure-node-latest#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Hlavní

Nakonec vytvořte `main` funkci a zavolejte ji.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat aplikaci Face Client Library pro JavaScript k provádění základních úloh rozpoznávání obličeje. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na rozhraní API pro rozpoznávání tváře (JavaScript)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [Co je služba Rozpoznávání tváře?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
