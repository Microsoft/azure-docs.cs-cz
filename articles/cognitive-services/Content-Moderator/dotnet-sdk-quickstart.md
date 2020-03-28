---
title: 'Úvodní příručka: Klientská knihovna Content Moderator pro rozhraní .NET'
titleSuffix: Azure Cognitive Services
description: Začněte s klientskou knihovnou Content Moderator pro rozhraní .NET s tímto rychlým startem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: dccd2ebb6ac2c11e19e986d39eabda5f0ab6a8fc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774293"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Úvodní příručka: Klientská knihovna Content Moderator pro rozhraní .NET

Začínáme s klientskou knihovnou Content Moderator pro rozhraní .NET. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Content Moderator je kognitivní služba, která kontroluje text, obrázek a video obsah pro materiál, který je potenciálně urážlivý, riskantní nebo jinak nežádoucí. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může zpracovávat obsah označený příznakem, aby vyhověla předpisům nebo udržovala zamýšlené prostředí pro uživatele.

Pomocí klientské knihovny Content Moderator pro rozhraní .NET můžete:

* [Střední text](#moderate-text)
* [Střední obrázky](#moderate-images)
* [Vytvoření recenze](#create-a-review)

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [knihovny](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-content-moderator-azure-resource"></a>Vytvoření prostředku Azure moderátora obsahu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro Content Moderator pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/)

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `CONTENT_MODERATOR_SUBSCRIPTION_KEY` `CONTENT_MODERATOR_ENDPOINT`adresu URL klíče a koncového bodu s názvem a , resp.

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace C#

Vytvořte novou aplikaci .NET Core ve preferovaném textovém editoru nebo ide. 

V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí `content-moderator-quickstart`příkazu novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```console
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění nebo chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

V adresáři projektu otevřete *soubor Program.cs* v upřednostňovaném editoru nebo rozhraní IDE. Přidejte následující příkazy `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

Ve třídě **Program** vytvořte proměnné pro umístění koncového bodu prostředku a klíč jako proměnné prostředí.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Pokud jste vytvořili proměnné prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které jej běží pro přístup k proměnným.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu Content Moderator pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce sady Content Moderator .NET SDK.

|Name (Název)|Popis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Tato třída je potřebná pro všechny funkce Content Moderator. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd.|
|[Moderování obrázku](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní informace nebo lidské tváře.|
|[Moderování textu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní informace.|
|[Recenze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Tato třída poskytuje funkce rozhraní API pro kontrolu, včetně metod pro vytváření úloh, vlastních pracovních postupů a lidských recenzí.|

## <a name="code-examples"></a>Příklady kódu


Tyto fragmenty kódu ukazují, jak provést následující úkoly v klientské knihovně Content Moderator pro rozhraní .NET:

* [Ověření klienta](#authenticate-the-client)
* [Střední text](#moderate-text)
* [Střední obrázky](#moderate-images)
* [Vytvoření recenze](#create-a-review)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci klientských objektů pomocí koncového bodu a klíče. Nepotřebujete jiného klienta pro každý scénář, ale může pomoci udržet váš kód uspořádaný.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Střední text

Následující kód používá klienta Content Moderator k analýze textu a tisku výsledků do konzoly. V kořenovém adresáři třídy **Program** definujte vstupní a výstupní soubory:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Potom v kořenovém adresáři projektu a přidejte soubor *TextFile.txt.* Přidejte do tohoto souboru vlastní text nebo použijte následující ukázkový text:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Přidejte do metody `Main` volání následující metody:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Potom definujte metodu moderování textu někde ve třídě **Programu:**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Střední obrázky

Následující kód používá klienta Content Moderator spolu s objektem [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) k analýze vzdálených obrázků pro obsah pro dospělé a pikantní.

> [!NOTE]
> Můžete také analyzovat obsah místního obrázku. Naleznete [v referenční dokumentaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) pro metody a operace, které pracují s místními obrázky.

### <a name="get-sample-images"></a>Získat ukázkové obrázky

Definujte vstupní a výstupní soubory:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Potom vytvořte vstupní soubor *ImageFiles.txt*v kořenovém adresáři projektu. V tomto souboru přidáte adresy URL obrázků&mdash;a analyzujete jednu adresu URL na každém řádku. Můžete použít následující ukázkové obrázky:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Předejte vstupní a výstupní soubory do `Main` následující ho volání metody v metodě. Tuto metodu definujete později.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definovat pomocnou třídu

V rámci třídy **Program** přidejte následující definici třídy. Tato vnitřní třída bude zpracovávat výsledky moderování obrazu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definování metody moderování obrazu

Následující metoda iterace prostřednictvím adres URL obrazu v textovém souboru, vytvoří **instance EvaluationData** a analyzuje obraz pro dospělé/pikantní obsah, text a lidské tváře. Potom přidá konečnou instanci **EvaluationData** do seznamu a zapíše úplný seznam vrácených dat do konzoly.

#### <a name="iterate-through-image-urls"></a>Iterate prostřednictvím adres URL obrázků

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analýza obsahu

Další informace o atributech obrázku, pro které se moderátor obsahu promítá, najdete v průvodci [koncepty moderování obrázků.](./image-moderation-api.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Zapsat výsledky moderování do souboru

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Vytvoření recenze

Sadu Content Moderator .NET SDK můžete použít k podávání obsahu do [nástroje Revize,](https://contentmoderator.cognitive.microsoft.com) aby jej mohli moderátoři lidské kontroly zkontrolovat. Další informace o nástroji Revize naleznete v [koncepčnípříručce nástroje revize](./review-tool-user-guide/human-in-the-loop.md).

Metoda v této části používá [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) třídy k vytvoření recenze, načíst jeho ID a zkontrolovat jeho podrobnosti po obdržení lidské vstup prostřednictvím nástroje revize webového portálu. Zaznamenává všechny tyto informace ve výstupním textovém souboru. Volání metody z `Main` vaší metody:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Získat ukázkové obrázky

Deklarujte následující pole v kořenovém adresáři třídy **Program.** Tato proměnná odkazuje na ukázkový obrázek, který má být k vytvoření recenze.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Získat pověření k recenzi

Přihlaste se k [nástroji Revize](https://contentmoderator.cognitive.microsoft.com) a načtěte název svého týmu. Pak ji přiřaďte k příslušné proměnné ve třídě **Program.** Volitelně můžete nastavit koncový bod zpětného volání pro příjem aktualizací o aktivitě recenze.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definovat pomocnou třídu

V rámci třídy **Program** přidejte následující definici třídy. Tato třída bude použita k reprezentaci jedné instance kontroly, která je odeslána do nástroje revize.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definovat pomocnou metodu

Do třídy **Program** přidejte následující metodu. Tato metoda bude zapisovat výsledky dotazů na recenze do výstupního textového souboru.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definování metody vytvoření revizí

Nyní jste připraveni definovat metodu, která bude zpracovávat vytváření revizí a dotazování. Přidejte novou metodu **CreateReviews**a definujte následující místní proměnné.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Zveřejňování recenzí do nástroje Revize

Potom přidejte následující kód k iterazování prostřednictvím daných ukázkových obrazů, přidejte metadata a odešlete je do nástroje revize v jedné dávce. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

Objekt vrácený z volání rozhraní API bude obsahovat jedinečné hodnoty ID pro každý obrázek nahraný. Následující kód analyzuje tato ID a potom je použije k dotazování moderátora obsahu na stav jednotlivých obrázků v dávce.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Získat podrobnosti o recenzích

Následující kód způsobí, že program čekat na vstup uživatele. Když k tomuto kroku dojde za běhu, můžete přejít na [nástroj revize](https://contentmoderator.cognitive.microsoft.com) sami, ověřit, zda byl ukázkový obrázek nahrán, a pracovat s ním. Informace o interakci s revizní kontrolou naleznete v [příručce S Pokyny pro návody k remám](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Po dokončení můžete stisknutím libovolné klávesy pokračovat v programu a načíst výsledky procesu kontroly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

Pokud jste v tomto scénáři použili koncový bod zpětného volání, měl by obdržet událost v tomto formátu:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z `dotnet run` adresáře aplikace pomocí příkazu.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak používat knihovnu Content Moderator .NET k úkolům moderování. Dále si přečtěte koncepční příručku, kde se dozvíte více o moderování obrázků nebo jiných médií.

> [!div class="nextstepaction"]
> [Koncepty moderování obrázků](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co je Azure Content Moderator?](./overview.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs).
