---
title: Rychlý Start klientské knihovny pro obličej
description: Pomocí klientské knihovny pro rozpoznávání tváře na cestách můžete detekovat obličeje, najít podobné (vyhledávání na ploše podle obrázku), identifikovat plošky (hledání rozpoznávání obličeje) a migrovat data z obličeje.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: 382a04021053bef0b5d3378231e38453885b0ef2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91322945"
---
Začněte s rozpoznáváním obličeje pomocí klientské knihovny pro tvář pro funkci přejít. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba Faceer poskytuje přístup k pokročilým algoritmům pro zjišťování a rozpoznávání lidských plošek na obrázcích.

Použijte klientskou knihovnu služby FACET k přechodu na:

* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořídit snímek migrace dat](#take-a-snapshot-for-data-migration)

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  [Stažení sady SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Předpoklady

* Nejnovější verze nástroje [Přejít](https://golang.org/dl/)
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Po získání klíče a koncového bodu [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč a koncový bod s názvem `FACE_SUBSCRIPTION_KEY` a v `FACE_ENDPOINT` uvedeném pořadí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-go-project-directory"></a>Vytvořit adresář projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt na cestách s názvem `my-app` a přejděte na něj.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Váš pracovní prostor bude obsahovat tři složky:

* **Src** – tento adresář bude obsahovat zdrojový kód a balíčky. Všechny balíčky nainstalované s `go get` příkazem budou v této složce.
* **pkg** – tento adresář bude obsahovat kompilované objekty balíčku pro přechod. Všechny tyto soubory mají `.a` rozšíření.
* **bin** – tento adresář bude obsahovat binární spustitelné soubory, které se vytvoří při spuštění `go install` .

> [!TIP]
> Další informace o struktuře pracovního prostoru přejděte v [dokumentaci jazyka přejděte](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro přejít

Dále nainstalujte klientskou knihovnu pro přejít:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

nebo pokud používáte DEP, v rámci vašeho úložiště úložišť:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Vytvoření aplikace v cestách

Dále vytvořte soubor v adresáři **Src** s názvem `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Otevřete `sample-app.go` v upřednostňovaném integrovaném vývojovém prostředí (IDE) nebo textovém editoru. Pak přidejte název balíčku a importujte následující knihovny:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

V dalším kroku začnete přidávat kód pro provádění různých operací služby obličeje.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé z hlavních funkcí služby Face Service přejít do klientské knihovny.

|Název|Popis|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Tato třída reprezentuje vaši autorizaci používat službu obličeje a potřebujete ji pro všechny funkce obličeje. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. |
|[Klient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete provádět s lidskými obličejemi. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Tato třída reprezentuje všechna data, která byla zjištěna z jedné plošky v obrázku. Můžete ji použít k získání podrobných informací o tváři.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Tato třída spravuje **FaceList** konstrukce uložené v cloudu, které ukládají roztříděné sady ploch. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu plošek, které patří jedné osobě.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Tato třída spravuje konstrukce cloudových uložených **osob** , které ukládají sadu objektů pro řazení **osob** . |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Tato třída spravuje funkci snímků. Můžete ji použít k dočasnému uložení všech vašich cloudových dat na bázi cloudu a k migraci těchto dat do nového předplatného Azure. |

## <a name="code-examples"></a>Příklady kódu

Tyto ukázky kódu ukazují, jak dokončit základní úlohy pomocí klientské knihovny služby FACET pro službu přejít:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)
* [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořídit snímek migrace dat](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE] 
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro svůj klíč a koncový bod s názvem `FACE_SUBSCRIPTION_KEY` a `FACE_ENDPOINT` v uvedeném pořadí.

Vytvořte funkci **Main** a přidejte do ní následující kód, který vytvoří instanci klienta s vaším koncovým bodem a klíčem. Vytvoříte objekt **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** s klíčem a použijete ho u svého koncového bodu k vytvoření objektu **[klienta](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** . Tento kód také vytvoří instanci objektu kontextu, který je potřeba pro vytváření objektů klienta. Definuje také vzdálené umístění, kde se nachází některé z ukázkových imagí v tomto rychlém startu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Do metody **Main** přidejte následující kód. Tento kód definuje vzdálený ukázkový obrázek a určuje, které funkce obličeje z obrázku jsou extrahovány. Určuje také, který model AI se má použít k extrakci dat ze zjištěných tváře. Informace o těchto možnostech najdete v tématu [Určení modelu rozpoznávání](../../Face-API-How-to-Topics/specify-recognition-model.md) . Nakonec metoda **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** provede v imagi operaci detekce obličeje a uloží výsledky do paměti programu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Zobrazit zjištěná data obličeje

Další blok kódu vezme první prvek v poli objektů **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** a vytiskne jeho atributy do konzoly. Pokud jste použili obrázek s více ploškami, měli byste místo toho iterovat v poli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód přebírá jednu zjištěnou plošku (zdroj) a hledá v sadě dalších tváře (cíle) k nalezení shody (hledání na základě obrázku). Pokud najde shodu, vytiskne ID spárovaného obličeje konzole.

### <a name="detect-faces-for-comparison"></a>Detekovat plošky pro porovnání

Nejdřív uložte odkaz na plochu, kterou jste zjistili v části [detekce ploch v obrázku](#detect-faces-in-an-image) . Tato ploška bude zdrojem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Poté zadejte následující kód pro detekci sady plošek v jiném obrázku. Tyto plošky budou cílem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Najít shody

Následující kód používá metodu **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** k vyhledání všech cílových plošek, které odpovídají zdrojové ploše.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Tisk shody

Následující kód vytiskne podrobnosti o shodě do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Vytvoření a výuka skupiny osob

Pro krokování v tomto scénáři je nutné uložit následující image do kořenového adresáře projektu: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Tato skupina imagí obsahuje tři sady imagí s jedním obličejem, které odpovídají třem různým lidem. Kód bude definovat tři objekty **osob** Person a přidružit je k souborům obrázků, které začínají `woman` na, `man` a `child` .

### <a name="create-persongroup"></a>Vytvořit osobu

Po stažení imagí přidejte do dolní části metody **Main** následující kód. Tento kód ověří objekt **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** a pak ho použije k definování nové **osoby**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Vytvořit osoby osoby

Další blok kódu ověří **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** a použije ho k definování tří objektů **Person** osoby. Jednotlivé objekty reprezentují jednu osobu v sadě imagí.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Přiřazení plošek k osobám

Následující kód seřadí obrázky podle jejich předpony, detekuje plošky a přiřadí tyto obličeje k jednotlivým **objektům person osoby na** základě názvu obrázkového souboru.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Pracovník pro vzdělávání

Jakmile budete mít k plošky, provedete tuto **osobu** , aby bylo možné identifikovat vizuální funkce přidružené k jednotlivým objektům **osob** . Následující kód volá metodu asynchronního **vlaku** a provede dotaz na výsledek a vytiskne stav do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identifikace obličeje

Operace identifikace přebírá obrázek osoby (nebo více lidí) a hledá identitu každé plošky v imagi (vyhledávání rozpoznávání obličeje). Porovnává každou zjištěnou plochu s **osobou**, databázi různých objektů **osob** , jejichž funkce obličeje jsou známy.

> [!IMPORTANT]
> Aby bylo možné spustit tento příklad, musíte nejprve spustit kód v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Získat image testu

Následující kód vypadá v kořenu projektu _test-image-person-group.jpg_ obrázku a načítá ho do paměti programu. Tento obrázek najdete ve stejném úložišti jako obrázky používané v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Detekovat zdrojové plošky v testovací imagi

Další blok kódu provede normální detekci na obrázku testu, aby se načetly všechny plošky a ušetřily se do pole.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identifikace tváří

Metoda **[Identifikace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** vezme pole zjištěných plošek a porovná je s danými **osobami** (definované a vyškolenou v předchozí části). Pokud se může porovnávat zjištěná ploška **osobě** ve skupině, uloží výsledek.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Tento kód pak vytiskne podrobné výsledky shody s konzolou.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Ověřit plošky

Operace ověření bere v případě ID obličeje a buď jiné ID obličeje, nebo objekt **Person** a určuje, zda patří stejné osobě.

Následující kód detekuje plošky ve dvou zdrojových obrázcích a pak každý z nich ověřuje proti obličeji zjištěnému z cílové image.

### <a name="get-test-images"></a>Získání imagí testu

Následující bloky kódu deklaruje proměnné, které budou ukazovat na cílové a zdrojové image pro operaci ověřování.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Detekovat obličeje pro ověření

Následující kód detekuje plošky ve zdrojových a cílových obrázcích a ukládá je do proměnných.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Získat výsledky ověřování

Následující kód porovnává každý zdrojový obraz s cílovou imagí a vytiskne zprávu, která označuje, zda patří stejné osobě.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Pořídit snímek migrace dat

Funkce snímků umožňuje přesunout uložená data o obličejích, jako je například školená **osoba**, do jiného předplatného služby Azure Cognitive Services Face. Tuto funkci můžete použít například v případě, že jste vytvořili objekt **Person** pomocí bezplatného předplatného a teď ho chcete migrovat na placené předplatné. Přehled funkce snímků najdete v tématu [migrace dat](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) o ploše.

V tomto příkladu migrujete skupinu **Person** , kterou jste vytvořili v části [Vytvoření a výuka skupiny osob](#create-and-train-a-person-group). Tuto část můžete buď dokončit jako první, nebo použít vlastní tváře datové konstrukce.

### <a name="set-up-target-subscription"></a>Nastavit cílové předplatné

Nejdřív musíte mít k dispozici druhé předplatné Azure s předním prostředkem; to můžete provést opakováním kroků v části [Nastavení](#setting-up) . 

Pak vytvořte následující proměnné v horní části metody **Main** . Budete také muset vytvořit nové proměnné prostředí pro ID předplatného vašeho účtu Azure a také klíč, koncový bod a ID předplatného vašeho nového (cílového) účtu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Pak zadejte hodnotu ID předplatného do pole pro další kroky.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Ověřit cílového klienta

Později ve svém skriptu uložte původní objekt klienta jako zdrojového klienta a pak ověřte nový objekt klienta pro cílové předplatné. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Pořízení snímku

V dalším kroku se pořídí snímek s příkazem **[přijmout](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, který ukládá vaše původní data z vašich předplatných do dočasného cloudového umístění. Tato metoda vrací ID, které slouží k dotazování na stav operace.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

V dalším kroku vydáte dotaz na ID, dokud se operace nedokončí.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Použít snímek

Pomocí operace **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** napíšete nově nahraná data ze strany do cílového předplatného. Tato metoda také vrací ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Znovu dotaz na ID, dokud se operace nedokončí.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Po dokončení těchto kroků máte k vašim novým (cílovým) předplatnému přístup k konstrukcím vašich obličejových dat.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pro rozpoznávání tváře z adresáře aplikace pomocí `go run <app-name>` příkazu.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste v tomto rychlém startu vytvořili pole **Person** a chcete ho odstranit, zavolejte metodu **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** . Pokud jste migrovali data pomocí funkce snímku v tomto rychlém startu, budete také muset odstranit **pracovní** buňku uloženou do cílového předplatného.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak použít klientské knihovny pro tvář k přechodu na základní úkoly rozpoznávání obličeje. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na Face API (Přejít)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Co je služba Rozpoznávání tváře?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
