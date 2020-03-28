---
title: 'Úvodní příručka: Klientská knihovna Content Moderator pro Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou Azure Cognitive Services Content Moderator pro Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772358"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Úvodní příručka: Klientská knihovna Content Moderator pro Python

Začínáme s klientskou knihovnou Content Moderator pro Python. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Content Moderator je kognitivní služba, která kontroluje text, obrázek a video obsah pro materiál, který je potenciálně urážlivý, riskantní nebo jinak nežádoucí. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele.

Pomocí klientské knihovny Content Moderator pro Python:

* [Střední text](#moderate-text)
* [Použití seznamu vlastních termínů](#use-a-custom-terms-list)
* [Střední obrázky](#moderate-images)
* [Použití vlastního seznamu obrázků](#use-a-custom-image-list)
* [Vytvoření recenze](#create-a-review)

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [Ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) [zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [knihovny (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-content-moderator-azure-resource"></a>Vytvoření prostředku Azure moderátora obsahu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro Content Moderator pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/)

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `CONTENT_MODERATOR_SUBSCRIPTION_KEY` `CONTENT_MODERATOR_ENDPOINT`adresu URL klíče a koncového bodu s názvem a , resp.
 
### <a name="create-a-python-script"></a>Vytvoření skriptu pythonu

Vytvořte nový skript Pythonu a otevřete ho v preferovaném editoru nebo IDE. Pak přidejte `import` následující příkazy do horní části souboru.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Dále vytvořte proměnné pro umístění koncového bodu prostředku a klíč jako proměnné prostředí. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Pokud jste vytvořili proměnné prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které jej běží pro přístup k proměnným.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu Content Moderator můžete nainstalovat pomocí následujícího příkazu:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce sady Content Moderator Python SDK.

|Name (Název)|Popis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Tato třída je potřebná pro všechny funkce Content Moderator. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd.|
|[ImageModeonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní informace nebo lidské tváře.|
|[TextModeonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní informace.|
[RecenzeProvoz](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Tato třída poskytuje funkce rozhraní API pro kontrolu, včetně metod pro vytváření úloh, vlastních pracovních postupů a lidských recenzí.|

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly v klientské knihovně Content Moderator pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Střední text](#moderate-text)
* [Použití seznamu vlastních termínů](#use-a-custom-terms-list)
* [Střední obrázky](#moderate-images)
* [Použití vlastního seznamu obrázků](#use-a-custom-image-list)
* [Vytvoření recenze](#create-a-review)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento rychlý start předpokládá, že jste [vytvořili proměnné prostředí](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro klíč a koncový bod moderátora obsahu.

Vytvořte instanci klienta pomocí koncového bodu a klíče. Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho s koncovým bodem k vytvoření objektu [ContentModeratorClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Střední text

Následující kód používá klienta Content Moderator k analýze textu a tisku výsledků do konzoly. Nejprve vytvořte **složku text_files/** v kořenovém adresáři projektu a přidejte soubor *content_moderator_text_moderation.txt.* Přidejte do tohoto souboru vlastní text nebo použijte následující ukázkový text:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Přidejte odkaz na novou složku.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Potom přidejte následující kód do skriptu Pythonu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Použití seznamu vlastních termínů

Následující kód ukazuje, jak spravovat seznam vlastních termínů pro moderování textu. Třídu [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) můžete použít k vytvoření seznamu termínů, správě jednotlivých termínů a zobrazení dalších těl textu proti ní.

### <a name="get-sample-text"></a>Získat ukázkový text

Chcete-li použít tuto ukázku, musíte vytvořit **složku text_files/** v kořenovém adresáři projektu a přidat soubor *content_moderator_term_list.txt.* Tento soubor by měl obsahovat organický text, který bude zkontrolován v seznamu termínů. Můžete použít následující ukázkový text:

```
This text contains the terms "term1" and "term2".
```

Pokud jste ji ještě nedefinovali, přidejte odkaz na složku.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Vytvoření seznamu

Přidejte do skriptu Pythonu následující kód, abyste vytvořili vlastní seznam termínů a uložili jeho hodnotu ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definování podrobností seznamu

ID seznamu můžete použít k úpravě jeho názvu a popisu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Přidání termínu do seznamu

Následující kód přidá `"term1"` termíny a `"term2"` do seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Získejte všechny termíny v seznamu

ID seznamu můžete použít k vrácení všech termínů v seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Aktualizace indexu seznamu

Vždy, když přidáte nebo odeberete termíny ze seznamu, je nutné aktualizovat index před použitím aktualizovaného seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Text obrazovky proti seznamu

Hlavní funkcí seznamu vlastních termínů je porovnání textu se seznamem a zjistit, zda existují nějaké odpovídající termíny. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Odebrání termínu ze seznamu

Následující kód odebere `"term1"` termín ze seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Odebrání všech termínů ze seznamu

Pomocí následujícího kódu zrušte seznam všech jeho podmínek.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Odstranit seznam

Pomocí následujícího kódu odstraňte vlastní seznam termínů.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Střední obrázky

Následující kód používá klienta Content Moderator spolu s objektem [ImageModeonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) k analýze obrázků pro obsah pro dospělé a pikantní.

### <a name="get-images"></a>Získání obrázků

Definujte odkaz na některé obrazy k analýze.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Pak přidejte následující kód k iterátu prostřednictvím obrázků. Zbytek kódu v této části půjde do této smyčky.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Kontrola obsahu pro dospělé/pikantní

Následující kód zkontroluje obrázek na dané adrese URL pro obsah pro dospělé nebo pikantní a vytiskne výsledky do konzoly. Informace o tom, co tyto pojmy znamenají, naleznete v průvodci [koncepty moderování obrázků.](./image-moderation-api.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Kontrola viditelného textu

Následující kód zkontroluje, zda obrázek nezobrazuje viditelný textový obsah, a vytiskne výsledky do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Kontrola ploch

Následující kód zkontroluje obrázek pro lidské tváře a vytiskne výsledky do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Použití vlastního seznamu obrázků

Následující kód ukazuje, jak spravovat vlastní seznam obrázků pro moderování obrázků. Tato funkce je užitečná, pokud vaše platforma často přijímá instance stejné sady obrázků, které chcete zobrazit. Udržováním seznamu těchto konkrétních obrázků můžete zlepšit výkon. Třída [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) umožňuje vytvořit seznam obrázků, spravovat jednotlivé obrázky v seznamu a porovnat s ním jiné obrázky.

Vytvořte následující textové proměnné pro uložení adres URL obrázků, které budete používat v tomto scénáři.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Toto není správný seznam sám, ale neformální seznam obrázků, `add images` které budou přidány v části kódu.


### <a name="create-an-image-list"></a>Vytvoření seznamu obrázků

Přidejte následující kód k vytvoření seznamu obrázků a uložení odkazu na jeho ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Přidání obrázků do seznamu

Následující kód přidá všechny obrázky do seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Definujte **add_images** pomocnou funkci jinde ve skriptu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Získání obrázků v seznamu

Následující kód vytiskne názvy všech obrázků v seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Podrobnosti o seznamu aktualizací

ID seznamu můžete použít k aktualizaci názvu a popisu seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Získat podrobnosti o seznamu

K tisku aktuálních podrobností seznamu použijte následující kód.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Aktualizace indexu seznamu

Po přidání nebo odebrání obrázků je nutné aktualizovat index seznamu, abyste jej mohli použít k prohledání jiných obrázků.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Porovnání obrázků se seznamem

Hlavní funkcí seznamů obrázků je porovnat nové obrázky a zjistit, zda existují nějaké shody.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Odebrání obrázku ze seznamu

Následující kód odebere položku ze seznamu. V tomto případě se jedná o obrázek, který neodpovídá kategorii seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Odebrání všech obrázků ze seznamu

Pomocí následujícího kódu vymažte seznam obrázků.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Odstranění seznamu obrázků

K odstranění daného seznamu obrázků použijte následující kód.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Vytvoření recenze

Sadu Content Moderator Python SDK můžete použít k podávání obsahu do [nástroje Revize,](https://contentmoderator.cognitive.microsoft.com) aby ho mohli moderátoři zkontroluje. Další informace o nástroji Revize naleznete v [koncepčnípříručce nástroje revize](./review-tool-user-guide/human-in-the-loop.md).

Následující kód používá [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) třídy k vytvoření revize, načíst jeho ID a zkontrolovat jeho podrobnosti po obdržení lidské vstup prostřednictvím nástroje pro kontrolu webového portálu.

### <a name="get-review-credentials"></a>Získat pověření pro kontrolu

Nejprve se přihlaste k nástroji Revize a načtěte název týmu. Pak ji přiřaďte k příslušné proměnné v kódu. Volitelně můžete nastavit koncový bod zpětného volání pro příjem aktualizací o aktivitě recenze.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Vytvoření recenze obrázku

Přidejte následující kód k vytvoření a odeslání recenze pro danou adresu URL obrázku. Kód uloží odkaz na ID recenze. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Získat podrobnosti o recenzích

Pomocí následujícího kódu zkontrolujte podrobnosti o dané recenzi. Po vytvoření recenze můžete přejít na nástroj revize sami a pracovat s obsahem. Informace o tom, jak to provést, naleznete v [příručce S Pokyny pro návody k remáži.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images) Po dokončení můžete tento kód spustit znovu a načte výsledky procesu kontroly.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

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

Spusťte aplikaci pomocí příkazu `python` v souboru quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat knihovnu Content Moderator Python k úkolům moderování. Dále si přečtěte koncepční příručku, kde se dozvíte více o moderování obrázků nebo jiných médií.

> [!div class="nextstepaction"]
>[Koncepty moderování obrázků](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co je Azure Content Moderator?](./overview.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).