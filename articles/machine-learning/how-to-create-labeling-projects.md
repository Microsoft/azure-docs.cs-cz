---
title: Vytvořit projekt pro označování dat
titleSuffix: Azure Machine Learning
description: Přečtěte si o tom, jak vytvořit a spustit označování projektů k označení dat pro strojové učení.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 864cccc4629140754a326823cbaebd7ad8933d3d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75765065"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Vytvoření popisku dat pro projekt a Export popisků 

Popisování voluminous dat v projektech Machine Learning je často starostí. Projekty, které mají komponentu počítačového vidění, jako je například klasifikace obrázku nebo detekce objektu, obecně vyžadují popisky pro tisíce imagí.
 
[Azure Machine Learning](https://ml.azure.com/) poskytuje centrální místo pro vytváření, správu a sledování projektů označování. Slouží ke koordinaci dat, popisků a členů týmu, aby bylo možné efektivně spravovat úkoly označování. Machine Learning podporuje klasifikaci obrázků, a to buď více popisků, více tříd, a identifikaci objektů společně s ohraničenými poli.

Machine Learning sleduje průběh a udržuje frontu neúplných úloh označování. Nálepky nepotřebují k účasti účet Azure. Po ověření pomocí účet Microsoft nebo [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)můžou dělat tolik popisků, kolik jejich doba umožňuje.

V Machine Learning spustíte a zastavíte projekt, přidáte a odeberete lidi a týmy a sledujete průběh. Můžete exportovat označená data ve formátu díky Coco nebo jako datovou sadu Azure Machine Learning.

> [!Important]
> V současné době jsou podporovány pouze projekty klasifikace obrázků a označení identifikace objektu. Kromě toho musí být image dat dostupné v úložišti dat objektů BLOB v Azure. (Pokud nemáte existující úložiště dat, můžete při vytváření projektu nahrávat obrázky.) 

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření projektu
> * Zadat data a strukturu projektu
> * Správa týmů a osob, které pracují na projektu
> * Spuštění a sledování projektu
> * Exportovat popisky


## <a name="prerequisites"></a>Požadavky

* Data, která chcete označit, buď v místních souborech nebo ve službě Azure Storage.
* Sada popisků, které chcete použít.
* Pokyny pro označování.
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree) před tím, než začnete.
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Vytvoření projektu s popisem

Označování projektů je spravováno z Azure Machine Learning. Stránku s **popisem projektů** můžete použít ke správě projektů a lidí. K projektu je přiřazen jeden nebo více týmů a tým má přiřazeného jednoho nebo více lidí.

Pokud vaše data jsou už v úložišti objektů BLOB v Azure, měli byste je před vytvořením projektu s popisem zpřístupnit jako úložiště dat. Podrobnosti najdete v tématu [Vytvoření a registrace úložišť dat](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores).

Chcete-li vytvořit projekt, vyberte možnost **Přidat projekt**. Dejte projektu vhodný název a vyberte **typ úlohy označování**.

![Průvodce vytvořením projektu s popisem](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Pokud chcete použít *jeden nebo více* štítků ze sady tříd na obrázek, vyberte možnost **klasifikace obrázků – vícenásobné označení** pro projekty. Například fotografie psa může být označená pomocí *psa* i *Daytime*.
* Vyberte možnost **klasifikace obrázku více tříd** pro projekty, pokud chcete použít pouze *jednu třídu* ze sady tříd na obrázek.
* Vyberte možnost **Identifikace objektu (ohraničovací rámeček)** pro projekty, pokud chcete přiřadit třídu a ohraničující rámeček ke každému objektu v rámci obrázku.

Až budete připraveni pokračovat, vyberte **Další** .

## <a name="specify-the-data-to-label"></a>Zadat data k označení

Pokud jste už vytvořili datovou sadu, která obsahuje vaše data, vyberte ji v rozevíracím seznamu **Vyberte existující datovou sadu** . Nebo vyberte možnost **vytvořit datovou sadu** pro použití stávajícího úložiště Azure DataStore nebo k nahrání místních souborů.

### <a name="create-a-dataset-from-an-azure-datastore"></a>Vytvoření datové sady z úložiště Azure

V mnoha případech je přesně možné pouze nahrávat místní soubory. [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) ale nabízí rychlejší a robustnější způsob přenosu velkého množství dat. Doporučujeme, abyste Průzkumník služby Storage jako výchozí způsob přesunutí souborů.

Chcete-li vytvořit datovou sadu z dat, která již byla uložena v úložišti objektů BLOB v Azure:

1. Vyberte **vytvořit datovou sadu** > **z úložiště dat**.
1. Přiřaďte k datové sadě **název** .
1. Jako **Typ datové sady**vyberte **soubor** .  
1. Vyberte úložiště dat.
1. Pokud jsou vaše data v podsložce v úložišti objektů blob, zvolte **Procházet** a vyberte cestu.
    * Přidejte do cesty znak "/* *", chcete-li zahrnout všechny soubory do podsložek vybrané cesty.
    * Připojíte * */* . * a zahrnete všechna data v aktuálním kontejneru a jejích podsložkách.
1. Zadejte popis pro datovou sadu.
1. Vyberte **Next** (Další).
1. Potvrďte podrobnosti. Výběrem **zpět** upravíte nastavení nebo **vytvořte** datovou sadu.

### <a name="create-a-dataset-from-uploaded-data"></a>Vytvoření datové sady z nahraných dat

Přímé nahrání dat:

1. Vyberte **vytvořit datovou sadu** > **z místních souborů**.
1. Přiřaďte k datové sadě **název** .
1. Jako **Typ datové sady**vyberte "soubor".
1. *Volitelné:* Vyberte **Upřesnit nastavení** a přizpůsobte úložiště dat, kontejner a cestu k datům.
1. Vyberte **Procházet** a vyberte místní soubory, které se mají nahrát.
1. Zadejte popis datové sady.
1. Vyberte **Next** (Další).
1. Potvrďte podrobnosti. Výběrem **zpět** upravíte nastavení nebo **vytvořte** datovou sadu.

Data se nahrají do výchozího úložiště objektů BLOB (workspaceblobstore) pracovního prostoru Machine Learning.

## <a name="specify-label-classes"></a>Určení tříd popisků

Na stránce **třídy popisků** určete sadu tříd pro kategorizaci dat. Provedete to pečlivě, protože přesnost a rychlost štítků bude ovlivněna jejich možností výběru mezi třídami. Například namísto kontroly pravopisu úplných rodů a druhů pro rostliny nebo živočichy použijte kód pole nebo zkratku rodu.

Zadejte jeden popisek na řádek. Chcete-li přidat nový řádek, použijte tlačítko **+** . Pokud máte více než 3 nebo 4 popisky, ale méně než 10, je možné, že budete chtít názvy označit čísly ("1:", "2:"), aby je mohli pomocí těchto klávesových zkratek zrychlit.

## <a name="describe-the-labeling-task"></a>Popsat úlohu označování

Je důležité jasně vysvětlit úlohu označování. Na stránce **pokyny k označování** můžete přidat odkaz na externí web pro pokyny k označování. Pro cílovou skupinu ponechejte pokyny orientované na úlohy a vhodné. Vezměte v úvahu tyto otázky:

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

>[!NOTE]
> Nezapomeňte si uvědomit, že popisky budou moci vybrat prvních 9 popisků pomocí číselných klíčů 1-9.

## <a name="initialize-the-labeling-project"></a>Inicializovat projekt značení

Po inicializaci projektu značení jsou některé aspekty projektu neměnné. Nemůžete změnit typ nebo datovou sadu úlohy. *Můžete* změnit popisky a adresu URL pro popis úlohy. Před vytvořením projektu pečlivě zkontrolujte nastavení. Po odeslání projektu se vrátíte na domovskou stránku s **popisem** , která zobrazí projekt jako **inicializovaný**. Tato stránka se automaticky neaktualizuje. Takže po pozastavení ručně aktualizujte stránku, aby se zobrazil stav projektu jako **vytvořený**.

## <a name="manage-teams-and-people"></a>Správa týmů a lidí

Ve výchozím nastavení každý vytvořený projekt štítků získá nový tým s vámi jako členem. Týmy ale můžou být taky sdílené mezi projekty. A projekty mohou mít více než jeden tým. Chcete-li vytvořit tým, vyberte na stránce **týmy** možnost **Přidat tým** .

Uživatele můžete spravovat na stránce **lidé** . Přidejte nebo odeberte lidi podle e-mailové adresy. Každý štítek se musí ověřit prostřednictvím účet Microsoft nebo Azure Active Directory, pokud ho používáte.  

Po přidání osoby můžete tuto osobu přiřadit k jednomu nebo více týmům: Přejít na stránku **týmy** , vybrat tým a pak vybrat **přiřadit lidi** nebo **Odebrat lidi**.

Pokud chcete týmu poslat e-mail, vyberte tým, na kterém se zobrazí stránka **Podrobnosti o týmu** . Na této stránce vyberte **e-mailový tým** a otevřete tak e-mailový koncept s adresami všech členů týmu.

## <a name="run-and-monitor-the-project"></a>Spuštění a sledování projektu

Po inicializaci projektu ho Azure spustí. Vyberte projekt na hlavní stránce s **popisem** , abyste přešli na **Podrobnosti o projektu**. Karta **řídicí panel** zobrazuje průběh úlohy označování.

Na kartě **data** můžete zobrazit datovou sadu a zkontrolovat data s popisky. Pokud se zobrazí nesprávně označená data, vyberte je a zvolte **odmítnout**. tím se odeberou popisky a data se převedou zpátky do fronty bez označení.

Použijte kartu **tým** k přiřazení nebo zrušení přiřazení týmů k projektu.

Chcete-li pozastavit nebo restartovat projekt, vyberte tlačítko **pozastavit**/**Start** . Data můžete označovat pouze v případě, že projekt běží.

Data můžete označit přímo ze stránky **Project Details (podrobnosti projektu** ), a to tak, že vyberete **Data popisku**.

## <a name="export-the-labels"></a>Exportovat popisky

Data popisku můžete exportovat pro Machine Learning experimentování kdykoli. Popisky obrázků lze exportovat ve [formátu díky Coco](http://cocodataset.org/#format-data) nebo jako datovou sadu Azure Machine Learning. Použijte tlačítko **exportovat** na stránce Project **Details (podrobnosti projektu** ) vašeho projektu s popisem.

Soubor díky Coco se vytvoří ve výchozím úložišti objektů BLOB v pracovním prostoru Azure Machine Learning ve složce v rámci *exportu/díky Coco*. Exportovaný Azure Machine Learning datovou sadu můžete zpřístupnit v části datové **sady** v Machine Learning. Stránka s podrobnostmi datové sady také poskytuje vzorový kód pro přístup k popiskům z Pythonu.

![Exportovaná datová sada](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Další kroky

* Obrázky popisků pro [klasifikaci obrázku nebo detekci objektů](how-to-label-images.md)
* Další informace o [Azure Machine Learning a Machine Learning Studio (Classic)](compare-azure-ml-to-studio-classic.md)
