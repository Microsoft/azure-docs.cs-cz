---
title: Plocha pro rychlý Start klientské knihovny .NET
description: Pomocí klientské knihovny pro rozpoznávání tváře pro .NET zjistíte obličeje, najděte podobné (vyhledávání na ploše podle obrázku), identifikujte obličeje (hledání rozpoznávání obličeje) a migrujte data ze strany obličeje.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: 80255790129468857e1115f3034516f04bc86d26
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91322943"
---
Začněte s rozpoznáváním obličeje pomocí klientské knihovny pro tvář pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba Faceer poskytuje přístup k pokročilým algoritmům pro zjišťování a rozpoznávání lidských plošek na obrázcích.

Pomocí klientské knihovny obličeje pro .NET:

* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořídit snímek migrace dat](#take-a-snapshot-for-data-migration)

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Ukázky](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Předpoklady

* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Po získání klíče a koncového bodu [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč a adresu URL koncového bodu s názvem `FACE_SUBSCRIPTION_KEY` a v `FACE_ENDPOINT` uvedeném pořadí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). 

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `face-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující `using` direktivy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

V `Main` metodě aplikace vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu pro rozhraní .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny rozhraní .NET pro obličej:

|Název|Popis|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Tato třída reprezentuje vaši autorizaci používat službu obličeje a potřebujete ji pro všechny funkce obličeje. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete provádět s lidskými obličejemi. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Tato třída reprezentuje všechna data, která byla zjištěna z jedné plošky v obrázku. Můžete ji použít k získání podrobných informací o tváři.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Tato třída spravuje **FaceList** konstrukce uložené v cloudu, které ukládají roztříděné sady ploch. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu plošek, které patří jedné osobě.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu objektů pro řazení **osob** . |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Tato třída spravuje funkci snímků. Můžete ji použít k dočasnému uložení všech vašich cloudových dat na bázi cloudu a k migraci těchto dat do nového předplatného Azure. |

## <a name="code-examples"></a>Příklady kódu

Níže uvedené fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro tvář pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořídit snímek migrace dat](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro svůj klíč a koncový bod s názvem `FACE_SUBSCRIPTION_KEY` a `FACE_ENDPOINT` .

V nové metodě vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** s klíčem a použijte ho u svého koncového bodu k vytvoření objektu **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Pravděpodobně budete chtít zavolat tuto metodu v `Main` metodě.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Deklarace pomocných polí

Následující pole jsou nutná pro několik operací tváře, které přidáte později. V kořenovém adresáři třídy definujte následující řetězec adresy URL. Tato adresa URL odkazuje na složku ukázkových imagí.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Definujte řetězce, které odkazují na různé typy modelů rozpoznávání. Později budete moct určit, který model rozpoznávání se má použít pro rozpoznávání tváře. Informace o těchto možnostech najdete v tématu [Určení modelu rozpoznávání](../../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Do metody **Main** přidejte následující volání metody. Pak definujete metodu Next. Konečná operace zjišťování bude mít objekt **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** , adresu URL obrázku a model rozpoznávání.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Získat zjištěné objekty Face

V dalším bloku kódu `DetectFaceExtract` Metoda detekuje obličeje ve třech obrázcích na dané adrese URL a vytvoří seznam objektů **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** v paměti pro programy. Seznam hodnot **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** určuje, které funkce se mají extrahovat. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Zobrazit zjištěná data obličeje

Zbytek `DetectFaceExtract` metody analyzuje a tiskne data atributu pro každou zjištěnou plochu. Každý atribut se musí zadat samostatně v původním volání rozhraní API pro detekci obličeje (v seznamu **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** ). Následující kód zpracuje každý atribut, ale bude pravděpodobně nutné použít pouze jeden nebo několik.

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

### <a name="create-and-train-a-person-group"></a>Vytvoření a výuka skupiny osob

Následující kód vytvoří **osobu** , která má šest různých objektů **Person** . Přidružuje každou **osobu** k sadě ukázkových imagí a pak vlaky zaznamená, aby každou osobu rozpoznali vlastnostmi obličeje. Objekty **Person** a **Person** se používají při operacích ověřit, identifikovat a seskupit.

#### <a name="create-persongroup"></a>Vytvořit osobu

Deklarujte řetězcovou proměnnou v kořenu vaší třídy, která bude představovat ID **osoby** , kterou vytvoříte.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

V nové metodě přidejte následující kód. Tato metoda provede operaci identifikace. První blok kódu přidruží názvy osob k jejich ukázkovým obrázkům.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Dále přidejte následující kód pro vytvoření objektu **Person** pro každého uživatele ve slovníku a přidejte Obličejová data z příslušných imagí. Každý objekt **Person** je přidružen ke stejnému objektu **Person** prostřednictvím jeho jedinečného řetězce ID. Nezapomeňte předat proměnné `client` , `url` a `RECOGNITION_MODEL1` do této metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>Pracovník pro vzdělávání

Po extrakci dat ze svých imagí a jejich jejich řazení do různých objektů **osob** je potřeba, **abyste ve své osobě** nastavili identifikaci vizuálních funkcí přidružených k jednotlivým objektům **osob** . Následující kód volá metodu asynchronního **vlaku** a provede dotaz na výsledky a vytiskne stav do konzoly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Tato skupina **uživatelů** a její přidružené objekty **osoby** jsou teď připravené k použití v operacích ověřování, identifikace nebo seskupení.

### <a name="get-a-test-image"></a>Získat image testu

Všimněte si, že kód pro [Vytvoření a výuku skupiny osob](#create-and-train-a-person-group) definuje proměnnou `sourceImageFileName` . Tato proměnná odpovídá zdrojové imagi &mdash; obrázku, který obsahuje lidi k identifikaci.

### <a name="identify-faces"></a>Identifikace tváří

Následující kód převezme zdrojový obraz a vytvoří seznam všech ploch zjištěných v obrázku. Jedná se o plošky, které se identifikují vůči **osobě person**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Další fragment kódu volá operaci **IdentifyAsync** a vytiskne výsledky do konzoly. V tomto případě se služba pokusí porovnat jednotlivé obličeje od zdrojové image k **osobě** v dané **osobě person**. Tím se ukončí vaše metoda identifikace.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pořídit snímek migrace dat

Funkce snímků umožňuje přesunout uložená data o obličejích, jako je například školená **osoba**, do jiného předplatného služby Azure Cognitive Services Face. Tuto funkci můžete chtít použít například v případě, že jste vytvořili objekt **Person** pomocí bezplatného předplatného a chcete ho migrovat do placeného předplatného. Přehled funkce snímků najdete v tématu [migrace dat](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) na ploše.

V tomto příkladu migrujete skupinu **Person** , kterou jste vytvořili v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group). Tuto část můžete buď dokončit jako první, nebo vytvořit vlastní tváře datové konstrukce, které chcete migrovat.

### <a name="set-up-target-subscription"></a>Nastavit cílové předplatné

Nejdřív musíte mít k dispozici druhé předplatné Azure s předním prostředkem; to můžete provést podle kroků v části [Nastavení](#setting-up) . 

Pak definujte v `Main` metodě programu následující proměnné. Budete muset vytvořit nové proměnné prostředí pro ID předplatného vašeho účtu Azure a také klíč, koncový bod a ID předplatného vašeho nového (cílového) účtu. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

V tomto příkladu deklarujte proměnnou pro ID cílové **osoby** &mdash; objektu, který patří do nového předplatného, do kterého budete kopírovat data.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Ověřit cílového klienta

Dále přidejte kód pro ověření předplatného sekundární plochy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Použití snímku

Zbývající operace snímku musí probíhat v rámci asynchronní metody. 

1. Prvním krokem je **pořídit** snímek, který ukládá data z původního předplatného do dočasného cloudového umístění. Tato metoda vrací ID, které slouží k dotazování na stav operace.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. V dalším kroku vydáte dotaz na ID, dokud se operace nedokončí.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Pak použijte operaci **Apply** pro zápis dat do cílového předplatného. Tato metoda také vrátí hodnotu ID.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Znovu spusťte dotaz na nové ID, dokud se operace nedokončí.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Nakonec dokončete blok try/catch a dokončete metodu.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

V tomto okamžiku by měl mít nový objekt **Person** stejná data jako původní a měla by být přístupná z vašeho nového (cílového) předplatného Azure Face.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pro rozpoznávání tváře z adresáře aplikace pomocí `dotnet run` příkazu.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste v tomto rychlém startu vytvořili pole **Person** a chcete ho odstranit, spusťte v programu následující kód:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Metodu odstranění definujte pomocí následujícího kódu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Kromě toho, pokud jste migrovali data pomocí funkce snímku v tomto rychlém startu, budete také muset odstranit **pracovníka** uloženou do cílového předplatného.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat klientské knihovny pro tvář pro technologii .NET k provádění základních úloh rozpoznávání obličeje. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na Face API (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Co je služba Rozpoznávání tváře?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
