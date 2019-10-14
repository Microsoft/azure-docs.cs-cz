---
title: Ukázkový plán PCI-DSS v 3.2.1 – nasazení kroků
description: Nasaďte kroky pro ukázka zabezpečení dat v odvětví platební karty Standard v 3.2.1, včetně podrobností o parametrech artefaktu podrobného plánu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 85f9eab29411ac896f73eed7aba607c733e4b95e
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299047"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Nasazení ukázky pro PCI-DSS v 3.2.1 podrobný plán

Pokud chcete nasadit ukázkovou ukázku pro Azure modrotisky PCI-DSS v 3.2.1, je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte ukázkový plán **PCI-DSS v 3.2.1** a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázkového plánu PCI-DSS v 3.2.1.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **Další: artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo Standard PCI-DSS v 3.2.1.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , jako je například "první verze publikovaná z: PCI-DSS v 3.2.1 podrobný plán ukázka". Potom v dolní části stránky vyberte **publikovat** .

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **přiřadit podrobný plán** .

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Práce

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Změňte podle potřeby nebo ponechte tak, jak je.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Azure Blueprint používá tuto spravovanou identitu k nasazení všech artefaktů v přiřazeném podrobném plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice**podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace najdete v tématu [uzamykání prostředků v modrotisky](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktu

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** . Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure modrotisky a předdefinované ukázky podrobného plánu jsou **zdarma**. Ceny prostředků Azure se účtují [podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|\[Preview @ no__t-1 audit PCI v 3.2.1: ovládací prvky pro 2018 a nasazení specifických rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Seznam typů prostředků | Auditování nastavení diagnostiky pro vybrané typy prostředků. Výchozí hodnota jsou vybrané všechny prostředky.| 
|Povolená umístění|Přiřazení zásad|Seznam povolených umístění|Seznam umístění datových center povolených pro všechny prostředky, které se mají nasadit do. Tento seznam je globálně přizpůsobitelný pro požadovaná umístění Azure. Vyberte umístění, která chcete přidělit.| 
|Povolená umístění pro skupiny prostředků|Přiřazení zásad |Povolené umístění |Tato zásada vám umožní omezit umístění, ve kterých může organizace vytvářet skupiny prostředků. Použijte k vymáhání vašich požadavků na geografické dodržování předpisů.| 
|Nasazení auditování na SQL serverech|Přiřazení zásad|Dny uchování|Uchovávání dat v počtu dnů. Výchozí hodnota je 180, ale sběrnice PCI vyžaduje 365.| 
|Nasazení auditování na SQL serverech|Přiřazení zásad|Název skupiny prostředků pro účet úložiště|Audit zapisuje události databáze do protokolu auditu ve vašem účtu Azure Storage (účet úložiště se vytvoří v každé oblasti, kde se vytvoří SQL Server, který bude sdílen všemi servery v této oblasti).| 

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky pro PCI-DSS v 3.2.1 podrobný plán, najdete v následujících článcích informace o přehledu a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 podrobný plán – přehled](./index.md)
> [PCI-DSS v 3.2.1 podrobného plánu – mapování ovládacích prvků](./control-mapping.md)

Další články o semodrotiskych a jejich použití:

- Přečtěte si informace o [životním cyklu](../../concepts/lifecycle.md)podrobného plánu.
- Naučte se používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí klasifikace](../../concepts/sequencing-order.md)podrobného plánu.
- Zjistěte, jak využít [uzamykání prostředků](../../concepts/resource-locking.md)podrobného plánu.
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).
