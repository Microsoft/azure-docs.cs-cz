---
title: 'Rychlý Start: značení formulářů, výuka modelu a analýza formulářů pomocí nástroje pro výběr popisků – rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete vzorový Nástroj pro rozpoznávání popisů formulářů k ručnímu označení dokumentů formuláře. Pak vytvoříte vlastní model zpracování dokumentů s označenými dokumenty a použijete model k extrakci párů klíč/hodnota.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: zpracování dokumentů
ms.openlocfilehash: 9642f9ce51cd3eb90344f96bc099da7adea93022
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364783"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Výukový model pro rozpoznávání formulářů pomocí popisků pomocí nástroje pro vzorkování popisků

V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s ukázkovým nástrojem pro označování, pomocí kterého můžete vytvořit vlastní model zpracování dokumentů s ručně označenými daty. Další informace o studiu pod dohledem pomocí nástroje pro rozpoznávání formulářů najdete v části [výuka s visačkami](../overview.md#train-with-labels) v přehledu.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu musíte mít:

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Sada alespoň šest forem stejného typu. Tato data použijete ke školení modelu a testování formuláře. Pro tento rychlý Start můžete použít [ukázkovou datovou sadu](https://go.microsoft.com/fwlink/?linkid=2090451) (stažení a extrakci *sample_data.zip*). Nahrajte školicí soubory do kořenového adresáře kontejneru úložiště objektů BLOB v účtu Azure Storage úrovně Standard-Performance.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Vyzkoušet

Pokud si chcete vyzkoušet, jak se na nástroji pro rozpoznávání formulářů přejít online, navštivte [Web FOTT](https://fott-preview.azurewebsites.net/).

# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Vyzkoušet předem připravené modely](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)
> [!div class="nextstepaction"]
> [Vyzkoušet předem připravené modely](https://fott-preview.azurewebsites.net/)

---

K vyzkoušení služby pro rozpoznávání formulářů budete potřebovat předplatné Azure ([můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services)) a koncový bod a klíč [prostředku pro rozpoznávání formuláře](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 


## <a name="set-up-the-sample-labeling-tool"></a>Nastavení nástroje pro označování ukázek

Pomocí modulu Docker spustíte ukázkový nástroj pro označování vzorků. Pro nastavení kontejneru Docker použijte následující postup. Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).

> [!TIP]
> Nástroj pro označování ve formě rozpoznávání znaků je také k dispozici jako open source projekt na GitHubu. Nástroj je webová aplikace TypeScript vytvořená pomocí reakce + Redux. Další informace nebo informace o tom, jak se přispívat, najdete v tématu o úložišti [nástrojů pro označování v rozpoznávání](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) Pokud si chcete nástroj vyzkoušet online, přečtěte si [Web FOTT](https://fott.azurewebsites.net/).   

1. Nejdřív nainstalujte Docker na hostitelském počítači. V této příručce se dozvíte, jak používat místní počítač jako hostitele. Pokud chcete použít hostující službu Docker v Azure, přečtěte si průvodce [nasazením ukázkového popisu nástroje pro označování](../deploy-label-tool.md) . 

   Hostitelský počítač musí splňovat následující požadavky na hardware:

    | Kontejner | Minimum | Doporučeno|
    |:--|:--|:--|
    |Ukázkový nástroj pro popisky|2 jádra, 4 GB paměti|4 jádra, 8 GB paměti|

   Nainstalujte do počítače Docker podle příslušných pokynů pro váš operační systém: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)



1. Získejte vzorový kontejner nástrojů pro označování pomocí `docker pull` příkazu.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
    # <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
    ```

    ---

1. Teď jste připraveni spustit kontejner pomocí `docker run` .

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```
    # <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept    
    ```

    --- 

   Tento příkaz zpřístupní ukázkovou jmenovku nástroje prostřednictvím webového prohlížeče. Přejděte na `http://localhost:3000`.

> [!NOTE]
> Můžete také označovat dokumenty a modely výuky pomocí REST API pro rozpoznávání formulářů. Chcete-li provést výuku a analýzu pomocí REST API, přečtěte si téma [výuka s popisky pomocí REST API a Pythonu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="set-up-input-data"></a>Nastavení vstupních dat

Nejprve se ujistěte, že všechny školicí dokumenty mají stejný formát. Pokud máte formuláře v několika formátech, uspořádejte je do podsložek na základě společného formátu. Při trénování budete muset přesměrovat rozhraní API do podsložky.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurace sdílení prostředků mezi doménami (CORS)

Povolte CORS v účtu úložiště. V Azure Portal vyberte svůj účet úložiště a v levém podokně klikněte na kartu **CORS** . Na dolním řádku vyplňte následující hodnoty. Pak v horní části klikněte na **Uložit** .

* Povolené zdroje = * 
* Povolené metody = \[ Vybrat vše\]
* Povolené hlavičky = *
* Zveřejněné hlavičky = * 
* Maximální stáří = 200

> [!div class="mx-imgBorder"]
> ![Nastavení CORS v Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Připojení k nástroji Sample labeling Tool

Nástroj pro označování ukázek se připojuje ke zdroji (kde jsou původní formuláře) a cíli (kde exportuje vytvořené popisky a výstupní data).

Připojení je možné nastavit a sdílet mezi projekty. Používají model rozšiřitelného poskytovatele, takže můžete snadno přidat nové poskytovatele prostředků a cílů.

Pokud chcete vytvořit nové připojení, klikněte na ikonu **nová připojení** (plug-in) v levém navigačním panelu.

Vyplňte pole následujícími hodnotami:

* **Zobrazovaný název** – zobrazovaný název připojení.
* **Popis** – Popis projektu.
* **Adresa URL SAS** – adresa URL sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Blob Storage. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Načítání adresy URL SAS":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="Nastavení připojení nástroje Sample labeling Tool":::


## <a name="create-a-new-project"></a>Vytvoření nového projektu

V nástroji pro označování ukázkových popisků se v projektech ukládají vaše konfigurace a nastavení. Vytvořte nový projekt a vyplňte pole následujícími hodnotami:

* **Zobrazované jméno** – zobrazovaný název projektu
* **Token zabezpečení** – některá nastavení projektu můžou obsahovat citlivé hodnoty, jako jsou klíče rozhraní API nebo jiné sdílené tajné klíče. Každý projekt vygeneruje token zabezpečení, který lze použít k šifrování/dešifrování citlivých nastavení projektu. Tokeny zabezpečení můžete v nastavení aplikace najít kliknutím na ikonu ozubeného kolečka v dolní části levého navigačního panelu.
* **Připojení ke zdroji** – připojení k Azure Blob Storage, které jste vytvořili v předchozím kroku, který chcete použít pro tento projekt.
* **Cesta ke složce** – volitelné – Pokud se zdrojové formuláře nacházejí ve složce v kontejneru objektů blob, zadejte sem název složky.
* **Identifikátor URI služby pro rozpoznávání formulářů** – adresa URL koncového bodu rozpoznávání formuláře.
* **Klíč rozhraní API** – klíč předplatného pro rozpoznávání formulářů
* **Popis** -volitelné – Popis projektu

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Nová stránka projektu s ukázkovým nástrojem pro označování":::

## <a name="label-your-forms"></a>Popisek formulářů

Při vytváření nebo otevírání projektu se otevře okno Editor hlavních značek. Editor značek se skládá ze tří částí:

* Podokno náhledu s možností změny velikosti, které obsahuje posuvný seznam formulářů ze zdrojového připojení.
* Hlavní podokno editoru, které umožňuje použít značky.
* Podokno editor značek umožňující uživatelům upravovat, zamykat, měnit pořadí a odstraňovat značky. 

### <a name="identify-text-elements"></a>Identifikujte textové prvky

Kliknutím na možnost **spustit optické rozpoznávání znaků u všech souborů** v levém podokně získáte informace o rozložení textu pro každý dokument. Nástroj pro popisování vykreslí ohraničující rámečky kolem každého prvku textu.

Zobrazí také, které tabulky byly automaticky extrahovány. Extrahovanou tabulku zobrazíte kliknutím na ikonu tabulky nebo mřížky na levé straně dokumentu. V tomto rychlém startu, protože obsah tabulky se automaticky extrahuje, nebudeme označovat obsah tabulky, ale místo toho se spoléháme na automatizovanou extrakci.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Vizualizace tabulky v nástroji ukázka popisků":::

### <a name="apply-labels-to-text"></a>Použít popisky na text

V dalším kroku vytvoříte značky (popisky) a použijete je na textové prvky, které chcete modelu analyzovat.

# <a name="v20"></a>[v2.0](#tab/v2-0)  
1. Nejprve pomocí podokna editor značek vytvořte značky, které chcete identifikovat.
   1. Kliknutím **+** vytvoříte novou značku.
   1. Zadejte název značky.
   1. Stisknutím klávesy ENTER značku uložíte.
1. V hlavním editoru kliknutím vyberte slova z zvýrazněných textových prvků.
1. Klikněte na značku, kterou chcete použít, nebo stiskněte odpovídající klávesovou zkratku. Číselné klíče se přiřazují jako klávesové zkratky pro prvních 10 značek. Můžete změnit pořadí značek pomocí ikon šipky nahoru a dolů v podokně editor značek.
    > [!Tip]
    > Při označování formulářů mějte na paměti následující tipy.
    > * Pro každý vybraný textový prvek lze použít pouze jednu značku.
    > * Každou značku lze na stránce použít pouze jednou. Pokud se hodnota ve stejném formuláři objeví víckrát, vytvořte pro každou instanci různé značky. Například: "Faktura č. 1", "Faktura č. 2" atd.
    > * Značky nemohou být rozloženy mezi stránkami.
    > * Hodnoty popisků tak, jak se zobrazí ve formuláři; Nepokoušejte se rozdělit hodnotu do dvou částí se dvěma různými značkami. Například pole adresa musí být označeno s jednou značkou, i když pokrývá více řádků.
    > * Nezahrnujte klíče do polí s příznakem &mdash; pouze hodnoty.
    > * Data tabulky by měla být zjištěna automaticky a budou k dispozici v konečném výstupním souboru JSON. Pokud však model nerozpozná všechna data tabulky, můžete tato pole také označit ručně. Označte každou buňku v tabulce jiným popiskem. Pokud mají vaše formuláře tabulky s různými počty řádků, ujistěte se, že jste si vystavili alespoň jednu formu s největší možnou tabulkou.
    > * Pomocí tlačítek napravo od **+** můžete vyhledat, přejmenovat, změnit pořadí a odstranit značky.
    > * Chcete-li odebrat použitou značku bez odstranění samotné značky, vyberte tagovaný obdélník v zobrazení dokumentu a stiskněte klávesu DELETE.


# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1) 
1. Nejprve pomocí podokna editor značek vytvořte značky, které chcete identifikovat.
   1. Kliknutím **+** vytvoříte novou značku.
   1. Zadejte název značky.
   1. Stisknutím klávesy ENTER značku uložíte.
1. V hlavním editoru kliknutím vyberte slova z zvýrazněných textových prvků. V rozhraní API _verze 2.1 Preview. 2_ můžete také kliknout na možnost vybrat _značky výběru_ , jako jsou přepínače a zaškrtávací políčka jako páry klíč-hodnota. Nástroj pro rozpoznávání formulářů určí, zda je jako hodnota vybrána možnost "výběr" nebo "Nevybráno".
1. Klikněte na značku, kterou chcete použít, nebo stiskněte odpovídající klávesovou zkratku. Číselné klíče se přiřazují jako klávesové zkratky pro prvních 10 značek. Můžete změnit pořadí značek pomocí ikon šipky nahoru a dolů v podokně editor značek.
    > [!Tip]
    > Při označování formulářů mějte na paměti následující tipy.
    > * Pro každý vybraný textový prvek lze použít pouze jednu značku.
    > * Každou značku lze na stránce použít pouze jednou. Pokud se hodnota ve stejném formuláři objeví víckrát, vytvořte pro každou instanci různé značky. Například: "Faktura č. 1", "Faktura č. 2" atd.
    > * Značky nemohou být rozloženy mezi stránkami.
    > * Hodnoty popisků tak, jak se zobrazí ve formuláři; Nepokoušejte se rozdělit hodnotu do dvou částí se dvěma různými značkami. Například pole adresa musí být označeno s jednou značkou, i když pokrývá více řádků.
    > * Nezahrnujte klíče do polí s příznakem &mdash; pouze hodnoty.
    > * Data tabulky by měla být zjištěna automaticky a budou k dispozici v konečném výstupním souboru JSON. Pokud však model nerozpozná všechna data tabulky, můžete tato pole také označit ručně. Označte každou buňku v tabulce jiným popiskem. Pokud mají vaše formuláře tabulky s různými počty řádků, ujistěte se, že jste si vystavili alespoň jednu formu s největší možnou tabulkou.
    > * Pomocí tlačítek napravo od **+** můžete vyhledat, přejmenovat, změnit pořadí a odstranit značky.
    > * Chcete-li odebrat použitou značku bez odstranění samotné značky, vyberte tagovaný obdélník v zobrazení dokumentu a stiskněte klávesu DELETE.


---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="Okno hlavního editoru nástroje pro ukázkové označování":::


Postupujte podle výše uvedených kroků a označte alespoň pět vašich forem.

### <a name="specify-tag-value-types"></a>Zadejte typy hodnot značek

Volitelně můžete pro každou značku nastavit očekávaný datový typ. Otevřete kontextovou nabídku napravo od značky a vyberte typ z nabídky. Tato funkce umožňuje, aby algoritmus detekce vytvářely určité předpoklady, které budou zlepšit přesnost detekce textu. Také zajišťuje, že zjištěné hodnoty budou vráceny ve standardizovaném formátu v konečném výstupu JSON. Informace o typu hodnoty jsou uloženy v *fields.js* souboru ve stejné cestě jako soubory popisků.

> [!div class="mx-imgBorder"]
> ![Výběr typu hodnoty pomocí nástroje pro vzorkování popisků](../media/whats-new/value-type.png)

V současné době jsou podporovány následující typy hodnot a variace:
* `string`
    * výchozí, `no-whitespaces` , `alphanumeric`
* `number`
    * výchozí `currency`
* `date` 
    * výchozí, `dmy` , `mdy` , `ymd`
* `time`
* `integer`
* `selectionMark` – _Novinka v verze 2.1 – Preview. 1!_

> [!NOTE]
> Tato pravidla se zobrazují pro formátování data:
> 
> Je nutné zadat formát ( `dmy` , `mdy` , `ymd` ) pro práci formátování data.
>
> Následující znaky lze použít jako oddělovače data: `, - / . \` . Prázdný znak nelze použít jako oddělovač. Příklad:
> * 01, 01, 2020
> * 01-01-2020
> * 01/01/2020
>
> Každý den a měsíc lze zapsat jako jednu nebo dvě číslice a rok může být dvě nebo čtyři číslice:
> * 1-1-2020
> * 1-01-20
>
> Pokud má řetězec data osm číslic, je oddělovač volitelný:
> * 01012020
> * 01 01 2020
>
> Měsíc může být také zapsán jako úplný nebo krátký název. Pokud je název použit, jsou znaky oddělovače volitelné. Tento formát je však možné rozpoznat méně přesně než jiné.
> * 01/leden/2020
> * 01Jan2020
> * 01 1. ledna 2020

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Kliknutím na ikonu výuka v levém podokně otevřete stránku školení. Potom kliknutím na tlačítko **výuka** začněte s modelem školení. Po dokončení procesu školení se zobrazí následující informace:

* **ID modelu** – ID modelu, který byl vytvořen a vyškolený. Každý školicí hovor vytvoří nový model s vlastním ID. Zkopírujte tento řetězec do zabezpečeného umístění; budete ho potřebovat, pokud chcete provést prediktivní volání prostřednictvím [REST API](./client-library.md?pivots=programming-language-rest-api) nebo [klientské knihovny](./client-library.md).
* **Průměrná přesnost** – Průměrná přesnost modelu Přesnost modelu můžete vylepšit tak, že si znovu označíte další formuláře a školení a vytvoříte nový model. Doporučujeme začít tím, že budete označovat pět forem a podle potřeby přidat další formuláře.
* Seznam značek a předpokládaná přesnost na značku.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Školicí zobrazení.":::

Po dokončení školení si Projděte hodnotu **Průměrná přesnost** . Pokud je nízká, měli byste přidat další vstupní dokumenty a opakovat výše uvedené kroky. Již označené dokumenty zůstanou v indexu projektu.

> [!TIP]
> Můžete také spustit školicí proces s voláním REST API. Další informace o tom, jak to udělat, najdete v tématu [výuka s popisky pomocí Pythonu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="compose-trained-models"></a>Vytváření vycvičených modelů

# <a name="v20"></a>[v2.0](#tab/v2-0)  

Tato funkce je aktuálně dostupná v v 2.1. Tisk. 

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1) 

S využitím funkce Model Compose můžete vytvořit až 100 modelů s jedním ID modelu. Když zavoláte analýzu s tímto složeným ID modelu, Rozpoznávání formulářů nejprve provede klasifikaci odeslaného formuláře, najde shodu s nejlépe odpovídajícím modelem a pak vrátí výsledky pro tento model. To je užitečné v případě, že příchozí formuláře můžou patřit k jedné z několika šablon.

Chcete-li vytvořit modely v nástroji pro označování ukázkových popisků, klikněte na ikonu vytvořit model (šipka vlevo) na levé straně. Na levé straně vyberte modely, které chcete vytvořit dohromady. Modely s ikonou šipky jsou již vytvořeny jako modely. Klikněte na tlačítko "vytvořit". V automaticky otevíraném okně pojmenujte nový složený model a klikněte na "vytvořit". Po dokončení operace by se nový sestavený model měl zobrazit v seznamu. 

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="Zobrazení uživatelského rozhraní pro vytváření modelů.":::

---

## <a name="analyze-a-form"></a>Analýza formuláře 

Kliknutím na ikonu prediktivní (žárovky) na levé straně otestujete svůj model. Nahrajte dokument formuláře, který jste nepoužili v procesu školení. Pak klikněte na tlačítko **předpověď** na pravé straně, abyste pro formulář získali předpovědi klíč/hodnota. Nástroj bude používat značky v ohraničujících polích a bude hlásit spolehlivost každé značky.

> [!TIP]
> Můžete také spustit rozhraní API pro analýzu pomocí volání REST. Další informace o tom, jak to udělat, najdete v tématu [výuka s popisky pomocí Pythonu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="improve-results"></a>Zlepšení výsledků

V závislosti na zaznamenané přesnosti možná budete chtít provést další školení pro zlepšení modelu. Po dokončení předpovědi si Projděte hodnoty spolehlivosti pro každou použitou značku. Pokud průměrná hodnota školení pro přesnost byla vysoká, ale hodnocení spolehlivosti je nízké (nebo výsledky jsou nepřesné), měli byste přidat soubor, který se použije pro předpověď do sady školení, označit ho a znovu ho naučit.

Uvedená Průměrná přesnost, hodnocení spolehlivosti a skutečná přesnost můžou být nekonzistentní, když se analyzované dokumenty liší od těch, které se použily při výuce. Mějte na paměti, že některé dokumenty vypadají při prohlížení lidem podobně, ale mohou se lišit od modelu AI. Můžete například vytvořit typ formuláře, který má dvě varianty, kde se sada školení skládá z 20% variace a a 80% variace B. Při předpovědi je pravděpodobně možné snížit skóre pro dokumenty variace A.

## <a name="save-a-project-and-resume-later"></a>Uložení projektu a pozdější pokračování

Pokud chcete projekt obnovit v jinou dobu nebo v jiném prohlížeči, je nutné uložit token zabezpečení projektu a později ho znovu zadat. 

### <a name="get-project-credentials"></a>Získat přihlašovací údaje projektu
Přejít na stránku nastavení projektu (ikona posuvníku) a poznamenejte si název tokenu zabezpečení. Pak přejdete na nastavení aplikace (ikona ozubeného kolečka), ve kterém se zobrazí všechny tokeny zabezpečení v aktuální instanci prohlížeče. Vyhledejte token zabezpečení projektu a zkopírujte jeho název a hodnotu klíče do zabezpečeného umístění.

### <a name="restore-project-credentials"></a>Obnovit přihlašovací údaje projektu
Pokud chcete pokračovat v projektu, musíte nejprve vytvořit připojení ke stejnému kontejneru úložiště objektů BLOB. Provedete to tak, že zopakujete výše uvedené kroky. Pak přejděte na stránku nastavení aplikace (ikona ozubeného kolečka) a podívejte se, jestli je k dispozici token zabezpečení vašeho projektu. Pokud ne, přidejte nový token zabezpečení a zkopírujte ho do svého názvu a klíče tokenu z předchozího kroku. Pak klikněte na Uložit nastavení. 

### <a name="resume-a-project"></a>Pokračování projektu

Nakonec přejděte na hlavní stránku (ikonu domu) a klikněte na otevřít cloudový projekt. Pak vyberte připojení úložiště objektů BLOB a vyberte soubor *. Fott* vašeho projektu. Aplikace načte všechna nastavení projektu, protože má token zabezpečení.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak pomocí nástroje pro rozpoznávání popisů formulářů vytvořit výuku modelu s ručně označenými daty. Pokud chcete vytvořit vlastní nástroj pro popisek školicích dat, použijte rozhraní REST API, které se týká školení s popisky dat.

> [!div class="nextstepaction"]
> [Výuka s popisky pomocí Pythonu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Co je služba Rozpoznávání formulářů?](../overview.md)
* [Rychlé zprovoznění pro rozpoznávání formulářů](client-library.md)
