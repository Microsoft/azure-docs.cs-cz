---
title: Galerie Azure AI experimenty | Microsoft Docs
description: Vyhledat a sdílet experimenty v galerii AI Azure.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.openlocfilehash: d6de7aa6d16196f415688d68251e8c1b0a4b9aee
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="discover-experiments-in-azure-ai-gallery"></a>Zjistit experimenty v galerii Azure AI
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Experimenty pro Machine Learning Studio
Galerie obsahuje širokou škálu [experimenty](https://gallery.cortanaintelligence.com/experiments) které byly vyvinuty v [Azure Machine Learning Studio](https://studio.azureml.net). Experimenty rozsahu od rychlé experimenty testování konceptu, které ukazují konkrétní strojového učení techniku, plně vyvinuté řešení problémů, komplexní machine learning.

> [!NOTE]
> ***Experimentovat*** je plátno v nástroji Machine Learning Studio, můžete použít k vytvoření model prediktivní analýzy. Vytvoření modelu propojením dat s různými analytickým modulům. Mohou zkuste jinou nápady, nemáte běží zkušební verze a nakonec model nasadit jako webovou službu v Azure. Příklad toho, jak vytvořit základní experiment, naleznete v části [kurz strojového učení: vytvoření prvního experimentu v nástroji Azure Machine Learning Studio](create-experiment.md). Komplexnější návod, jak vytvořit řešení prediktivní analýzy, najdete v části [návod: vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Informace
Procházet experimenty [v galerii](http://gallery.cortanaintelligence.com), v horní části domovské stránce galerie, vyberte **experimenty**.

**[Experimenty](https://gallery.cortanaintelligence.com/experiments)** stránky zobrazí seznam nedávno přidané a populární experimenty. Pokud chcete zobrazit všechny experimenty, vyberte **zobrazit všechny** tlačítko. Chcete-li vyhledat konkrétní experiment, vyberte **zobrazit všechny**a potom vyberte filtr kritéria. Také můžete zadat hledaný text v **vyhledávání** pole v horní části stránky galerie.

Můžete získat další informace o experimentu na stránce podrobností experimentu. Otevřete stránku experimentu podrobnosti, vyberte experimentu. V experimentu podrobnosti stránky, v **komentáře** části, můžete komentář, poskytnout zpětnou vazbu nebo klást otázky týkající se experimentu. Experiment můžete také sdílet s přátele nebo kolegy na Twitteru nebo LinkedIn. Také můžete poštovní odkaz na stránku podrobností experimentu pozvaným jiní uživatelé při zobrazení stránky.

![Sdílet tuto položku s friends](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Přidat vlastní komentáře](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Ke stažení
Do pracovního prostoru Machine Learning Studio můžete stáhnout kopii všech experimentu v galerii. Potom můžete upravit vaší kopie vytvářet vlastní řešení.

Galerie AI Azure nabízí dva způsoby, jak importovat kopii experimentu:

* **Z Galerie**. Pokud zjistíte experimentu, který chcete v galerii, můžete stáhnout kopii a potom ho otevřete v pracovním prostoru Machine Learning Studio.
* **V nástroji strojového učení Studio**. V nástroji Machine Learning Studio můžete všechny experimentu v galerii jako šablonu pro vytvoření nového experimentu.

### <a name="from-the-gallery"></a>Z Galerie

1. V galerii otevřete stránku Podrobnosti experimentu.
2. Vyberte **Open in Studio**.

    ![Otevřete experimentu z Galerie](./media/gallery-experiments/open-experiment-from-gallery.png)

Když vyberete **Open in Studio**, otevře se v pracovním prostoru Machine Learning Studio experimentu. (Pokud jste již přihlášení do nástroje Machine Learning Studio, zobrazí se výzva k první přihlášení pomocí účtu Microsoft.)

### <a name="from-within-machine-learning-studio"></a>V nástroji strojového učení Studio

1. V nástroji Machine Learning Studio, vyberte **nový**.
2. Vyberte **experimentu**. Můžete vybrat ze seznamu galerie experimenty nebo najít konkrétní experimentu pomocí **vyhledávání** pole.
3. Přesuňte ukazatel myši na experiment a potom vyberte **Open in Studio**. (Pokud chcete zobrazit informace o experiment, vyberte **zobrazit v galerii**. Tím přejdete na stránku podrobností experimentu v galerii.)

    ![Otevření Galerie experimentovat z uvnitř Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

Můžete přizpůsobit, iterace a nasadit stažené experimentu jako ostatní experimentu, který vytvoříte v nástroji Machine Learning Studio.

![Experiment otevřít v sadě Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Přispívání
Při přihlášení do galerie, stane se členem Galerie komunit. Jako člena komunity můžete přispívat vlastní experimenty, takže další uživatelé mohou mít prospěch z řešení, které jste se seznámili s.

### <a name="publish-your-experiment-to-the-gallery"></a>Publikování experimentu do Galerie

1. Přihlaste se do nástroje Machine Learning Studio pomocí účtu Microsoft.
2. Vytvoření experimentu a potom ho spusťte.
3. Až budete připraveni k publikování experimentu v galerii, v seznamu akcí níže na plátno experimentu vyberte **publikovat do Galerie**.

    ![Vyberte možnost "publikovat do Galerie"](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. Na **experimentu popis** stránky, zadejte název a značky. Zkontrolujte popisný název a značky. Zvýrazněte technik, které jste použili nebo řešení skutečných problémů. Příklad experimentu popisný název je "binární klasifikace: Twitter Analysis postojích."

    ![Zadejte název a značky pro publikování](./media/gallery-experiments/experiment-description.png)
5. V **souhrnné** zadejte souhrn experimentu. Krátce popište problém, který byl odstraněn experiment a jak dosaženy.
6. V **podrobný popis** pole, popisují kroky trvalo v jednotlivých součástí experimentu. Jsou některé užitečné témata, které zahrnují:
   * Snímek obrazovky experimentu grafu
   * Vysvětlení a zdroje dat
   * Zpracování dat
   * Návrh funkcí
   * Popis modelu
   * Výsledky a vyhodnocení modelu výkonu

   Můžete markdown k formátování vaše popis. Chcete-li zjistit, jak bude vaše záznamy na stránce experimentu popis zobrazovat experimentu je při publikování, vyberte **Preview**.

   ![Vyberte "Náhled", chcete-li zobrazit vzhled text](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Do textových polí, zadaná pro úpravy markdownu a preview jsou malé. Doporučujeme, aby zapisovat dokumentaci experimentu v editoru markdownu, zkopírujte jej a vložte dokončené dokumentace v textovém poli v galerii. Po publikování experimentu můžete provádět všechny opravy pomocí standardní webové nástroje této markdownu použijte pro úpravy a preview.

7. Na **výběr Image** vyberte miniaturu pro experimentu. Na miniaturu se zobrazí v horní části stránce s podrobnostmi o experiment a na dlaždici experimentu. Ostatní uživatelé se zobrazí na miniaturu při procházení Galerie. Můžete nahrát bitovou kopii z vašeho počítače nebo vyberte uložených bitovou kopii z galerie.
    </br>
    ![Nahrát, nebo můžete vybrat bitovou kopii pro galerii](./media/gallery-experiments/select-gallery-image.png)
8. Na **nastavení** v části **viditelnost**, vyberte, zda chcete publikovat obsah veřejně (**veřejné**) nebo je přístupné pouze pro uživatele, kteří odkaz na stránku ( **Neuvedené**).

    ![Vyberte, zda chcete publikovat veřejně nebo jako neuvedené](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Pokud chcete, aby se v dokumentaci k vypadá správné před veřejně vydání, můžete publikovat nejprve experimentu jako **Unlisted**. Později, můžete změnit nastavení viditelnost na **veřejné** na stránce podrobností experimentu.
   >
   >
9. Chcete-li publikovat experimentu do galerie, vyberte **OK** zaškrtnutí.

    ![Vyberte zatržítko OK publikovat experimentu](./media/gallery-experiments/ok-checkmark.png)

Tipy o tom, jak publikovat vysoce kvalitní Galerie experimentu najdete v tématu [tipy pro dokumentaci a publikování experimentu](#tips-for-documenting-and-publishing-your-experiment).

Je to – všechny dokončení.

Teď můžete zobrazit v galerii experimentu a sdílet s ostatními odkaz. Pokud jste publikovali experimentu pomocí **veřejné** viditelnost nastavení, experimentu se zobrazí ve výsledcích Procházet a hledání v galerii. Můžete upravit dokumentaci experimentu na stránce podrobností experiment, když jste přihlášení do galerie.

Pokud chcete zobrazit seznam vaše příspěvky, vyberte bitové kopie v pravém horním rohu libovolné stránky galerie. Potom vyberte název otevřete stránku účtu.

![Vyberte název účtu](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Aktualizovat experimentu
Pokud potřebujete, můžete provést změny pracovního postupu (moduly, parametry a tak dále) v jednom experimentu, kterou jste publikovali do galerie. V nástroji Machine Learning Studio proveďte požadované změny, které byste chtěli provést experiment a potom znovu publikovat. Experimentu publikované aktualizují na změny.

Pro experimentu přímo v galerii můžete změnit některé z následujících informací:

* Název experimentu
* Souhrn nebo popis
* Značky
* Image
* Nastavení viditelnosti (**veřejné** nebo **Unlisted**)

Můžete také odstranit experimentu z galerie.

Můžete tyto změny nebo odstranění experiment, na stránce podrobností experimentu nebo z vašeho profilu stránky v galerii.


#### <a name="from-the-experiment-details-page"></a>Na stránce podrobností experimentu
Na stránce s podrobnostmi o experiment, chcete-li změnit podrobnosti experimentu, vyberte **upravit**.

![Vyberte upravit, chcete-li upravit experimentu](./media/gallery-experiments/edit-button.png)

Stránce s podrobnostmi o vstupuje do režimu úprav. Chcete-li provést změny, vyberte **upravit** vedle název experimentu, souhrn nebo značky. Po dokončení provádění změn, vyberte **provádí**.

![Vyberte "Upravit" Chcete-li upravit podrobnosti a vyberte možnost "Hotovo" po dokončení](./media/gallery-experiments/edit-details-page.png)

Chcete-li změnit nastavení viditelnosti experimentu (**veřejné** nebo **Unlisted**), nebo odstranit experimentu z galerie, vyberte **nastavení** ikonu.

![Vyberte "Nastavení" Změna viditelnost nebo odstranit experimentu](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Ze stránky profilu
Na stránce vašeho profilu, vyberte na šipku dolů experimentu a pak vyberte **upravit**. Tím přejdete na stránku podrobností pro experimentu v režimu úprav. Po dokončení změn vyberte **provádí**.

Chcete-li odstranit experimentu z galerie, vyberte **odstranit**.

![Vyberte možnost "Upravit" nebo "Odstranit"](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Tipy pro dokumentaci a publikování experimentu
* Můžete předpokládat, že čtečka má vědecké zpracování předchozí dat prostředí, ale může být užitečné použití jednoduchého jazyka. Popisují věcí podrobně kdykoli je to možné.
* Cortana Intelligence Suite je relativně nové. Ne všechny čtečky se seznámíte s způsobu jeho použití. Zadejte dostatek informace a podrobné vysvětlení pomohou čtečky přejděte experimentu.
* Vizuály může být užitečné pro čtečky jak interpretovat a používat dokumentaci experimentu správně. Vizuální prvky zahrnují grafy experimentů a snímky obrazovek data. Další informace o tom, jak zahrnují Image v dokumentaci k experimentu najdete v tématu [publikování pokyny a příklady kolekce](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Pokud jste zahrnuli datové sady v experimentu (není tedy Import datové sady přes modul importovat Data), datová sada je součástí experimentu a je publikována v galerii. Ujistěte se, že sadu dat, kterou publikujete má licenční smlouvy, které umožňujících sdílení a stahování každému uživateli. Příspěvky galerie jsou popsané v části Azure [podmínky použití](https://azure.microsoft.com/support/legal/website-terms-of-use/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Jaké jsou požadavky pro odesílání nebo úprava obrázku pro experimentu?**

Bitové kopie, které odešlete s experimentu slouží k vytváření dlaždici vašeho příspěvku experimentu. Doporučujeme vám, že Image být menší než 500 KB, aspect ratio 3:2 a překlad 960 &#215; 640.

**Co se stane se sada dat, které lze použít v rámci experimentu? V datové sadě publikovali také v galerii?**

Pokud sadu dat je součástí experimentu a není importovaných prostřednictvím modulu Import dat, datová sada je publikován v galerii v rámci experimentu. Ujistěte se, že sada dat, kterou chcete publikovat pomocí experimentu obsahuje příslušné licenční podmínky. Licenční podmínky by měla umožnit všem uživatelům sdílet a stáhnout data.

**Je nutné experimentu, který používá modul importovat Data k získání dat z Azure HDInsight nebo SQL Server. Používá pověření k načtení požadovaných dat. Můžete publikovat tento druh experimentu? Jak můžu si být jistí, že moje pověření nebude sdílet?**

V současné době nelze publikovat experimentu, který používá přihlašovací údaje v galerii.

**Zadávání více značek**

Po zadání značku, zadat jiné značky, stiskněte klávesu Tabulátor.

**[Přejděte do Galerie](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
