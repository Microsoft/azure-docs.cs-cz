---
title: 'Úvodní příručka: Klientská knihovna Face pro Python'
description: Tento rychlý start vám pomůže začít s klientskou knihovnou Face pro Python, abyste zjistili, našli podobné, identifikovali, ověřili a další.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7ac1b85ee44627990931cfc7b3a65f6f7a661b3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165818"
---
# <a name="quickstart-face-client-library-for-python"></a>Úvodní příručka: Klientská knihovna Face pro Python

Začínáme s klientskou knihovnou Face pro Python. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Služba Face vám poskytuje přístup k pokročilým algoritmům pro detekci a rozpoznávání lidských tváří v obrazech.

Pomocí klientské knihovny Face pro Python:

* Rozpoznávání tváří na obrázku
* Vyhledání podobných tváří
* Vytvoření a trénování skupiny osob
* Identifikace obličeje
* Ověřit plochy
* Pořízení snímku pro migraci dat

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Ukázky](https://docs.microsoft.com/samples/browse/?products=azure&term=face) [zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [knihovny (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-face-azure-resource"></a>Vytvoření prostředku Face Azure

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro Face pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/)

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč s názvem `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte například&mdash;nový skript Pythonu*quickstart-file.py*. Pak jej otevřete v upřednostňovaném editoru nebo rozhraní IDE a importujte následující knihovny.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Potom vytvořte proměnné pro koncový bod azure a klíč vašeho prostředku. Možná budete muset změnit první část koncového bodu (`westus`) tak, aby odpovídala vašemu předplatnému.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu můžete nainstalovat pomocí:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Face Python SDK.

|Name (Název)|Popis|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Tato třída představuje oprávnění k používání služby Face a potřebujete ji pro všechny funkce Face. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete dělat s lidskými plochami. |
|[Zjištěná plocha](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Tato třída představuje všechna data, která byla zjištěna z jedné plochy v obraze. Můžete ji použít k načtení podrobných informací o ploše.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Tato třída spravuje cloudové konstrukce **FaceList,** které ukládají různé sady ploch. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Tato třída spravuje cloudové konstrukce **Person,** které ukládají sadu ploch, které patří jedné osobě.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Tato třída spravuje cloudové konstrukce **PersonGroup,** které ukládají sadu tříděných objektů **Person.** |
|[ShapshotOperace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Tato třída spravuje funkce snímek; můžete ji použít k dočasnému uložení všech dat scloudové tváře a migraci těchto dat do nového předplatného Azure. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s knihovnou klienta Face pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Vyhledání podobných tváří](#find-similar-faces)
* [Vytvoření a trénování skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Ověřit plochy](#verify-faces)
* [Pořízení snímku pro migraci dat](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento rychlý start předpokládá, že jste [vytvořili proměnnou prostředí](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro klíč Face s názvem `FACE_SUBSCRIPTION_KEY`.

Vytvořte instanci klienta pomocí koncového bodu a klíče. Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho s koncovým bodem k vytvoření objektu [FaceClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Následující kód detekuje tvář ve vzdáleném obrázku. Vytiskne id zjištěné tváře do konzoly a také jej uloží do programové paměti. Poté detekuje tváře v obraze s více lidmi a vytiskne jejich ID do konzoly. Změnou parametrů v [metodě detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) můžete vrátit různé informace s každým objektem [DetectedFace.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Další scénáře zjišťování najdete v ukázkovém kódu na [GitHubu.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)

### <a name="display-and-frame-faces"></a>Zobrazit plochy a plochy rámečku

Následující kód vyveze daný obrázek na zobrazení a nakreslí obdélníky kolem tváří pomocí vlastnostdetectedface.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Mladá žena s červeným obdélníkem nakresleným kolem obličeje](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód přebírá jednu zjištěnou plochu a prohledává sadu dalších tváří, aby vyhledal shody. Když najde shodu, vytiskne obdélníkové souřadnice odpovídající plochy do konzoly. 

### <a name="find-matches"></a>Najít shody

Nejprve spusťte kód ve výše uvedené části ([Rozpoznat plochy v obraze](#detect-faces-in-an-image)) a uložte odkaz na jednu plochu. Pak spusťte následující kód získat odkazy na několik tváří v obrázku skupiny.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Pak přidejte následující blok kódu, abyste našli instance první plochy ve skupině. Naleznete [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) metodu, jak změnit toto chování.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Tisk shod

Následující kód slouží k tisku podrobností shody do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Vytvoření a trénování skupiny osob

Následující kód vytvoří **PersonGroup se** třemi různými **Person** objekty. Spojuje každou **osobu** se sadou příkladů obrázků, a pak trénuje, aby bylo možné rozpoznat každou osobu. 

### <a name="create-persongroup"></a>Vytvořit skupinu osob

Chcete-li krokovat v tomto scénáři, je třeba uložit https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesnásledující obrázky do kořenového adresáře projektu: .

Tato skupina obrázků obsahuje tři sady obrázků obličeje odpovídající třem různým lidem. Kód definuje tři objekty **Person** a přidruží `man`je `child`k obrazovým souborům, které začínají v , `woman`, a .

Po nastavení obrázků definujte popisek v horní části skriptu pro objekt **PersonGroup,** který vytvoříte.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Potom přidejte následující kód do dolní části skriptu. Tento kód vytvoří **PersonGroup** a tři **Person** objekty.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Přiřazení ploch osobám

Následující kód seřadí obrázky podle jejich předpony, detekuje plochy a přiřadí plochy každému objektu **Person.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Vlak PersonGroup

Po přiřazení ploch je nutné trénovat **skupinu osob,** aby mohla identifikovat vizuální prvky přidružené ke každému z jejích objektů **Person.** Následující kód volá metodu asynchronního **trainu** a vyvolá výsledek a tiskne stav do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identifikace obličeje

Následující kód pořídí obrázek s více plochami a vyhledá identitu každé osoby v obrázku. Porovnává každou detekoci tvář **persongroup**, databáze různých osob **objekty,** jejichž rysy obličeje jsou známy.

> [!IMPORTANT]
> Chcete-li spustit tento příklad, musíte nejprve spustit kód v [vytvořit a trénování skupiny osob](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Získání testovacího obrázku

Následující kód vyhledá v kořenovém adresáři projektu _obrázek test-image-person-group.jpg_ a detekuje tváře v obraze. Tento obrázek najdete s obrázky použitými pro správu **PersonGroup:** https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identifikace tváří

Metoda **identifikace** přebírá pole zjištěných tváří a porovnává je s **PersonGroup**. Pokud může spárovat zjištěnou tvář **s osobou**, uloží výsledek. Tento kód vytiskne podrobné výsledky shody do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Ověřit plochy

Operace Ověření přebírá ID tváře a jiné ID plochy nebo objekt **Person** a určuje, zda patří stejné osobě.

Následující kód detekuje tváře ve dvou zdrojových obrazech a poté je ověří proti ploše zjištěné z cílového obrazu.

### <a name="get-test-images"></a>Získat testovací obrázky

Následující bloky kódu deklarují proměnné, které budou ukazovat na zdrojové a cílové bitové kopie pro ověřovací operaci.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Detekce ploch pro ověření

Následující kód detekuje tváře ve zdrojových a cílových obrázcích a uloží je do proměnných.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Získání výsledků ověření

Následující kód porovná každý zdrojový obrázek s cílovým obrázkem a vytiskne zprávu s informací, zda patří stejné osobě.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pořízení snímku pro migraci dat

Funkce Snímky umožňuje přesunout uložená data tváře, jako je například trénovaná **PersonGroup**, do jiného předplatného služby Azure Cognitive Services Face. Tuto funkci můžete použít například v případě, že jste vytvořili objekt **PersonGroup** pomocí bezplatného zkušebního předplatného a nyní ji chcete migrovat do placeného předplatného. Podívejte se na podrobné informace o funkci Snímky v [tématu Migrace osobních dat.](../Face-API-How-to-Topics/how-to-migrate-face-data.md)

V tomto příkladu budete migrovat **PersonGroup,** kterou jste vytvořili v [create and train a person group](#create-and-train-a-person-group). Můžete buď nejprve vyplnit tento oddíl, nebo použít vlastní konstrukce dat tváře.

### <a name="set-up-target-subscription"></a>Nastavení cílového předplatného

Nejprve musíte mít druhé předplatné Azure s prostředkem Face; můžete to provést podle kroků v části [Nastavení.](#setting-up) 

Potom vytvořte následující proměnné v horní části skriptu. Budete také muset vytvořit nové proměnné prostředí pro ID předplatného vašeho účtu Azure, stejně jako klíč, koncový bod a ID předplatného vašeho nového (cílového) účtu. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Ověření cílového klienta

Později ve skriptu uložte aktuální objekt klienta jako zdrojového klienta a potom ověřte nový objekt klienta pro cílové předplatné. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Použití snímku

Zbývající operace snímek probíhá v rámci asynchronní funkce. 

1. Prvním krokem je **pořízení** snímku, který uloží data tváře původního předplatného do dočasného umístění v cloudu. Tato metoda vrátí ID, které používáte k dotazování stav operace.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Dále dotaz ID, dokud operace byla dokončena.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Tento kód využívá `wait_for_operation` funkci, kterou byste měli definovat samostatně:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Vraťte se ke své asynchronní funkci. Pomocí operace **apply** zapište data obličeje do cílového předplatného. Tato metoda také vrátí ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Znovu použijte `wait_for_operation` funkci k dotazování ID, dokud nebude operace dokončena.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Po dokončení těchto kroků budete mít přístup k vašim konstrukcím dat tváří z vašeho nového (cílového) předplatného.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `python` v souboru quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Pokud jste v tomto rychlém startu **vytvořili skupinu osob** a chcete ji odstranit, spusťte ve skriptu následující kód:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Pokud jste v tomto rychlém startu migrovali data pomocí funkce Snímek, budete muset také odstranit **PersonGroup** uloženou do cílového předplatného.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat knihovnu tváří pro Python k úkolům základů. Dále prozkoumejte referenční dokumentaci, abyste se dozvěděli více o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na rozhraní API pro obličej (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Co je služba Rozpoznávání tváře?](../overview.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
