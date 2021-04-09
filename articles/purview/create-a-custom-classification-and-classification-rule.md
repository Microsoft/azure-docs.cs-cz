---
title: Vytvoření vlastního pravidla klasifikace a klasifikace (Preview)
description: Naučte se vytvářet vlastní klasifikace pro definování datových typů v nemovitosti, které jsou jedinečné pro vaši organizaci ve službě Azure dosah.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: 2966618619aa40ed60c2f3d0fb2c8e080d34a016
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617042"
---
# <a name="custom-classifications-in-azure-purview"></a>Vlastní klasifikace v Azure dosah

Tento článek popisuje, jak můžete vytvořit vlastní klasifikace pro definování datových typů v datovém majetku, které jsou jedinečné pro vaši organizaci. Popisuje také vytváření vlastních pravidel klasifikace, která vám umožní najít zadaná data v celém datovém majetku.

## <a name="default-classifications"></a>Výchozí klasifikace

Azure dosah Data Catalog poskytuje velkou sadu výchozích klasifikací, které představují typické osobní datové typy, které můžete mít ve své datové nemovitosti.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="vybrat klasifikaci" border="true":::

Máte také možnost vytvářet vlastní klasifikace, pokud některá z výchozích klasifikací nevyhovuje vašim potřebám.

## <a name="steps-to-create-a-custom-classification"></a>Postup vytvoření vlastní klasifikace

Pokud chcete vytvořit vlastní klasifikaci, udělejte toto:

1. Z katalogu vyberte v nabídce vlevo položku **Centrum pro správu** .

2. V části **Správa metadat** vyberte **klasifikace** .

3. Vybrat **+ Nový**

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Nová klasifikace" border="true":::

Otevře se podokno **Přidat novou klasifikaci** , kde můžete zadat název a popis své klasifikace. Je vhodné použít konvenci pro odstup názvů, jako je například `your company name.classification name` .

Klasifikace systému Microsoft jsou seskupené pod rezervovaným `MICROSOFT.` oborem názvů. Příkladem může být **Microsoft. Schod. Vylepšení. \_ \_ číslo sociálního pojištění**.

Název vaší klasifikace musí začínat písmenem, za kterým následuje posloupnost písmen, číslic a tečky (.) nebo podtržítka. Nejsou povoleny žádné mezery. Při psaní uživatelského rozhraní se automaticky vygeneruje popisný název. Tento popisný název se uživatelům zobrazí při použití pro Asset v katalogu.

Aby byl název krátký, systém vytvoří popisný název založený na následující logice:

- Všechny kromě posledních dvou segmentů oboru názvů jsou oříznuty.

- Velikost písmen se upraví tak, aby se první písmeno každého slova nastavilo na velká. Všechna ostatní písmena jsou převedena na malá.

- Všechna podtržítka ( \_ ) jsou nahrazena mezerami.

Příklad: Pokud jste pojmenovali klasifikaci **contoso.hr. \_ID zaměstnance**, popisný název je uložený v systému jako **hr. ID zaměstnance**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Vyberte **OK** a nová klasifikace se přidá do seznamu klasifikace.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Vlastní klasifikace" border="true":::

Výběrem klasifikace v seznamu se otevře stránka podrobnosti klasifikace. Tady najdete všechny podrobnosti o klasifikaci.

Mezi tyto podrobnosti patří počet zjištěných instancí, formální název, přidružená pravidla klasifikace (pokud existují) a jméno vlastníka.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Vybrat klasifikaci" border="true":::

## <a name="custom-classification-rules"></a>Vlastní pravidla klasifikace

Služba katalogu poskytuje sadu výchozích pravidel klasifikace, které skener používá k automatickému zjišťování určitých datových typů. Můžete také přidat vlastní pravidla klasifikace, abyste zjistili další typy dat, které by se vám mohly zajímat při hledání v rámci vašich datových nemovitostí. Tato funkce může být velmi výkonná, když se \' znovu pokusíte najít data v rámci své datové nemovitosti.

Příklad: řekněme, \' že společnost s názvem Contoso má ID zaměstnanců, které jsou standardizovány v celé společnosti, a to ve slově \" Zaměstnanec, \" následovaný identifikátorem GUID, aby bylo možné vytvořit zaměstnance {GUID}. Například jedna instance ID zaměstnance vypadá jako `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` .

Společnost Contoso může nakonfigurovat skenovací systém, aby vyhledal instance těchto ID vytvořením vlastního pravidla klasifikace. Mohou poskytovat regulární výraz, který odpovídá vzoru dat, v tomto případě `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Případně, pokud jsou data obvykle ve sloupci, který zná název, jako je například \_ ID zaměstnance nebo ČísloZaměstnance, může přidat regulární výraz vzorce sloupce, aby bylo prohledávání ještě přesnější. Příkladem regulárního výrazu je \_ ID zaměstnance \| ČísloZaměstnance.

Skenovací systém pak může toto pravidlo použít k prohlédnutí skutečných dat ve sloupci a názvu sloupce, který se pokusí identifikovat každou instanci, kde se našel vzor ID zaměstnance.

## <a name="steps-to-create-a-custom-classification-rule"></a>Postup vytvoření vlastního pravidla klasifikace

Vytvoření vlastního pravidla klasifikace:

1. Podle pokynů uvedených v předchozí části vytvořte vlastní klasifikaci. Tuto vlastní klasifikaci přidáte do konfigurace pravidla klasifikace, aby ji systém při hledání shody ve sloupci vyplatil.

2. Vyberte ikonu **centra pro správu** .

3. Vyberte část **pravidla klasifikací** .

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Dlaždice pravidel klasifikace" border="true":::

4. Vyberte možnost pro **novou** položku.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Přidat nové pravidlo klasifikace" border="true":::

5. Otevře se dialogové okno **nové pravidlo klasifikace** . Vyplňte pole a rozhodněte se, jestli chcete vytvořit **pravidlo regulárního výrazu** nebo **pravidlo slovníku**.

   |Pole     |Popis  |
   |---------|---------|
   |Název   |    Povinná hodnota. Maximální počet je 100 znaků.    |
   |Description      |Nepovinný parametr. Maximální počet je 256 znaků.    |
   |Název klasifikace    | Povinná hodnota. V rozevíracím seznamu vyberte název klasifikace a sdělte tak, že se bude používat, pokud se najde shoda.        |
   |Stav   |  Povinná hodnota. Možnosti jsou povoleny nebo zakázány. Výchozí hodnota je Enabled.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Vytvořit nové pravidlo klasifikace" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Vytvoření pravidla regulárního výrazu

1. Při vytváření pravidla regulárního výrazu se zobrazí následující obrazovka. Volitelně můžete nahrát soubor, který se použije ke **generování navržených vzorů regulárního výrazu** pro pravidlo.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Vytvořit nové pravidlo regulárního výrazu" border="true":::

1. Pokud se rozhodnete vygenerovat navrhovaný vzor regulárního výrazu, po nahrání souboru vyberte jeden z navrhovaných vzorů a kliknutím na tlačítko **Přidat ke vzorům** použijte navrhovaná data a vzorce sloupců. Navrhované vzory můžete upravit nebo můžete také zadat vlastní vzory bez odeslání souboru.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Generovat navrhovaný regulární výraz" border="true":::

   |Pole     |Description  |
   |---------|---------|
   |Datový vzor    |Nepovinný parametr. Regulární výraz, který představuje data uložená v datovém poli. Limit je velmi velký. V předchozím příkladu vzorce dat testují ID zaměstnance, který je doslova slovem `Employee{GUID}` .  |
   |Vzor sloupce    |Nepovinný parametr. Regulární výraz, který představuje názvy sloupců, které chcete vyhledat. Limit je velmi velký. |

1. V části **datový vzor** existují dva prahové hodnoty, které můžete nastavit:

   - **Rozdílová prahová hodnota shody**: celkový počet jedinečných datových hodnot, které musí být ve sloupci nalezeny předtím, než skener spustí datový vzor. Navrhovaná hodnota je 8. Tuto hodnotu lze ručně upravit v rozsahu od 2 do 32. Tato hodnota vyžaduje, aby tento sloupec obsahoval dostatek dat, aby ho mohl přesně klasifikovat skener. Například sloupec, který obsahuje více řádků, které obsahují hodnotu 1, nebude klasifikován. Sloupce, které obsahují jeden řádek s hodnotou a zbytek řádků, obsahují hodnoty null ani nebudou klasifikovány. Pokud zadáte více vzorů, bude tato hodnota platit pro každý z nich.

   - **Minimální prahová hodnota shody**: pomocí tohoto nastavení můžete nastavit minimální procento rozdílných hodnot dat ve sloupci, který musí skener najít, aby se klasifikace použila. Navrhovaná hodnota je 60%. Musíte být opatrní s tímto nastavením. Pokud snížíte úroveň nižší než 60%, můžete do katalogu uvést falešně pozitivní klasifikace. Pokud zadáte více vzorů dat, je toto nastavení zakázáno a hodnota je opravena na 60%.

1. Nyní můžete ověřit pravidlo a **vytvořit** ho.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Před vytvořením ověřit pravidlo" border="true":::

### <a name="creating-a-dictionary-rule"></a>Vytvoření pravidla slovníku

1. Při vytváření pravidla slovníku se zobrazí následující obrazovka. Nahrajte soubor, který obsahuje všechny možné hodnoty pro klasifikaci, kterou vytváříte v jednom sloupci.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Vytvořit pravidlo slovníku" border="true":::

1. Po vygenerování slovníku můžete upravit jedinečnou shodu a prahovou hodnotu minimální shody a pravidlo odeslat.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Pravidlo dosah slovníku – upravte rozdílovou prahovou hodnotu shody a minimální prahovou hodnotu shody." border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Vytvoří pravidlo slovníku s zaškrtnutím slovníku generovaného slovníkem." border="true":::

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili pravidlo klasifikace, je připravené ho přidat do sady pravidel skenování, aby kontrola při kontrole použila pravidlo. Další informace najdete v tématu [Vytvoření sady pravidel skenování](create-a-scan-rule-set.md).
