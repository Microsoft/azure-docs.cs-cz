---
title: 'Úvodní příručka: Popisky formulářů, trénování modelu a analýza formuláře pomocí nástroje pro označování vzorků – Nástroj pro rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete nástroj pro rozpoznávání vzorků formuláře k ručnímu označení dokumentů formuláře. Pak budete trénovat vlastní model s popiskovanými dokumenty a pomocí modelu extrahovat páry klíč/hodnota.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 790e2a148385f9da54df82f597c2ca52124dc2be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529841"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Trénování modelu rozpoznávání formulářů pomocí popisků pomocí nástroje pro označování vzorků

V tomto rychlém startu použijete rozhraní REST API nástroje pro rozpoznávání formulářů s ukázkovým nástrojem pro označování k trénování vlastního modelu s ručně označenými daty. Další informace o této funkci najdete v části [Vlak s popisky](../overview.md#train-with-labels) v přehledu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento rychlý start, musíte mít:

- Sada nejméně šesti forem stejného typu. Tato data použijete k trénování modelu a testování formuláře. Pro tento rychlý start můžete použít [ukázkovou sadu dat.](https://go.microsoft.com/fwlink/?linkid=2090451) Nahrajte školicí soubory do kořenového adresáře kontejneru úložiště objektů blob v účtu Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Nastavení ukázkového štítku

Budete používat modul Docker ke spuštění ukázkového štítku. Podle těchto kroků nastavte kontejner Dockeru. Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).

> [!TIP]
> Nástroj pro popisování formulářů OCR je také k dispozici jako open source projekt na GitHubu. Nástroj je webová aplikace postavena pomocí React + Redux, a je napsán v TypeScriptu. Další informace nebo příspěvky naleznete [v tématu Nástroj pro označování formulářů OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application).

1. Nejprve nainstalujte Docker do hostitelského počítače. Tato příručka vám ukáže, jak používat místní počítač jako hostitele. Pokud chcete používat hostingovou službu Dockeru v Azure, [přečtěte si návod, jak nasadit ukázkový nástroj pro popisy.](../deploy-label-tool.md) 

   Hostitelský počítač musí splňovat následující požadavky na hardware:

    | Kontejner | Minimální | Doporučené|
    |:--|:--|:--|
    |Nástroj pro označování vzorků|2 jádra, 4 GB paměti|4 jádra, 8 GB paměti|

   Nainstalujte Docker do počítače podle příslušných pokynů pro váš operační systém: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Získejte kontejner nástrojů pro `docker pull` označování vzorků pomocí příkazu.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Nyní jste připraveni spustit kontejner `docker run`s .
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Tento příkaz zpřístupní ukázkový nástroj pro popisky prostřednictvím webového prohlížeče. Přejděte [http://localhost:3000](http://localhost:3000)na .

> [!NOTE]
> Dokumenty a trénování modelů můžete také označit pomocí rozhraní REST API nástroje PRO ROZPOZNÁVÁNÍ FORMULÁŘŮ. Pokud chcete trénovat a analyzovat pomocí rozhraní REST API, přečtěte si informace [o trénování pomocí popisků pomocí rozhraní REST API a Pythonu](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Nastavení vstupních dat

Nejprve se ujistěte, že všechny školicí dokumenty jsou ve stejném formátu. Pokud máte formuláře ve více formátech, uspořádejte je do podsložek na základě běžného formátu. Když trénujete, budete muset rozhraní API nasměrovat do podsložky.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurace sdílení prostředků mezi doménami (CORS)

Povolte CORS na svém účtu úložiště. Vyberte svůj účet úložiště na webu Azure Portal a klikněte na kartu **CORS** v levém podokně. Na spodním řádku vyplňte následující hodnoty. Nahoře klikněte na **Uložit.**

* Povolené počátky = * 
* Povolené metody \[= vybrat všechny\]
* Povolené hlavičky = *
* Exponované hlavičky = * 
* Maximální věk = 200

> [!div class="mx-imgBorder"]
> ![Nastavení CORS na webu Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Připojení k nástroji pro popisek vzorku

Ukázkový nástroj pro popisování se připojuje ke zdroji (kde jsou původní formuláře) a cíli (kde exportuje vytvořené popisky a výstupní data).

Připojení lze nastavit a sdílet mezi projekty. Používají rozšiřitelný model zprostředkovatele, takže můžete snadno přidat nové poskytovatele zdrojů a cílů.

Chcete-li vytvořit nové připojení, klepněte na ikonu **Nová připojení** (zástrčka) na levém navigačním panelu.

Vyplňte pole následujícími hodnotami:

* **Zobrazovaný název** - Zobrazovaný název připojení.
* **Popis** - Popis projektu.
* **Adresa URL SAS** – adresa URL sdíleného přístupového podpisu (SAS) kontejneru úložiště objektů blob Azure. Chcete-li načíst adresu URL SAS, otevřete Průzkumníka úložiště Microsoft Azure, klikněte pravým tlačítkem myši na kontejner a vyberte **získat sdílený přístupový podpis**. Nastavte dobu vypršení platnosti na určitou dobu poté, co budete službu používat. Zkontrolujte, zda jsou zaškrtnuta oprávnění **Číst**, **Zápis**, **Odstranit**a **Seznam,** a klepněte na tlačítko **Vytvořit**. Pak zkopírujte hodnotu v části **ADRESA URL.** Měl by mít `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formu: .

![Nastavení připojení ukázkového štítkovacího nástroje](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Vytvoření nového projektu

V ukázkovém štítkovacím nástroji projekty ukládají konfigurace a nastavení. Vytvořte nový projekt a vyplňte pole následujícími hodnotami:

* **Zobrazovaný název** - zobrazovaný název projektu
* **Token zabezpečení** – některá nastavení projektu mohou obsahovat citlivé hodnoty, například klíče rozhraní API nebo jiné sdílené tajné klíče. Každý projekt vygeneruje token zabezpečení, který lze použít k šifrování nebo dešifrování citlivých nastavení projektu. Bezpečnostní tokeny najdete v nastavení aplikace kliknutím na ikonu ozubeného kola v dolní části levého navigačního panelu.
* **Zdrojpřipojení** – Azure blob storage připojení, které jste vytvořili v předchozím kroku, který chcete použít pro tento projekt.
* **Cesta ke složce** – volitelné – pokud jsou zdrojové formuláře umístěny ve složce kontejneru objektů blob, zadejte název složky zde
* **Identifikátor URI služby pro rozpoznávání** formulářů – adresa URL koncového bodu nástroje pro rozpoznávání formulářů.
* **Klíč rozhraní API** – klíč předplatného nástroje pro rozpoznávání formulářů.
* **Popis** - Nepovinné - Popis projektu

![Nová stránka projektu v nástroji pro ukázkové popisky](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Označení formulářů

Když vytvoříte nebo otevřete projekt, otevře se okno hlavního editoru značek. Editor značek se skládá ze tří částí:

* Podokno náhledu s nastavitelnou pomocí, které obsahuje posuvný seznam formulářů ze zdrojového připojení.
* Podokno hlavního editoru, které umožňuje použít značky.
* Podokno editoru značek, které uživatelům umožňuje upravovat, uzavírat, měnit pořadí a odstraňovat značky. 

### <a name="identify-text-elements"></a>Identifikace textových prvků

Chcete-li získat informace o rozložení textu pro každý dokument, klepněte na **tlačítko Spustit rozpoznávání OCR u všech souborů** v levém podokně. Nástroj pro popisky nakreslí ohraničovací rámečky kolem každého textového prvku.

### <a name="apply-labels-to-text"></a>Aplikování popisků na text

Dále vytvoříte značky (popisky) a použijete je na textové prvky, které má model rozpoznat.

1. Nejprve použijte podokno editoru značek k vytvoření značek, které chcete identifikovat.
   1. Klepnutím **+** vytvoříte novou značku.
   1. Zadejte název značky.
   1. Stisknutím klávesy Enter značku uložte.
1. V hlavním editoru klepnutím a tažením vyberte jedno nebo více slov ze zvýrazněných textových prvků.
1. Klikněte na značku, kterou chcete použít, nebo stiskněte odpovídající klávesu. Číselné klávesy jsou přiřazeny jako klávesové zkratky pro prvních 10 značek. Pořadí značek můžete nastavit pomocí ikon šipky nahoru a dolů v podokně editoru značek.
    > [!Tip]
    > Při označování formulářů mějte na paměti následující tipy.
    > * Na každý vybraný textový prvek lze aplikovat pouze jednu značku.
    > * Každou značku lze použít pouze jednou na stránku. Pokud se hodnota ve stejném formuláři zobrazí vícekrát, vytvořte pro každou instanci různé značky. Například: "faktura# 1", "faktura# 2" a tak dále.
    > * Značky nemohou přesahovat stránky.
    > * Popisek hodnot tak, jak jsou zobrazeny ve formuláři; nepokoušejte se rozdělit hodnotu na dvě části se dvěma různými značkami. Například pole adresy by mělo být označeno jednou značkou, i když pokrývá více řádků.
    > * Nezahrnujte do označených&mdash;polí klíče pouze hodnoty.
    > * Data tabulky by měla být detekována automaticky a budou k dispozici v konečném výstupním souboru JSON. Pokud se však modelu nepodaří zjistit všechna data tabulky, můžete tato pole označit ručně také. Označte každou buňku v tabulce jiným popiskem. Pokud formuláře obsahují tabulky s různým počtem řádků, nezapomeňte označit alespoň jeden formulář s největší možnou tabulkou.

![Hlavní okno editoru ukázkového štítkovacího nástroje](../media/label-tool/main-editor.png)

Podle výše uvedených kroků označte alespoň pět formulářů.

### <a name="specify-tag-value-types"></a>Určení typů hodnot tagů

Volitelně můžete nastavit očekávaný datový typ pro každou značku. Otevřete místní nabídku napravo od značky a vyberte z nabídky typ. Tato funkce umožňuje algoritmu zjišťování provádět určité předpoklady, které zlepší přesnost detekce textu. Také zajišťuje, že zjištěné hodnoty budou vráceny ve standardizovaném formátu v konečném výstupu JSON. 

> [!div class="mx-imgBorder"]
> ![Výběr typu hodnoty nástrojem pro popisek vzorku](../media/whats-new/formre-value-type.png)

V současné době jsou podporovány následující typy a varianty hodnot:
* `string`
    * výchozí, `no-whitespaces``alphanumeric`
* `number`
    * Výchozí`currency`
* `date` 
    * výchozí, `dmy` `mdy`, ,`ymd`
* `time`
* `integer`

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Kliknutím na ikonu Vlak v levém podokně otevřete stránku Školení. Poté klikněte na tlačítko **Vlak** a začněte trénovat model. Po dokončení procesu školení se zobrazí následující informace:

* **ID modelu** - ID modelu, který byl vytvořen a trénovaný. Každé volání školení vytvoří nový model s vlastním ID. Zkopírujte tento řetězec do zabezpečeného umístění. budete potřebovat, pokud chcete provést volání předpověď prostřednictvím rozhraní REST API.
* **Průměrná přesnost** – průměrná přesnost modelu. Přesnost modelu můžete zlepšit dalším označením a dalším školením a vytvořením nového modelu. Doporučujeme začít označením pěti formulářů a přidáním dalších formulářů podle potřeby.
* Seznam značek a odhadovaná přesnost na značku.

![zobrazení školení](../media/label-tool/train-screen.png)

Po dokončení školení zkontrolujte hodnotu **Průměrná přesnost.** Pokud je nízká, měli byste přidat další vstupní dokumenty a zopakovat výše uvedené kroky. Dokumenty, které jste již označili, zůstanou v indexu projektu.

> [!TIP]
> Můžete také spustit proces školení s voláním rozhraní REST API. Další informace o tom, jak to udělat, najdete [v tématu Trénování s popisky pomocí Pythonu](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analýza formuláře

Klikněte na ikonu Předpovědět (žárovka) vlevo a otestujte svůj model. Nahrajte dokument formuláře, který jste nepoužili v procesu školení. Potom klikněte na tlačítko **Předpovědět** vpravo, abyste získali předpovědi klíč/hodnota pro formulář. Nástroj použije značky v ohraničovacích polích a bude hlásit spolehlivost každé značky.

> [!TIP]
> Můžete také spustit analyzovat rozhraní API s voláním REST. Další informace o tom, jak to udělat, najdete [v tématu Trénování s popisky pomocí Pythonu](./python-labeled-data.md).

## <a name="improve-results"></a>Zlepšete výsledky

V závislosti na hlášené přesnosti můžete chtít provést další školení ke zlepšení modelu. Po dokončení předpovědi zkontrolujte hodnoty spolehlivosti pro každou z použitých značek. Pokud byla průměrná hodnota školení přesnosti vysoká, ale skóre spolehlivosti jsou nízké (nebo výsledky jsou nepřesné), měli byste přidat soubor použitý pro předpověď do sady školení, označit ji a znovu trénovat.

Hlášená průměrná přesnost, skóre spolehlivosti a skutečná přesnost mohou být nekonzistentní, pokud se analyzované dokumenty liší od dokumentů používaných při školení. Mějte na paměti, že některé dokumenty vypadají podobně, když je lidé prohlížejí, ale mohou vypadat odlišně od modelu AI. Můžete například trénovat s typem formuláře, který má dvě varianty, kde se tréninková sada skládá z 20 % varianty A a 80 % varianty B. Během predikce je pravděpodobné, že skóre spolehlivosti pro dokumenty varianty A bude nižší.

## <a name="save-a-project-and-resume-later"></a>Uložení projektu a pokračování později

Chcete-li pokračovat v projektu v jiném čase nebo v jiném prohlížeči, je třeba uložit token zabezpečení projektu a později jej znovu zadat. 

### <a name="get-project-credentials"></a>Získání pověření projektu
Přejděte na stránku nastavení projektu (ikona posuvníku) a poznamenejte si název tokenu zabezpečení. Pak přejděte na nastavení aplikace (ikona ozubeného kola), která zobrazuje všechny tokeny zabezpečení v aktuální instanci prohlížeče. Najděte token zabezpečení projektu a zkopírujte jeho název a hodnotu klíče do zabezpečeného umístění.

### <a name="restore-project-credentials"></a>Obnovení pověření projektu
Pokud chcete pokračovat v projektu, musíte nejprve vytvořit připojení ke stejnému kontejneru úložiště objektů blob. Opakujte výše uvedené kroky. Potom přejděte na stránku nastavení aplikace (ikona ozubeného kola) a zjistěte, zda je tam bezpečnostní token vašeho projektu. Pokud tomu tak není, přidejte nový token zabezpečení a zkopírujte název tokenu a klíč z předchozího kroku. Pak klikněte na Uložit nastavení. 

### <a name="resume-a-project"></a>Obnovení projektu

Nakonec přejděte na hlavní stránku (ikona domu) a klikněte na Otevřít cloudový projekt. Pak vyberte připojení úložiště objektů blob a vyberte soubor *VOTT* projektu. Aplikace načte všechna nastavení projektu, protože má token zabezpečení.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak pomocí nástroje pro rozpoznávání vzorků formuláře trénovat model s ručně označenými daty. Pokud chcete integrovat nástroj pro označování do vlastní aplikace, použijte REST API, které se zabývají školení označená data.

> [!div class="nextstepaction"]
> [Trénování s popisky pomocí Pythonu](./python-labeled-data.md)
