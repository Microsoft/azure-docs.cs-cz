---
title: Ukázka podrobného plánu vstupní zóny migrace CAF
description: Nasazení kroků pro ukázku podrobného plánu cílové zóny migrace CAF, včetně podrobností o parametru artefaktu podrobného plánu.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: da17083e8e1b27739288a9d9ac087d071ff4861c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545463"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Nasazení rozhraní Microsoft Cloud Adoption Framework pro Azure migrují ukázku podrobného plánu cílové zóny

Chcete-li nasadit ukázku plánu cílové zóny migrace caf azure, je třeba podniknout následující kroky:

> [!div class="checklist1"]
> - Doporučujeme nasadit ukázku podrobného plánu [nadace CAF Foundation.](../caf-foundation/index.md)

> [!div class="checklist2"]
> - Vytvoření nového podrobného plánu z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvoření podrobného plánu z ukázky

Nejprve implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako startéru.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce **Začínáme** vlevo vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. Najděte ukázku podrobného plánu **cílové zóny migrace CAF** v části _Další ukázky_ a vyberte **Použít tuto ukázku**.

1. Zadejte _základy ukázky_ podrobného plánu:
   - **Název podrobného plánu** Zadejte název pro kopii ukázky podrobného plánu zóny migrace CAF.
   - **Umístění definice** Použijte tři tečky a vyberte skupinu pro správu, do které chcete uložit kopii vzorku.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Po dokončení revize ukázky podrobného plánu vyberte **Uložit koncept.**

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopie

Vaše kopie ukázky podrobného plánu byla nyní vytvořena ve vašem prostředí. Je vytvořen v režimu **konceptu** a musí být **publikovánpřed** jeho přiřazením a nasazením. Kopii ukázky podrobného plánu lze přizpůsobit vašemu prostředí a potřebám, ale tato změna ji může přesunout mimo pokyny pro migrační zónu CAF.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **Publikovat podrobný plán.** Na nové stránce vpravo zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud provedete změnu později. **Poskytněte poznámky ke změně,** například "První verze publikovaná z ukázky podrobného plánu zóny migrace CAF". Pak vdolní části stránky vyberte **Publikovat.**

## <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Jakmile je kopie ukázky podrobného plánu úspěšně **publikována**, lze ji přiřadit k předplatnému v rámci skupiny pro správu, do které byla uložena. Tento krok je, kde jsou k dispozici parametry, aby každé nasazení kopie ukázky podrobného plánu jedinečné.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán.**

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy
     - **Odběry:** Vyberte jedno nebo více předplatných, které jsou ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, bude pro každé z nich vytvořeno přiřazení pomocí zadaných parametrů.
     - **Název přiřazení**: Název je předem vyplněný na základě názvu podrobného plánu.
       Změňte podle potřeby nebo odejděte tak, jak je.
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořena.
     - Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu.
       Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice podrobného plánu**: Vyberte **publikovanou** verzi ukázky podrobného plánu.
    
   - Přiřazení zámku

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Zvolte buď výchozí _možnost přiřazenou_ spravovanou identitu, nebo možnost _identity přiřazené uživateli._

   - Parametry podrobného plánu

     Parametry definované v této části jsou používány mnoho artefaktů v definici podrobného plánu poskytnout konzistenci.

       - **Organizace**: Zadejte název organizace, například Contoso nebo Fabrikam, musí být jedinečný.
       - **AzureRegion**: Vyberte jednu oblast Azure pro nasazení.
       
   - Parametry artefaktů

     Parametry definované v této části platí pro artefakt, pod kterým je definován. Tyto parametry jsou [dynamické parametry,](../../concepts/parameters.md#dynamic-parameters) protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popisy naleznete v [tabulce parametrů artefaktu](#artifact-parameters-table).

1. Po zadání všech parametrů **vyberte** Přiřadit v dolní části stránky. Přiřazení podrobného plánu je vytvořen a artefakt nasazení začíná. Nasazení trvá zhruba pět minut. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobných plánů jsou **zdarma**. Prostředky Azure jsou [oceněny podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na spuštění prostředků nasazených v této ukázce podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktu

V následující tabulce je uveden seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|Nasazení přistávací zóny virtuální sítě|Šablona Resource Manageru|IPAddress_Space|**Uzamčeno** - Zadejte první dva oktety příklad, 10.0|
|Nasazení trezoru klíčů|Šablona Resource Manageru|Zásady přístupu kV|**Uzamčeno** – ID skupiny nebo objektu uživatele, které udělují oprávnění v trezoru klíčů|
|Nasazení analýzy protokolů|Šablona Resource Manageru|LogAnalytics_DataRetention|**Uzamčeno** – počet dní, po které budou data v Log Analytics uchována.|
|Nasazení analýzy protokolů|Šablona Resource Manageru|LogAnalytics_Location|**Uzamčeno** – oblast používaná při vytváření pracovního prostoru|
|Nasazení migrace Azure|Šablona Resource Manageru|Azure_Migrate_Location|**Uzamčeno** – vyberte oblast, která má nasadit Migraci Azure.|

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali kroky nasazení ukázky podrobného plánu pro migraci vstupní zóny CAF, navštivte následující články, kde se dozvíte o architektuře:

> [!div class="nextstepaction"]
> [Podrobný plán cílové zóny migrace CAF - Přehled](./index.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).