---
title: Označení obrázků v projektu s popisem
title.suffix: Azure Machine Learning
description: Naučte se používat nástroje pro označování dat k rychlé přípravě dat pro Machine Learning v projektu Azure Machine Learning značení.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: b138b3eb52a0bfb6dca2ec6fdf0b855223bab698
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984443"
---
# <a name="tag-images-in-a-labeling-project"></a>Označení obrázků v projektu s popisem 

Poté, co správce projektu [vytvoří projekt označování](./how-to-create-labeling-projects.md#create-a-labeling-project) v Azure Machine Learning, můžete použít nástroj pro popisování k rychlé přípravě dat projektu Machine Learning. Tento článek popisuje:

> [!div class="checklist"]
> * Jak získat přístup k projektům označování
> * Nástroje pro označování
> * Jak používat nástroje pro konkrétní úkoly označování

## <a name="prerequisites"></a>Požadavky

* [Účet Microsoft](https://account.microsoft.com/account) nebo účet Azure Active Directory pro organizaci a projekt
* Přístup na úrovni přispěvatele k pracovnímu prostoru, který obsahuje projekt značení.

## <a name="sign-in-to-the-workspace"></a>Přihlášení k pracovnímu prostoru

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. Vyberte předplatné a pracovní prostor obsahující projekt značení.  Tyto informace získáte od správce projektu.

1. Na levé straně vyberte **data** , na které se má projekt najít.  

## <a name="understand-the-labeling-task"></a>Pochopení úlohy označování

V tabulce s popisky dat vyberte **odkaz popisek** pro váš projekt.

Zobrazí se pokyny, které jsou specifické pro váš projekt. Vysvětlují typ dat, na které máte přístup, jak byste měli učinit rozhodnutí a další důležité informace. Po přečtení těchto informací v horní části stránky vyberte **úlohy**.  Nebo v dolní části stránky vyberte možnost **Spustit popis**.

## <a name="common-features-of-the-labeling-task"></a>Běžné funkce úlohy označování

V části všechny úlohy označování imagí můžete zvolit vhodnou značku nebo značky ze sady určené správcem projektu. První devět značek můžete vybrat pomocí klávesových zkratek na klávesnici.  

V úkolech klasifikace obrázků můžete zvolit zobrazení více imagí současně. Pro výběr rozložení použijte ikony nad oblastí obrázku. 

Chcete-li vybrat všechny zobrazené obrázky současně, použijte **možnost Vybrat vše**. Chcete-li vybrat jednotlivé obrázky, použijte tlačítko kruhového výběru v pravém horním rohu obrázku. Chcete-li použít značku, je nutné vybrat alespoň jeden obrázek. Vyberete-li více obrázků, bude pro všechny vybrané bitové kopie použita jakákoli vybraná značka.

Tady jsme zvolili oboustranné rozložení a chystáme se použít značku "Savcé" na obrázky z nosičů a Orca. Obrázek žraloka už je označený jako "Cartilaginous ryba" a iguana ještě není označený.

![Více rozložení a výběr obrázků](./media/how-to-label-images/layouts.png)

> [!Important] 
> Pokud máte novou stránku neoznačených dat, přepínejte jenom rozložení. Přepínání rozložení vymaže práci s označováním probíhajících prvků stránky.

Azure povolí tlačítko **Odeslat** , když jste označili všechny obrázky na stránce. Vyberte **Odeslat** a uložte svou práci.

Po odeslání značek pro data v Azure aktualizuje stránku pomocí nové sady imagí z pracovní fronty.

### <a name="assisted-machine-learning-preview"></a>Strojové učení s asistencí (Preview) 

> [!IMPORTANT]
> Strojové učení s asistencí je aktuálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžou se aktivovat algoritmy strojového učení. Pokud jsou tyto algoritmy v projektu povolené, může se zobrazit následující:

* Po označení některých imagí se mohou v horní části obrazovky vedle názvu projektu zobrazit **úkoly v clusteru** .  To znamená, že se obrázky seskupí tak, aby obsahovaly podobné obrázky na stejné stránce.  Pokud ano, přepněte na jedno z zobrazení více obrázků, abyste mohli využít výhod seskupení.  

* Později se můžete podívat na úkoly, které jsou **označené** vedle názvu projektu.  Obrázky se pak zobrazí s navrhovaným popiskem, který pochází z modelu klasifikace Machine Learning. Žádný model strojového učení nemá 100% přesnost. I když používáme jenom image, pro které je model jistý, můžou být tyto image pořád nesprávně označené.  Když vidíte tyto popisky, před odesláním stránky opravte případné nesprávné popisky.  

* Pro modely detekce objektů můžete vidět ohraničená pole a popisky, které už existují.  Opravte všechny, které jsou nesprávné před odesláním stránky.

Model strojového učení, který je zvláště zpočátku v rámci označování projektu, může být dostatečně přesný, aby bylo možné předoznačit malou podmnožinu imagí. Po označení těchto imagí se projekt označování vrátí k ručnímu označování, aby se získalo více dat pro další kolo školení o modelu. V průběhu času se model bude spolehlivější o větší část imagí, což vede k pozdějšímu předznačení úkolů v projektu.

## <a name="tag-images-for-multi-class-classification"></a>Označování obrázků pro účely klasifikace s více třídami

Pokud je váš projekt typu "Multi-Class klasifikace obrázku", přiřadíte k celému obrázku jednu značku. Pokud si chcete projít pokyny kdykoli, navštivte stránku s **pokyny** a vyberte **Zobrazit podrobné pokyny**.

Pokud si myslíte, že jste po přiřazení značky k imagi udělali chybu, můžete ji opravit. Vyberte **X** na popisku, který je zobrazený pod obrázkem, a vymažte značku. Případně vyberte obrázek a zvolte jinou třídu. Nově vybraná hodnota nahradí dřív použitou značku.

## <a name="tag-images-for-multi-label-classification"></a>Označování obrázků pro účely klasifikace s více popisky

Pokud pracujete na projektu typu vícenásobný popisek klasifikace obrázku, použijete jednu *nebo více* značek pro obrázek. Chcete-li zobrazit směry specifické pro projekt, vyberte **pokyny** a přejděte k **zobrazení podrobných pokynů**.

Vyberte obrázek, který chcete označit, a pak vyberte značku. Tato značka se aplikuje na všechny vybrané image a pak se odberou obrázky. Chcete-li použít více značek, je nutné znovu vybrat obrázky. Následující animace znázorňuje označení s více štítky:

1. **Možnost Vybrat vše** se používá k aplikování tagu "oceánu".
1. Je vybraný jeden obrázek a označený jako "closeup".
1. Jsou vybrány tři obrázky a označené "širokoúhlého úhlu".

![Animace znázorňuje tok s více štítky](./media/how-to-label-images/multilabel.gif)

Chcete-li opravit chybu, klikněte na "**X**", chcete-li vymazat jednotlivou značku, nebo vybrat obrázky a pak vybrat značku, která vymaže značku ze všech vybraných imagí. Tento scénář je uvedený tady. Když kliknete na možnost půda, tato značka se z obou vybraných imagí vymaže.

![Snímek obrazovky znázorňující více odvýběrů](./media/how-to-label-images/multiple-deselection.png)

Azure povolí tlačítko **Odeslat** jenom po použití aspoň jedné značky pro každý obrázek. Vyberte **Odeslat** a uložte svou práci.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Označování obrázků a určování ohraničujících rámečků pro účely detekce objektů

Pokud je váš projekt typu "Identifikace objektu (ohraničující pole)", zadáte jeden nebo více ohraničujících polí v obrázku a použijete značku pro každé pole. Obrázky mohou mít více ohraničujících polí, z nichž každá má jednu značku. Použijte **Zobrazit podrobné pokyny** k určení, zda je v projektu použito více ohraničujících polí.

1. Vyberte značku pro ohraničující rámeček, který chcete vytvořit.
1. Vyberte **obdélníkový** panel nástrojů obdélníkového pole ![ ](./media/how-to-label-images/rectangular-box-tool.png) nebo vyberte R.
3. Kliknutím a přetažením úhlopříčně v celém cíli vytvoříte hrubě ohraničující rámeček. Chcete-li upravit ohraničovací rámeček, přetáhněte okraje nebo rohy.

![Vytváření ohraničovacího rámečku](./media/how-to-label-images/bounding-box-sequence.png)

Chcete-li odstranit ohraničovací rámeček, klikněte na cíl ve tvaru X, který se po vytvoření zobrazí vedle ohraničujícího rámečku.

Nemůžete změnit značku existujícího ohraničovacího rámečku. Pokud uděláte přiřazení značek omylem, je nutné odstranit ohraničovací rámeček a vytvořit nový se správnou značkou.

Ve výchozím nastavení můžete upravit existující ohraničovací rámečky. Nástroj pro zamčení/odemčení **oblastí** nástroje ![ ](./media/how-to-label-images/lock-bounding-boxes-tool.png) nebo "L" přepíná toto chování. Pokud jsou oblasti zamčené, můžete změnit jenom tvar nebo umístění nového ohraničovacího rámečku.

Chcete-li upravit existující ohraničovací rámeček, použijte nástroj pro manipulaci s oblastmi pro manipulaci s **oblastmi** ![ ](./media/how-to-label-images/regions-tool.png) nebo "M". Přetažením okrajů nebo rohů upravte tvar. Klikněte na vnitřek, abyste mohli přetahovat celý ohraničovací rámeček. Pokud nemůžete upravit oblast, pravděpodobně jste přepnuli nástroj **oblasti uzamčení/odemknutí** .

**Template-based box** ![ ](./media/how-to-label-images/template-box-tool.png) K vytvoření více ohraničujících polí se stejnou velikostí použijte nástroj pro pole šablony založené na šablonách nebo "T". Pokud se v obrázku neobsahují žádná ohraničující pole a aktivujete pole na základě šablon, nástroj vytvoří pole 50 až 50-pixel. Pokud vytvoříte ohraničující rámeček a pak aktivujete pole založené na šablonách, budou mít všechny nové ohraničovací rámečky velikost posledního pole, které jste vytvořili. Po umístění lze změnit velikost polí založených na šablonách. Změna velikosti pole založeného na šablonách mění pouze velikost určitého pole.

Chcete-li odstranit *všechny* ohraničovací rámečky v aktuální imagi, vyberte nástroj **Odstranit všechny oblasti** nástroj ![ Odstranit oblasti ](./media/how-to-label-images/delete-regions-tool.png) .

Po vytvoření ohraničujících polí pro obrázek vyberte **Odeslat** a uložte svou práci, jinak se vaše Nedokončená práce nebude ukládat.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Tagování obrázků a určení mnohoúhelníků pro segmentaci obrázků 

Pokud je váš projekt typu "segmentace instancí (mnohoúhelník)", zadáte jeden nebo více mnohoúhelníků v obrázku a použijete značku na každý mnohoúhelník. Obrázky mohou mít více ohraničujících mnohoúhelníků, z nichž každá má jednu značku. Použijte **Zobrazit podrobné pokyny** k určení, zda je v projektu použito více ohraničujících mnohoúhelníků.

1. Vyberte značku mnohoúhelníku, kterou chcete vytvořit.
1. Vyberte nástroj **roztažení oblasti mnohoúhelníku** panel pro ![ vykreslení oblasti mnohoúhelníku ](./media/how-to-label-images/polygon-tool.png) nebo vyberte "P".
1. Klikněte na každý bod mnohoúhelníku.  Po dokončení tohoto tvaru poklikejte na tlačítko Dokončit.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Vytváření mnohoúhelníků pro Cat a pes":::

Chcete-li odstranit mnohoúhelník, klikněte na cíl ve tvaru X, který se po vytvoření zobrazí vedle mnohoúhelníku.

Chcete-li změnit značku mnohoúhelníku, vyberte nástroj **přesunout oblast** , klikněte na mnohoúhelník a vyberte správnou značku.

Můžete upravit existující mnohoúhelníky. Nástroj **oblasti uzamčení/odemknutí** ![ upraví mnohoúhelníky pomocí nástroje pro uzamknutí/odemknutí oblastí ](./media/how-to-label-images/lock-bounding-boxes-tool.png) nebo "L" přepíná toto chování. Pokud jsou oblasti zamčené, můžete změnit jenom tvar nebo umístění nového mnohoúhelníku.

K úpravě existujícího mnohoúhelníku použijte nástroj **Přidat nebo odebrat body mnohoúhelníku** ![ ](./media/how-to-label-images/add-remove-points-tool.png) nebo "U". Kliknutím na mnohoúhelník přidáte nebo odeberete bod. Pokud nemůžete upravit oblast, pravděpodobně jste přepnuli nástroj **oblasti uzamčení/odemknutí** .

Chcete-li odstranit *všechny* mnohoúhelníky v aktuální imagi, vyberte nástroj **Odstranit všechny oblasti** nástroj ![ Odstranit všechny oblasti ](./media/how-to-label-images/delete-regions-tool.png) .

Po vytvoření mnohoúhelníků pro obrázek vyberte **Odeslat** a uložte svou práci, jinak se vaše Nedokončená práce nebude ukládat.

## <a name="finish-up"></a>Dokončení

Když odešlete stránku s tagovanými daty, Azure vám z pracovní fronty přiřadí nová neoznačená data. Pokud nejsou k dispozici žádná další neoznačená data, zobrazí se zpráva s odkazem na domovskou stránku portálu.

Až budete s popiskem hotovi, vyberte své jméno v pravém horním rohu portálu pro označování a pak vyberte **Odhlásit** se. Pokud se odhlásíte, nakonec Azure vyprší časový limit a přiřadíte data jinému popisku.

## <a name="next-steps"></a>Další kroky

* Naučte se naučit [modely klasifikace imagí v Azure](./tutorial-train-models-with-aml.md) .


