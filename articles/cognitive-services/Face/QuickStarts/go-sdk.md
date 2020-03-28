---
title: 'Úvodní příručka: Knihovna klienta Face pro Go | Dokumenty společnosti Microsoft'
description: Začínáme s klientskou knihovnou Face pro Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76941427"
---
# <a name="quickstart-face-client-library-for-go"></a>Úvodní příručka: Klientská knihovna Face pro Go

Začínáme s klientskou knihovnou Face pro Go. Podle těchto kroků nainstalujte knihovnu a vyzkoušejte naše příklady základních úkolů. Služba Face vám poskytuje přístup k pokročilým algoritmům pro detekci a rozpoznávání lidských tváří v obrazech.

Pomocí klientské knihovny služby Plocha pro přejít na:

* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Vyhledání podobných tváří](#find-similar-faces)
* [Vytvoření a trénování skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořízení snímku pro migraci dat](#take-a-snapshot-for-data-migration)

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK ke stažení](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Nejnovější verze [Go](https://golang.org/dl/)

## <a name="set-up"></a>Nastavit

### <a name="create-a-face-azure-resource"></a>Vytvoření prostředku Face Azure 

Začněte používat službu Face vytvořením prostředku Azure. Vyberte typ prostředku, který je pro vás ten pravý:

* [Zkušební prostředek](https://azure.microsoft.com/try/cognitive-services/#decision) (není potřeba žádné předplatné Azure): 
    * Platí sedm dní, zdarma. Po registraci bude zkušební klíč a koncový bod k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * To je skvělá volba, pokud chcete vyzkoušet službu Face, ale nemáte předplatné Azure.
* [Zdroj služby Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace):
    * K dispozici prostřednictvím portálu Azure, dokud neodstraníte prostředek.
    * Využijte bezplatnou cenovou úroveň k vyzkoušení služby a upgradujte později na placenou úroveň pro produkční prostředí.
* [Prostředek s více službami](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne):
    * K dispozici prostřednictvím portálu Azure, dokud neodstraníte prostředek.  
    * Použijte stejný klíč a koncový bod pro vaše aplikace, napříč několika službami Cognitive Services.

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

>[!NOTE]
> Koncové body pro nezkušební prostředky vytvořené po 1. Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Pomocí klíče a koncového bodu z prostředku, který jste vytvořili, vytvořte dvě proměnné prostředí pro ověřování:
* `FACE_SUBSCRIPTION_KEY`- Klíč zdroje pro ověřování vašich požadavků.
* `FACE_ENDPOINT`- Koncový bod prostředku pro odesílání požadavků rozhraní API. Bude to vypadat takto: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Použijte pokyny pro váš operační systém.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
***

### <a name="create-a-go-project-directory"></a>Vytvoření adresáře projektu Go

V okně konzoly (cmd, PowerShell, Terminál, Bash) vytvořte nový `my-app`pracovní prostor pro projekt Go s názvem a přejděte na něj.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Pracovní prostor bude obsahovat tři složky:

* **src** - Tento adresář bude obsahovat zdrojový kód a balíčky. Všechny balíčky `go get` nainstalované s příkazem budou v této složce.
* **pkg** - Tento adresář bude obsahovat zkompilované objekty balíčku Go. Všechny tyto soubory `.a` mají příponu.
* **bin** - Tento adresář bude obsahovat binární spustitelné `go install`soubory, které jsou vytvořeny při spuštění .

> [!TIP]
> Další informace o struktuře pracovního prostoru Go najdete v [dokumentaci k jazykům Go](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje `$GOPATH` `$GOROOT`informace pro nastavení a .

### <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro Přejít

Dále nainstalujte klientskou knihovnu pro Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

nebo pokud používáte dep, v rámci vašeho repo běhu:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Vytvoření aplikace Go

Dále vytvořte soubor v adresáři `sample-app.go` **src** s názvem :

```bash
cd src
touch sample-app.go
```

Otevřete `sample-app.go` v upřednostňovaném rozhraní IDE nebo textovém editoru. Potom přidejte název balíčku a importujte následující knihovny:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Dále začnete přidávat kód k provádění různých operací služby Face Service.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Face Go SDK.

|Name (Název)|Popis|
|---|---|
|[Základní klient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Tato třída představuje oprávnění k používání služby Face a potřebujete ji pro všechny funkce Face. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd. |
|[Klienta](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Tato třída zpracovává základní úlohy zjišťování a rozpoznávání, které můžete dělat s lidskými plochami. |
|[Zjištěná plocha](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Tato třída představuje všechna data, která byla zjištěna z jedné plochy v obraze. Můžete ji použít k načtení podrobných informací o ploše.|
|[Klient a seznam](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Tato třída spravuje cloudové konstrukce **FaceList,** které ukládají různé sady ploch. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Tato třída spravuje cloudové konstrukce **Person,** které ukládají sadu ploch, které patří jedné osobě.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Tato třída spravuje cloudové konstrukce **PersonGroup,** které ukládají sadu tříděných objektů **Person.** |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Tato třída spravuje funkce snímek. Můžete ji použít k dočasnému uložení všech dat na bázi cloudu a migraci těchto dat do nového předplatného Azure. |

## <a name="code-examples"></a>Příklady kódu

Tyto ukázky kódu ukazují, jak dokončit základní úkoly pomocí knihovny klienta služby Face pro Go:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Vyhledání podobných tváří](#find-similar-faces)
* [Vytvoření a trénování skupiny osob](#create-and-train-a-person-group)
* [Identifikace obličeje](#identify-a-face)
* [Pořízení snímku pro migraci dat](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE] 
> Tento rychlý start předpokládá, že jste [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` klíč Face a koncový bod s názvem a v uvedeném pořadí.

Vytvořte **hlavní** funkci a přidejte do ní následující kód pro vytvoření instance klienta pomocí koncového bodu a klíče. Vytvoření **[Objektu CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** s klíčem a použít jej s koncovým bodem k vytvoření objektu **[Klienta.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** Tento kód také konkretizovat objekt kontextu, který je potřebný pro vytváření objektů klienta. Definuje také vzdálené umístění, kde jsou nalezeny některé ukázkové obrázky v tomto rychlém startu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Přidejte následující kód v **hlavní** metodě. Tento kód definuje vzdálený ukázkový obraz a určuje, které prvky plochy se mají z obrazu extrahovat. Také určuje, který model AI se má použít k extrahování dat z detekovaných ploch. Informace o těchto možnostech naleznete [v tématu Určení modelu rozpoznávání.](../Face-API-How-to-Topics/specify-recognition-model.md) Nakonec **[detectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** metoda provádí operaci detekce obličeje na obrázku a uloží výsledky v paměti programu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Zobrazit zjištěná data plochy

Další blok kódu přebírá první prvek v poli **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** objekty a vytiskne jeho atributy do konzoly. Pokud jste použili obraz s více plochami, měli byste místo toho iterate přes pole.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Následující kód přebírá jednu zjištěnou tvář (zdroj) a prohledává sadu dalších tváří (cíl) najít shody. Když najde shodu, vytiskne ID odpovídající plochy do konzoly.

### <a name="detect-faces-for-comparison"></a>Rozpoznat plochy pro porovnání

Nejprve uložte odkaz na plochu, kterou jste zjistili v části Rozpoznat plochy v části [obrázku.](#detect-faces-in-an-image) Tahle tvář bude zdrojem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Poté zadejte následující kód pro detekci sady ploch v jiném obrázku. Tyto tváře budou cílem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Najít shody

Následující kód používá **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** metoda najít všechny cílové plochy, které odpovídají zdrojové plochy.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Tisk shod

Následující kód vytiskne podrobnosti shody do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Vytvoření a trénování skupiny osob

Chcete-li krokovat v tomto scénáři, je třeba uložit https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesnásledující obrázky do kořenového adresáře projektu: .

Tato skupina obrázků obsahuje tři sady jednoobličejových obrázků, které odpovídají třem různým lidem. Kód definuje tři objekty **PersonGroup Person a** přidružení k obrazovým souborům, které začínají v , `woman` `man`a `child`.

### <a name="create-persongroup"></a>Vytvořit skupinu osob

Po stažení obrázků přidejte následující kód do dolní části **hlavní** metody. Tento kód ověří objekt **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** a potom jej použije k definování nové **skupiny PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Vytvořit osoby skupiny PersonGroup

Další blok kódu ověří **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** a použije jej k definování tří nových objektů **PersonGroup Person.** Tyto objekty představují jednu osobu v sadě obrázků.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Přiřazení ploch osobám

Následující kód seřadí obrazy podle jejich předpony, detekuje plochy a přiřadí plochy každému příslušnému objektu **PersonGroup Person,** na základě názvu souboru obrázku.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Vlak PersonGroup

Po přiřazení tváří trénujete **skupinu Osob,** aby mohla identifikovat vizuální prvky přidružené ke každému z jejích objektů **Person.** Následující kód volá metodu asynchronního **trainu** a vyvolá výsledek a tiskne stav do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identifikace obličeje

Následující kód pořídí obrázek s více plochami a vyhledá identitu každé osoby v obrázku. Porovnává každou detekoci tvář **persongroup**, databáze různých osob **objekty,** jejichž rysy obličeje jsou známy.

> [!IMPORTANT]
> Chcete-li spustit tento příklad, musíte nejprve spustit kód v [vytvořit a trénování skupiny osob](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Získání testovacího obrázku

Následující kód vyhledá v kořenovém adresáři projektu _pro image test-image-person-group.jpg_ a načte jej do paměti programu. Tento obrázek najdete ve stejném repo jako obrázky použité v [části Vytvořit a trénovat skupinu osob](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Detekce zdrojových tváří v testovacím obrázku

Další blok kódu provádí běžnou detekci obličeje na testovacím obrázku, aby načetl všechny plochy a uložil je do pole.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identifikace tváří

Identify **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** Metoda trvá pole zjištěných tváří a porovná je s danou **PersonGroup** (definované a vyškolené v předchozí části). Pokud může spárovat zjištěnou tvář **s osobou** ve skupině, uloží výsledek.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Tento kód pak vytiskne podrobné výsledky shody do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Ověřit plochy

Operace Ověření přebírá ID tváře a jiné ID plochy nebo objekt **Person** a určuje, zda patří stejné osobě.

Následující kód detekuje tváře ve dvou zdrojových obrazech a poté ověří každou z nich proti ploše zjištěné z cílového obrázku.

### <a name="get-test-images"></a>Získat testovací obrázky

Následující bloky kódu deklarují proměnné, které budou ukazovat na cílové a zdrojové bitové kopie pro ověřovací operaci.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Detekce ploch pro ověření

Následující kód detekuje tváře ve zdrojových a cílových obrázcích a uloží je do proměnných.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Získání výsledků ověření

Následující kód porovná každý zdrojový obrázek s cílovým obrázkem a vytiskne zprávu s informací, zda patří stejné osobě.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Pořízení snímku pro migraci dat

Funkce Snímky umožňuje přesunout uložená data tváře, jako je například trénovaná **PersonGroup**, do jiného předplatného služby Azure Cognitive Services Face. Tuto funkci můžete použít například v případě, že jste vytvořili objekt **PersonGroup** pomocí bezplatného zkušebního předplatného a nyní jej chcete migrovat do placeného předplatného. Podívejte se na podrobné informace o funkci Snímky v [tématu Migrace osobních dat.](../Face-API-How-to-Topics/how-to-migrate-face-data.md)

V tomto příkladu budete migrovat **PersonGroup,** kterou jste vytvořili v [části Vytvořit a trénovat skupinu osob](#create-and-train-a-person-group). Můžete buď nejprve vyplnit tento oddíl, nebo použít vlastní konstrukce dat tváře.

### <a name="set-up-target-subscription"></a>Nastavení cílového předplatného

Nejprve musíte mít druhé předplatné Azure s prostředkem Face; můžete to provést opakováním kroků v části [Nastavení.](#set-up) 

Potom vytvořte následující proměnné v horní části **hlavní** metody. Budete také muset vytvořit nové proměnné prostředí pro ID předplatného vašeho účtu Azure, stejně jako klíč, koncový bod a ID předplatného vašeho nového (cílového) účtu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Potom vložte hodnotu ID předplatného do pole pro další kroky.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Ověření cílového klienta

Později ve skriptu uložte původní objekt klienta jako zdrojového klienta a potom ověřte nový objekt klienta pro cílové předplatné. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Pořízení snímku

Dalším krokem je pořízení snímku pomocí **[funkce Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, která uloží data tváře původního předplatného do dočasného umístění v cloudu. Tato metoda vrátí ID, které používáte k dotazování stav operace.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Dále dotaz ID, dokud operace byla dokončena.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Použití snímku

Pomocí operace **[Použít](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** zapište nově nahraná data tváře do cílového předplatného. Tato metoda také vrátí ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Znovu dotaz ID, dokud operace byla dokončena.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Po dokončení těchto kroků můžete přistupovat k vašim konstrukcím dat obličeje z nového (cílového) předplatného.

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci `go run [arguments]` Go pomocí příkazu z adresáře aplikace.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste v tomto rychlém startu **vytvořili skupinu osob** a chcete ji odstranit, zavolejte metodu **[Delete.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** Pokud jste v tomto rychlém startu migrovali data pomocí funkce Snímek, budete muset také odstranit **PersonGroup** uloženou do cílového předplatného.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat knihovnu tváří pro základní úkoly Přejít na základy. Dále prozkoumejte referenční dokumentaci, abyste se dozvěděli více o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na rozhraní API pro obličej (Přejít)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Co je služba Rozpoznávání tváře?](../overview.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
