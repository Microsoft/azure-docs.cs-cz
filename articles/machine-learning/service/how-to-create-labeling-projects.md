---
title: Získat popisky pro data
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte, jak vytvořit a spustit označování projektů k označení dat pro strojové učení.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 76f995901814c90ff9fd78585c98d56b3478e8b4
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612757"
---
# <a name="get-labels-for-data"></a>Získat popisky pro data

Popisování velkých objemů dat je často starostí v projektech strojového učení. Projekty ML s komponentou pro počítačové zpracování obrazu, jako je například klasifikace obrázku nebo detekce objektu, obecně vyžadují tisíce imagí a odpovídající popisky. 
 
Azure Machine Learning Studio poskytuje centrální umístění pro vytváření, správu a sledování projektů označování. Označování projektů usnadňuje koordinaci dat, popisků a členů týmu, což vám umožní efektivněji spravovat úlohy označování. Aktuálně podporované úlohy jsou klasifikace obrázku, a to buď pomocí ohraničených polí, nebo s více třídami, nebo s identifikací objektů.

Azure sleduje průběh a udržuje frontu neúplných úloh označování. Nálepky nevyžadují, aby se účet Azure účastnil. Po ověření pomocí svého účtu Microsoft (MSA) nebo [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)můžou dělat tolik a méně popisků, kolik to čas povoluje. Pomocí klávesových zkratek můžou popisky přiřazovat a měnit. 

Můžete spouštět a zastavovat projekt, přidávat a odebírat lidi a týmy a sledovat průběh. Data s popisky můžete exportovat buď ve formátu díky Coco, nebo jako datovou sadu Azure ML. 

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření projektu
> * Zadat data a strukturu projektu
> * Správa týmů a lidí pracujících na projektu
> * Spuštění a sledování projektu
> * Exportovat popisky 

## <a name="prerequisites"></a>Požadavky

* Data, která chcete označit, buď v místních souborech nebo již ve službě Azure Storage
* Sada popisků, které chcete použít
* Pokyny k označování
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Vytvoření projektu s popisem

Označování projektů je spravováno z [Azure Machine Learning studia](https://ml.azure.com/). Stránka s **popisem projektů** vám umožní spravovat projekty, týmy a lidi. K projektu je přiřazen jeden nebo více týmů a tým má přiřazeného jednoho nebo více lidí. 

Pokud jsou vaše data už uložená v úložišti objektů BLOB v Azure, měli byste je před vytvořením projektu s popisem zpřístupnit jako úložiště dat. Informace najdete v tématu [Vytvoření a registrace úložišť dat](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores). 

Chcete-li vytvořit projekt, klikněte na tlačítko **Přidat projekt**. Zadejte odpovídající název a vyberte **typ úlohy označování**. 

![Průvodce vytvořením projektu s popisem](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Pro projekty, ve kterých se dá použít **jeden _nebo více_**  štítků ze sady tříd pro obrázek, vyberte **Vícenásobný popisek klasifikace imagí** . Například fotografie psa může být označená pomocí *psa* i *Daytime* .
* Volba **klasifikace imagí multi-Class** pro projekty, ve kterých se dá použít jenom **jedna třída** ze sady tříd, pro obrázek
* Zvolte **identifikaci objektu (ohraničující rámeček)** pro projekty, ve kterých je úkol pro přiřazení třídy k objektu v rámci obrázku, a určení ohraničujícího pole kolem objektu.

Až budete připraveni na přesun, klikněte na tlačítko **Další** .

## <a name="specify-data-to-be-labeled"></a>Zadat data, která mají být označena

Pokud jste už vytvořili datovou sadu obsahující vaše data, můžete ji vybrat v rozevíracím seznamu **Vybrat existující datovou sadu** . Nebo zvolte možnost **vytvořit datovou sadu** pro výběr existujícího úložiště dat Azure nebo pro nahrání místních souborů. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Vytvoření datové sady z úložiště Azure

I když volba přímé nahrávání místních souborů je v mnoha případech pro použití velmi spolehlivá, [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) je robustnější a rychlejší pro přenos významných objemů dat. Doporučujeme, abyste Průzkumník služby Azure Storage jako výchozí způsob přesunutí souborů.

Chcete-li vytvořit datovou sadu z dat, která již byla uložena v úložišti objektů BLOB v Azure:

1. Volba možnosti **vytvořit datovou sadu** a **úložiště** dat
1. Přiřazení **názvu** pro datovou sadu
1. Jako **Typ datové sady** musíte zvolit "soubor".  
1. Vyberte úložiště dat. 
1. Pokud jsou vaše data v podsložce v úložišti objektů blob, zvolte **Procházet** a vyberte cestu. 
    * Můžete navíc připojit `/**` poté, co cesta bude obsahovat všechny soubory v podsložkách vybrané cesty.
    * Pomocí `**/*.*` zahrňte všechna data v aktuálním kontejneru a v jejích podsložkách.
1. Zadejte popis datové sady.
1. Zvolit **Další** 
1. Potvrďte podrobnosti. Můžete si vybrat **zpátky** a upravit nastavení nebo vytvořit datovou sadu kliknutím na **vytvořit** .

### <a name="create-a-dataset-by-uploading-data"></a>Vytvoření datové sady pomocí nahrávání dat

Pokud chcete nahrávat přímo data:

1. Volba možnosti **vytvořit datovou sadu** a **z místních souborů**
1. Přiřazení **názvu** pro datovou sadu
1. Jako **Typ datové sady** musíte zvolit "soubor".
1. Pokud zvolíte **Rozšířená nastavení**, můžete přizpůsobit úložiště dat, kontejner a cestu k datům.
1. Zvolte **Procházet** a vyberte místní soubory k nahrání.
1. Zadejte popis datové sady.
1. Zvolit **Další** 
1. Potvrďte podrobnosti. Můžete si vybrat **zpátky** a upravit nastavení nebo vytvořit datovou sadu kliknutím na **vytvořit** .

Data se nahrají do výchozího úložiště objektů BLOB (`workspaceblobstore`) vašeho pracovního prostoru Azure ML.

## <a name="specify-label-classes"></a>Určení tříd popisků

Na stránce **třídy popisků** zadáte sadu tříd, které se používají k kategorizaci vašich dat. Přidělte se k těmto třídám, protože přesnost a rychlost štítků bude ovlivněna tím, s jakou se můžou snadno vybírat. Například namísto hláskování úplných rodů a druhů pro rostliny nebo zvířata může být vhodnější použít kódy polí nebo zkrátit rod. 

Zadejte jeden popisek na řádek a pomocí tlačítka **+** přidejte nový řádek. Pokud máte více než 3 nebo 4 popisky, ale méně než 10, zvažte jejich prefixování s použitím "1:", "2:" atd. k poskytnutí některých pokynů pro popisky pomocí klávesových zkratek pro zrychlení práce. 

## <a name="describe-the-labeling-task"></a>Popsat úlohu označování

Jasně se vysvětluje, že je úloha označování důležitá. Stránka s **pokyny k označování** vám umožní propojit externí web s pokyny, které je třeba předložit popiskům. Pro cílovou skupinu ponechejte pokyny orientované na úlohy a vhodné. 

* Jaké jsou popisky, které uvidí a jak mezi nimi budou volit? Existuje referenční text, na který by se měly odkazovat?
* Co má dělat, pokud se žádný popisek nejeví jako vhodný? 
* Co byste měli dělat, pokud je vhodné více štítků?
* Jaká prahová hodnota spolehlivosti by měla platit pro popisek? Chcete jejich "nejlepší odhad", pokud nejsou konkrétní?
* Co byste měli dělat s částečně zastíněna nebo překrývajícími se objekty, které vás zajímají?
* Co byste měli dělat, pokud je objekt zájmu oříznutý okrajem obrázku?
* Co byste měli dělat, pokud se jim zdá, že po odeslání udělali chybu s obrázkem? 

S ohraničujícími poli jsou další důležité otázky:

* Jak se definuje ohraničovací rámeček pro tuto úlohu? Mělo by být zcela vnitřní k objektu, co je co nejpřesněji možné do svého rozsahu nebo kolik je přijatelné pro určitý prostor. 
* Jakou úroveň péče a konzistence očekáváte, že se má popisek použít pro definování ohraničujících polí?

>[!Note]
> Nezapomeňte si být jisti, že popisek bude moci zvolit mezi prvními 9 štítky a čísly klíče 1-9. 

## <a name="initialize-the-labeling-project"></a>Inicializovat projekt značení

Po inicializaci některé aspekty projektu označování nejsou proměnlivé: nemůžete změnit typ nebo datovou sadu úlohy. Můžete změnit popisky a změnit adresu URL pro popis úlohy. Před vytvořením projektu pečlivě zkontrolujte nastavení. Po odeslání projektu se vrátíte na domovskou stránku s **popisem** , která zobrazí projekt jako **inicializovaný**. Tato stránka se neaktualizuje, takže po určité době ji ručně aktualizujete a zobrazí se projekt jako **vytvořený**. 

## <a name="manage-teams-and-people"></a>Správa týmů a lidí

Projekt označování Získá výchozí tým a přidá vás jako výchozího člena. Každý projekt označování získá nový výchozí tým, ale týmy lze sdílet mezi projekty. Projekty mohou mít více než jeden tým. Vytvoření týmu se provádí výběrem možnosti **Přidat tým** na stránce **týmy** . 

Lidé se spravují na stránce **lidé** . Na své e-mailové adresy můžete přidat a odebrat osoby. Každý štítek bude nutné provést ověření pomocí svého účtu Microsoft nebo Azure Active Directory, pokud ho používáte.  

Po přidání osoby ji můžete přiřadit jednomu nebo více týmům. Přejděte na stránku **týmy** , vyberte konkrétní tým, pro který vás zajímáte, a pak podle potřeby použijte **přiřazení osob** nebo **odeberte lidi** .

Pokud někdy chcete poslat e-mail všem členům týmu, můžete to udělat tak, že zvolíte tým, který zobrazí stránku **podrobností týmu** . Na této stránce se v **e-mailovém týmu** otevře editor e-mailů s adresami všech členů týmu.

## <a name="run-and-monitor-the-project"></a>Spuštění a sledování projektu

Jakmile se projekt inicializuje, Azure ho spustí. Pokud na stránce s hlavním **popiskem** kliknete na projekt, budete mít k **disprojektové podrobnosti**. Na kartě **řídicí panel** se zobrazí průběh úkolu s popisem. 

Na kartě **data** si můžete prohlédnout datovou sadu a zkontrolovat data s popisky. Pokud vidíte, že data jsou nesprávně označená, můžete je **Vybrat** a zvolit **odmítnout**. tím se odeberou popisky a data se převedou zpátky do neoznačené fronty. 

Na kartě **tým** můžete přiřadit nebo zrušit přiřazení týmů k tomuto projektu. 

Chcete-li projekt převést do režimu offline nebo online, klikněte na tlačítko **pozastavit**/**Start** , které přepne stav spuštění projektu.

Data můžete označit přímo ze stránky **Project Details (podrobnosti projektu** ), a to tak, že vyberete **Data popisku**. Data můžete označovat pouze v případě, že projekt běží. 

## <a name="export-the-labels"></a>Exportovat popisky

V každém okamžiku můžete exportovat data popisku pro experimentování ve službě Machine Learning. Popisky obrázků je možné exportovat ve [formátu díky Coco](http://cocodataset.org/#format-data) nebo jako datovou sadu Azure ml. Tlačítko **exportovat** najdete na stránce Project **Details (podrobnosti projektu** ) vašeho projektu s popisem.

Soubor díky Coco se vytvoří ve výchozím úložišti objektů BLOB pracovního prostoru Azure ML ve složce v rámci **exportu/díky Coco**. Exportovanou datovou sadu Azure ML můžete zpřístupnit v části datové **sady** sady Studio. Stránka s podrobnostmi datové sady také poskytuje vzorový kód pro přístup k popiskům z Pythonu.

![Exportovaná datová sada](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Další kroky

* Obrázky popisků pro [klasifikaci obrázku nebo detekci objektů](how-to-label-images.md)
* Další informace o [Azure Machine Learning a studiu](../compare-azure-ml-to-studio-classic.md)