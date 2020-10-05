---
title: Rychlý Start knihovny pro klienta Pythonu pro Python
description: Pomocí klientské knihovny obličeje pro Python můžete detekovat plošky, najít podobné (vyhledávání obličejem na obrázku), identifikovat plošky (hledání rozpoznávání obličeje) a migrovat data z obličeje.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: f746a61850567014ce216c47df472d035f1ae123
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91322944"
---
Začněte s rozpoznáváním obličeje pomocí klientské knihovny pro tvář pro Python. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba Faceer poskytuje přístup k pokročilým algoritmům pro zjišťování a rozpoznávání lidských plošek na obrázcích.

Pomocí klientské knihovny obličeje pro Python:

* Rozpoznávání tváří na obrázku
* Vyhledání podobných tváří
* Vytvoření a výuka skupiny osob
* Identifikace obličeje
* Ověřit plošky
* Pořídit snímek migrace dat

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)  |  [Balíček (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/)  |  [Ukázky](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Předpoklady

* [Python 3.x](https://www.python.org/)
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Po získání klíče a koncového bodu [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč a koncový bod s názvem `FACE_SUBSCRIPTION_KEY` a v `FACE_ENDPOINT` uvedeném pořadí.

## <a name="setting-up"></a>Nastavení
 
### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový skript Pythonu &mdash; *Quickstart-File.py*, například. Pak ho otevřete v preferovaném editoru nebo integrovaném vývojovém prostředí a importujte následující knihovny.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Pak vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu můžete nainstalovat pomocí nástroje:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny Pythonu pro tváře.

|Název|Popis|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Tato třída reprezentuje vaši autorizaci používat službu obličeje a potřebujete ji pro všechny funkce obličeje. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete provádět s lidskými obličejemi. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Tato třída reprezentuje všechna data, která byla zjištěna z jedné plošky v obrázku. Můžete ji použít k získání podrobných informací o tváři.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Tato třída spravuje **FaceList** konstrukce uložené v cloudu, které ukládají roztříděné sady ploch. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu plošek, které patří jedné osobě.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu objektů pro řazení **osob** . |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Tato třída spravuje funkci snímků. můžete ji použít k dočasnému uložení všech vašich cloudových dat na bázi cloudu a k migraci těchto dat do nového předplatného Azure. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úkoly s klientskou knihovnou obličeje pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Ověřit plošky](#verify-faces)
* [Pořídit snímek migrace dat](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro svůj klíč obličeje s názvem `FACE_SUBSCRIPTION_KEY` .

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Následující kód detekuje obličej ve vzdálené imagi. Vytiskne ID zjištěné plochy do konzoly a také je uloží do paměti programu. Pak detekuje plošky v obrázku s více lidmi a vytiskne také jejich ID do konzoly. Změnou parametrů v metodě [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) můžete vracet různé informace s každým objektem [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Další scénáře detekce najdete v ukázkovém kódu na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) .

### <a name="display-and-frame-faces"></a>Zobrazit a orámovat obličeje

Následující kód vytvoří výstup daného obrázku do zobrazení a Nakreslí obdélníky kolem obličeje pomocí vlastnosti DetectedFace. faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Mladí žena s červeným obdélníkem nakresleným kolem plochy](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód přebírá jednu zjištěnou plošku (zdroj) a hledá v sadě dalších tváře (cíle) k nalezení shody (hledání na základě obrázku). Pokud najde shodu, vytiskne ID spárovaného obličeje konzole.

### <a name="find-matches"></a>Najít shody

Nejdřív spusťte kód v části výše ([detekuje plošky v obrázku](#detect-faces-in-an-image)) a uložte odkaz na jednu plošku. Pak spusťte následující kód, který získá odkazy na několik plošek v imagi skupiny.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Pak přidejte následující blok kódu pro vyhledání instancí první plochy ve skupině. Informace o tom, jak toto chování změnit, najdete v metodě [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Tisk shody

Použijte následující kód k vytištění podrobností o shodě do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Vytvoření a výuka skupiny osob

Následující kód vytvoří samostatnou **osobu** se třemi různými objekty **Person** . Přidružuje každou **osobu** k sadě ukázkových imagí a pak IT vlaky, aby dokázali rozpoznat každou osobu. 

### <a name="create-persongroup"></a>Vytvořit osobu

Pro krokování v tomto scénáři je nutné uložit následující image do kořenového adresáře projektu: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Tato skupina imagí obsahuje tři sady imagí obličeje, které odpovídají třem různým lidem. Kód bude definovat tři objekty **Person** a přidružit je k souborům obrázků, které začínají `woman` na, `man` a `child` .

Po nastavení imagí definujte v horní části skriptu popisek pro objekt **Person** , který vytvoříte.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Potom do dolní části skriptu přidejte následující kód. Tento kód vytvoří objekty **Person** a tři **osoby** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Přiřazení plošek k osobám

Následující kód seřadí obrázky podle jejich předpony, detekuje obličeje a přiřadí objekty ke všem objektům **Person** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Pracovník pro vzdělávání

Jakmile budete mít k plošky, je nutné, abyste vytvořili svoji **osobu** , aby bylo možné identifikovat vizuální funkce přidružené k jednotlivým objektům **osob** . Následující kód volá metodu asynchronního **vlaku** a provede dotaz na výsledek a vytiskne stav do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identifikace obličeje

Operace identifikace přebírá obrázek osoby (nebo více lidí) a hledá identitu každé plošky v imagi (vyhledávání rozpoznávání obličeje). Porovnává každou zjištěnou plochu s **osobou**, databázi různých objektů **osob** , jejichž funkce obličeje jsou známy.

> [!IMPORTANT]
> Aby bylo možné spustit tento příklad, musíte nejprve spustit kód v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Získat image testu

Následující kód vypadá v kořenu projektu _test-image-person-group.jpg_ a detekuje plošky v obrázku. Tuto image najdete s obrázky použitými pro správu **osob** : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identifikace tváří

Metoda **Identifikace** vezme pole zjištěných plošek a porovná je s objektem **Person**. Pokud se může shodovat s uživatelem zjištěné **osoby**, uloží výsledek. Tento kód vytiskne podrobné výsledky shody s konzolou.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Ověřit plošky

Operace ověření bere v případě ID obličeje a buď jiné ID obličeje, nebo objekt **Person** a určuje, zda patří stejné osobě.

Následující kód detekuje plošky ve dvou zdrojových obrázcích a pak je ověřuje proti obličeji zjištěnému z cílové image.

### <a name="get-test-images"></a>Získání imagí testu

Následující bloky kódu deklaruje proměnné, které budou ukazovat na zdrojové a cílové image pro operaci ověřování.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Detekovat obličeje pro ověření

Následující kód detekuje plošky ve zdrojových a cílových obrázcích a ukládá je do proměnných.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Získat výsledky ověřování

Následující kód porovnává každý zdrojový obraz s cílovou imagí a vytiskne zprávu, která označuje, zda patří stejné osobě.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pořídit snímek migrace dat

Funkce snímků umožňuje přesunout uložená data o obličejích, jako je například školená **osoba**, do jiného předplatného služby Azure Cognitive Services Face. Tuto funkci můžete chtít použít například v případě, že jste vytvořili objekt **Person** pomocí bezplatného předplatného a teď ho chcete migrovat do placeného předplatného. Přehled funkce snímků najdete v tématu [migrace dat](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) o ploše.

V tomto příkladu migrujete skupinu **Person** , kterou jste vytvořili v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group). Tuto část můžete buď dokončit jako první, nebo použít vlastní tváře datové konstrukce.

### <a name="set-up-target-subscription"></a>Nastavit cílové předplatné

Nejdřív musíte mít k dispozici druhé předplatné Azure s předním prostředkem; to můžete provést podle kroků v části [Nastavení](#setting-up) . 

Pak v horní části skriptu vytvořte následující proměnné. Budete také muset vytvořit nové proměnné prostředí pro ID předplatného vašeho účtu Azure a také klíč, koncový bod a ID předplatného vašeho nového (cílového) účtu. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Ověřit cílového klienta

Později ve svém skriptu uložte aktuální objekt klienta jako zdrojový klient a pak ověřte nový objekt klienta pro cílové předplatné. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Použití snímku

Zbývající operace snímku se provádějí v rámci asynchronní funkce. 

1. Prvním krokem je **pořídit** snímek, který ukládá data z původního předplatného do dočasného cloudového umístění. Tato metoda vrací ID, které slouží k dotazování na stav operace.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. V dalším kroku vydáte dotaz na ID, dokud se operace nedokončí.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Tento kód využívá `wait_for_operation` funkci, kterou byste měli definovat samostatně:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Vraťte se k asynchronní funkci. Pomocí operace **Apply** můžete zapsat data vaší plochy do cílového předplatného. Tato metoda také vrací ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Znovu použijte `wait_for_operation` funkci pro dotazování ID, dokud se operace nedokončí.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Až dokončíte tyto kroky, budete mít přístup k konstrukcím vašich datových dat z vašeho nového (cílového) předplatného.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pro rozpoznávání tváře z adresáře aplikace pomocí `python` příkazu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Pokud jste v tomto rychlém startu vytvořili pole **Person** a chcete ho odstranit, spusťte ve svém skriptu následující kód:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Pokud jste migrovali data pomocí funkce snímku v tomto rychlém startu, budete také muset odstranit **pracovní** buňku uloženou do cílového předplatného.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat aplikaci Face Client Library pro Python k provádění základních úloh rozpoznávání obličeje. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na Face API (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Co je služba Rozpoznávání tváře?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
