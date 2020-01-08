---
title: 'Rychlý Start: Klientská knihovna pro tvář pro .NET'
description: V tomto rychlém startu můžete začít s klientskou knihovnou pro .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 269f2c9a2cf8578438971e8ad75fec1379a21c19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448186"
---
# <a name="quickstart-face-client-library-for-net"></a>Rychlý Start: Klientská knihovna pro tvář pro .NET

Začínáme s klientskou knihovnou obličeje pro .NET Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba Face API poskytuje přístup k pokročilým algoritmům pro zjišťování a rozpoznávání lidských plošek na obrázcích.

Pomocí klientské knihovny obličeje pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Detekce plošek v obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořídit snímek migrace dat](#take-a-snapshot-for-data-migration)

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Ukázka](https://docs.microsoft.com/samples/browse/?products=azure&term=face) | [ukázka zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [balíčků (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-face-azure-resource"></a>Vytvoření prostředku Azure FACET

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro obličej pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku [Vytvořte proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíčovou adresu URL klíče a koncového bodu s názvem `FACE_SUBSCRIPTION_KEY` a `FACE_ENDPOINT`, v uvedeném pořadí.

### <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). 

V okně konzoly (například cmd, PowerShell nebo bash) použijte příkaz `dotnet new` a vytvořte novou konzolovou aplikaci s názvem `face-quickstart`. Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```dotnetcli
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

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující direktivy `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

V metodě `Main` aplikace vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu pro rozhraní .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé z hlavních funkcí sady Face .NET SDK:

|Name (Název)|Popis|
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
* [Detekce plošek v obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořídit snímek migrace dat](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro svůj klíč a koncový bod s názvem `FACE_SUBSCRIPTION_KEY` a `FACE_ENDPOINT`.

V nové metodě vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Pravděpodobně budete chtít zavolat tuto metodu v metodě `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

V kořenovém adresáři třídy definujte následující řetězec adresy URL. Tato adresa URL odkazuje na sadu ukázkových imagí.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Volitelně můžete zvolit, který model AI se má použít k extrakci dat ze zjištěných tváře. Informace o těchto možnostech najdete v tématu [Určení modelu rozpoznávání](../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

Konečná operace zjišťování bude mít objekt [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) , adresu URL obrázku a model rozpoznávání.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Získat zjištěné objekty Face

V dalším bloku kódu `DetectFaceExtract` metoda detekuje plošky ve třech obrázcích na dané adrese URL a vytvoří seznam objektů [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) v paměti pro programy. Seznam hodnot [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) určuje, které funkce se mají extrahovat. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Zobrazit zjištěná data obličeje

Zbytek metody `DetectFaceExtract` analyzuje a tiskne data atributů pro každou zjištěnou plochu. Každý atribut se musí zadat samostatně v původním volání rozhraní API pro detekci obličeje (v seznamu [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) ). Následující kód zpracuje každý atribut, ale bude pravděpodobně nutné použít pouze jeden nebo několik.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód vezme jednu zjištěnou plošku (zdroj) a vyhledá v sadě dalších plošek (Target), aby našli shody. Pokud najde shodu, vytiskne ID spárovaného obličeje konzole.

### <a name="detect-faces-for-comparison"></a>Detekovat plošky pro porovnání

Nejdřív Definujte metodu detekce druhé plochy. Je potřeba detekovat plošky na obrázcích předtím, než je můžete porovnat, a tato metoda detekce je optimalizována pro operace porovnání. Neextrahuje podrobné atributy obličeje jako v části výše a používá jiný model rozpoznávání.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Najít shody

Následující metoda detekuje plošky v sadě cílových imagí a v jediné zdrojové imagi. Pak je porovná a vyhledá všechny cílové image, které jsou podobné zdrojové imagi.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Tisk shody

Následující kód vytiskne podrobnosti o shodě do konzoly:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Vytvoření a výuka skupiny osob

Následující kód vytvoří **osobu** , která má šest různých objektů **Person** . Přidružuje každou **osobu** k sadě ukázkových imagí a pak vlaky zaznamená, aby každou osobu rozpoznali vlastnostmi obličeje. Objekty **Person** a **Person** se používají při operacích ověřit, identifikovat a seskupit.

Pokud jste to ještě neudělali, v kořenu vaší třídy definujte následující řetězec adresy URL. To ukazuje na sadu ukázkových imagí.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Kód dále v této části určí model rozpoznávání pro extrakci dat z plošek a následující fragment kódu vytvoří odkazy na dostupné modely. Informace o modelech rozpoznávání najdete v tématu [Určení modelu rozpoznávání](../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Vytvořit osobu

Deklarujte řetězcovou proměnnou v kořenu vaší třídy, která bude představovat ID **osoby** , kterou vytvoříte.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

V nové metodě přidejte následující kód. Tento kód přidruží názvy osob k jejich ukázkovým obrázkům.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Dále přidejte následující kód pro vytvoření objektu **Person** pro každého uživatele ve slovníku a přidejte Obličejová data z příslušných imagí. Každý objekt **Person** je přidružen ke stejnému objektu **Person** prostřednictvím jeho jedinečného řetězce ID. Nezapomeňte předat proměnné `client`, `url`a `RECOGNITION_MODEL1` do této metody.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Pracovník pro vzdělávání

Po extrakci dat ze svých imagí a jejich jejich řazení do různých objektů **osob** je potřeba, **abyste ve své osobě** nastavili identifikaci vizuálních funkcí přidružených k jednotlivým objektům **osob** . Následující kód volá metodu asynchronního **vlaku** a provede dotaz na výsledky a vytiskne stav do konzoly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Tato skupina **uživatelů** a její přidružené objekty **osoby** jsou teď připravené k použití v operacích ověřování, identifikace nebo seskupení.

## <a name="identify-a-face"></a>Identifikace obličeje

Operace identifikace přebírá obrázek osoby (nebo více lidí) a hledá identitu každé plošky v obrázku. Porovnává každou zjištěnou plochu s **osobou**, databázi různých objektů **osob** , jejichž funkce obličeje jsou známy.

> [!IMPORTANT]
> Aby bylo možné spustit tento příklad, musíte nejprve spustit kód v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group). Proměnné používané v této části&mdash;`client`, `url`a `RECOGNITION_MODEL1`&mdash;musí být také k dispozici zde.

### <a name="get-a-test-image"></a>Získat image testu

Všimněte si, že kód pro [Vytvoření a výuku skupiny osob](#create-and-train-a-person-group) definuje proměnnou `sourceImageFileName`. Tato proměnná odpovídá zdrojové imagi&mdash;image, která obsahuje lidi k identifikaci.

### <a name="identify-faces"></a>Identifikace tváří

Následující kód převezme zdrojový obraz a vytvoří seznam všech ploch zjištěných v obrázku. Jedná se o plošky, které se identifikují vůči **osobě person**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

Další fragment kódu volá operaci identifikace a vytiskne výsledky do konzoly. V tomto případě se služba pokusí porovnat jednotlivé obličeje od zdrojové image k **osobě** v dané **osobě person**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pořídit snímek migrace dat

Funkce snímků umožňuje přesunout uložená data o obličejích, jako je například školená **osoba**, do jiného předplatného služby Azure Cognitive Services Face. Tuto funkci můžete chtít použít například v případě, že jste vytvořili objekt **Person** pomocí bezplatné zkušební verze předplatného a chcete ho migrovat do placeného předplatného. Přehled funkce snímků najdete v tématu [migrace dat](../Face-API-How-to-Topics/how-to-migrate-face-data.md) na ploše.

V tomto příkladu migrujete skupinu **Person** , kterou jste vytvořili v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group). Tuto část můžete buď dokončit jako první, nebo vytvořit vlastní tváře datové konstrukce, které chcete migrovat.

### <a name="set-up-target-subscription"></a>Nastavit cílové předplatné

Nejdřív musíte mít k dispozici druhé předplatné Azure s předním prostředkem; to můžete provést podle kroků v části [Nastavení](#setting-up) . 

Pak definujte následující proměnné v metodě `Main` programu. Budete muset vytvořit nové proměnné prostředí pro ID předplatného vašeho účtu Azure a také klíč, koncový bod a ID předplatného vašeho nového (cílového) účtu. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

V tomto příkladu deklarujete proměnnou pro ID cílové **osoby**&mdash;objektu, který patří do nového předplatného, do kterého budete kopírovat data.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Ověřit cílového klienta

Dále přidejte kód pro ověření předplatného sekundární plochy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Použití snímku

Zbývající operace snímku musí probíhat v rámci asynchronní metody. 

1. Prvním krokem je **pořídit** snímek, který ukládá data z původního předplatného do dočasného cloudového umístění. Tato metoda vrací ID, které slouží k dotazování na stav operace.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. V dalším kroku vydáte dotaz na ID, dokud se operace nedokončí.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Pak použijte operaci **Apply** pro zápis dat do cílového předplatného. Tato metoda také vrátí hodnotu ID.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Znovu spusťte dotaz na nové ID, dokud se operace nedokončí.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Nakonec dokončete blok try/catch a dokončete metodu.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

V tomto okamžiku by měl mít nový objekt **Person** stejná data jako původní a měla by být přístupná z vašeho nového (cílového) předplatného Azure Face.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí příkazu `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste v tomto rychlém startu vytvořili pole **Person** a chcete ho odstranit, spusťte v programu následující kód:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Metodu odstranění definujte pomocí následujícího kódu:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Kromě toho, pokud jste migrovali data pomocí funkce snímku v tomto rychlém startu, budete také muset odstranit **pracovníka** uloženou do cílového předplatného.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat knihovnu obličeje pro .NET k provádění základních úloh. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na Face API (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Co je Face API?](../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs)
