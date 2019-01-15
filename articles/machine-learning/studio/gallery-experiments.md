---
Název: Galerie Azure AI experimenty titleSuffix: Azure Machine Learning Studio Popis: Zjišťování a sdílení experimenty v galerii Azure AI. Experiment je plátna v nástroji Machine Learning Studio, můžete použít k vytvoření služby modelu prediktivní analýzy: machine learningu ms.service: ms.component strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 04/26/2017
---
# <a name="discover-azure-machine-learning-studio-experiments-in-azure-ai-gallery"></a>Objevte Azure Machine Learning Studio experimenty v galerii Azure AI

[Galerie Azure AI](http://gallery.cortanaintelligence.com) obsahuje řadu [experimenty](https://gallery.cortanaintelligence.com/experiments) , které byly vyvinuty v [Azure Machine Learning Studio](https://studio.azureml.net). Experimenty v rozsahu od rychlé testování konceptu experimenty, které ukazují na konkrétní počítač studijní techniku pro propracovaný řešení problémů složitých machine learning.

> [!NOTE]
> ***Experimentovat*** je plátna v nástroji Machine Learning Studio, který vám pomůže vytvořit tak model prediktivní analýzy. Vytvoření modelu po propojení dat s různými analytickým modulům. Zkuste jiné nápady, proveďte zkušební spuštění a nakonec svůj model nasadit jako webovou službu v Azure. Příklad toho, jak vytvořit základní experimentu najdete v části [kurz strojového učení: Vytvoření prvního experimentu v nástroji Azure Machine Learning Studio](create-experiment.md). Komplexnější návod, jak vytvořit řešení prediktivní analýzy, najdete v části [názorný postup: Vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Informace
Procházet experimenty [ve galerii](http://gallery.cortanaintelligence.com), v horní části stránky domovskou stránku galerie, vyberte **experimenty**.

**[Experimenty](https://gallery.cortanaintelligence.com/experiments)** stránce se zobrazí seznam nedávno přidaných a Oblíbené experimentů. Zobrazí se všechny experimenty, vyberte **zobrazit všechny** tlačítko. Chcete-li vyhledat konkrétní experiment, vyberte **zobrazit všechny**a potom vyberte kritéria. Také můžete zadat hledaný text v **hledání** pole v horní části na stránku galerie.

Můžete získat další informace o experimentu na stránce s podrobnostmi o experimentu. Otevřete stránku Podrobnosti testu, vyberte experimentu. Na experiment o stránce **komentáře** oddílu, můžete komentovat, poskytnout zpětnou vazbu nebo pokládání otázek na experiment. Experiment můžete sdílet i s přáteli nebo kolegy na Twitteru nebo LinkedIn. Také můžete odeslat odkaz na stránku podrobností experiment, a pozvat další uživatele zobrazíte stránku.

![Sdílet tuto položku s přáteli](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Přidat vlastní komentáře](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Ke stažení
Do pracovního prostoru Machine Learning Studio můžete stáhnout kopii všech experiment v galerii. Potom můžete upravit kopii vytvářet vlastní řešení.

Galerie Azure AI nabízí dva způsoby, jak importovat kopii experimentu:

* **Z Galerie**. Pokud zjistíte experiment, který vás zajímá v galerii, můžete stáhnout kopii a pak ho otevřete v pracovního prostoru Machine Learning Studio.
* **V rámci služby Machine Learning Studio**. V nástroji Machine Learning Studio můžete všechny experiment v galerii jako šablonu k vytvoření nového experimentu.

### <a name="from-the-gallery"></a>Z Galerie

1. V galerii otevřete stránce s podrobnostmi o experimentu.
2. Vyberte **Open in Studio**.

    ![Otevřete experiment z Galerie](./media/gallery-experiments/open-experiment-from-gallery.png)

Když vyberete **Open in Studio**, experiment otevře ve vašem pracovním prostoru Machine Learning Studio. (Pokud jste ještě nejste přihlášení ke službě Machine Learning Studio, zobrazí se výzva k první přihlášení pomocí účtu Microsoft.)

### <a name="from-within-machine-learning-studio"></a>V rámci služby Machine Learning Studio

1. V nástroji Machine Learning Studio, vyberte **nový**.
2. Vyberte **Experiment**. Můžete vybrat ze seznamu experimenty v galerii nebo vyhledat konkrétní experimentu pomocí **hledání** pole.
3. Přesuňte ukazatel myši na experiment a vyberte **Open in Studio**. (Chcete-li zobrazit informace o experimentu, vyberte **zobrazit v galerii**. Tím přejdete na stránku podrobností experiment v galerii.)

    ![Otevřít galerii experimentovat v v nástroji Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

Můžete přizpůsobit, iterovat a stažený experiment stejně jako ostatní experiment, který vytvoříte v nástroji Machine Learning Studio nasadit.

![Experiment otevřít v sadě Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Přispívat
Při přihlášení do galerie, stane se členem komunity galerie. Jako člen komunity může přispívat vlastní experimenty, takže ostatní uživatelé mohou mít prospěch z řešení, které jste se seznámili.

### <a name="publish-your-experiment-to-the-gallery"></a>Publikování experimentu v galerii

1. Přihlaste se ke službě Machine Learning Studio pomocí účtu Microsoft.
2. Vytvoření experimentu a spusťte jej.
3. Až budete připraveni publikovat experimentu v galerii, v seznamu akcí níže na plátno experimentu vyberte **publikovat do Galerie**.

    ![Vyberte možnost "publikovat do Galerie"](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. Na **Experiment popis** stránky, zadejte název a značky. Zkontrolujte popisný název a značky. Zvýraznit techniky, které jste použili nebo řešení skutečných problémů. Příklad experimentu popisný název je "binární klasifikace: Analýza Sentimentu na twitteru."

    ![Zadejte název a značky pro publikování](./media/gallery-experiments/experiment-description.png)
5. V **souhrnu** zadejte souhrn experimentu. Krátce popište problém, který byl odstraněn experiment a jak dosaženy.
6. V **podrobný popis** pole, popisují kroky jste provedli v jednotlivých součástí experimentu. Jsou některé užitečné témata, které zahrnují:
   * Snímek grafu experimentu
   * Vysvětlení a zdroje dat
   * Zpracování dat
   * Návrh funkcí
   * Popis modelu
   * Výsledky a vyhodnocení výkonu modelu

   Markdownu můžete použít k formátování popisu. Pokud chcete zobrazit, vzhled vaší položky na stránce experimentu popis publikovaného testu, vyberte **ve verzi Preview**.

   ![Vyberte "Náhled", chcete-li zobrazit text bude vypadat](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Textových polí zobrazených pro markdown úprav a náhledu jsou malé. Doporučujeme vám, napište dokumentaci experiment v editoru markdownu, zkopírujte ho a dokončené dokumentaci vložením do textového pole v galerii. Po publikování experimentu můžete provádět všechny opravy pomocí standardní webové nástroje tohoto používání markdownu pro úpravy a ve verzi preview.

7. Na **výběr Image** zvolte obrázek miniatury pro experiment. Miniaturu se zobrazí v horní části stránky s podrobnostmi experiment a na dlaždici experimentu. Ostatní uživatelé uvidí na miniaturu při procházení Galerie. Můžete nahrát obrázek z vašeho počítače nebo vyberte základní image z galerie.
    </br>
    ![Odeslání nebo vybrat obrázek v galerii](./media/gallery-experiments/select-gallery-image.png)
8. Na **nastavení** stránce v části **viditelnost**, vyberte, jestli k publikování obsahu veřejně (**veřejné**) nebo je přístupná pouze na uživatele, kteří mají odkaz na stránku ( **Neuvedené**).

    ![Zvolte, jestli se má publikovat veřejně nebo jako neuvedené v seznamu](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Pokud chcete Ujistěte se, že ještě před vydáním se veřejně vypadá správně v dokumentaci k sadě, můžete nejprve publikovat experimentu jako **Unlisted**. Později, můžete změnit nastavení viditelnosti na **veřejné** na stránce s podrobnostmi experimentu.
   >
   >
9. Chcete-li publikovat experimentu do galerie, vyberte **OK** zaškrtávací políčko.

    ![Vyberte na zatržítko OK publikovat experimentu](./media/gallery-experiments/ok-checkmark.png)

Tipy, jak publikovat experiment Galerie vysoce kvalitní, naleznete v tématu [tipy, jak dokumentovat a publikování experimentů](#tips-for-documenting-and-publishing-your-experiment).

To je vše – všechno je hotové.

Nyní můžete zobrazit experimentu v galerii a sdílet odkaz s ostatními. Pokud jste publikovali experimentu s použitím **veřejné** viditelnost nastavení testu se zobrazí ve výsledcích procházení a hledání v galerii. Můžete upravit v experimentu dokumentaci na stránce s podrobnostmi experiment pokaždé, když se přihlásíte do galerie.

Pokud chcete zobrazit seznam vašich příspěvků, vyberte bitové kopie v pravém horním rohu stránky libovolné galerii. Potom vybere vaše jméno, otevřete stránku svého účtu.

![Vyberte název svého účtu](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Aktualizovat experimentu
Pokud budete potřebovat, můžete provádět změny pracovního postupu (moduly, parametry a tak dále) v jednom experimentu, kterou jste publikovali v galerii. V nástroji Machine Learning Studio proveďte požadované změny, které chcete provést experiment a publikovat znovu. Publikovaných experimentů se aktualizují se změnami.

Pro experiment přímo v galerii, můžete změnit některý z následujících informací:

* Název experimentu
* Souhrn nebo popisu
* Značky
* Image
* Nastavení viditelnosti (**veřejné** nebo **Unlisted**)

Můžete také odstranit experiment z galerie.

Můžete tyto změny nebo odstranění experiment, ze stránky podrobností experiment nebo na stránce svého profilu v galerii.


#### <a name="from-the-experiment-details-page"></a>Na stránce podrobností experimentu
Na stránce s podrobnostmi experiment, chcete-li změnit podrobnosti pro experiment, vyberte **upravit**.

![Vyberte upravit, chcete-li upravit experimentu](./media/gallery-experiments/edit-button.png)

Stránce s podrobnostmi o přejde do režimu úprav. Chcete-li provést změny, vyberte **upravit** vedle název experimentu, souhrn nebo značky. Jakmile budete hotovi, změny, vyberte **provádí**.

![Vyberte "Edit" pro úpravu podrobností a vyberte "Hotovo" po dokončení](./media/gallery-experiments/edit-details-page.png)

Chcete-li změnit nastavení viditelnosti pro experiment (**veřejné** nebo **Unlisted**), nebo pokud chcete odstranit experiment z galerie, vyberte **nastavení** ikonu.

![Vyberte "Nastavení" Změna viditelnosti nebo odstranit experimentu](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Na stránce svého profilu
Na stránce svého profilu, vyberte šipku dolů u testu a pak vyberte **upravit**. Tím přejdete na stránku podrobností pro experiment, v režimu úprav. Po dokončení změn vyberte **provádí**.

Chcete-li odstranit experiment z galerie, vyberte **odstranit**.

![Vyberte možnost "Upravit" nebo "Odstranit"](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Tipy, jak dokumentovat a publikování experimentů
* Můžete předpokládat, že má čtečka předchozí datové vědy prostředí, ale může být užitečné používat Jednoduchý jazyk. Vysvětluje co podrobně kdykoli je to možné.
* Cortana Intelligence Suite je relativně nové. Ne všechny čtenáři obeznámeni s jeho použitím. Poskytuje dostatek informace a podrobné vysvětlení k pomáhají čtenářům přejděte experimentu.
* Vizuály mohou být užitečné pro čtenáře jak interpretovat a správně používat dokumentaci k experimentu. Vizuály patří grafy experimentů a snímky obrazovek pro data. Další informace o tom, jak zahrnout i obrázky v dokumentaci k experimentu najdete v článku [pokyny pro publikování a příklady kolekce](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Pokud zahrnete datové sady v experimentu (není tedy Import datové sady přes modul pro Import dat), datová sada je součástí experimentu a, se publikují v galerii. Ujistěte se, že datové sady, které publikujete má licenční podmínky, které umožňují sdílení a stahování kdokoli. Příspěvky v galerii se vztahuje smlouva Azure [Terms of Use](https://azure.microsoft.com/support/legal/website-terms-of-use/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Jaké jsou požadavky na odeslání nebo úprava obrázku pro experimentu?**

Bitové kopie, které publikujete pomocí svého experimentu se používají k vytvoření dlaždice experiment svůj příspěvek. Doporučujeme vám, že Image být menší než 500 KB, se aspect ratio 3:2 a rozlišení 960 &#215; 640.

**Co se stane sady dat, použita v experimentu? Jsou datové sady publikované v galerii?**

Pokud vaši datovou sadu je součástí experimentu a neimportujeme pomocí modulu Import dat, datové sady se publikují v galerii v rámci experimentu. Ujistěte se, že datová sada, kterou publikujete pomocí experiment má příslušné licenční podmínky. Licenční podmínky by měl povolit všem uživatelům sdílet a stáhnout data.

**Mám experiment, který používá modul importovat Data k získání dat z Azure HDInsight nebo SQL Server. Aby se načetla data používá přihlašovací údaje. Můžete publikovat tento druh experimentu? Jak si můžu být jistí, že se nebudou sdílet přihlašovací údaje?**

V současné době nelze publikovat experiment, který používá přihlašovací údaje v galerii.

**Jak mám zadat více značek?**

Po zadání značku, zadejte jinou značku, stiskněte klávesu Tabulátor.

**[Přejít do Galerie](http://gallery.cortanaintelligence.com)**


