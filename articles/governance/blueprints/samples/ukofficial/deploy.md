---
title: Nasazení OFICIÁLNÍch ukázek pro Spojené království & NHS UK
description: Nasaďte kroky pro oficiální Spojené království a ukázky pro Spojené království (NHS), včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: fc41e7d9d3867123286f970d7c54f5e6fff2d276
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578029"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Nasazení ukázek pro Spojené království a ukázky pro Spojené království NHS podrobný plán

K nasazení ukázek pro Spojené království a NHS podrobného plánu je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte oficiální nebo **NHS** podrobný plán na velká **Británie** a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky podrobného plánu.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Projděte si seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

## <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo Standard.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Poskytněte **poznámky ke změnám** , jako je například "první verze publikovaná ze vzorové Británie nebo ukázky pro Velká Británie (NHS)." Na konci stránky pak vyberte **Publikovat**.

## <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Po úspěšném **publikování** kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku zadáte parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán**.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každé z nich pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Podle potřeby ho změňte nebo ponechte.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice** podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení uzamčení podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktů podrobného plánu:

Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|Iniciativa pro Spojené království a oficiální nebo Velká Británie NHS|Přiřazení zásady |Typy prostředků pro audit diagnostických protokolů (zásady: podrobný plán iniciativ pro Spojené království a oficiální nebo Velká Británie NHS) |Seznam typů prostředků, které se mají auditovat, pokud je v nastavení diagnostického protokolu zapnutá možnost Poznámka  Přijatelné hodnoty najdete v tématu [podporované služby, schémata a kategorie pro diagnostické protokoly Azure](../../../../azure-monitor/essentials/resource-logs-schema.md). |
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux |Přiřazení zásady |Volitelné: seznam imagí virtuálních počítačů, které jsou podporované operačním systémem Linux pro přidání do oboru (zásady: \[ verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux) |Volitelné Výchozí hodnota je _none_. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../../../azure-monitor/logs/quick-create-workspace.md). |
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows |Přiřazení zásady |Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru (Policy: \[ Preview \] : nasazení Log Analytics Agent pro virtuální počítače s Windows) |Volitelné Výchozí hodnota je _none_. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../../../../azure-monitor/logs/quick-create-workspace.md). |

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
