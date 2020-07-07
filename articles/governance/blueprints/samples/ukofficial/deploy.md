---
title: Nasazení OFICIÁLNÍch ukázek pro Spojené království & NHS UK
description: Nasaďte kroky pro oficiální Spojené království a ukázky pro Spojené království (NHS), včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 05/08/2020
ms.topic: sample
ms.openlocfilehash: 2ac2d17c08b3f585f86d1b2d339c3c7cf52844ce
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037167"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Nasazení ukázek pro Spojené království a ukázky pro Spojené království NHS podrobný plán

K nasazení ukázek pro Spojené království a NHS podrobného plánu je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free), ještě než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte oficiální nebo **NHS** podrobný plán na velká **Británie** a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázky podrobného plánu.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **Další: artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo Standard.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Poskytněte **poznámky ke změnám** , jako je například "první verze publikovaná ze vzorové Británie nebo ukázky pro Velká Británie (NHS)." Potom v dolní části stránky vyberte **publikovat** .

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **přiřadit podrobný plán** .

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Změňte podle potřeby nebo ponechte tak, jak je.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice**podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktu

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** . Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure modrotisky a předdefinované ukázky podrobného plánu jsou **zdarma**. Ceny prostředků Azure se účtují [podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktu podrobného plánu:

Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|Iniciativa pro Spojené království a oficiální nebo Velká Británie NHS|Přiřazení zásad |Typy prostředků pro audit diagnostických protokolů (zásady: podrobný plán iniciativ pro Spojené království a oficiální nebo Velká Británie NHS) |Seznam typů prostředků, které se mají auditovat, pokud je v nastavení diagnostického protokolu zapnutá možnost Poznámka  Přijatelné hodnoty najdete v tématu [podporované služby, schémata a kategorie pro diagnostické protokoly Azure](../../../../azure-monitor/platform/resource-logs-schema.md). |
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux |Přiřazení zásad |Volitelné: seznam imagí virtuálních počítačů, které jsou podporované operačním systémem Linux pro přidání do oboru (zásady: \[ verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux) |Volitelné Výchozí hodnota je _none_. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows |Přiřazení zásad |Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru (Policy: \[ Preview \] : nasazení Log Analytics Agent pro virtuální počítače s Windows) |Volitelné Výchozí hodnota je _none_. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli kroky k nasazení ukázek OFICIÁLNÍch a NHS podrobných plánů na Velká Británie, najdete v následujících článcích informace o přehledu a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Oficiální a NHSové plány Spojené království – přehled](./index.md) 
>  [Oficiální Velká Británie a NHS v modrotisky – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
