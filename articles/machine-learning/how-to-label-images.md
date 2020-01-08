---
title: Označení obrázků v projektu s popisem
title.suffix: Azure Machine Learning
description: Naučte se používat nástroje pro označování dat v Azure Machine Learning projektu značení.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7ff3651d082f1649326e1b41b13fa177069c40d3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533367"
---
# <a name="tag-images-in-a-labeling-project"></a>Označení obrázků v projektu s popisem

Poté, co správce projektu [vytvoří projekt označování](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-labeling-projects#create-a-labeling-project) v Azure Machine Learning, můžete použít nástroj pro popisování k rychlé přípravě dat projektu Machine Learning. Tento článek popisuje:

> [!div class="checklist"]
> * Jak získat přístup k projektům označování
> * Nástroje pro označování
> * Jak používat nástroje pro konkrétní úkoly označování

## <a name="prerequisites"></a>Požadavky

* Adresa URL portálu pro popisky pro běžící projekt pro popisky dat
* [Účet Microsoft](https://account.microsoft.com/account) nebo účet Azure Active Directory pro organizaci a projekt

> [!NOTE]
> Správce projektu může na kartě **Podrobnosti** stránky s **podrobnostmi projektu** najít adresu URL portálu pro označování.

## <a name="sign-in-to-the-projects-labeling-portal"></a>Přihlaste se k portálu pro označování projektu.

Přejít na adresu URL portálu pro označování, kterou poskytuje správce projektu. Přihlaste se pomocí e-mailového účtu, který správce použil k přidání do týmu. U většiny uživatelů se jedná o účet Microsoft. Pokud se v projektu používá označení Azure Active Directory, to je způsob, jakým se budete přihlašovat.

## <a name="understand-the-labeling-task"></a>Pochopení úlohy označování

Po přihlášení se zobrazí stránka přehled projektu.

Projděte si **podrobné pokyny**. Tyto pokyny jsou specifické pro váš projekt. Vysvětlují typ dat, na které máte přístup, jak byste měli učinit rozhodnutí a další důležité informace. Po přečtení těchto informací se vraťte na stránku projektu a vyberte možnost **Spustit označování**.

## <a name="common-features-of-the-labeling-task"></a>Běžné funkce úlohy označování

V části všechny úlohy označování imagí můžete zvolit vhodnou značku nebo značky ze sady určené správcem projektu. První devět značek můžete vybrat pomocí klávesových zkratek na klávesnici.  

V úkolech klasifikace obrázků můžete zvolit zobrazení více imagí současně. Pro výběr rozložení použijte ikony nad oblastí obrázku. Chcete-li vybrat všechny zobrazené obrázky současně, použijte **možnost Vybrat vše**. Chcete-li vybrat jednotlivé obrázky, použijte tlačítko kruhového výběru v pravém horním rohu obrázku. Chcete-li použít značku, je nutné vybrat alespoň jeden obrázek. Vyberete-li více obrázků, bude pro všechny vybrané bitové kopie použita jakákoli vybraná značka.

Tady jsme zvolili oboustranné rozložení a chystáme se použít značku "Savcé" na obrázky z nosičů a Orca. Obrázek žraloka už je označený jako "Cartilaginous ryba" a iguana ještě není označený.

![Více rozložení a výběr obrázků](./media/how-to-label-images/layouts.png)

> [!Important] 
> Pokud máte novou stránku neoznačených dat, přepínejte jenom rozložení. Přepínání rozložení vymaže práci s označováním probíhajících prvků stránky.

Azure povolí tlačítko **Odeslat** , když jste označili všechny obrázky na stránce. Vyberte **Odeslat** a uložte svou práci.

Po odeslání značek pro data v Azure aktualizuje stránku pomocí nové sady imagí z pracovní fronty.

## <a name="tag-images-for-multi-class-classification"></a>Obrázky značek pro klasifikaci více tříd

Pokud je váš projekt typu "Multi-Class klasifikace obrázku", přiřadíte k celému obrázku jednu značku. Pokud si chcete projít pokyny kdykoli, navštivte stránku s **pokyny** a vyberte **Zobrazit podrobné pokyny**.

Pokud si myslíte, že jste po přiřazení značky k imagi udělali chybu, můžete ji opravit. Vyberte**X**na popisku, který je zobrazený pod obrázkem, a vymažte značku. Případně vyberte obrázek a zvolte jinou třídu. Nově vybraná hodnota nahradí dřív použitou značku.

## <a name="tag-images-for-multi-label-classification"></a>Obrázky značek pro klasifikaci s více štítky

Pokud pracujete na projektu typu vícenásobný popisek klasifikace obrázku, použijete jednu *nebo více* značek pro obrázek. Chcete-li zobrazit směry specifické pro projekt, vyberte **pokyny** a přejděte k **zobrazení podrobných pokynů**.

Vyberte obrázek, který chcete označit, a pak vyberte značku. Tato značka se aplikuje na všechny vybrané image a pak se odberou obrázky. Chcete-li použít více značek, je nutné znovu vybrat obrázky. Následující animace znázorňuje označení s více štítky:

1. **Možnost Vybrat vše** se používá k aplikování tagu "oceánu".
1. Je vybraný jeden obrázek a označený jako "closeup".
1. Jsou vybrány tři obrázky a označené "širokoúhlého úhlu".

![Animace znázorňuje tok s více štítky](./media/how-to-label-images/multilabel.gif)

Chcete-li opravit chybu, klikněte na "**X**", chcete-li vymazat jednotlivou značku, nebo vybrat obrázky a pak vybrat značku, která vymaže značku ze všech vybraných imagí. Tento scénář je uvedený tady. Když kliknete na možnost půda, tato značka se z obou vybraných imagí vymaže.

![Snímek obrazovky znázorňující více odvýběrů](./media/how-to-label-images/multiple-deselection.png)

Azure povolí tlačítko **Odeslat** jenom po použití aspoň jedné značky pro každý obrázek. Vyberte **Odeslat** a uložte svou práci.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Tagování obrázků a určení ohraničujících polí pro detekci objektů

Pokud je váš projekt typu "Identifikace objektu (ohraničující pole)", zadáte jeden nebo více ohraničujících polí v obrázku a použijete značku pro každé pole. Obrázky mohou mít více ohraničujících polí, z nichž každá má jednu značku. Použijte **Zobrazit podrobné pokyny** k určení, zda je v projektu použito více ohraničujících polí.

1. Vyberte značku pro ohraničující rámeček, který chcete vytvořit.
1. Vyberte nástroj **obdélníkového pole** ![panelu obdélníkového pole](./media/how-to-label-images/rectangular-box-tool.png) nebo vyberte R.
3. Kliknutím a přetažením úhlopříčně v celém cíli vytvoříte hrubě ohraničující rámeček. Chcete-li upravit ohraničovací rámeček, přetáhněte okraje nebo rohy.

![Snímek obrazovky ukazuje vytvoření základního ohraničovacího rámečku.](./media/how-to-label-images/bounding-box-sequence.png)

Chcete-li odstranit ohraničovací rámeček, klikněte na cíl ve tvaru X, který se po vytvoření zobrazí vedle ohraničujícího rámečku.

Nemůžete změnit značku existujícího ohraničovacího rámečku. Pokud uděláte přiřazení značek omylem, je nutné odstranit ohraničovací rámeček a vytvořit nový se správnou značkou.

Ve výchozím nastavení můžete upravit existující ohraničovací rámečky. Nástroj **oblasti uzamčení/odemknutí** ![nástroji uzamknout/odemknout oblasti](./media/how-to-label-images/lock-bounding-boxes-tool.png) nebo "L" přepíná toto chování. Pokud jsou oblasti zamčené, můžete změnit jenom tvar nebo umístění nového ohraničovacího rámečku.

Pomocí nástroje pro vyřízení **oblasti** ![oblasti pro práci s oblastmi](./media/how-to-label-images/regions-tool.png) nebo M upravte existující ohraničovací rámeček. Přetažením okrajů nebo rohů upravte tvar. Klikněte na vnitřek, abyste mohli přetahovat celý ohraničovací rámeček. Pokud nemůžete upravit oblast, pravděpodobně jste přepnuli nástroj **oblasti uzamčení/odemknutí** .

Pomocí nástroje **box založeného na šablonách** ![nástroj Template-box](./media/how-to-label-images/template-box-tool.png) nebo "t" pro vytvoření více ohraničujících rámečků se stejnou velikostí. Pokud se v obrázku neobsahují žádná ohraničující pole a aktivujete pole na základě šablon, nástroj vytvoří pole 50 až 50-pixel. Pokud vytvoříte ohraničující rámeček a pak aktivujete pole založené na šablonách, budou mít všechny nové ohraničovací rámečky velikost posledního pole, které jste vytvořili. Po umístění lze změnit velikost polí založených na šablonách. Změna velikosti pole založeného na šablonách mění pouze velikost určitého pole.

Chcete-li odstranit *všechna* ohraničující pole v aktuální imagi, vyberte nástroj pro **odstranění všech oblastí ![odstranit všechny oblasti**](./media/how-to-label-images/delete-regions-tool.png).

Po vytvoření ohraničujících polí pro obrázek vyberte **Odeslat** a uložte svou práci, jinak se vaše Nedokončená práce nebude ukládat.

## <a name="finish-up"></a>Dokončení

Když odešlete stránku s tagovanými daty, Azure vám z pracovní fronty přiřadí nová neoznačená data. Pokud nejsou k dispozici žádná další neoznačená data, zobrazí se zpráva s odkazem na domovskou stránku portálu.

Až budete s popiskem hotovi, vyberte své jméno v pravém horním rohu portálu pro označování a pak vyberte **Odhlásit**se. Pokud se odhlásíte, nakonec Azure vyprší časový limit a přiřadíte data jinému popisku.

## <a name="next-steps"></a>Další kroky

* Naučte se naučit [modely klasifikace imagí v Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml) .
* Přečtěte si o [detekci objektů pomocí Azure a "rychlejší R-CNN" technika](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
