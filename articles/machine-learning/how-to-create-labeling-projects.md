---
title: Vytvoření projektu označování dat
titleSuffix: Azure Machine Learning
description: Zjistěte, jak vytvářet a spouštět projekty označování pro označování dat pro strojové učení.  Nástroje zahrnují ml asistované označování, nebo člověk ve smyčce značení na pomoc s úkolem.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: d39cf8745c6f53cb11bb12561fd452325fe52ac6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296944"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Vytvoření projektu označování dat a exportu popisků 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Označování objemných dat v projektech strojového učení je často bolest hlavy. Projekty, které mají součást počítačového vidění, jako je například klasifikace obrázků nebo detekce objektů, obvykle vyžadují popisky pro tisíce obrazů.
 
[Azure Machine Learning](https://ml.azure.com/) poskytuje centrální místo pro vytváření, správu a monitorování projektů označování. Slouží ke koordinaci dat, popisků a členů týmu pro efektivní správu úloh popisování. Strojové učení podporuje klasifikaci obrázků, buď vícepopisek nebo více tříd, a identifikaci objektu s ohraničenými poli.

Strojové učení sleduje průběh a udržuje frontu neúplných úloh označování. Labelery k účasti nepotřebují účet Azure. Po ověření pomocí vašeho účtu Microsoft nebo [Služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)můžou dělat tolik popisků, kolik jejich času dovolí.

Můžete spustit a zastavit projekt, přidat a odebrat labelers a týmy a sledovat průběh označování. Data označená ve formátu COCO nebo jako datovou sadu Azure Machine Learning můžete exportovat.

> [!Important]
> V současné době jsou podporovány pouze projekty klasifikace obrázků a identifikace objektů. Kromě toho musí být k dispozici image dat v úložišti dat objektů blob Azure. (Pokud nemáte existující úložiště dat, můžete nahrát obrázky během vytváření projektu.) 

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření projektu
> * Určení dat a struktury projektu
> * Správa týmů a lidí, kteří na projektu pracují
> * Spuštění a sledování projektu
> * Export štítků


## <a name="prerequisites"></a>Požadavky

* Data, která chcete označit, buď v místních souborech nebo v úložišti objektů blob Azure.
* Sada popisků, které chcete použít.
* Pokyny pro označování.
* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://aka.ms/AMLFree) než začnete.
* Pracovní prostor Machine Learning. Viz [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Vytvoření projektu popisování

Projekty označování se spravují z Azure Machine Learning. Stránka **Projekty označování** slouží ke správě projektů a osob. V projektu je přiřazen jeden nebo více týmů a k němu je přiřazen jeden nebo více lidí.

Pokud vaše data jsou již v úložišti objektů Blob Azure, měli byste je zpřístupnit jako úložiště dat před vytvořením projektu označování. Podrobnosti naleznete v [tématu Vytvoření a registrace úložišť dat](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores).

Chcete-li vytvořit projekt, vyberte **přidat projekt**. Pojmenujte projekt a vyberte **Typ úkolu Popisování**.

![Průvodce vytvořením projektu popisem](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Zvolte **Klasifikace obrázků více tříd** pro projekty, pokud chcete použít pouze *jednu třídu* ze sady tříd na bitovou kopii.
* Pokud chcete na obrázek použít jeden *nebo více* popisků ze sady tříd, zvolte **vícepopisek klasifikace** obrázků pro projekty. Například, fotografie psa může být označen a to jak *pes* a *denní*.
* Pokud chcete každému objektu v obraze přiřadit třídu a ohraničovací rámeček, zvolte **identifikaci objektu (ohraničovací rámeček).**

Až budete připraveni pokračovat, vyberte **Další.**

## <a name="specify-the-data-to-label"></a>Určení dat, která mají být označena popiskem

Pokud jste již vytvořili datovou sadu, která obsahuje vaše data, vyberte ji z rozevíracího seznamu **Vybrat existující datovou sadu.** Nebo vyberte **Vytvořit datovou sadu** pro použití existujícího úložiště dat Azure nebo pro nahrání místních souborů.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Vytvoření datové sady z úložiště dat Azure

V mnoha případech je v pořádku jen nahrát místní soubory. Ale [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) poskytuje rychlejší a robustnější způsob přenosu velkého množství dat. Jako výchozí způsob přesouvání souborů doporučujeme Průzkumníka úložišť.

Vytvoření datové sady z dat, která jste už uložili v úložišti objektů Blob Azure:

1. Vyberte **Vytvořit datovou sadu** > **ze úložiště dat**.
1. Přiřaďte datové sadě **název.**
1. Jako typ **datové sady**zvolte **Soubor** .  
1. Vyberte úložiště dat.
1. Pokud jsou vaše data v podsložce v úložišti objektů blob, vyberte cestu, kterou chcete **procházet.**
    * Přidejte "/**" k cestě, aby zahrnovala všechny soubory v podsložkách vybrané cesty.
    * Připojit "**/*.*" zahrnout všechna data v aktuálním kontejneru a jeho podsložky.
1. Zadejte popis datové sady.
1. Vyberte **další**.
1. Potvrďte detaily. Vyberte **Zpět,** chcete-li změnit **nastavení,** nebo vytvořit datovou sadu.

> [!NOTE]
> Vybraná data se načtou do projektu.  Přidání dalších dat do úložiště dat se po vytvoření projektu v tomto projektu nezobrazí.  

### <a name="create-a-dataset-from-uploaded-data"></a>Vytvoření datové sady z nahraných dat

Chcete-li přímo nahrát data:

1. Vyberte **Vytvořit datovou sadu** > **z místních souborů**.
1. Přiřaďte datové sadě **název.**
1. Jako typ datové **sady**zvolte "Soubor" .
1. *Nepovinné:* Vyberte **Upřesnit nastavení,** chcete-li přizpůsobit úložiště dat, kontejner a cestu k datům.
1. Vyberte **Procházet** a vyberte místní soubory, které chcete nahrát.
1. Zadejte popis datové sady.
1. Vyberte **další**.
1. Potvrďte detaily. Vyberte **Zpět,** chcete-li změnit **nastavení,** nebo vytvořit datovou sadu.

Data se nahrají do výchozího úložiště objektů blob ("workspaceblobstore") pracovního prostoru Machine Learning.

## <a name="specify-label-classes"></a>Určení tříd popisků

Na stránce **Label třídy** zadejte sadu tříd pro kategorizaci dat. Udělejte to opatrně, protože přesnost a rychlost vašich štítkovačů bude ovlivněna jejich schopností vybrat si mezi třídami. Například místo toho, aby hláskování plného rodu a druhů pro rostliny nebo zvířata, použijte kód pole nebo zkrátit rod.

Zadejte jeden popisek na řádek. Pomocí **+** tlačítka přidejte nový řádek. Pokud máte více než 3 nebo 4 popisky, ale méně než 10, můžete předponu jména s čísly ("1: ", "2: "), takže labelers můžete použít číselné klávesy k urychlení jejich práce.

## <a name="describe-the-labeling-task"></a>Popište úkol popisování

Je důležité jasně vysvětlit úkol označování. Na stránce **Pokyny k popisku** můžete přidat odkaz na externí web pro pokyny k označení nebo zadat pokyny v textovém poli na stránce. Udržujte pokyny zaměřené na úkoly a vhodné pro publikum. Zamyslete se nad těmito otázkami:

* Jaké jsou štítky, které uvidí, a jak si mezi nimi vyberou? Existuje referenční text, na který se odkazuje?
* Co by měli dělat, pokud se žádný štítek nezdá vhodný?
* Co by měli dělat, pokud se vám zdá vhodné více štítků?
* Jaký práh spolehlivosti by se měl vztahovat na štítek? Chcete jejich "nejlepší odhad", pokud si nejsou jisti?
* Co by měli dělat s částečně uzavřenými nebo překrývajícími se předměty zájmu?
* Co by měli dělat, pokud je objekt zájmu oříznut okrajem obrazu?
* Co by měli dělat poté, co předloží štítek, pokud si myslí, že udělali chybu?

Pro ohraničovací rámečky jsou důležité otázky:

* Jak je pro tento úkol definován ohraničovací rámeček? Mělo by to být zcela na vnitřní straně objektu, nebo by to mělo být na vnější straně? Mělo by být oříznuto co nejblíže, nebo je nějaká vůle přijatelná?
* Jakou úroveň péče a konzistence očekáváte, že štítkovače budou používat při definování ohraničovacích rámečků?
* Jak označit objekt, který je částečně zobrazen na obrázku? 
* Jak označit objekt, který je částečně zakrytý jiným objektem?

>[!NOTE]
> Nezapomeňte si uvědomit, že labelery budou moci vybrat prvních 9 štítků pomocí číselných tlačítek 1-9.

## <a name="use-ml-assisted-labeling"></a>Použití označení s pomocí ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Stránka **s asistencí ml umožňuje** aktivovat modely automatického strojového učení pro urychlení úlohy označování. Na začátku projektu označování jsou obrázky zamíchány do náhodného pořadí, aby se snížila potenciální zkreslení. Všechny zkreslení, které jsou přítomny v datové sadě se však projeví v trénovaném modelu. Například pokud 80 % obrázků jsou z jedné třídy, pak přibližně 80 % dat použitých k trénování modelu bude této třídy. Toto školení nezahrnuje aktivní učení.

Tato funkce je k dispozici pro klasifikace obrázků (vícetřídní nebo vícepopisné) úkoly.  

Vyberte *Povolit označení s asistencí ML* a určete grafický procesor, který umožní asistované označování, které se skládá ze dvou fází:
* Clustering
* Předběžné označení

Přesný počet označených obrázků potřebných k zahájení asistovaného označování není pevným číslem.  To se může výrazně lišit od jednoho projektu označování do druhého. U některých projektů je někdy možné zobrazit předběžné popisky nebo úlohy clusteru po ručním označení 300 bitových kopií. Ml Asistované značení používá techniku nazvanou *Transfer Learning*, která používá předem trénovaný model k nastartování procesu školení. Pokud jsou třídy vaší datové sady podobné třídám v předem trénovaném modelu, mohou být předběžné popisky k dispozici pouze po několika stovkách ručně označených obrázků. Pokud vaše datová sada se výrazně liší od dat použitých k pre-trénování modelu, může trvat mnohem déle.

Vzhledem k tomu, že konečné popisky stále spoléhají na vstup z labeleru, tato technologie se někdy nazývá *lidské ve smyčce* značení.

### <a name="clustering"></a>Clustering

Po odeslání určitého počtu štítků začne model strojového učení seskupit podobné obrázky.  Tyto podobné obrázky jsou prezentovány na labelers na stejné obrazovce pro urychlení ruční značkování. Clustering je zvláště užitečné, když labeler je zobrazení mřížky 4, 6 nebo 9 obrazů. 

Jakmile je model strojového učení trénovaný na ručně označených datech, model se zkrátí na poslední plně připojenou vrstvu. Neoznačené obrazy jsou pak předány zkrácený model v procesu běžně známém jako "vkládání" nebo "featurization." Tím se každý obraz vloží do vysoce rozměrného prostoru definovaného touto vrstvou modelu. Obrázky, které jsou nejbližšími sousedy v prostoru, se používají pro clustering úkoly. 

### <a name="prelabeling"></a>Předběžné označení

Po odeslání dalších popisků obrázků se k předvídání značek obrázků používá klasifikační model.  Popisek nyní vidí stránky, které obsahují předpokládané popisky, které jsou již v každém obrázku k dispozici.  Úkolem je pak zkontrolovat tyto popisky a opravit všechny chybně označené obrázky před odesláním stránky.  

Jakmile je model strojového učení trénovaný na ručně označených datech, model je vyhodnocen na testovací sadě ručně označených bitových kopií, aby se zjistila jeho přesnost na různých prahových hodnotách spolehlivosti. Tento proces hodnocení se používá k určení prahovou hodnotu spolehlivosti, nad kterou je model dostatečně přesný, aby zobrazoval předběžné popisky. Model je pak vyhodnocen proti neoznačeným datům. Obrázky s předpověďmi jistější než tato prahová hodnota se používají pro předběžné označení.

> [!NOTE]
> Označení s asistencí ML je k dispozici **pouze** v pracovních prostorech edice Enterprise.

## <a name="initialize-the-labeling-project"></a>Inicializovat projekt označování

Po inicializace projektu popisky jsou některé aspekty projektu neměnné. Typ úkolu nebo datovou sadu nelze změnit. *Můžete* upravit popisky a adresu URL popisu úkolu. Před vytvořením projektu si pečlivě zkontrolujte nastavení. Po odeslání projektu se vrátíte na domovskou stránku **označení dat,** která zobrazí projekt jako **Inicializace**. Tato stránka se automaticky neaktualizuje. Po pauze tedy ručně aktualizujte stránku, abyste viděli stav projektu jako **Vytvořeno**.

## <a name="manage-teams-and-people"></a>Správa týmů a lidí

Ve výchozím nastavení každý projekt označování, který vytvoříte získá nový tým s vámi jako členem. Týmy však mohou být také sdíleny mezi projekty. A projekty mohou mít více než jeden tým. Pokud chcete vytvořit tým, na stránce **Týmy** vyberte **Přidat tým.**

Spravujete lidi na stránce **Lidé.** Přidávejte a odebírat lidi podle e-mailové adresy. Každý labeler se musí ověřit prostřednictvím vašeho účtu Microsoft nebo Služby Azure Active Directory, pokud ho používáte.  

Po přidání osoby můžete tuto osobu přiřadit k jednomu nebo více týmům: Přejděte na stránku **Týmy,** vyberte tým a pak vyberte **Přiřadit lidi** nebo **Odebrat lidi**.

Chcete-li týmu odeslat e-mail, vyberte tým, který zobrazí stránku **s podrobnostmi o týmu.** Na této stránce vyberte **E-mailový tým,** chcete-li otevřít koncept e-mailu s adresami všech členů týmu.

## <a name="run-and-monitor-the-project"></a>Spuštění a sledování projektu

Po inicializaci projektu Azure začne spouštět. Vyberte projekt na hlavní stránce **Popisy dat** a přejděte na **podrobnosti projektu**. Karta **Řídicí panel** zobrazuje průběh úkolu popisky.

Na kartě **Data** uvidíte datovou sadu a zkontrolujte data s popiskem. Pokud se zobrazí nesprávně označená data, vyberte je a zvolte **Odmítnout**, který odstraní popisky a vrátí data zpět do fronty bez popisku.

Karta **Tým** slouží k přiřazení nebo zrušení přiřazení týmů k projektu.

Chcete-li projekt pozastavit nebo restartovat, vyberte tlačítko **Pozastavit**/**start.** Data lze označovat pouze v případě, že je spuštěn projekt.

Data můžete označit přímo ze stránky **Podrobnosti projektu** výběrem **popisků dat**.

## <a name="add-new-label-class-to-a-project"></a>Přidání nové třídy popisků do projektu

Během procesu popisování můžete zjistit, že pro klasifikaci obrázků jsou potřeba další popisky.  Můžete například přidat popisek "Neznámý" nebo "Jiné", který označuje matoucí obrázky.

Pomocí následujících kroků můžete do projektu přidat jeden nebo více popisků:

1. Vyberte projekt na hlavní stránce **Popisování dat.**
1. V horní části stránky vyberte **Pozastavit,** chcete-li zastavit sledování štítkovačů.
1. Vyberte kartu **Podrobnosti**.
1. V seznamu vlevo vyberte **Label třídy**.
1. V horní části seznamu vyberte **+ Přidat popisky** ![Přidat popisek](media/how-to-create-labeling-projects/add-label.png)
1. Ve formuláři přidejte nový štítek a zvolte, jak chcete pokračovat.  Vzhledem k tomu, že jste změnili dostupné popisky obrázku, zvolíte způsob, jakým budete zacházet s již označenými daty:
    * Začněte znovu a odeberete všechny existující popisky.  Tuto možnost zvolte, pokud chcete začít s popisky od začátku novou úplnou sadou popisků. 
    * Začněte znovu a zachovte všechny existující štítky.  Tuto možnost zvolte, chcete-li označit všechna data jako neoznačená, ale zachovat existující popisky jako výchozí značku pro obrazy, které byly dříve označeny.
    * Pokračujte a zachovte všechny existující popisky. Tuto možnost zvolte, chcete-li zachovat všechna data, která jsou již označena tak, jak jsou, a začněte používat nový popisek pro data, která ještě nebyla označena.
1. Podle potřeby upravte stránku s pokyny pro nové štítky.
1. Po přidání všech nových popisků vyberte v horní části stránky **možnost Start,** chcete-li projekt restartovat.  

## <a name="export-the-labels"></a>Export štítků

Data popisků pro experimentování machine learningu můžete kdykoli exportovat. Popisky obrázků lze exportovat ve [formátu COCO](http://cocodataset.org/#format-data) nebo jako datovou sadu Azure Machine Learning. Tlačítko **Exportovat** na stránce **Podrobnosti projektu** projektu projektu s popisky.

Soubor COCO se vytvoří ve výchozím úložišti objektů blob pracovního prostoru Azure Machine Learning ve složce v *rámci exportu/kokosu*. K exportované datové sadě Azure Machine Learning můžete přistupovat v části **Datové sady** v Machine Learningu. Stránka podrobností datové sady také poskytuje ukázkový kód pro přístup k vašim popiskům z Pythonu.

![Exportovaná datová sada](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Další kroky

* Popisky obrázků pro [klasifikaci obrázků nebo detekci objektů](how-to-label-images.md)
* Další informace o [Azure Machine Learning a Machine Learning Studio (klasické)](compare-azure-ml-to-studio-classic.md)
