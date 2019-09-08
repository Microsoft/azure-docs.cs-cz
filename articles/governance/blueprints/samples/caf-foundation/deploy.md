---
title: Ukázky – plán CAF Foundation – postup nasazení
description: Nasaďte kroky ukázky podrobného plánu CAF Foundation.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: 98ade4e5c8a14c2d4f5c2a22c2c973fac844861f
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802555"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Nasazení rozhraní Microsoft Cloud pro přijetí v ukázce pro Azure Foundation podrobný plán

Chcete-li Microsoft Cloud nasadit ukázkovou strukturu plánů pro Azure (CAF) Foundation, je nutné provést následující kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte vzorový plán **CAF Foundation** a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: Zadejte název vaší kopie ukázky podrobného plánu CAF Foundation.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu, do které chcete kopii ukázky Uložit.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo plán CAF Foundation.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Poskytněte **poznámky ke změnám** , jako je například "první verze publikovaná z ukázky podrobného plánu CAF Foundation". Potom v dolní části stránky vyberte **publikovat** .

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **přiřadit podrobný plán** .

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základní informace
       - **Předplatná:** Vyberte jedno nebo více předplatných, které jsou ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: Název je předem vyplněný pro vás na základě názvu podrobného plánu.
       Změňte podle potřeby nebo ponechte tak, jak je.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit.
     - Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu.
       Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice**podrobného plánu: Vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Uzamknout přiřazení

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Vyberte buď výchozí možnost spravovaná identita _přiřazená systémem_ , nebo možnost Identita _přiřazená uživatelem_ .

   - Parametry podrobného plánu

     Parametry definované v tomto oddílu používá mnoho artefaktů v definici podrobného plánu k zajištění konzistence.

     - **Organizace**: Zadejte název vaší organizace, například contoso, musí být jedinečný.
     - **Oblast Azure**: Vyberte oblast Azure pro nasazení.
     - **Povolená umístění**: Které oblasti Azure umožníte sestavení prostředků?
     
   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** . Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure modrotisky a předdefinované ukázky podrobného plánu jsou **zdarma**. Ceny prostředků Azure se účtují [podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|Povolené skladové položky účtu úložiště|Přiřazení zásad|Policy_Allowed-StorageAccount-SKU|SKU používané v účtech úložiště diagnostického protokolu|
|Povolené skladové položky virtuálních počítačů|Přiřazení zásad|Policy_Allowed-VM – SKU|Povolené skladové položky virtuálních počítačů|
|Přidat značku CostCenter do skupin prostředků|Přiřazení zásad|Policy_CostCenter_Tag|Připojit značku CostCenter a její hodnotu ze skupiny prostředků|
|Typy prostředků, které nechcete ve svém prostředí povolit|Přiřazení zásad|_Allowed zásad – typy prostředků|Které prostředky Azure mají být ve vašem prostředí povolené|
|Nasazení Key Vault|Šablona Resource Manageru|KV – AccessPolicy|**Uzamčené** – skupina nebo uživatel <Object ID> Azure AD, kterým chcete udělit oprávnění v Key Vault|
|Nasazení Log Analytics|Šablona Resource Manageru|LogAnalytics_DataRetention|**Uzamčený** počet dní, po které se budou uchovávat data v Log Analytics|
|Nasazení Log Analytics|Šablona Resource Manageru|LogAnalytics_Location|**Zamčené** – oblast použitá při vytváření pracovního prostoru|

## <a name="next-steps"></a>Další postup

Teď, když jste si prohlédli postup nasazení ukázky podrobného plánu CAF Foundation, najdete v následujícím článku informace o architektuře:

> [!div class="nextstepaction"]
> [Podrobný plán CAF Foundation – přehled](./index.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)