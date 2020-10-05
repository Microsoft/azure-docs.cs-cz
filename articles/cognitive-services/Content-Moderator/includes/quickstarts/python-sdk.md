---
title: Rychlý Start klientské knihovny Content Moderator Pythonu
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou Azure Content Moderator pro Python. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: bea422514b109f446ee30633b0074730f9b89af0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332550"
---
Začněte s klientskou knihovnou Azure Content Moderator pro Python. Pomocí těchto kroků nainstalujete balíček PiPy a vyzkoušíte ukázkový kód pro základní úlohy. 

Content Moderator je služba AI, která umožňuje zpracovávat obsah, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Použijte službu pro moderování obsahu s AI ke skenování textu, obrázku a videí a automatické použití příznaků obsahu. Potom Integrujte svoji aplikaci pomocí nástroje pro kontrolu online, což je prostředí moderátora pro tým pro lidské kontrolory. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.

Použití klientské knihovny Content Moderator pro Python pro:

* [Střední text](#moderate-text)
* [Použití vlastního seznamu podmínek](#use-a-custom-terms-list)
* [Střední obrázky](#moderate-images)
* [Použití vlastního seznamu obrázků](#use-a-custom-image-list)
* [Vytvořit recenzi](#create-a-review)

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator)  |  [Balíček (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Python 3.x](https://www.python.org/)

## <a name="create-a-content-moderator-resource"></a>Vytvoření prostředku Content Moderator

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Content Moderator pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Zobrazení prostředku na [Azure Portal](https://portal.azure.com/)

Po získání klíče z prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč a adresu URL koncového bodu s názvem `CONTENT_MODERATOR_SUBSCRIPTION_KEY` a `CONTENT_MODERATOR_ENDPOINT` , v uvedeném pořadí.
 
## <a name="create-a-new-python-script"></a>Vytvoření nového skriptu Pythonu

Vytvořte nový skript Pythonu a otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Pak na `import` začátek souboru přidejte následující příkazy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Dále vytvořte proměnné pro umístění a klíč koncového bodu prostředku jako proměnné prostředí. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Pokud jste po spuštění aplikace vytvořili proměnné prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k proměnným.

## <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu Content Moderator můžete nainstalovat pomocí následujícího příkazu:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce klientské knihovny Content Moderator Pythonu.

|Název|Popis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Tato třída je potřebná pro všechny funkce Content Moderator. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní údaje nebo lidské obličeje.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní údaje.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Tato třída poskytuje funkce v rozhraních API pro revize, včetně metod vytváření úloh, vlastních pracovních postupů a lidských recenzí.|

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Content Moderator klientské knihovny pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Střední text](#moderate-text)
* [Použití vlastního seznamu podmínek](#use-a-custom-terms-list)
* [Střední obrázky](#moderate-images)
* [Použití vlastního seznamu obrázků](#use-a-custom-image-list)
* [Vytvořit recenzi](#create-a-review)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste pro svůj Content Moderator klíč a koncový bod [vytvořili proměnné prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) .

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Střední text

Následující kód používá klienta Content Moderator k analýze textu a k vytištění výsledků do konzoly. Nejprve vytvořte **text_files nebo** složku v kořenovém adresáři projektu a přidejte soubor *content_moderator_text_moderation.txt* . Přidejte do tohoto souboru vlastní text nebo použijte následující vzorový text:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Přidejte odkaz do nové složky.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Potom do skriptu Pythonu přidejte následující kód.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Použití vlastního seznamu podmínek

Následující kód ukazuje, jak spravovat seznam vlastních podmínek pro moderování textu. Pomocí třídy [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) můžete vytvořit seznam podmínek, spravovat jednotlivé výrazy a na základě nich zobrazit další těla textu.

### <a name="get-sample-text"></a>Získat vzorový text

Chcete-li použít tuto ukázku, je nutné vytvořit **text_files nebo** složku v kořenovém adresáři projektu a přidat *content_moderator_term_list.txt* soubor. Tento soubor by měl obsahovat organický text, který se bude kontrolovat podle seznamu podmínek. Můžete použít následující vzorový text:

```
This text contains the terms "term1" and "term2".
```

Přidejte odkaz na složku, pokud jste ji ještě nedefinovali.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Vytvoří seznam.

Do skriptu Pythonu přidejte následující kód, který vytvoří vlastní seznam podmínek a uloží jeho hodnotu ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definovat podrobnosti seznamu

Pomocí ID seznamu můžete upravit jeho název a popis.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Přidat termín do seznamu

Následující kód přidá výrazy `"term1"` a `"term2"` do seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Získat všechny výrazy v seznamu

K vrácení všech podmínek v seznamu můžete použít ID seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Aktualizovat index seznamu

Kdykoli v seznamu přidáte nebo odeberete výrazy, je nutné index aktualizovat, aby bylo možné použít aktualizovaný seznam.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Text obrazovky na seznamu

Hlavní funkcí seznamu vlastních podmínek je porovnat tělo textu se seznamem a zjistit, jestli existují nějaké vyhovující výrazy. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Odebrání termínu ze seznamu

Následující kód odstraní termín `"term1"` ze seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Odebere všechny výrazy ze seznamu.

Pomocí následujícího kódu vymažte seznam všech jeho podmínek.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-a-list"></a>Odstraní seznam.

Pomocí následujícího kódu odstraňte vlastní seznam podmínek.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Střední obrázky

Následující kód používá klienta Content Moderator společně s objektem [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) k analýze imagí pro dospělé a pikantní obsah.

### <a name="get-sample-images"></a>Získat ukázkové obrázky

Definujte odkaz na některé obrázky, které chcete analyzovat.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Pak přidejte následující kód k iterování imagí. Zbytek kódu v této části se bude nacházet uvnitř této smyčky.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Kontrolovat obsah pro dospělé/pikantní

Následující kód zkontroluje obrázek na dané adrese URL pro dospělé nebo pikantní obsah a vytiskne výsledky do konzoly. Informace o tom, co znamenají, najdete v příručce [Koncepty pro moderování imagí](../../image-moderation-api.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Kontrolovat viditelný text

Následující kód zkontroluje obrázek pro viditelný textový obsah a vytiskne výsledky do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Kontrolovat obličeje

Následující kód zkontroluje obrázek pro lidské obličeje a vytiskne výsledky do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Použití vlastního seznamu obrázků

Následující kód ukazuje, jak spravovat vlastní seznam imagí pro moderování obrázku. Tato funkce je užitečná v případě, že vaše platforma často přijímá instance stejné sady imagí, které chcete vymonitorovat. Udržováním seznamu těchto konkrétních imagí můžete zlepšit výkon. Třída [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) vám umožňuje vytvořit seznam obrázků, spravovat jednotlivé obrázky v seznamu a porovnat ostatní image.

Vytvořte následující textové proměnné pro uložení adres URL imagí, které budete používat v tomto scénáři.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Toto není správný seznam, ale neoficiální seznam imagí, který bude přidán do `add images` části kódu.


### <a name="create-an-image-list"></a>Vytvoření seznamu obrázků

Přidejte následující kód k vytvoření seznamu obrázků a uložte odkaz na jeho ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Přidání obrázků do seznamu

Následující kód přidá všechny vaše obrázky do seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

V rámci skriptu definujte funkci pomocné funkce **add_images** jinde.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Získat obrázky v seznamu

Následující kód vytiskne názvy všech obrázků v seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Aktualizovat podrobnosti seznamu

K aktualizaci názvu a popisu seznamu můžete použít ID seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Získat podrobnosti o seznamu

Chcete-li vytisknout aktuální podrobnosti o seznamu, použijte následující kód.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Aktualizovat index seznamu

Po přidání nebo odebrání obrázků je potřeba aktualizovat index seznamu, abyste ho mohli použít k zobrazení dalších imagí.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Porovná obrázky se seznamem.

Hlavní funkcí seznamů obrázků je porovnání nových imagí a zjištění, zda existují žádné shody.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Odebrání obrázku ze seznamu

Následující kód odstraní položku ze seznamu. V tomto případě se jedná o obrázek, který se neshoduje s kategorií seznamu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Odebere všechny obrázky ze seznamu.

Pomocí následujícího kódu vymažte seznam obrázků.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Odstranit seznam obrázků

Pomocí následujícího kódu odstraňte daný seznam obrázků.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Vytvořit recenzi

Můžete použít knihovnu klienta Content Moderator Pythonu k zakládání obsahu do [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com) , aby ho lidé mohli zkontrolovat. Další informace o nástroji pro kontrolu najdete v [koncepční příručce k nástroji pro kontrolu](../../review-tool-user-guide/human-in-the-loop.md).

Následující kód používá třídu [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) k vytvoření revize, načtení jeho ID a kontrolu jeho podrobností po přijetí lidského vstupu prostřednictvím webového portálu nástroje pro kontrolu.

### <a name="get-review-credentials"></a>Získat pověření pro kontrolu

Nejdřív se přihlaste k nástroji pro revizi a načtěte název svého týmu. Pak ji přiřaďte příslušné proměnné v kódu. Volitelně můžete nastavit koncový bod zpětného volání pro příjem aktualizací aktivity revize.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Vytvoření revize obrázku

Přidejte následující kód k vytvoření a odeslání revize pro danou adresu URL obrázku. Kód uloží odkaz na ID revize. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Získat podrobnosti o kontrole

Pomocí následujícího kódu zkontrolujte podrobnosti dané recenze. Po vytvoření recenze můžete přejít k nástroji pro kontrolu sami a interagovat s obsahem. Informace o tom, jak to provést, najdete v [příručce k recenzi](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Až budete hotovi, můžete znovu spustit tento kód a načte výsledky procesu kontroly.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

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

Spusťte aplikaci pomocí `python` příkazu v souboru rychlého startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí Content Moderator knihovny Pythonu provádět úlohy moderování. V dalším kroku se dozvíte další informace o moderování imagí nebo jiných médií. Přečtěte si koncepční průvodce.

> [!div class="nextstepaction"]
>[Koncepty moderování obrázků](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co je Azure Content Moderator?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).