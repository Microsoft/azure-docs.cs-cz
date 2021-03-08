---
title: Průvodce rychlým startem klientské knihovny Content Moderator .NET
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou Azure Content Moderator pro .NET. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: d6f27ca3635ff1efb80e5261b7a5a07269304dfd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444732"
---
Začněte s klientskou knihovnou Azure Content Moderator pro .NET. Postupujte podle těchto kroků a nainstalujte balíček NuGet a vyzkoušejte ukázkový kód pro základní úlohy. 

Content Moderator je služba AI, která umožňuje zpracovávat obsah, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Použijte službu pro moderování obsahu s AI ke skenování textu, obrázku a videí a automatické použití příznaků obsahu. Potom Integrujte svoji aplikaci pomocí nástroje pro kontrolu online, což je prostředí moderátora pro tým pro lidské kontrolory. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.

Pomocí klientské knihovny Content Moderator pro .NET:

* Střední text
* Střední obrázky
* Vytvořit recenzi

[Referenční dokumentace](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Ukázky](../../samples-dotnet.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/) nebo aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" vytvořte prostředek Content moderator vytvoření prostředku "  target="_blank"> Content Moderator </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k Content Moderator budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

#### <a name="visual-studio-ide"></a>[Integrované vývojové prostředí sady Visual Studio](#tab/visual-studio)

Pomocí sady Visual Studio vytvořte novou aplikaci .NET Core. 

### <a name="install-the-client-library"></a>Instalace klientské knihovny 

Po vytvoření nového projektu nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení projektu v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předprodejní** a vyhledejte `Microsoft.Azure.CognitiveServices.ContentModerator` . Vyberte verzi `2.0.0` a pak **nainstalujte**. 

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `content-moderator-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

V adresáři aplikace nainstalujte Content Moderator klientskou knihovnu pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs), který obsahuje příklady kódu v tomto rychlém startu.

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující příkazy `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Ve třídě **program** vytvořte proměnné pro klíč a koncový bod prostředku.

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek Content Moderator, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


V `main()` metodě aplikace přidejte volání metod používaných v rámci tohoto rychlého startu. Budete je vytvářet později.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce klientské knihovny Content Moderator .NET.

|Název|Popis|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Tato třída je potřebná pro všechny funkce Content Moderator. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní údaje nebo lidské obličeje.|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní údaje.|
|[Recenze](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Tato třída poskytuje funkce v rozhraních API pro revize, včetně metod vytváření úloh, vlastních pracovních postupů a lidských recenzí.|

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny Content Moderator pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Střední text](#moderate-text)
* [Střední obrázky](#moderate-images)
* [Vytvořit recenzi](#create-a-review)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci objektů klienta s vaším koncovým bodem a klíčem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Střední text

Následující kód používá klienta Content Moderator k analýze textu a k vytištění výsledků do konzoly. V kořenovém adresáři třídy **programu** definujte vstupní a výstupní soubory:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Potom v kořenovém adresáři projektu přidejte soubor *TextFile.txt* . Přidejte do tohoto souboru vlastní text nebo použijte následující vzorový text:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Pak definujte metodu moderování textu někde ve vaší třídě **programu** :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Střední obrázky

Následující kód používá klienta Content Moderator společně s objektem [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) k analýze vzdálených imagí pro dospělé a pikantní obsah.

> [!NOTE]
> Můžete také analyzovat obsah místní image. V [referenční dokumentaci](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) najdete metody a operace, které fungují s místními imagemi.

### <a name="get-sample-images"></a>Získat ukázkové obrázky

Definujte vstupní a výstupní soubory v kořenovém adresáři vaší třídy **programu** :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Pak vytvořte vstupní soubor *ImageFiles.txt* v kořenu projektu. V tomto souboru přidáte adresy URL obrázků k analýze &mdash; jedné adresy URL na každém řádku. Můžete použít následující ukázkové obrázky:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definovat pomocnou třídu

Do třídy **program** přidejte následující definici třídy. Tato vnitřní třída zpracuje výsledky moderování obrázku.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definovat metodu moderování obrázku

Následující metoda prochází pomocí adres URL obrázků v textovém souboru, vytvoří instanci **EvaluationData** a analyzuje obrázek pro dospělé/pikantní obsah, text a lidské obličeje. Pak přidá poslední instanci **EvaluationData** do seznamu a zapíše úplný seznam vrácených dat do konzoly.

#### <a name="iterate-through-images"></a>Iterovat prostřednictvím imagí

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analýza obsahu

Další informace o atributech obrázků, které Content Moderator obrazovky pro, najdete v příručce [Koncepty pro moderování imagí](../../image-moderation-api.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Zapsat výsledky moderování do souboru

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Vytvořit recenzi

Můžete použít knihovnu klienta Content Moderator .NET k zakládání obsahu do [Nástroje pro revize](https://contentmoderator.cognitive.microsoft.com) , aby ho mohli recenzenti prohlížet. Další informace o nástroji pro kontrolu najdete v [koncepční příručce k nástroji pro kontrolu](../../review-tool-user-guide/human-in-the-loop.md).

Metoda v tomto oddílu používá třídu [Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) k vytvoření revize, načtení jeho ID a kontrolu jeho podrobností po přijetí lidského vstupu prostřednictvím webového portálu nástroje pro kontrolu. Protokoluje všechny tyto informace ve výstupním textovém souboru. 

### <a name="get-sample-images"></a>Získat ukázkové obrázky

Deklarujete následující pole v kořenovém adresáři vaší třídy **programu** . Tato proměnná odkazuje na vzorový obrázek, který se má použít k vytvoření revize.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Získat pověření pro kontrolu

Přihlaste se k [nástroji pro revizi](https://contentmoderator.cognitive.microsoft.com) a načtěte název svého týmu. Pak ji přiřaďte příslušné proměnné ve třídě **program** . Volitelně můžete nastavit koncový bod zpětného volání pro příjem aktualizací aktivity revize.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definovat pomocnou třídu

Do třídy **programu** přidejte následující definici třídy. Tato třída bude sloužit k reprezentaci jedné instance revize, která je odeslána do nástroje pro revize.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definovat pomocnou metodu

Do třídy **Program** přidejte následující metodu. Tato metoda zapíše výsledky kontroly dotazů do výstupního textového souboru.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definice metody vytváření Revize

Nyní jste připraveni definovat metodu, která bude zpracovávat vytváření a dotazování na revizi. Přidejte novou metodu, **CreateReviews** a definujte následující místní proměnné.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Zveřejnění revizí nástroje pro kontrolu

Pak přidejte následující kód pro iteraci pomocí daných ukázkových imagí, přidejte metadata a odešlete je do nástroje pro kontrolu v rámci jedné dávky. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Objekt vrácený voláním rozhraní API bude obsahovat jedinečné hodnoty ID pro každý nahraný obrázek. Následující kód analyzuje tato ID a pak je používá k dotazování Content Moderator pro stav jednotlivých imagí v dávce.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Získat podrobnosti o kontrole

Následující kód způsobí, že program čeká na vstup uživatele. Když do tohoto kroku přejdete za běhu, můžete přejít na nástroj pro [kontrolu](https://contentmoderator.cognitive.microsoft.com) sami, ověřit, jestli se ukázková image nahrála, a s ní pracovat. Informace o tom, jak pracovat s revizí, najdete v [příručce](../../review-tool-user-guide/review-moderated-images.md)k recenzi. Až budete hotovi, můžete stisknutím libovolné klávesy pokračovat v programu a načíst výsledky procesu kontroly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Pokud jste v tomto scénáři použili koncový bod zpětného volání, měla by se zobrazit událost v tomto formátu:

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí knihovny Content Moderator .NET provádět úlohy moderování. V dalším kroku se dozvíte další informace o moderování imagí nebo jiných médií. Přečtěte si koncepční průvodce.

> [!div class="nextstepaction"]
> [Koncepty moderování obrázků](../../image-moderation-api.md)

* [Co je Azure Content Moderator?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
