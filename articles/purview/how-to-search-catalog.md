---
title: 'Postupy: hledání Data Catalog'
description: Tento článek poskytuje přehled o tom, jak hledat v katalogu dat.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552738"
---
# <a name="search-the-azure-purview-data-catalog"></a>Hledání ve službě Azure dosah Data Catalog

Tento článek popisuje, jak používat různé funkce vyhledávání v Data Catalog Azure dosah.

## <a name="search-the-catalog-for-assets"></a>Hledání prostředků v katalogu

Hledání assetu je možné provést takto:

1. Kliknutím na **Hledat v katalogu** [otevřete dialogové okno hledání assetu](#open-the-asset-search-dialog) .
1. Pokud chcete najít assety s charakteristikami, které odpovídají podmínkám, [Zadejte hledané výrazy](#enter-search-terms) .
1. [Nastavte rychlé filtry](#set-quick-filters) pro zúžení hledání.
1. [Spusťte hledání](#start-the-search) a pokračujte na výsledky hledání.

Nezáleží na tom, zda jste nastavili rychlé filtry před nebo po zadání hledaných podmínek.

Pokud neexistují žádné hledané podmínky a žádné filtry, výsledky hledání zahrnují všechny prostředky.

### <a name="open-the-asset-search-dialog"></a>Otevření dialogového okna hledání assetu

Kliknutím na **Hledat v katalogu** otevřete dialogové okno hledání assetu.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Pod ' Search Catalog ' obsahuje levé podokno s vyhledávacími filtry a pravé podokno s posledními hledáními." border="true":::

Dialogové okno hledání zobrazuje rychlé filtry, historii hledání a seznam nedávno použitých prostředků.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="Seznam hledání je v pravém podokně pod seznamem vyhledat katalog." border="true":::

### <a name="enter-search-terms"></a>Zadat hledané výrazy

V **katalogu hledání** zadejte jeden nebo více hledaných výrazů. Při psaní jsou v **posledních hledáních** uvedeny párové hledané výrazy z nedávných hledání. navrhované vyhledávací výrazy jsou uvedené v seznamu **návrhy hledání** a v seznamu **návrhy assetů** jsou uvedené stejné datové assety.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Snímek obrazovky zobrazující výsledky hledání, které se zadává v poli katalog hledání":::

Výsledky hledání zahrnují pouze prostředky s jednou nebo více charakteristikami, které odpovídají hledaným podmínkám. Mezi tyto vlastnosti patří název assetu, typ assetu, klasifikace a kontakty.

#### <a name="types-of-search-criteria"></a>Typy kritérií hledání

Azure dosah podporuje následující typy kritérií hledání.

> [!Note]
> Vždy zadejte logické operátory (**a**, **nebo**, **ne**) u všech velkých písmen. V opačném případě nezáleží na velikosti písmen a udělejte nadbytečné mezery.

- **podregistr**: vyhledejte dokumenty, které obsahují **podregistr**.
- **databáze podregistru**: vyhledejte dokumenty, které obsahují přesně **databázi podregistru**.
- **PODREGISTR nebo databáze**: Najděte dokumenty, které obsahují **podregistr** nebo **databázi**, nebo obojí.
- **PODREGISTR a databáze**, **podregistr && databáze**: najít dokumenty, které obsahují **podregistr** i **databázi**.
- **PODREGISTR a (databáze nebo sklad)**: najít dokumenty, které obsahují **podregistr** a buď **databázi** nebo datový **Sklad**, nebo obojí.
- **PODREGISTR není databáze**: vyhledejte dokumenty, které obsahují **podregistr**, ale ne **databázi**.
- **HIV**: vyhledejte dokumenty, které obsahují slovo, které začíná **HIV**. Například **HIV**, **podregistr**, **hivbar** (* je zástupný znak, který odpovídá jakémukoli počtu znaků).

### <a name="set-quick-filters"></a>Nastavit rychlé filtry

Seznam výsledků hledání je založený na hledaných výrazech, které zadáte do **katalogu hledání**, a na hodnotách, které vyberete pro rychlé filtry.

Rychlý filtr omezuje seznam výsledků hledání na prostředky, které mají vybranou hodnotu charakteristiky. Filtr má rozevírací seznam a textové pole. V rozevíracím seznamu se zobrazí hodnoty vlastnosti, které jsou v *aktuálním* výsledku hledání. Vedle každé hodnoty v seznamu je počet prostředků v aktuálním výsledku hledání, které mají tuto hodnotu. Pokud vyberete hodnotu ze seznamu, výsledky hledání budou omezené na prostředky, které mají tuto hodnotu. Můžete vybrat jenom jednu hodnotu.

Aktuální výsledky hledání, které se používají při vytváření rozevíracího seznamu, určují:

- Hledané výrazy, které jsou zadány v **katalogu hledání**. 
- Hodnoty, které jsou vybrány v rychlých filtrech.

Tady je příklad rychlého filtru "typ prostředku".

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Příklad rychlého filtru typu assetu" border="true":::

Do textového pole můžete zadat text, abyste omezili hodnoty z rozevíracího seznamu na hodnoty, které odpovídají nebo částečně odpovídaly textu. Příklady použití textového pole najdete v tématech [rychlý filtr hledání: filtr podle typu assetu](#search-quick-filter-filter-by-asset-type)a [rychlý filtr hledání: filtrovat podle klasifikace](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Hledání rychlého filtru: filtrovat podle typu assetu

Pokud chcete filtrovat podle typu assetu, použijte rychlý filtr **typu assetu** . V rozevíracím seznamu se zobrazí typy assetů, které se našly v aktuálních výsledcích hledání, jak jsou určené hledanými výrazy a rychlými filtry. Pro každý typ je zobrazen počet prostředků tohoto typu.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Rychlé filtry typu assetu jsou zvýrazněné. Zobrazuje typy assetů a počet pro každý z nich." border="true":::

Vyberte typ assetu, který omezí výsledky hledání na prostředky daného typu. Můžete vybrat jenom jeden typ.

Chcete-li zobrazit pouze typy prostředků, jejichž názvy odpovídají řetězci, zadejte řetězec do textového pole. Chcete-li například zobrazit v názvech pouze typy assetů s **SQL** , zadejte **příkaz SQL**.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="Podokno rychlé filtry má SQL pro typ Assetu. Seznam prostředků, které obsahují SQL, zobrazuje tři položky." border="true":::

Vyberte typ assetu, který omezí výsledky hledání na prostředky daného typu. Můžete vybrat jenom jeden typ.

#### <a name="search-quick-filter-filter-by-classification"></a>Hledání rychlého filtru: filtrovat podle klasifikace

Pokud chcete filtrovat podle klasifikace assetů, použijte rychlý filtr **klasifikace** . V rozevíracím seznamu se zobrazí klasifikace, které byly přiřazeny k jednomu nebo více assetům v aktuálních výsledcích hledání, jak jsou určeny hledanými výrazy a rychlými filtry. U každé klasifikace se zobrazuje počet prostředků, které tuto klasifikaci přiřadily.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="Rychlé filtry klasifikace jsou zvýrazněné." border="true":::

Vyberte klasifikaci, aby se výsledky hledání omezily na prostředky přiřazené této klasifikaci. Můžete vybrat jenom jednu klasifikaci.

Chcete-li zobrazit pouze klasifikace, jejichž názvy odpovídají řetězci, zadejte řetězec do textového pole. Pokud například chcete zobrazit pouze klasifikace s **číslem** v jejich jménech, zadejte **číslo**.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="V podokně rychlé filtry je klasifikace &quot;banka&quot; a všechny uvedené klasifikace obsahují tuto hodnotu." border="true":::

Vyberte klasifikaci, abyste omezili výsledky hledání na prostředky, kterým byla tato klasifikace přiřazena. Nemůžete vybrat více než jednu klasifikaci.

#### <a name="search-quick-filter-filter-by-contacts"></a>Hledání rychlého filtru: filtrovat podle kontaktů

*Kontakt* je osoba, která je přiřazená k assetu jako vlastník nebo odborník. Když zobrazíte podrobnosti o aktivech, kontakty se zobrazí na kartě **Kontakty** .

Existují dva způsoby, jak vyhledat prostředky, ke kterým je přiřazen konkrétní kontakt.

- Do **vyhledávacího katalogu** zadejte celý název kontaktu nebo jeho část a proveďte hledání. Výsledky hledání budou zahrnovat prostředky, které mají kontakty, jejichž názvy odpovídají hledaným podmínkám.
- V poli rychlý filtr **kontaktu** vyberte kontakt na zájem a proveďte hledání.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="Hodnota person v podokně rychlé filtry je &quot;Darren&quot;. V podokně návrhy se nacházejí tři návrhy." border="true":::

## <a name="search-example"></a>Příklad hledání

Podívejme se na hypotetický příklad, který vám pomůže zjistit, jak vyhledávané podmínky a rychlé filtry vzájemně ovlivňují výsledky hledání. Konkrétně monitoruje počet typů assetů **Azure Blob Storage**.

- Provedeme první hledání bez zadaných hledaných výrazů a v rychlých filtrech nejsou vybrané žádné hodnoty. Hledání najde všechny prostředky v katalogu. Seznam výsledků hledání a rychlý filtr **typu assetu** odhalí:

    - Seznam výsledků hledání obsahuje 164 230 prostředků, což jsou všechny prostředky v katalogu.
    - Rozevírací seznam **typ prostředku** obsahuje 43 položek. Toto jsou všechny typy assetů v katalogu. Vzhledem k tomu, že každý Asset má jeden a jenom jeden typ, součet počtu typů assetů 43 je 164 230.
    - Typ assetu **BLOB Storage Azure** je první položkou v rozevíracím seznamu rychlého filtru **typu Asset** . Hodnoty jsou seřazené podle počtu, co nejdříve, takže **Azure Blob Storage** je nejběžnějším typem assetu. Jeho počet je 118 174.

- Nyní do **katalogu hledání** zadáte **Parquet** a provedete další hledání. Výsledky hledání zahrnují pouze prostředky s charakteristikami, které odpovídají **Parquet**. Tím se zmenší všechny počty, a to následujícím způsobem:

    - Seznam výsledků hledání obsahuje 493 prostředků. Pouze 493 prostředků 164 230 v katalogu má vlastnosti, které odpovídají "Parquet".
    - Rozevírací seznam **typ prostředku** obsahuje 15 položek. Každé z těchto prostředků 493 je jeden z těchto 15 typů a součet počtu 15 typů je 493.
    - K dispozici jsou 456 assety typu **Azure Blob Storage**. Ostatní prostředky 37 (493 minus 456) jsou jedním z dalších 14 typů.

- Teď se podíváme na rozevírací seznam s jiným rychlým filtrem, **klasifikací**:

    - V seznamu výsledků hledání je 12 klasifikací pro prostředky 493. Počty prostředků 493 se nepočítají na 493, protože k assetu je možné přiřadit libovolný počet klasifikací.
    - Klasifikace **jména osoby** je přiřazená k 36 prostředkům, což je víc než jakákoli jiná klasifikace.

- Vybereme klasifikaci **jména osoby** . Seznam výsledků hledání vede na 36 prostředků, jak bylo očekáváno, protože počet pro **jméno osoby** byl 36. Žádný z těchto výsledků není typu **Azure Blob Storage**.

Můžeme se rozhodnout, že není k dispozici žádný prostředek, jehož typ je **Azure Blob Storage** , který odpovídá **Parquet** a má klasifikaci **jména osoby**.

## <a name="start-the-search"></a>Spustit hledání

Hledané výrazy, které zadáte v **katalogu hledání** , se při hledání shodují s charakteristikami prostředků. Mezi tyto vlastnosti patří název, typ, klasifikace a kontakty. Prostředky se shodnými vlastnostmi se zobrazí v seznamu výsledků hledání, pokud nejsou vyloučeny pomocí rychlého filtru.

Až zadáte hledané podmínky a nastavíte rychlé filtry, spusťte hledání jedním z následujících způsobů:

- Chcete-li vyhledávat na základě vámi zadaných podmínek, vyberte ikonu hledání ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), stiskněte klávesu **ENTER** nebo vyberte možnost **Zobrazit výsledky hledání**.
- Pokud chcete hledat pomocí podmínek z předchozího hledání, vyberte je z **nedávných** hledání.
- Pokud chcete hledat pomocí navrhovaných podmínek, vyberte je z nabídky **návrhy hledání**.

Pokud chcete přejít přímo na stránku podrobností pro daný prostředek, vyberte Asset z **návrhů assetů** . Není provedeno žádné hledání.

Seznam výsledků pro návrhy a hledání uživatelů se může mírně lišit. Výsledky v seznamu návrhy vycházejí z přibližných shod, zatímco uživatelem iniciované výsledky hledání jsou založené na přesných shodách.

Při hledání se zobrazí stránka **výsledky hledání** a zobrazí seznam assetů, které hledání najde.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Snímek obrazovky zobrazující výsledky hledání hodnoty pro hledání ve společnosti Contoso":::

Chcete-li zobrazit podrobnosti o aktivech, vyberte název assetu.

Pomocí ovládacích prvků v dolní části stránky výsledků hledání můžete přejít na další stránky výsledků hledání.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Snímek obrazovky znázorňující procházení stránek s výsledky hledání":::

### <a name="sort-search-results"></a>Seřadit výsledky hledání

K řazení výsledků hledání podle relevance nebo **názvu** použijte **řazení podle** **relevance** .

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Snímek obrazovky znázorňující způsob řazení výsledků hledání V tomto příkladu je rozevírací seznam seřadit podle nastavený na relevance.":::

### <a name="search-results-dynamic-filters"></a>Dynamické filtry výsledků hledání

Podokno **filtru** na stránce **výsledků hledání** obsahuje filtry, které poskytují dynamické filtrování prostředků v seznamu výsledků hledání. Filtrování je dynamické v tom, že další filtry mohou být zobrazeny v závislosti na výběrech filtru.

Dynamické filtry mají zaškrtávací políčko pro každou hodnotu v rozevíracím seznamu. Pomocí těchto zaškrtávacích políček můžete filtrovat tolik hodnot, kolik chcete.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Dynamický filtr výsledků hledání: filtrovat podle typu assetu

Pokud v rozevíracím seznamu **typ prostředku** vyberete typ assetu, zobrazí se dynamické filtry, které vám poskytnou další způsoby zúžení výsledků hledání. Filtry se liší v závislosti na vybraném typu prostředku. Pokud například vyberete **Azure SQL Database**, zobrazí se dynamické filtry pro server, databázi a schéma. Hodnoty v těchto filtrech jsou z prostředků ve výsledcích hledání vybraného typu.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="Položka filtru Azure SQL Database je jediná položka Typ prostředku, která je vybrána. Je zvýrazněn také výsledek hledání daného typu prostředku." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Dynamický filtr výsledků hledání: filtrovat podle klasifikace

Každá klasifikace v seznamu **klasifikace** se vztahuje alespoň na jednu položku v seznamu výsledků hledání. Vyberte jednu nebo více klasifikací pro zúžení výsledků hledání s cílem vyhodnotit prostředky vybraných klasifikací.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="Je zvýrazněný filtr klasifikace výsledků hledání." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Upravit a odstranit filtry výsledků hledání

Chcete-li odebrat filtr, zrušte zaškrtnutí políčka vedle názvu filtru.

### <a name="recently-accessed-assets"></a>Nedávno použité prostředky

Část s **posledním** zobrazením v rozšířeném vyhledávacím poli zobrazuje naposledy použité prostředky, které jsou k dispozici.

- Pokud chcete zobrazit úplný seznam nedávno použitých prostředků, vyberte **Zobrazit vše** v části **nedávno použité** .

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Snímek obrazovky znázorňující část posledního použití rozšířeného vyhledávacího pole.":::

   Zobrazí se seznam nedávno použitých prostředků.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Snímek obrazovky se seznamem nedávno používaných prostředků":::

- Pokud chcete filtrovat podle názvu, zadejte hledaný řetězec do **filtru podle názvu**.

- Pokud chcete položky ze seznamu odebrat, vyberte je se svými políček a pak vyberte **Odebrat**.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Snímek obrazovky znázorňující odebrání položek ze seznamu naposledy použitých assetů":::

- Chcete-li vymazat celý seznam, vyberte možnost **Vymazat**.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Snímek obrazovky ukazující, jak vymazat seznam naposledy použitých assetů":::

### <a name="search-assets"></a>Hledat prostředky

Mnoho stránek kromě **domovského** má v horní části pole pro **hledání assetů** . Tady je příklad stránky s podrobnostmi o prostředcích, které mají zvýrazněné **hledané prostředky** .

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Snímek obrazovky se zvýrazněnou stránkou s podrobnostmi o prostředcích hledání":::

Vyberte **Hledat prostředky** a spusťte vyhledávací pole, jako je ten, který jste získali z **katalogu hledání** na **domovské stránce**, se stejnými funkcemi.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Snímek obrazovky znázorňující pole rozšířené vyhledávací prostředky":::

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet, importovat a exportovat terminologie glosáře](how-to-create-import-export-glossary.md)
- [Správa termínových šablon pro obchodní Glosář](how-to-manage-term-templates.md)
