---
title: Označení obrazů v projektu popisků
title.suffix: Azure Machine Learning
description: Zjistěte, jak používat nástroje pro označování dat v projektu označování Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 2e14b669aadeec4c6a7245be8940051ec604059b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79409554"
---
# <a name="tag-images-in-a-labeling-project"></a>Označení obrazů v projektu popisků

Poté, co správce projektu [vytvoří projekt označování](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) v Azure Machine Learning, můžete použít nástroj pro označování rychle připravit data pro projekt Machine Learning. Tento článek popisuje:

> [!div class="checklist"]
> * Jak získat přístup k projektům označování
> * Nástroje pro označování
> * Jak používat nástroje pro konkrétní úkoly popisky

## <a name="prerequisites"></a>Požadavky

* Adresa URL portálu pro označování dat pro spuštěný projekt označování dat
* [Účet Microsoft](https://account.microsoft.com/account) nebo účet Azure Active Directory pro organizaci a projekt

> [!NOTE]
> Správce projektu může najít adresu URL portálu pro označování na kartě **Podrobnosti** na stránce **Podrobnosti projektu.**

## <a name="sign-in-to-the-projects-labeling-portal"></a>Přihlášení k portálu pro označování projektu

Přejděte na adresu URL portálu pro označování, kterou poskytuje správce projektu. Přihlaste se pomocí e-mailového účtu, který správce použil k přidání do týmu. Pro většinu uživatelů to bude váš účet Microsoft. Pokud projekt označování používá Azure Active Directory, tak se přihlašujete.

## <a name="understand-the-labeling-task"></a>Principy úkolu popisování

Po přihlášení se zobrazí stránka s přehledem projektu.

Přejděte na **zobrazit podrobné pokyny**. Tyto pokyny jsou specifické pro váš projekt. Vysvětlují typ dat, kterým čelíte, jak byste se měli rozhodovat, a další relevantní informace. Po přečtení těchto informací se vraťte na stránku projektu a vyberte **Spustit popisování**.

## <a name="common-features-of-the-labeling-task"></a>Společné rysy úlohy označování

Ve všech úlohách označování obrázků zvolíte příslušnou značku nebo značky ze sady určené správcem projektu. Prvních devět značek můžete vybrat pomocí číselné klávesy na klávesnici.  

Při úlohách klasifikace obrázků můžete zvolit zobrazení více obrazů současně. Pomocí ikon nad oblastí obrázku vyberte rozložení. Chcete-li vybrat všechny zobrazené obrázky současně, použijte **možnost Vybrat vše**. Chcete-li vybrat jednotlivé obrazy, použijte kruhové tlačítko výběru v pravém horním rohu obrazu. Chcete-li značku použít, musíte vybrat alespoň jeden obrázek. Pokud vyberete více obrazů, všechny vybrané značky se použijí na všechny vybrané obrazy.

Zde jsme si vybrali rozložení dva po dvou a chystáme se použít značku "Mammal" na obrazy medvěda a kosatky. Obrázek žraloka byl již označen jako "chrupavčitá ryba" a leguán ještě nebyl označen.

![Více rozložení a výběr obrázků](./media/how-to-label-images/layouts.png)

> [!Important] 
> Rozložení přepínejte pouze v případě, že máte novou stránku s neoznačenými daty. Přepínání rozložení vymaže probíhající tagování stránky.

Azure povolí **tlačítko Odeslat,** když jste označili všechny obrázky na stránce. Chcete-li uložit svou práci, vyberte **Odeslat.**

Po odeslání značek pro data po ruce Azure aktualizuje stránku s novou sadou ibiz z pracovní fronty.

### <a name="assisted-machine-learning"></a>Strojové učení s asistencí 

Algoritmy strojového učení se mohou aktivovat během úlohy klasifikace více tříd nebo více popisků. Pokud jsou tyto algoritmy v projektu povoleny, může se zobrazit následující:

* Po označení určitého množství obrázků se mohou v horní části obrazovky vedle názvu projektu **zobrazit úkoly seskupené** v horní části obrazovky.  To znamená, že obrázky jsou seskupeny tak, aby na stejné stránce představovaly podobné obrázky.  Pokud ano, přepněte do jednoho z více zobrazení obrázku, abyste mohli využít výhod seskupení.  

* Později se může zobrazit **úkoly předoznačované** vedle názvu projektu.  Obrázky se pak zobrazí s navrhovaným popiskem, který pochází z modelu klasifikace strojového učení. Žádný model strojového učení nemá 100% přesnost. Zatímco používáme pouze obrázky, pro které je model jistý, tyto obrázky mohou být stále nesprávně předznačené.  Když se zobrazí tyto popisky, před odesláním stránky oopravte všechny nesprávné štítky.  

Zejména v rané fázi projektu označování může být model strojového učení dostatečně přesný, aby předem označil malou podmnožinu obrazů. Jakmile jsou tyto obrázky označeny, projekt označování se vrátí k ručnímu označování, aby shromáždil další data pro další kolo školení modelu. V průběhu času bude model jistější ohledně vyššího podílu obrázků, což povede k dalším úkolům s předznačkovací značkou později v projektu.

## <a name="tag-images-for-multi-class-classification"></a>Označení obrázků pro klasifikaci více tříd

Pokud je váš projekt typu "Klasifikace obrázků více třídy", přiřadíte k celému obrázku jednu značku. Pokyny chcete kdykoli zkontrolovat, přejděte na stránku **Pokyny** a vyberte **Zobrazit podrobné pokyny**.

Pokud si uvědomíte, že jste po přiřazení značky k obrázku udělali chybu, můžete ji opravit. Vyberte **"X**" na štítku, který je zobrazen pod obrázkem, aby se značka vyčistila. Nebo vyberte obrázek a zvolte jinou třídu. Nově vybraná hodnota nahradí dříve použitou značku.

## <a name="tag-images-for-multi-label-classification"></a>Označení obrázků pro klasifikaci s více popisky

Pokud pracujete na projektu typu "Klasifikace obrázků s více popisky", použijete na obrázek jednu *nebo více* značek. Chcete-li zobrazit pokyny pro konkrétní projekt, vyberte **možnost Pokyny** a přejděte na zobrazit **podrobné pokyny**.

Vyberte obraz, který chcete označit, a pak vyberte značku. Tag se aplikuje na všechny vybrané obrazy a pak se obrazy odznačí. Chcete-li použít více tagů, musíte obrazy znovu vybrat. Následující animace ukazuje více-label značkování:

1. **Vyberte vše,** co se používá k použití značky "Oceán".
1. Je vybrán jeden obrázek a označen "Detailní snímek".
1. Tři obrazy jsou vybrány a označeny "Široký úhel".

![Animace zobrazuje tok více popisků](./media/how-to-label-images/multilabel.gif)

Chcete-li chybu opravit, klepnutím na tlačítko **"X"** vymažte jednotlivé značky nebo vyberte obrázky a pak vyberte značku, která vymaže značku ze všech vybraných obrazů. Tento scénář je zobrazen zde. Kliknutím na "Země" se tato značka ze dvou vybraných obrázků vymaže.

![Snímek obrazovky ukazuje několik deelections](./media/how-to-label-images/multiple-deselection.png)

Azure povolí tlačítko **Odeslat** jenom po použití alespoň jedné značky pro každou bitovou kopii. Chcete-li uložit svou práci, vyberte **Odeslat.**


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Označení obrazů a určení ohraničovacích rámečků pro detekci objektů

Pokud je projekt typu "Identifikace objektu (ohraničovací rámečky)," zadáte v obraze jeden nebo více ohraničovacích rámečků a na každé pole použijete značku. Obrazy mohou mít více ohraničovacích rámečků, každý s jednou značkou. Pomocí **podrobných pokynů zobrazení** určete, zda se v projektu používá více ohraničovacích rámečků.

1. Vyberte značku pro ohraničovací rámeček, který chcete vytvořit.
1. Vyberte nástroj](./media/how-to-label-images/rectangular-box-tool.png) **obdélníkový rámeček** ![Obdélníkový rámeček nebo vyberte "R".
3. Kliknutím a tavým diagonálně přes cíl vytvořte hrubý ohraničovací rámeček. Chcete-li upravit ohraničovací rámeček, přetáhněte okraje nebo rohy.

![Snímek obrazovky zobrazuje základní vytvoření ohraničovacího rámečku.](./media/how-to-label-images/bounding-box-sequence.png)

Chcete-li odstranit ohraničovací rámeček, klepněte na cíl ve tvaru písmene X, který se po vytvoření zobrazí vedle ohraničovacího rámečku.

Značku existujícího ohraničovacího rámečku nelze změnit. Pokud uděláte chybu přiřazení značky, musíte odstranit ohraničovací rámeček a vytvořit nový se správnou značkou.

Ve výchozím nastavení můžete upravovat existující ohraničovací rámečky. Nástroj **Zamknout/odemknout oblasti** ![Zamknout/odemknout oblasti nebo](./media/how-to-label-images/lock-bounding-boxes-tool.png) "L" přepíná toto chování. Pokud jsou oblasti zamknuté, můžete změnit pouze tvar nebo umístění nového ohraničovacího rámečku.

Pomocí **manipulačního** ![](./media/how-to-label-images/regions-tool.png) nástroje Oblasti oblastí nebo "M" nastavte existující ohraničovací rámeček. Přetažením hran nebo rohů upravte tvar. Klikněte do interiéru, abyste mohli přetáhnout celý ohraničovací rámeček. Pokud oblast nemůžete upravit, pravděpodobně jste přepnei nástroj **Zamknout/odemknout oblasti.**

Pomocí nástroje](./media/how-to-label-images/template-box-tool.png) **šablona-založené box** ![šablona-box box nebo "T" vytvořit více ohraničovacích rámečků stejné velikosti. Pokud obrázek nemá žádné ohraničovací rámečky a aktivujete pole založená na šablonách, nástroj vytvoří rámečky o rozměrech 50 x 50 pixelů. Pokud vytvoříte ohraničovací rámeček a pak aktivujete pole založená na šablonách, budou mít všechna nová ohraničovací rámečky velikost posledního pole, které jste vytvořili. Po umístění lze velikost polí založených na šablonách. Změna velikosti pole založeného na šabloně pouze změní velikost tohoto konkrétního pole.

Chcete-li odstranit *všechna* ohraničovací pole v ![aktuálním](./media/how-to-label-images/delete-regions-tool.png)obraze, vyberte nástroj **Odstranit všechny oblasti** Odstranit oblasti .

Po vytvoření ohraničovacích rámečků pro obrázek vyberte **Odeslat,** chcete-li uložit svou práci, jinak se vaše nedokončená práce neuloží.

## <a name="finish-up"></a>Dokončení

Když odešlete stránku označených dat, Azure vám přiřadí nová data bez popisku z pracovní fronty. Pokud nejsou k dispozici žádná další neoznačená data, zobrazí se zpráva s uznanou majestátem spolu s odkazem na domovskou stránku portálu.

Po dokončení označování vyberte své jméno v pravém horním rohu portálu pro označování a pak vyberte **odhlásit .** Pokud se neodhlásíte, Azure vás nakonec "vyhotovuje" a přiřadí vaše data jinému labeleru.

## <a name="next-steps"></a>Další kroky

* Naučte se [trénovat modely klasifikace obrázků v Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

