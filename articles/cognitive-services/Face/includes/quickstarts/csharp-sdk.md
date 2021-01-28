---
title: Plocha pro rychlý Start klientské knihovny .NET
description: Pomocí klientské knihovny pro rozpoznávání tváře pro .NET zjistíte obličeje, najděte podobné (vyhledávání na ploše podle obrázku), identifikujte obličeje (hledání rozpoznávání obličeje) a migrujte data ze strany obličeje.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: a01afdcb54ff124bf4141d6ceb34b77303bef104
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947865"
---
Začněte s rozpoznáváním obličeje pomocí klientské knihovny pro tvář pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba Faceer poskytuje přístup k pokročilým algoritmům pro zjišťování a rozpoznávání lidských plošek na obrázcích.

Pomocí klientské knihovny obličeje pro .NET:

* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvořit skupinu osob](#create-a-person-group)
* [Identifikace obličeje](#identify-a-face)

[Referenční dokumentace](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Ukázky](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Požadavky


* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/) nebo aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání tváře budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

#### <a name="visual-studio-ide"></a>[Integrované vývojové prostředí sady Visual Studio](#tab/visual-studio)

Pomocí sady Visual Studio vytvořte novou aplikaci .NET Core. 

### <a name="install-the-client-library"></a>Instalace klientské knihovny 

Po vytvoření nového projektu nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení projektu v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předprodejní** a vyhledejte `Microsoft.Azure.CognitiveServices.Vision.Face` . Vyberte verzi `2.6.0-preview.1` a pak **nainstalujte**. 

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `face-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*. 

```console
dotnet new console -n face-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny 

V adresáři aplikace nainstalujte klientskou knihovnu pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs), který obsahuje příklady kódu v tomto rychlém startu.


Z adresáře projektu otevřete soubor *program.cs* a přidejte následující `using` direktivy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Do třídy **programu** aplikace vytvořte proměnné pro klíč a koncový bod prostředku.


> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek obličeje, který jste vytvořili v části **předpoklady** , nasadil úspěšně, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

V metodě **Main** aplikace přidejte volání metod používaných v rámci tohoto rychlého startu. Budete je implementovat později.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny rozhraní .NET pro obličej:

|Název|Popis|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Tato třída reprezentuje vaši autorizaci používat službu obličeje a potřebujete ji pro všechny funkce obličeje. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete provádět s lidskými obličejemi. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Tato třída reprezentuje všechna data, která byla zjištěna z jedné plošky v obrázku. Můžete ji použít k získání podrobných informací o tváři.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Tato třída spravuje **FaceList** konstrukce uložené v cloudu, které ukládají roztříděné sady ploch. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu plošek, které patří jedné osobě.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu objektů pro řazení **osob** . |

## <a name="code-examples"></a>Příklady kódu

Níže uvedené fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro tvář pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvořit skupinu osob](#create-a-person-group)
* [Identifikace obličeje](#identify-a-face)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** s klíčem a použijte ho u svého koncového bodu k vytvoření objektu **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Deklarace pomocných polí

Následující pole jsou nutná pro několik operací tváře, které přidáte později. V kořenovém adresáři třídy **programu** definujte následující řetězec adresy URL. Tato adresa URL odkazuje na složku ukázkových imagí.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

V metodě **Main** definujte řetězce, které odkazují na různé typy modelů rozpoznávání. Později budete moct určit, který model rozpoznávání se má použít pro rozpoznávání tváře. Informace o těchto možnostech najdete v tématu [Určení modelu rozpoznávání](../../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

### <a name="get-detected-face-objects"></a>Získat zjištěné objekty Face

Vytvořte novou metodu pro detekci plošek. `DetectFaceExtract`Metoda zpracuje tři obrázky na dané adrese URL a vytvoří seznam objektů **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** v paměti pro programy. Seznam hodnot **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** určuje, které funkce se mají extrahovat. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> Můžete také detekovat plošky v místní imagi. Podívejte se na metody [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) , jako je **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Zobrazit zjištěná data obličeje

Zbytek `DetectFaceExtract` metody analyzuje a tiskne data atributu pro každou zjištěnou plochu. Každý atribut se musí zadat samostatně v původním volání rozhraní API pro detekci obličeje (v seznamu **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** ). Následující kód zpracuje každý atribut, ale bude pravděpodobně nutné použít pouze jeden nebo několik.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód přebírá jednu zjištěnou plošku (zdroj) a hledá v sadě dalších tváře (cíle) k nalezení shody (hledání na základě obrázku). Pokud najde shodu, vytiskne ID spárovaného obličeje konzole.

### <a name="detect-faces-for-comparison"></a>Detekovat plošky pro porovnání

Nejdřív Definujte metodu detekce druhé plochy. Je potřeba detekovat plošky na obrázcích předtím, než je můžete porovnat, a tato metoda detekce je optimalizována pro operace porovnání. Neextrahuje podrobné atributy obličeje jako v části výše a používá jiný model rozpoznávání.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Najít shody

Následující metoda detekuje plošky v sadě cílových imagí a v jediné zdrojové imagi. Pak je porovná a vyhledá všechny cílové image, které jsou podobné zdrojové imagi.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Tisk shody

Následující kód vytiskne podrobnosti o shodě do konzoly:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identifikace obličeje

Operace identifikace přebírá obrázek osoby (nebo více lidí) a hledá identitu každé plošky v imagi (vyhledávání rozpoznávání obličeje). Porovnává každou zjištěnou plochu s **osobou**, databázi různých objektů **osob** , jejichž funkce obličeje jsou známy. Aby bylo možné operaci identifikace provést, musíte nejprve vytvořit a naučit pracovní samostatnou **osobu** .

### <a name="create-a-person-group"></a>Vytvořit skupinu osob

Následující kód vytvoří **osobu** , která má šest různých objektů **Person** . Přidružuje každou **osobu** k sadě ukázkových imagí a pak vlaky zaznamená, aby každou osobu rozpoznali vlastnostmi obličeje. Objekty **Person** a **Person** se používají při operacích ověřit, identifikovat a seskupit.

Deklarujte řetězcovou proměnnou v kořenu vaší třídy, která bude představovat ID **osoby** , kterou vytvoříte.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

V nové metodě přidejte následující kód. Tato metoda provede operaci identifikace. První blok kódu přidruží názvy osob k jejich ukázkovým obrázkům.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Všimněte si, že tento kód definuje proměnnou `sourceImageFileName` . Tato proměnná odpovídá zdrojové imagi &mdash; obrázku, který obsahuje lidi k identifikaci.

Dále přidejte následující kód pro vytvoření objektu **Person** pro každého uživatele ve slovníku a přidejte Obličejová data z příslušných imagí. Každý objekt **Person** je přidružen ke stejnému objektu **Person** prostřednictvím jeho jedinečného řetězce ID. Nezapomeňte předat proměnné `client` , `url` a `RECOGNITION_MODEL1` do této metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Můžete také vytvořit objekt **Person** z místních imagí. Podívejte se na metody [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) , jako je **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>Naučit se ve službě person

Po extrakci dat ze svých imagí a jejich jejich řazení do různých objektů **osob** je potřeba, **abyste ve své osobě** nastavili identifikaci vizuálních funkcí přidružených k jednotlivým objektům **osob** . Následující kód volá metodu asynchronního **vlaku** a provede dotaz na výsledky a vytiskne stav do konzoly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> Rozhraní API pro rozpoznávání tváře běží na sadě předem sestavených modelů, které jsou statické podle povahy (výkon modelu nebude znovu fungovat ani se nevylepšit při spuštění služby). Výsledky, které model vytvoří, se můžou změnit, pokud Microsoft aktualizuje back-end modelu bez migrace na zcela novou verzi modelu. Aby bylo možné využít novější verzi modelu, můžete přeškolit svoji **osobu** a zadat novější model jako parametr se stejnými registračními kopiemi.

Tato skupina **uživatelů** a její přidružené objekty **osoby** jsou teď připravené k použití v operacích ověřování, identifikace nebo seskupení.

### <a name="identify-faces"></a>Identifikace tváří

Následující kód převezme zdrojový obraz a vytvoří seznam všech ploch zjištěných v obrázku. Jedná se o plošky, které se identifikují vůči **osobě person**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Další fragment kódu volá operaci **IdentifyAsync** a vytiskne výsledky do konzoly. V tomto případě se služba pokusí porovnat jednotlivé obličeje od zdrojové image k **osobě** v dané **osobě person**. Tím se ukončí vaše metoda identifikace.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Spuštění aplikace

#### <a name="visual-studio-ide"></a>[Integrované vývojové prostředí sady Visual Studio](#tab/visual-studio)

Spusťte aplikaci kliknutím na tlačítko **ladění** v horní části okna IDE.

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste v tomto rychlém startu vytvořili pole **Person** a chcete ho odstranit, spusťte v programu následující kód:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Metodu odstranění definujte pomocí následujícího kódu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak používat funkci klientské knihovny pro tvář pro .NET k provádění základních úloh rozpoznávání obličeje. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na rozhraní API pro rozpoznávání tváře (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [Co je služba Rozpoznávání tváře?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
