---
title: Vytvořit projekt pro označování dat
titleSuffix: Azure Machine Learning
description: Naučte se, jak vytvořit a spustit označování projektů k označení dat pro strojové učení.  Mezi tyto nástroje patří označení ml s asistencí nebo lidské v rámci popisků pro pomoc s úkolem.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 601434628258b8af3fb5fb9336f701be72441c64
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318121"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Vytvoření popisku dat pro projekt a Export popisků 



Popisování voluminous dat v projektech Machine Learning je často starostí. Projekty, které mají komponentu počítačového vidění, jako je například klasifikace obrázku nebo detekce objektu, obecně vyžadují popisky pro tisíce imagí.
 
[Azure Machine Learning](https://ml.azure.com/) popisování dat poskytuje centrální místo pro vytváření, správu a sledování projektů označování. Slouží ke koordinaci dat, popisků a členů týmu, aby bylo možné efektivně spravovat úkoly označování. Machine Learning podporuje klasifikaci obrázků, a to buď s více popisky, nebo s více třídami, a s ohraničenými poli.

Označování dat sleduje průběh a udržuje frontu neúplných úloh označování.

Můžete spustit a zastavit projekt a řídit průběh označování. Můžete zkontrolovat označená data a exportovat popisky ve formátu díky Coco nebo jako datovou sadu Azure Machine Learning.

> [!Important]
> V současné době jsou podporovány pouze projekty klasifikace obrázků a označení identifikace objektu. Kromě toho musí být image dat dostupné v úložišti dat objektů BLOB v Azure. (Pokud nemáte existující úložiště dat, můžete při vytváření projektu nahrávat obrázky.)

V tomto článku se naučíte:

> [!div class="checklist"]
> * Vytvoření projektu
> * Zadat data a strukturu projektu
> * Spuštění a sledování projektu
> * Export popisků


## <a name="prerequisites"></a>Předpoklady

* Data, která chcete označit, buď v místních souborech nebo v úložišti objektů BLOB v Azure.
* Sada popisků, které chcete použít.
* Pokyny pro označování.
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://aka.ms/AMLFree).
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Vytvoření projektu s popisem

Označování projektů je spravováno z Azure Machine Learning. Stránku s **popisem projektů** můžete použít ke správě projektů.

Pokud vaše data jsou už v úložišti objektů BLOB v Azure, měli byste je před vytvořením projektu s popisem zpřístupnit jako úložiště dat. Příklad použití úložiště dat najdete v tématu [kurz: vytvoření prvního projektu označení klasifikace obrázku](tutorial-labeling.md).

Chcete-li vytvořit projekt, vyberte možnost **Přidat projekt**. Dejte projektu vhodný název a vyberte **typ úlohy označování**.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Průvodce vytvořením projektu s popisem":::

* Vyberte možnost **klasifikace obrázků s více třídami** pro projekty, pokud chcete použít pouze *jeden štítek* ze sady štítků na obrázek.
* Pokud chcete použít *jeden nebo více* štítků ze sady štítků na obrázek, vyberte možnost **klasifikace obrázků – vícenásobné označení** pro projekty. Například fotografie psa může být označená pomocí *psa* i *Daytime*.
* Vyberte možnost **Identifikace objektu (ohraničovací rámeček)** pro projekty, pokud chcete přiřadit popisek a ohraničující rámeček ke každému objektu v rámci obrázku.

Až budete připraveni pokračovat, vyberte **Další** .

## <a name="specify-the-data-to-label"></a>Zadat data k označení

Pokud jste už vytvořili datovou sadu, která obsahuje vaše data, vyberte ji v rozevíracím seznamu **Vyberte existující datovou sadu** . Nebo vyberte možnost **vytvořit datovou sadu** pro použití stávajícího úložiště Azure DataStore nebo k nahrání místních souborů.

> [!NOTE]
> Projekt nemůže obsahovat více než 500 000 imagí.  Pokud vaše datová sada obsahuje víc, načte se jenom prvních 500 000 imagí.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Vytvoření datové sady z úložiště Azure

V mnoha případech je přesně možné pouze nahrávat místní soubory. [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) ale nabízí rychlejší a robustnější způsob přenosu velkého množství dat. Doporučujeme, abyste Průzkumník služby Storage jako výchozí způsob přesunutí souborů.

Chcete-li vytvořit datovou sadu z dat, která již byla uložena v úložišti objektů BLOB v Azure:

1. Vyberte **vytvořit datovou sadu**  >  **z úložiště** dat.
1. Přiřaďte k datové sadě **název** .
1. Jako **Typ datové sady** vyberte **soubor** .  Jsou podporovány pouze typy datových sad souborů.
1. Vyberte úložiště dat.
1. Pokud jsou vaše data v podsložce v úložišti objektů blob, zvolte **Procházet** a vyberte cestu.
    * Přidejte do cesty znak "/* *", chcete-li zahrnout všechny soubory do podsložek vybrané cesty.
    * Připojíte * */* . * a zahrnete všechna data v aktuálním kontejneru a jejích podsložkách.
1. Zadejte popis pro datovou sadu.
1. Vyberte **Další**.
1. Potvrďte podrobnosti. Výběrem **zpět** upravíte nastavení nebo **vytvořte** datovou sadu.


### <a name="create-a-dataset-from-uploaded-data"></a>Vytvoření datové sady z nahraných dat

Přímé nahrání dat:

1. Vyberte **vytvořit datovou sadu**  >  **z místních souborů**.
1. Přiřaďte k datové sadě **název** .
1. Jako **Typ datové sady** vyberte "soubor".
1. *Volitelné:* Vyberte **Upřesnit nastavení** a přizpůsobte úložiště dat, kontejner a cestu k datům.
1. Vyberte **Procházet** a vyberte místní soubory, které se mají nahrát.
1. Zadejte popis datové sady.
1. Vyberte **Další**.
1. Potvrďte podrobnosti. Výběrem **zpět** upravíte nastavení nebo **vytvořte** datovou sadu.

Data se nahrají do výchozího úložiště objektů BLOB (workspaceblobstore) pracovního prostoru Machine Learning.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Konfigurovat přírůstkovou aktualizaci

Pokud máte v úmyslu přidat nové image do datové sady, použijte přírůstkovou aktualizaci pro přidání těchto nových imagí do projektu.   Když je zapnutá **přírůstková aktualizace** , datová sada se pravidelně kontroluje, aby se do projektu přidaly nové image, a to na základě sazby dokončení označení.   Kontroly nových dat se zastaví, když projekt obsahuje maximálně 500 000 imagí.

K přidání dalších imagí do projektu použijte [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) k nahrání do příslušné složky v úložišti objektů BLOB. 

Zaškrtněte políčko **Povolit přírůstkovou aktualizaci** , pokud chcete, aby projekt průběžně sledoval nová data v úložišti dat.

Zrušte toto políčko, pokud nechcete, aby byly do projektu přidány nové obrázky, které se zobrazí v úložišti dat.

Časové razítko pro nejnovější aktualizaci najdete v části **přírůstková aktualizace** na kartě **Podrobnosti** pro váš projekt.


## <a name="specify-label-classes"></a>Určení tříd popisků

Na stránce **třídy popisků** určete sadu tříd pro kategorizaci dat. Provedete to pečlivě, protože přesnost a rychlost štítků bude ovlivněna jejich možností výběru mezi třídami. Například namísto kontroly pravopisu úplných rodů a druhů pro rostliny nebo živočichy použijte kód pole nebo zkratku rodu.

Zadejte jeden popisek na řádek. **+** K přidání nového řádku použijte tlačítko. Pokud máte více než 3 nebo 4 popisky, ale méně než 10, je možné, že budete chtít názvy označit čísly ("1:", "2:"), aby je mohli pomocí těchto klávesových zkratek zrychlit.

## <a name="describe-the-labeling-task"></a>Popsat úlohu označování

Je důležité jasně vysvětlit úlohu označování. Na stránce **pokyny k označování** můžete přidat odkaz na externí web pro pokyny k označování nebo zadat pokyny v poli pro úpravy na stránce. Pro cílovou skupinu ponechejte pokyny orientované na úlohy a vhodné. Vezměte v úvahu tyto otázky:

* Jaké popisky uvidí a jak se z nich budou vybírat? Existuje referenční text, na který se má odkazovat?
* Co má dělat, pokud se žádný popisek nejeví jako vhodný?
* Co byste měli dělat, pokud je vhodné více štítků?
* Jaká prahová hodnota spolehlivosti by měla platit pro popisek? Chcete jejich "nejlepší odhad", pokud nejsou konkrétní?
* Co byste měli dělat s částečně zastíněna nebo překrývajícími se objekty, které vás zajímají?
* Co byste měli dělat, pokud je objekt zájmu oříznutý okrajem obrázku?
* Co se má udělat po odeslání popisku, pokud si myslíte, že udělali chybu?

V případě ohraničujících polí patří mezi důležité otázky:

* Jak se definuje ohraničovací rámeček pro tuto úlohu? By měl být zcela na vnitřku objektu, nebo by měl být na vnějším objektu? Mělo by se co nejtěsněji oříznout, nebo je nějaký přijatelný prostor?
* Jakou úroveň péče a konzistence očekáváte, že se popisky použijí při definování ohraničujících polí?
* Jak označit objekt, který je částečně zobrazen v obrázku? 
* Jak označit objekt, který částečně pokrývá jiný objekt?

>[!NOTE]
> Nezapomeňte si uvědomit, že popisky budou moci vybrat prvních 9 popisků pomocí číselných klíčů 1-9.

## <a name="use-ml-assisted-labeling"></a>Použití popisků ML s asistencí

Stránka s **popisem s asistencí ml** vám umožní aktivovat automatické modely strojového učení a urychlit tak úlohu označování. Na začátku projektu označování se obrázky přecházejí do náhodného pořadí, aby se snížil případný posun. Nicméně všechny posuny, které jsou k dispozici v datové sadě, se projeví v trained model. Například pokud je 80% imagí z jedné třídy, pak přibližně 80% dat použitých ke výukě modelu bude tato třída. Toto školení nezahrnuje aktivní učení.

Vyberte možnost *Povolit popisky na základě ml s asistencí* a určete GPU, která umožňuje poučení s asistencí, které se skládá ze dvou fází:
* Clustering
* Předznačení

Přesný počet imagí označených popiskem, které jsou nutné ke spuštění s asistencí, není pevným číslem.  To se může výrazně lišit od jednoho popisku projektu k jinému. U některých projektů je někdy možné, že se po 300 imagí, které jsou označeny ručně, zobrazí předznačení nebo úkoly clusteru. Označování s asistencí ML používá techniku nazývanou *učení přenosu* , která používá předem připravený model k tomu, abyste mohli začít školicí proces. Pokud jsou třídy vaší datové sady podobné těm v předškolených modelech, mohou být před popisky dostupné až po několika stovkách, které jsou ručně označené. Pokud se vaše datová sada významně liší od dat používaných k předběžnému učení modelu, může to trvat mnohem déle.

Vzhledem k tomu, že závěrečné popisky stále spoléhají na vstup od štítku, tato technologie se někdy označuje jako *člověk v označení smyčky* .

> [!NOTE]
> Označení dat s asistencí podle ML nepodporuje výchozí účty úložiště zabezpečené za [virtuální sítí](how-to-network-security-overview.md). Pro označení dat s podporou ML je nutné použít jiný než výchozí účet úložiště. Účet úložiště, který není výchozí, může být zabezpečený za virtuální sítí. 

### <a name="clustering"></a>Clustering

Po odeslání určitého počtu popisků se model strojového učení pro klasifikaci obrázků začne seskupovat podobně jako v sobě podobné obrázky.  Tyto podobné obrázky jsou prezentovány popiskům na stejné obrazovce, aby se urychlilo ruční označování. Clustering je zvláště užitečný v případě, že popisek zobrazuje mřížku 4, 6 nebo 9 imagí. 

Jakmile se model strojového učení vyškole na vaše ručně označené údaje, model se zkrátí na jeho poslední plně připojenou vrstvu. Neoznačené obrázky se pak předávají pomocí zkráceného modelu v procesu, který se běžně označuje jako "vložení" nebo "featurization". Tím se vloží každý obrázek v prostoru s vysokým rozměrem definovaným touto vrstvou modelu. Obrázky, které jsou nejbližší sousední prostory v prostoru, se používají pro úlohy clusteringu. 

Pro modely detekce objektu se nezobrazuje fáze clusteringu.

### <a name="prelabeling"></a>Předznačení

Po odeslání dostatečného popisku obrázku se pro předpověď značek obrázku používá klasifikační model. Nebo model detekce objektu se používá k předpovědi ohraničujících polí. Štítek teď uvidí stránky, které obsahují předpovězené popisky, které už jsou na každém obrázku přítomné. V případě detekce objektu se zobrazí také předpokládaná pole. Úkol pak před odesláním stránky zkontrolujte tyto předpovědi a opravte všechny image s nesprávnými popisky.  

Jakmile se model strojového učení vyškole na vaše ručně označené údaje, model se vyhodnotí na základě sady testů ručně označených imagí, aby se zjistila její přesnost na nejrůznějších různých práh spolehlivosti. Tento proces hodnocení se používá k určení prahové hodnoty spolehlivosti, nad kterou je model dostatečně přesný, aby se zobrazily předdefinované popisky. Model se pak vyhodnotí proti neoznačeným datům. Obrázky s předpovědiější, než je tato prahová hodnota, se používají pro předběžné označování.

## <a name="initialize-the-labeling-project"></a>Inicializovat projekt značení

Po inicializaci projektu značení jsou některé aspekty projektu neměnné. Nemůžete změnit typ nebo datovou sadu úlohy. *Můžete* změnit popisky a adresu URL pro popis úlohy. Před vytvořením projektu pečlivě zkontrolujte nastavení. Po odeslání projektu se vrátíte na domovskou stránku s **popisem dat** , která zobrazí projekt jako **inicializovaný**.

> [!NOTE]
> Tato stránka se nemusí automaticky aktualizovat. Takže po pozastavení ručně aktualizujte stránku, aby se zobrazil stav projektu jako **vytvořený**.

## <a name="run-and-monitor-the-project"></a>Spuštění a sledování projektu
Po inicializaci projektu ho Azure spustí. Pokud chcete zobrazit podrobnosti projektu, vyberte projekt na stránce hlavní **popisky dat** .

Pokud chcete pozastavit nebo restartovat projekt, přepněte stav **spuštění** v pravém horním rohu. Data můžete označovat pouze v případě, že projekt běží.

### <a name="dashboard"></a>Řídicí panel

Karta **řídicí panel** zobrazuje průběh úlohy označování.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Řídicí panel pro popisky dat":::

Graf průběhu ukazuje, kolik položek bylo označeno a kolik ještě není hotovo.  Nevyřízené položky mohou být:

* Ještě není přidané do úlohy.
* Zahrnuto do úlohy, která je přiřazená k popisku, ale ještě není dokončená. 
* Ve frontě úloh, které se ještě mají přiřadit

V prostřední části se zobrazuje fronta úloh, které se ještě mají přiřadit. Pokud je označení ML s asistencí vypnuto, v této části se zobrazuje počet ručních úloh, které se mají přiřadit. Když je zapnuté označení ML s asistencí, zobrazí se také:

* Úlohy obsahující clusterované položky ve frontě
* Úlohy obsahující předoznačené položky ve frontě

Kromě toho, když je zapnuté označování ML s asistencí, se při dalším spuštění školení zobrazí malý indikátor průběhu.  Části experimenty obsahují odkazy na jednotlivé běhy strojového učení.

* Školení – vlaky model pro předpověď popisků
* Ověřování – určuje, jestli se má pro položky před označením tohoto modelu použít předpověď. 
* Odvození – spuštění předpovědi pro nové položky
* Featurization – položky clusterů (pouze pro projekty klasifikace imagí)

Na pravé straně je rozdělení popisků pro tyto úkoly, které jsou dokončené.  Mějte na paměti, že v některých typech projektů může položka mít více popisků. v takovém případě celkový počet popisků může být větší než celkový počet položek.

### <a name="data-tab"></a>Karta data

Na kartě **data** můžete zobrazit datovou sadu a zkontrolovat data s popisky. Pokud se zobrazí nesprávně označená data, vyberte je a zvolte **odmítnout**. tím se odeberou popisky a data se převedou zpátky do fronty bez označení.

### <a name="details-tab"></a>Karta Podrobnosti

Zobrazit podrobnosti o projektu.  Na této kartě můžete:

* Zobrazení podrobností projektu a vstupních datových sad
* Povolit přírůstkovou aktualizaci
* Zobrazit podrobnosti kontejneru úložiště, který se používá k uložení výstupů s označením v projektu
* Přidání popisků do projektu
* Upravit pokyny, které přidáváte popiskům
* Úprava podrobností s popisem s asistencí, včetně povolení nebo zakázání

### <a name="access-for-labelers"></a>Přístup pro popisky

Kdokoli, kdo má přístup k vašemu pracovnímu prostoru, může označovat data v projektu.  Můžete také přizpůsobit oprávnění pro své popisky tak, aby mohl přístup označovat popisky, ale ne jiné části pracovního prostoru nebo projektu s popisem.  Další podrobnosti najdete v tématu [Správa přístupu k pracovnímu prostoru Azure Machine Learning](how-to-assign-roles.md)a Naučte se, jak vytvořit [vlastní roli pro popisek](how-to-assign-roles.md#labeler).

## <a name="add-new-label-class-to-a-project"></a>Přidat novou třídu Label do projektu

Během procesu označování se můžete setkat s tím, že pro klasifikaci imagí jsou potřeba další popisky.  Například můžete chtít přidat popisek "Neznámý" nebo "jiný", který označuje matoucí obrázky.

Pomocí těchto kroků můžete přidat jeden nebo více štítků do projektu:

1. Vyberte projekt na hlavní stránce pro **popisky dat** .
1. V pravém horním rohu **stránky přepnutím** na **pozastaveno** zastavíte jmenovky od jejich aktivity.
1. Vyberte kartu **Podrobnosti**.
1. V seznamu na levé straně vyberte **třídy popisků**.
1. V horní části seznamu vyberte **+ Přidat popisky** ![ Přidat popisek.](media/how-to-create-labeling-projects/add-label.png)
1. Ve formuláři přidejte nový popisek a vyberte způsob, jak pokračovat.  Vzhledem k tomu, že jste změnili dostupné popisky pro obrázek, zvolíte způsob, jakým se mají již označená data zacházet:
    * Začněte znovu a odeberte všechny existující popisky.  Tuto možnost vyberte, pokud chcete začít popisky od začátku s novou úplnou sadou popisků. 
    * Začněte znovu a zachová všechny existující popisky.  Tuto možnost vyberte, pokud chcete všechna data označit jako neoznačená, ale stávající popisky nechejte jako výchozí značku pro image, které byly dříve označené.
    * Pokračujte a zachováte všechny existující popisky. Tuto možnost vyberte, pokud chcete zachovat všechna data, která jsou už označená, a začít používat nový popisek pro data, která ještě nejsou označená.
1. Upravte stránku s pokyny podle potřeby pro nové popisky.
1. Po přidání všech nových popisků se v pravém horním rohu stránky **pozastaveno** **a spustí** se restartování projektu.  

## <a name="export-the-labels"></a>Export popisků

Data popisku můžete exportovat pro Machine Learning experimentování kdykoli. Popisky obrázků lze exportovat ve [formátu díky Coco](http://cocodataset.org/#format-data) nebo jako datovou sadu Azure Machine Learning. Použijte tlačítko **exportovat** na stránce Project **Details (podrobnosti projektu** ) vašeho projektu s popisem.

Soubor díky Coco se vytvoří ve výchozím úložišti objektů BLOB v pracovním prostoru Azure Machine Learning ve složce v rámci *exportu/díky Coco*. Exportovaný Azure Machine Learning datovou sadu můžete zpřístupnit v části datové **sady** v Machine Learning. Stránka s podrobnostmi datové sady také poskytuje vzorový kód pro přístup k popiskům z Pythonu.

![Exportovaná datová sada](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Další kroky

* [Kurz: vytvoření prvního projektu označování klasifikace imagí](tutorial-labeling.md).
* Obrázky popisků pro [klasifikaci obrázku nebo detekci objektů](how-to-label-images.md)
* Další informace o [Azure Machine Learning a Machine Learning Studio (Classic)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)