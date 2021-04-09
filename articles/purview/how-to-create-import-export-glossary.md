---
title: Jak vytvářet, importovat a exportovat terminologie glosáře
description: Naučte se vytvářet, importovat a exportovat terminologie v Azure dosah.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 6f7f481ae0e0c75b14d894080f791161346cd93f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952401"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Jak vytvářet, importovat a exportovat terminologie glosáře

Tento článek popisuje, jak ve službě Azure dosah Data Catalog vytvořit pojem podnikového glosáře a importovat a exportovat pojmy glosáře pomocí souborů. csv.

## <a name="create-a-new-term"></a>Vytvořit nový termín

Chcete-li vytvořit nový pojem Glosář, proveďte následující kroky:

1. Vyberte ikonu Glosář v levém navigačním panelu na domovské stránce, abyste přešli na stránku seznam termínů.

2. Na stránce **glosářové podmínky** vyberte **+ nový termín**. Otevře se stránka s vybranou výchozí šablonou **systému** . Zvolte šablonu, se kterou chcete vytvořit pojem Glosář, a vyberte **pokračovat**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Snímek obrazovky s novým vytvořením termínu" border="true":::

3. Dejte novému termínu název, který musí být v katalogu jedinečný. V názvu se rozlišují velká a malá písmena, což znamená, že v katalogu můžete mít výraz s názvem **Sample** a **Sample** .

4. Přidejte **definici**.

5. Nastavte **stav** období. Nové výrazy ve výchozím nastavení mají stav **konceptu** .

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Snímek obrazovky s možnostmi stavu" border="true":::

   Tyto stavové značky jsou Metadata přidružená k termínu. V současné době můžete pro každý termín nastavit následující stav:

   - **Koncept**: Tato podmínka ještě není oficiálně implementovaná.
   - **Schváleno**: Tento pojem je oficiální/standardní/schválený.
   - **Vypršela platnost**: Tento termín již neměl být použit.
   - **Výstraha**: Tento pojem vyžaduje pozornost.

6. Přidejte **prostředky** a **akronym**. Pokud je termín součástí hierarchie, můžete na kartě Přehled Přidat nadřazené podmínky na **nadřazenou** položku.

7. Na související kartu přidejte **synonyma** a **související výrazy** .

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Snímek obrazovky s novým termínem související >." border="true":::

8. Volitelně můžete vybrat kartu **kontaktů** a přidat odborníky a Stewards k vašemu termínu.

9. Vyberte **vytvořit** a vytvořte svůj termín.

## <a name="import-terms-into-the-glossary"></a>Importovat výrazy do glosáře

Azure dosah Data Catalog poskytuje soubor Template. CSV pro import vašich podmínek do glosáře.

Můžete importovat výrazy v katalogu. Duplicitní výrazy v souboru budou přepsány.

Všimněte si, že názvy termínů rozlišují malá a velká písmena. Například `Sample` a `saMple` můžou existovat ve stejném glosáři.

### <a name="to-import-terms-follow-these-steps"></a>Pokud chcete importovat tyto výrazy, postupujte podle těchto kroků.

1. Pokud jste na stránce **glosářové výrazy** , vyberte **importovat výrazy**.

2. Otevře se stránka s termínem šablony. Porovnává s termínem šablonu s typem. Sdílený svazek clusteru, který chcete importovat.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Snímek obrazovky se stránkou pojmů Glosář, tlačítko importovat výrazy":::

3. Stáhněte šablonu sdíleného svazku clusteru a použijte ji k zadání svých výrazů, které chcete přidat.

   > [!Important]
   > Systém podporuje pouze import sloupců, které jsou k dispozici v šabloně. Šablona "systémové výchozí" bude mít všechny výchozí atributy.
   > Vlastní šablony termínů ale budou mít z atributů pole a další vlastní atributy definované v šabloně. Proto je. Soubor CSV se liší od celkového počtu sloupců a názvů sloupců v závislosti na vybrané šabloně termínu. V souboru můžete také zkontrolovat problémy po nahrání.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Snímek obrazovky s výrazy glosáře vyberte soubor pro import.":::

4. Až dokončíte vyplňování souboru. csv, vyberte soubor, který chcete importovat, a pak vyberte **OK**.

5. Systém nahraje soubor a přidá všechny tyto požadavky do vašeho katalogu.
 
   > [!Important]
   > E-mailová adresa pro Stewards a odborníky by měla být primární adresou uživatele ze skupiny AAD. Alternativní e-maily, hlavní název uživatele a e-maily, které nejsou AAD, se zatím nepodporují. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Exportovat výrazy z glosáře s vlastními atributy

Z glosáře byste měli být schopni exportovat podmínky, pokud vybrané podmínky patří do stejné šablony termínu.

1. Když jste v glosáři, je ve výchozím nastavení tlačítko **exportovat** zakázané. Po výběru podmínek, které chcete exportovat, je tlačítko **exportovat** povoleno, pokud vybrané výrazy patří do stejné šablony.

2. Vyberte **exportovat** a Stáhněte si vybrané výrazy.

 > [!Important]
   > Pokud podmínky v hierarchii patří do různých šablon termínů, je třeba je rozdělit na jiné. Soubory CSV pro import. Aktualizace nadřazeného termínu Term se také v současné době nepodporuje pomocí procesu importu.


## <a name="next-steps"></a>Další kroky

Postup najdete v tomto [kurzu: vytváření a import podmínek glosáře](tutorial-import-create-glossary-terms.md) a další informace.
