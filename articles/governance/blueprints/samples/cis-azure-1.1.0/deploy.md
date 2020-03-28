---
title: Nasazení ukázky podrobného plánu srovnávacího testu nadace CIS Microsoft Azure Foundations
description: Nasazení kroků pro ukázku podrobného plánu benchmarku CIS Microsoft Azure Foundations, včetně podrobností o parametrech artefaktu podrobného plánu.
ms.date: 10/14/2019
ms.topic: sample
ms.openlocfilehash: 7476addee3d31ec355c793ab7fbe1fbb732b8013
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74544526"
---
# <a name="deploy-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Nasazení ukázky podrobného plánu srovnávacího testu nadace CIS Microsoft Azure Foundations

Chcete-li nasadit ukázku podrobného plánu řešení Azure Blueprints CIS Microsoft Azure Foundations, je třeba podniknout následující kroky:

> [!div class="checklist"]
> - Vytvoření nového podrobného plánu z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvoření podrobného plánu z ukázky

Nejprve implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako startéru.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce **Začínáme** vlevo vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. Najděte srovnávací test **CIS Microsoft Azure Foundations v1.1.0** v části _Další ukázky_ a vyberte **Použít tuto ukázku**.

1. Zadejte _základy ukázky_ podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky podrobného plánu benchmarku CIS Microsoft Azure Foundations.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu, do které chcete uložit kopii vzorku.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Po dokončení revize ukázky podrobného plánu vyberte **Uložit koncept.**

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopie

Vaše kopie ukázky podrobného plánu byla nyní vytvořena ve vašem prostředí. Je vytvořen v režimu **konceptu** a musí být **publikovánpřed** jeho přiřazením a nasazením. Kopii ukázky podrobného plánu lze přizpůsobit vašemu prostředí a potřebám, ale tato úprava ji může přesunout od zarovnání s doporučeními benchmarku CIS Microsoft Azure Foundations.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **Publikovat podrobný plán.** Na nové stránce vpravo zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud provedete změnu později. **Poskytněte poznámky ke změně,** například "První verze publikovaná z ukázky podrobného plánu benchmarku CIS Microsoft Azure Foundations.". Pak vdolní části stránky vyberte **Publikovat.**

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
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořena. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice podrobného plánu**: Vyberte **publikovanou** verzi ukázky podrobného plánu.

   - Přiřazení zámku

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí možnost spravované identity _přiřazenou systému._

   - Parametry artefaktů

     Parametry definované v této části platí pro artefakt, pod kterým je definován. Tyto parametry jsou [dynamické parametry,](../../concepts/parameters.md#dynamic-parameters) protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popisy naleznete v [tabulce parametrů artefaktu](#artifact-parameters-table).

1. Po zadání všech parametrů **vyberte** Přiřadit v dolní části stránky. Přiřazení podrobného plánu je vytvořen a artefakt nasazení začíná. Nasazení trvá zhruba hodinu. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobných plánů jsou **zdarma**. Prostředky Azure jsou [oceněny podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na spuštění prostředků nasazených v této ukázce podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktu

V následující tabulce je uveden seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|\[Preview\]: Auditujte doporučení standardu 1.1.0 nadace CIS Microsoft Azure Foundations a nasaďte konkrétní podpůrná rozšíření virtuálních her.|Přiřazení zásad|Seznam oblastí, kde by měl být povolen sledování sítě|Středník-oddělený seznam oblastí. Chcete-li zobrazit úplný seznam oblastí, použijte Get-AzLocation. Ex: eastus; eastus2|
|\[Preview\]: Auditujte doporučení standardu 1.1.0 nadace CIS Microsoft Azure Foundations a nasaďte konkrétní podpůrná rozšíření virtuálních her.|Přiřazení zásad|Seznam rozšíření virtuálních strojů, která jsou schválena pro použití|Středník-oddělený seznam rozšíření . Chcete-li zobrazit úplný seznam rozšíření virtuálních strojů, použijte Get-AzVMExtensionImage. Ex: AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali kroky k nasazení ukázky podrobného plánu benchmarku CIS Microsoft Azure Foundations, navštivte následující články, kde se dozvíte o mapování podrobného plánu a ovládacích prvku:

> [!div class="nextstepaction"]
> [Podrobný plán srovnávacího testu nadace CIS Microsoft Azure Foundations – přehled](./index.md)
> [podrobného plánu srovnávacího testu nadace CIS Microsoft Azure Foundations – mapování řízení](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).
