---
title: 'Rychlý Start: značení formulářů, výuka modelu a analýza formuláře pomocí nástroje pro výběr popisků – rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete vzorový Nástroj pro rozpoznávání popisů formulářů k ručnímu označení dokumentů formuláře. Pak vytvoříte vlastní model s označenými dokumenty a použijete model k extrakci párů klíč/hodnota.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6330a77f5971348c3f63fdaa7602ebba9ddf45ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186335"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Výukový model pro rozpoznávání formulářů pomocí popisků pomocí nástroje pro vzorkování popisků

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů REST API s ukázkovým nástrojem pro označování, pomocí kterého můžete vytvořit vlastní model s ručně označenými daty. Další informace o této funkci najdete v části [výuka s visačkami](../overview.md#train-with-labels) v přehledu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu musíte mít:

- Sada alespoň šest forem stejného typu. Tato data použijete ke školení modelu a testování formuláře. Pro tento rychlý Start můžete použít [ukázkovou datovou sadu](https://go.microsoft.com/fwlink/?linkid=2090451) . Nahrajte školicí soubory do kořenového adresáře kontejneru úložiště objektů BLOB v účtu Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Nastavení nástroje pro označování ukázek

Pomocí modulu Docker spustíte ukázkový nástroj pro označování vzorků. Pro nastavení kontejneru Docker použijte následující postup. Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).

> [!TIP]
> Nástroj pro označování ve formě rozpoznávání znaků je také k dispozici jako open source projekt na GitHubu. Nástroj je webová aplikace sestavená pomocí reakce + Redux a je napsaná v TypeScript. Další informace nebo přispívání naleznete v tématu [Nástroj pro označování ve formě rozpoznávání OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application).

1. Nejdřív nainstalujte Docker na hostitelském počítači. V této příručce se dozvíte, jak používat místní počítač jako hostitele. Pokud chcete použít hostující službu Docker v Azure, přečtěte si průvodce [nasazením ukázkového popisu nástroje pro označování](../deploy-label-tool.md) . 

   Hostitelský počítač musí splňovat následující požadavky na hardware:

    | Kontejner | Minimální | Doporučené|
    |:--|:--|:--|
    |Ukázkový nástroj pro popisky|2 jádra, 4 GB paměti|4 jádra, 8 GB paměti|

   Nainstalujte do počítače Docker podle příslušných pokynů pro váš operační systém: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Získejte vzorový kontejner nástrojů pro označování pomocí `docker pull` příkazu.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Teď jste připraveni spustit kontejner pomocí `docker run`.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Tento příkaz zpřístupní ukázkovou jmenovku nástroje prostřednictvím webového prohlížeče. Přejděte do části `http://localhost:3000` (Soubor > Nový > Jiné).

> [!NOTE]
> Můžete také označovat dokumenty a modely výuky pomocí REST API pro rozpoznávání formulářů. Chcete-li provést výuku a analýzu pomocí REST API, přečtěte si téma [výuka s popisky pomocí REST API a Pythonu](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Nastavení vstupních dat

Nejprve se ujistěte, že všechny školicí dokumenty mají stejný formát. Pokud máte formuláře v několika formátech, uspořádejte je do podsložek v závislosti na společném formátu. Při učení budete muset rozhraní API nasměrovat do podsložky.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurace sdílení prostředků mezi doménami (CORS)

Povolte CORS v účtu úložiště. V Azure Portal vyberte svůj účet úložiště a v levém podokně klikněte na kartu **CORS** . Na dolním řádku vyplňte následující hodnoty. Pak v horní části klikněte na **Uložit** .

* Povolené zdroje = * 
* Povolené metody = \[vybrat vše\]
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
* **Adresa URL SAS** – adresa URL sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Blob Storage. Pokud chcete načíst adresu URL SAS, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Nastavte čas vypršení platnosti na nějakou dobu, kdy jste službu použili. Ujistěte se, že jsou zaškrtnutá oprávnění **číst**, **zapisovat**, **Odstranit**a **seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tvar: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![Nastavení připojení nástroje Sample labeling Tool](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Vytvoření nového projektu

V nástroji pro označování ukázkových popisků se v projektech ukládají vaše konfigurace a nastavení. Vytvořte nový projekt a vyplňte pole následujícími hodnotami:

* **Zobrazované jméno** – zobrazovaný název projektu
* **Token zabezpečení** – některá nastavení projektu můžou obsahovat citlivé hodnoty, jako jsou klíče rozhraní API nebo jiné sdílené tajné klíče. Každý projekt vygeneruje token zabezpečení, který lze použít k šifrování/dešifrování citlivých nastavení projektu. Tokeny zabezpečení můžete v nastavení aplikace najít kliknutím na ikonu ozubeného kolečka v dolní části levého navigačního panelu.
* **Připojení ke zdroji** – připojení k Azure Blob Storage, které jste vytvořili v předchozím kroku, který chcete použít pro tento projekt.
* **Cesta ke složce** – volitelné – Pokud se zdrojové formuláře nacházejí ve složce v kontejneru objektů blob, zadejte sem název složky.
* **Identifikátor URI služby pro rozpoznávání formulářů** – adresa URL koncového bodu rozpoznávání formuláře.
* **Klíč rozhraní API** – klíč předplatného pro rozpoznávání formulářů
* **Popis** -volitelné – Popis projektu

![Nová stránka projektu s ukázkovým popisem nástroje](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Popisek formulářů

Při vytváření nebo otevírání projektu se otevře okno Editor hlavních značek. Editor značek se skládá ze tří částí:

* Podokno náhledu s možností změny velikosti, které obsahuje posuvný seznam formulářů ze zdrojového připojení.
* Hlavní podokno editoru, které umožňuje použít značky.
* Podokno editor značek umožňující uživatelům upravovat, zamykat, měnit pořadí a odstraňovat značky. 

### <a name="identify-text-elements"></a>Identifikujte textové prvky

Kliknutím na možnost **spustit optické rozpoznávání znaků u všech souborů** v levém podokně získáte informace o rozložení textu pro každý dokument. Nástroj pro popisování vykreslí ohraničující rámečky kolem každého prvku textu.

### <a name="apply-labels-to-text"></a>Použít popisky na text

V dalším kroku vytvoříte značky (popisky) a použijete je na textové prvky, které má model rozpoznat.

1. Nejprve pomocí podokna editor značek vytvořte značky, které chcete identifikovat.
   1. Kliknutím **+** vytvoříte novou značku.
   1. Zadejte název značky.
   1. Stisknutím klávesy ENTER značku uložíte.
1. V hlavním Editoru klikněte a tažením vyberte jedno nebo více slov z zvýrazněných textových prvků.
1. Klikněte na značku, kterou chcete použít, nebo stiskněte odpovídající klávesovou zkratku. Číselné klíče se přiřazují jako klávesové zkratky pro prvních 10 značek. Můžete změnit pořadí značek pomocí ikon šipky nahoru a dolů v podokně editor značek.
    > [!Tip]
    > Při označování formulářů mějte na paměti následující tipy.
    > * Pro každý vybraný textový prvek lze použít pouze jednu značku.
    > * Každou značku lze na stránce použít pouze jednou. Pokud se hodnota ve stejném formuláři objeví víckrát, vytvořte pro každou instanci různé značky. Například: "Faktura č. 1", "Faktura č. 2" atd.
    > * Značky nemohou být rozloženy mezi stránkami.
    > * Hodnoty popisků tak, jak se zobrazí ve formuláři; Nepokoušejte se rozdělit hodnotu do dvou částí se dvěma různými značkami. Například pole adresa musí být označeno s jednou značkou, i když pokrývá více řádků.
    > * Nezahrnujte klíče do polí&mdash;s příznakem pouze hodnoty.
    > * Data tabulky by měla být zjištěna automaticky a budou k dispozici v konečném výstupním souboru JSON. Pokud však model nerozpozná všechna data tabulky, můžete tato pole také označit ručně. Označte každou buňku v tabulce jiným popiskem. Pokud mají vaše formuláře tabulky s různými počty řádků, ujistěte se, že jste si vystavili alespoň jednu formu s největší možnou tabulkou.

![Okno hlavního editoru nástroje pro ukázkové označování](../media/label-tool/main-editor.png)

Postupujte podle výše uvedených kroků a označte alespoň pět vašich forem.

### <a name="specify-tag-value-types"></a>Zadejte typy hodnot značek

Volitelně můžete pro každou značku nastavit očekávaný datový typ. Otevřete kontextovou nabídku napravo od značky a vyberte typ z nabídky. Tato funkce umožňuje, aby algoritmus detekce vytvářely určité předpoklady, které budou zlepšit přesnost detekce textu. Také zajišťuje, že zjištěné hodnoty budou vráceny ve standardizovaném formátu v konečném výstupu JSON. 

> [!div class="mx-imgBorder"]
> ![Výběr typu hodnoty pomocí nástroje pro vzorkování popisků](../media/whats-new/formre-value-type.png)

V současné době jsou podporovány následující typy hodnot a variace:
* `string`
    * výchozí, `no-whitespaces`,`alphanumeric`
* `number`
    * výchozí`currency`
* `date` 
    * výchozí, `dmy`, `mdy`,`ymd`
* `time`
* `integer`

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Kliknutím na ikonu výuka v levém podokně otevřete stránku školení. Potom kliknutím na tlačítko **výuka** začněte s modelem školení. Po dokončení procesu školení se zobrazí následující informace:

* **ID modelu** – ID modelu, který byl vytvořen a vyškolený. Každý školicí hovor vytvoří nový model s vlastním ID. Zkopírujte tento řetězec do zabezpečeného umístění; budete ho potřebovat, pokud chcete provést prediktivní volání prostřednictvím REST API.
* **Průměrná přesnost** – Průměrná přesnost modelu Přesnost modelu můžete vylepšit tak, že si znovu označíte další formuláře a školení a vytvoříte nový model. Doporučujeme začít tím, že budete označovat pět forem a podle potřeby přidat další formuláře.
* Seznam značek a předpokládaná přesnost na značku.

![školicí zobrazení](../media/label-tool/train-screen.png)

Po dokončení školení si Projděte hodnotu **Průměrná přesnost** . Pokud je nízká, měli byste přidat další vstupní dokumenty a opakovat výše uvedené kroky. Již označené dokumenty zůstanou v indexu projektu.

> [!TIP]
> Můžete také spustit školicí proces s voláním REST API. Další informace o tom, jak to udělat, najdete v tématu [výuka s popisky pomocí Pythonu](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analýza formuláře

Kliknutím na ikonu prediktivní (žárovky) na levé straně otestujete svůj model. Nahrajte dokument formuláře, který jste nepoužili v procesu školení. Pak klikněte na tlačítko **předpověď** na pravé straně, abyste pro formulář získali předpovědi klíč/hodnota. Nástroj bude používat značky v ohraničujících polích a bude hlásit spolehlivost každé značky.

> [!TIP]
> Můžete také spustit rozhraní API pro analýzu pomocí volání REST. Další informace o tom, jak to udělat, najdete v tématu [výuka s popisky pomocí Pythonu](./python-labeled-data.md).

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

Nakonec přejděte na hlavní stránku (ikonu domu) a klikněte na otevřít cloudový projekt. Pak vyberte připojení úložiště objektů BLOB a vyberte soubor *. vott* vašeho projektu. Aplikace načte všechna nastavení projektu, protože má token zabezpečení.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak pomocí nástroje pro rozpoznávání popisů formulářů vytvořit výuku modelu s ručně označenými daty. Pokud chcete nástroj pro označování integrovat do vlastní aplikace, použijte rozhraní REST API, která se týkají školení s popisky dat.

> [!div class="nextstepaction"]
> [Výuka s popisky pomocí Pythonu](./python-labeled-data.md)
