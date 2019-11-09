---
title: Jak používat nástroj pro popisky dat v Azure Machine Learning
title.suffix: Azure Machine Learning
description: V tomto článku se naučíte, jak používat nástroje pro označování dat v Azure Machine Learning projektu značení.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9cbebb905ba39dabaf905be2fa741702bd1a0088
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838946"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Označení obrázků v projektu s popisem

Jakmile správce projektu vytvoří v Azure Machine Learning projekt označování, můžete k rychlé přípravě dat pro projekt strojového učení použít nástroj pro popisování. 

> [!div class="checklist"]
> * Jak získat přístup k projektům označování
> * Nástroje pro označování
> * Jak používat nástroje pro konkrétní úkoly označování

## <a name="prerequisites"></a>Požadavky

* Adresa URL portálu pro popisky pro běžící projekt pro popisky dat
* [Účet Microsoft](https://account.microsoft.com/account) nebo
* Účet Azure Active Directory pro organizaci a projekt

> [!Note]
> Správce projektu může na kartě **Podrobnosti** na stránce s **podrobnostmi projektu** najít adresu URL portálu pro označování. 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Přihlášení k portálu pro označování projektu

Přejít na adresu URL portálu pro označování, kterou vám poskytl správce projektu. 

Přihlaste se pomocí e-mailového účtu, který vám správce použil k přidání do týmu. Pro většinu uživatelů se přihlašování provede s vaším účet Microsoft. Pokud je v projektu označení použito Azure Active Directory, je to způsob, jakým se budete přihlašovat. 

## <a name="understanding-the-labeling-task"></a>Princip úlohy označování

Jakmile se přihlásíte, přejdete na stránku Přehled projektu. 

První věc, kterou je třeba udělat, je **Zobrazit podrobné pokyny**. Tyto pokyny jsou specifické pro váš projekt a vysvětlují typ dat, která vás zajímají, jak byste měli učinit rozhodnutí a další důležité informace. Až budete hotovi, vraťte se na stránku projektu a vyberte možnost **Spustit popis**.

## <a name="common-features-of-the-labeling-task"></a>Běžné funkce úlohy označování

Všechny úlohy označování imagí zahrnují výběr vhodné značky nebo značek ze sady určené správcem projektu. Můžete vybrat z prvních devíti značek pomocí klávesových zkratek na klávesnici.  

Úlohy klasifikace obrázků umožňují vybrat zobrazení více imagí současně. Různá rozložení můžete zvolit pomocí ikon nad oblastí obrázku. Kliknutím na tlačítko **Vybrat vše** můžete vybrat všechny zobrazené obrázky současně. Vyberte jednotlivé fotografie pomocí tlačítka kruhového výběru v pravém horním rohu oblasti obrázku. Chcete-li použít značku, je nutné vybrat alespoň jeden obrázek. Pokud jste vybrali více obrázků, pak výběrem značky se tato značka aplikuje na každou vybranou fotografii.

Tady jsme zvolili rozložení 2x2 a chystáme se použít značku "Savcé" na obrázky nosičů a Orca. Image s žralokem je už označená jako "Cartilaginous ryba" a iguana ještě není označená.

![Více rozložení a výběr obrázků](media/how-to-label-images/layouts.png)

> [!Important] 
> Pokud máte novou stránku neoznačených dat, přepínejte jenom rozložení. Přepínání rozložení vymaže práci s označováním probíhajících prvků stránky. 

Azure povolí tlačítko **Odeslat** , když jste označili všechny obrázky na stránce. Kliknutím na **Odeslat** uložte svoji práci. 

Po odeslání značek pro data se v Azure aktualizuje stránka o novou sadu imagí z pracovní fronty.  

## <a name="tagging-images-for-multi-class-classification"></a>Označování obrázků pro klasifikaci více tříd

Pokud je váš projekt typu "Multi-Class klasifikace obrázku", přiřadíte k celému obrázku jednu značku. V každém okamžiku klikněte na stránku s **pokyny** a přejděte k **podrobnostem** , kde najdete podrobné pokyny pro konkrétní projekt. 

Jak bylo popsáno dříve, můžete vybrat z několika rozložení pro prezentaci obrázků. Pokud po výběru image a přiřazení značky k němu jste zjistili, že jste udělali chybu, můžete ji opravit. Pokud v popisku, který se zobrazuje pod obrázkem, kliknete na cíl `X`, zrušíte zaškrtnutí značky. Nebo, pokud vyberete obrázek a zvolíte jinou třídu, značka se přepne na nově vybranou hodnotu.

## <a name="tagging-images-for-multi-label-classification"></a>Označování obrázků pro klasifikaci s více štítky

Pokud pracujete na projektu typu "vícenásobný popisek" klasifikace obrázku ", aplikujete na obrázek jednu _nebo více_ značek. V každém okamžiku klikněte na stránku s **pokyny** a přejděte k **podrobnostem** , kde najdete podrobné pokyny pro konkrétní projekt. 

Vyberte obrázek, který chcete označit, a potom klikněte na značku. Výběr značky ho použije na všechny vybrané obrázky a jejich výběr se odvolí. Chcete-li použít více značek, je nutné znovu vybrat obrázky. Tato animace ukazuje stránku označování s více štítky:

* **Možnost Vybrat vše** se používá k aplikování značky "oceánu".
* Je vybraný jeden obrázek a označený jako "closeup".
* Jsou vybrány tři obrázky s označením "širokoúhlého úhlu".

![Animace znázorňující tok s více popisky](media/how-to-label-images/multilabel.gif)

Chcete-li opravit chybu, můžete buď kliknout na `X`, chcete-li vymazat jednotlivé značky, vybrat obrázky a vybrat značku, která smaže značku ze všech vybraných imagí. Tento scénář se tady zobrazí, pokud kliknete na možnost půda, tato značka se ze dvou vybraných imagí vymaže.

![Snímek obrazovky znázorňující vícenásobné vybírání](media/how-to-label-images/multiple-deselection.png)

Azure povolí tlačítko **Odeslat** jenom po použití aspoň jedné značky pro každý obrázek. Kliknutím na **Odeslat** svou práci uložte.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Označování obrázků a ohraničovacích rámečků pro detekci objektů

Pokud je váš projekt typu "Identifikace objektu (ohraničující pole)", zadáte jeden nebo více ohraničujících polí v obrázku a použijete značku k tomuto poli. Každý obrázek může mít více ohraničujících polí, z nichž každý má jednu značku. Použijte **Zobrazit podrobné pokyny** k určení, zda je přidání více ohraničujících polí vhodné pro váš projekt.

1. Vyberte značku pro ohraničující rámeček, který chcete vytvořit.
1. Vyberte nástroj **obdélníkového pole** ![panelu obdélníkového pole](media/how-to-label-images/rectangular-box-tool.png) nebo stiskněte klávesu R. 
1. Kliknutím a přetažením úhlopříčně v celém cíli vytvoříte hrubý ohraničovací rámeček.
    * Upravte ohraničovací rámeček kliknutím a přetažením hran nebo rohů rámečku.

![Snímek obrazovky základního vytváření ohraničovacího rámečku](media/how-to-label-images/bounding-box-sequence.png)

Pokud chcete odstranit ohraničovací rámeček, klikněte na cíl ve tvaru X, který se po vytvoření zobrazí vedle ohraničovacího rámečku.

Nemůžete změnit přiřazení značky existujícího ohraničovacího rámečku. Pokud uděláte chybu s přiřazením značek, je nutné odstranit ohraničovací rámeček a vytvořit nový se správnou značkou.

Ve výchozím nastavení mohou být upraveny existující ohraničovací rámečky. Nástroj **oblasti uzamčení/odemknutí** ![nástroji uzamknout/odemknout oblasti](media/how-to-label-images/lock-bounding-boxes-tool.png) nebo "L" přepíná toto chování. Pokud jsou oblasti zamčené, můžete změnit jenom tvar nebo umístění nového ohraničovacího rámečku.

Pomocí nástroje pro vyřízení **oblasti** ![oblasti pro práci s oblastmi](media/how-to-label-images/regions-tool.png) nebo M upravte existující ohraničovací rámeček. Chcete-li upravit tvar, můžete kliknout na hrany nebo rohy a přetáhnout je. Pokud kliknete na vnitřek, můžete přetáhnout celý ohraničovací rámeček. Pokud nemůžete upravit oblast, pravděpodobně jste přepnuli nástroj **oblasti uzamčení/odemknutí** . 

Pomocí nástroje **box založeného na šablonách** ![nástroj Template-box](media/how-to-label-images/template-box-tool.png) nebo "t" pro vytvoření více ohraničujících rámečků se stejnou velikostí. Pokud se v obrázku neobsahují žádná ohraničující pole a aktivujete pole na základě šablon, nástroj vytvoří 50x50 pixelová pole. Pokud jste vytvořili ohraničovací rámeček a pak aktivovali pole na základě šablon, nová ohraničovací pole budou mít velikost posledního, kterou jste provedli. Po umístění lze změnit velikost polí založených na šablonách. Změna velikosti pole založeného na šablonách mění pouze velikost určitého pole. 

Chcete-li odstranit _všechny_ ohraničovací rámečky v aktuální imagi, můžete zvolit nástroj **odstranit všechny oblasti** ![odstranit oblasti](media/how-to-label-images/delete-regions-tool.png). 

Po vytvoření ohraničujících polí pro obrázek klikněte na **Odeslat** a uložte svoji práci. Vaše Nedokončená práce se neuloží, dokud nestisknete tlačítko **Odeslat** . 

## <a name="finishing-up"></a>Dokončuje se 

Když odešlete stránku s tagovanými daty, Azure přiřadí nová neoznačená data z pracovní fronty. Pokud neexistují žádná další nepopisovaná data, zobrazí se vám v tomto efektu zpráva s odkazem zpátky na domovskou stránku portálu. 

Pokud víte, že nebudete moct dělat popisky, vyberte své jméno v pravém horním rohu portálu pro označování a vyberte možnost **Odhlásit**se. Pokud se odhlásíte, nakonec Azure vyprší časový limit a přiřadíte data jinému popisku. 

## <a name="next-steps"></a>Další kroky

* Naučte se naučit [modely klasifikace imagí v Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml) .
* Přečtěte si o [detekci objektů s využitím Azure a rychlejší techniky R-CNN](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)