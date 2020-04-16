---
title: 'Úvodní příručka: Face klientská knihovna pro rozhraní .NET'
description: Začněte s klientskou knihovnou Face pro rozhraní .NET s tímto rychlým startem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5e0073bd14744338ff28c9c45193f126a1bba717
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403030"
---
# <a name="quickstart-face-client-library-for-net"></a>Úvodní příručka: Face klientská knihovna pro rozhraní .NET

Začínáme s klientskou knihovnou Face pro rozhraní .NET. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Služba Face vám poskytuje přístup k pokročilým algoritmům pro detekci a rozpoznávání lidských tváří v obrazech.

Pomocí knihovny klienta Face pro rozhraní .NET:

* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Vyhledání podobných tváří](#find-similar-faces)
* [Vytvoření a trénování skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořízení snímku pro migraci dat](#take-a-snapshot-for-data-migration)

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [knihovny](https://docs.microsoft.com/samples/browse/?products=azure&term=face) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-face-azure-resource"></a>Vytvoření prostředku Face Azure

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro Face pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také získat [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT`adresu URL klíče a koncového bodu s názvem a , resp.

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace C#

Vytvořte novou aplikaci .NET Core ve vašem upřednostňovaném editoru nebo ide. 

V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí `face-quickstart`příkazu novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění nebo chyby. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

V adresáři projektu otevřete *soubor Program.cs* v upřednostňovaném editoru nebo rozhraní IDE. Přidejte `using` následující direktivy:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

V `Main` metodě aplikace vytvořte proměnné pro koncový bod a klíč azure vašeho prostředku.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte knihovnu klienta Face pro rozhraní .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Face .NET SDK:

|Název|Popis|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Tato třída představuje oprávnění k používání služby Face a potřebujete ji pro všechny funkce Face. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete dělat s lidskými plochami. |
|[Zjištěná plocha](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Tato třída představuje všechna data, která byla zjištěna z jedné plochy v obraze. Můžete ji použít k načtení podrobných informací o ploše.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Tato třída spravuje cloudové konstrukce **FaceList,** které ukládají různé sady ploch. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Tato třída spravuje cloudové konstrukce **Person,** které ukládají sadu ploch, které patří jedné osobě.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Tato třída spravuje cloudové konstrukce **PersonGroup,** které ukládají sadu tříděných objektů **Person.** |
|[ShapshotOperace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Tato třída spravuje funkce snímek. Můžete ji použít k dočasnému uložení všech dat na bázi cloudu a migraci těchto dat do nového předplatného Azure. |

## <a name="code-examples"></a>Příklady kódu

Níže uvedené fragmenty kódu ukazují, jak provést následující úkoly s knihovnou klienta Face pro rozhraní .NET:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Vyhledání podobných tváří](#find-similar-faces)
* [Vytvoření a trénování skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořízení snímku pro migraci dat](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento rychlý start předpokládá, že jste [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT`klíč face a koncový bod s názvem a .

V nové metodě vytvořte instanci klienta pomocí koncového bodu a klíče. Vytvořte objekt **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** s klíčem a použijte ho s koncovým bodem k vytvoření objektu **[FaceClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Pravděpodobně budete chtít volat tuto metodu v metodě. `Main`

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

V kořenovém adresáři třídy definujte následující řetězec adresy URL. Tato adresa URL odkazuje na sadu ukázkových obrázků.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Volitelně můžete zvolit, který model AI se má použít k extrahování dat z detekovaných ploch. Informace o těchto možnostech naleznete [v tématu Určení modelu rozpoznávání.](../Face-API-How-to-Topics/specify-recognition-model.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

Konečná operace Detect bude mít objekt **[FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** adresu URL obrázku a model rozpoznávání.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Nechat se detekovačit objekty obličeje

V dalším bloku kódu `DetectFaceExtract` metoda detekuje tváře ve třech obrázcích na dané adrese URL a vytvoří seznam objektů **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** v paměti programu. Seznam hodnot **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** určuje, které prvky mají být extrahovány. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Zobrazit zjištěná data plochy

Zbytek `DetectFaceExtract` metody analyzuje a vytiskne data atributů pro každou zjištěnou plochu. Každý atribut musí být zadán samostatně v původním volání rozhraní API pro detekci tváří (v seznamu **[FaceAttributeType).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** Následující kód zpracovává každý atribut, ale pravděpodobně budete potřebovat použít pouze jeden nebo několik.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód přebírá jednu zjištěnou tvář (zdroj) a prohledává sadu dalších tváří (cíl) najít shody. Když najde shodu, vytiskne ID odpovídající plochy do konzoly.

### <a name="detect-faces-for-comparison"></a>Rozpoznat plochy pro porovnání

Nejprve definujte druhou metodu detekce obličeje. Před porovnáním je třeba rozpoznat plochy v obrázcích a tato metoda detekce je optimalizována pro operace porovnání. Neextrahuje podrobné atributy plochy jako ve výše uvedené části a používá jiný model rozpoznávání.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Najít shody

Následující metoda detekuje plochy v sadě cílových obrazů a v jednom zdrojovém obrazu. Potom je porovná a najde všechny cílové obrázky, které jsou podobné zdrojovému obrazu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Tisk shod

Následující kód vytiskne podrobnosti shody do konzoly:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Vytvoření a trénování skupiny osob

Následující kód vytvoří **PersonGroup** se šesti různými **person** objekty. Spojuje každou **osobu** se sadou příkladů a pak trénuje, aby rozpoznala každou osobu podle jejich obličejových charakteristik. **Objekty Person** a **PersonGroup** se používají v operacích Ověřit, Identifikovat a Seskupit.

Pokud jste tak ještě neučinili, definujte následující řetězec adresy URL v kořenovém adresáři vaší třídy. To ukazuje na sadu ukázkových obrazů.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Kód dále v této části určí model rozpoznávání extrahovat data z ploch a následující úryvek vytvoří odkazy na dostupné modely. Viz [Určení modelu rozpoznávání](../Face-API-How-to-Topics/specify-recognition-model.md) pro informace o modelech rozpoznávání.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Vytvořit skupinu osob

Deklarujte proměnnou řetězce v kořenovém adresáři vaší třídy, která bude představovat ID **Skupiny osob,** kterou vytvoříte.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

V nové metodě přidejte následující kód. Tento kód přidruží jména osob s jejich ukázkové obrázky.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Dále přidejte následující kód pro vytvoření objektu **Person** pro každou osobu ve Slovníku a přidejte data plochy z příslušných obrázků. Každý objekt **Person** je přidružen ke stejné **skupině PersonGroup** prostřednictvím svého jedinečného řetězce ID. Nezapomeňte předat proměnné `client`, `url`, `RECOGNITION_MODEL1` a do této metody.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Vlak PersonGroup

Jakmile extrahujete data o obličeji z obrázků a seřadíte je do různých objektů **Person,** musíte skupinu **PersonGroup** trénovat k identifikaci vizuálních prvků přidružených ke každému z jejích objektů **Person.** Následující kód volá metodu asynchronního **trainu** a vyvolá výsledky a tiskne stav do konzoly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Tato skupina **Osoba** a její přidružené objekty **Person** jsou nyní připraveny k použití v operacích Ověření, Identifikace nebo Skupina.

## <a name="identify-a-face"></a>Identifikace obličeje

Operace Identifikovat pořídí obrázek osoby (nebo více osob) a vyhledá identitu každé tváře v obrázku. Porovnává každou detekoci tvář **persongroup**, databáze různých osob **objekty,** jejichž rysy obličeje jsou známy.

> [!IMPORTANT]
> Chcete-li spustit tento příklad, musíte nejprve spustit kód v [vytvořit a trénování skupiny osob](#create-and-train-a-person-group). Proměnné použité v tomto&mdash;`client` `url`oddíle `RECOGNITION_MODEL1` &mdash;, a musí být také k dispozici zde.

### <a name="get-a-test-image"></a>Získání testovacího obrázku

Všimněte si, že kód pro [Vytvořit a trénování skupiny osob](#create-and-train-a-person-group) definuje proměnnou `sourceImageFileName`. Tato proměnná odpovídá zdrojovému obrázku,&mdash;který obsahuje osoby k identifikaci.

### <a name="identify-faces"></a>Identifikace tváří

Následující kód pořídí zdrojový obraz a vytvoří seznam všech tváří zjištěných v obraze. Jedná se o tváře, které budou identifikovány proti **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

Další fragment kódu volá operaci Identifikace a vytiskne výsledky do konzoly. Zde se služba pokusí porovnat každou tvář ze zdrojového obrázku **s osobou** v dané **skupině PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pořízení snímku pro migraci dat

Funkce Snímky umožňuje přesunout uložená data plochy, jako je například trénovaná **PersonGroup**, do jiného předplatného služby Azure Cognitive Services Face. Tuto funkci můžete použít například v případě, že jste vytvořili objekt **PersonGroup** pomocí bezplatného zkušebního předplatného a chcete jej migrovat do placeného předplatného. Přehled funkce Snímky najdete v tématu [Migrace dat obličeje.](../Face-API-How-to-Topics/how-to-migrate-face-data.md)

V tomto příkladu budete migrovat **PersonGroup,** kterou jste vytvořili v [create and train a person group](#create-and-train-a-person-group). Můžete buď nejprve dokončit tento oddíl, nebo vytvořit vlastní konstrukce dat plochy, které chcete migrovat.

### <a name="set-up-target-subscription"></a>Nastavení cílového předplatného

Nejprve musíte mít druhé předplatné Azure s prostředkem Face; můžete to provést podle kroků v části [Nastavení.](#setting-up) 

Potom definujte následující proměnné `Main` v metodě programu. Budete muset vytvořit nové proměnné prostředí pro ID předplatného vašeho účtu Azure, stejně jako klíč, koncový bod a ID předplatného vašeho nového (cílového) účtu. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

V tomto příkladu deklarujte proměnnou pro ID cílové **skupiny PersonGroup**&mdash;objekt, který patří do nového předplatného, do kterého zkopírujete data.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Ověření cílového klienta

Dále přidejte kód k ověření sekundárního předplatného Face.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Použití snímku

Zbývající operace snímek musí probíhat v rámci asynchronní metody. 

1. Prvním krokem je **pořízení** snímku, který uloží data tváře původního předplatného do dočasného umístění v cloudu. Tato metoda vrátí ID, které používáte k dotazování stav operace.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Dále dotaz ID, dokud operace byla dokončena.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Pak použijte **operaci použít** k zápisu dat obličeje do cílového předplatného. Tato metoda také vrátí hodnotu ID.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Znovu dotaz nové ID, dokud operace byla dokončena.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Nakonec dokončete blok try/catch a dokončete metodu.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

V tomto okamžiku nový **Objekt PersonGroup** by měl mít stejná data jako původní a měl by být přístupný z vašeho nového (cílového) předplatného Azure Face.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z `dotnet run` adresáře aplikace pomocí příkazu.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste v tomto rychlém startu **vytvořili skupinu persongroup** a chcete ji odstranit, spusťte v programu následující kód:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Definujte metodu odstranění pomocí následujícího kódu:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Navíc pokud jste migrovali data pomocí funkce Snímek v tomto rychlém startu, budete také muset odstranit **PersonGroup** uložené do cílového předplatného.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak používat knihovnu tváří pro rozhraní .NET k úkolům základů. Dále prozkoumejte referenční dokumentaci, abyste se dozvěděli více o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na rozhraní API pro obličej (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Co je služba Rozpoznávání tváře?](../overview.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).
