---
title: Ukázka - v3.2.1 PCI-DSS podrobného plánu - kroky nasazení
description: Kroky Standard odvětví platebních karet Data zabezpečení vzorku v3.2.1 podrobného plánu nasazení.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b4e9435e5f569a076fc5beb6441b9da935b87f3a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561366"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Nasazení ukázkové PCI-DSS v3.2.1 podrobného plánu

Pokud chcete nasadit ukázková plány Azure PCI-DSS v3.2.1 podrobného plánu, třeba takto:

> [!div class="checklist"]
> - Vytvořit nový plán, podle kterého z ukázky
> - Označte si kopii ukázky jako **publikováno**
> - Přiřazení kopie podrobný plán k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

První tak, že vytvoříte nový plán ve vašem prostředí pomocí ukázky jako starter implementaci vzorku podrobného plánu.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Z **Začínáme** stránky na levé straně, vyberte **vytvořit** tlačítko _vytvořit podrobný plán_.

1. Najít **PCI-DSS v3.2.1** podrobného plánu ukázky v části _Další ukázky_ a vyberte **fungování této ukázky**.

1. Zadejte _Základy_ vzorku podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky PCI-DSS v3.2.1 podrobného plánu.
   - **Umístění definice**: Použít na tři tečky a vyberte skupiny pro správu k uložení kopie vzorku.

1. Vyberte _artefakty_ kartě v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Projděte si seznam artefakty, které tvoří vzorku podrobného plánu. Řada artefakty, které má parametry, které budeme definovat později. Vyberte **uložit koncept** až dokončíte revize vzorku podrobného plánu.

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopírování

Vaši kopii ukázky podrobného plánu byla vytvořena ve vašem prostředí. Vytvoří se v **koncept** režimu a musí být **publikováno** předtím, než může být přiřazena a nasadit. Kopii ukázky podrobný plán lze přizpůsobit, aby vaše prostředí a požadavky, ale tato změna může přesunout mimo standard PCI-DSS v3.2.1.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru vyhledejte vaši kopii ukázky podrobného plánu a vyberte ji.

1. Vyberte **publikovat podrobný plán** v horní části stránky. Nová stránka na pravé straně, zadejte **verze** vaši kopii ukázky podrobného plánu. Tato vlastnost je užitečná pro, pokud provedete změny později. Zadejte **změnit poznámky** jako "první verze publikování z ukázky PCI-DSS v3.2.1 podrobného plánu." Potom vyberte **publikovat** v dolní části stránky.

## <a name="assign-the-sample-copy"></a>Přiřazení kopie vzorku

Jakmile kopii ukázky podrobný plán byl úspěšně **publikováno**, je možné přiřadit na předplatné v rámci se uložila do skupiny pro správu. Tento krok je, kde jsou k dispozici parametry aby každého nasazení kopii ukázky podrobného plánu jedinečný.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru vyhledejte vaši kopii ukázky podrobného plánu a vyberte ji.

1. Vyberte **přiřazení podrobného plánu** v horní části stránky definice podrobného plánu.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná:** Vyberte jednu nebo několik předplatných, které jsou ve skupině pro správu můžete uložit kopii ukázku podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: Název je předem vyplněna za vás na základě názvu podrobný plán.
       Nechte, jak je změnit podle potřeby.
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořen v. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice podrobného plánu**: Vyberte **publikováno** verzi vaší kopii ukázky podrobného plánu.

   - Přiřazení zámku

     Vyberte nastavení pro vaše prostředí zámek podrobného plánu. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí nastavení _přiřazenou systémem_ spravované identity možnost.

   - Parametry artefaktu

     Parametry definované v této části se vztahují na artefakt, pod kterým je definována. Tyto parametry jsou [dynamických parametrů](../../concepts/parameters.md#dynamic-parameters) protože máte definovaný během přiřazování podrobný plán. Úplný seznam nebo artefaktů parametry a jejich popis najdete v tématu [artefaktů parametry tabulky](#artifact-parameters-table).

1. Po zadání všech parametrů se vyberte **přiřadit** v dolní části stránky. Přiřazení podrobného plánu se vytvoří a začne artefaktů nasazení. Nasazení trvá přibližně hodinu. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure plány a ukázky předdefinovaných podrobného plánu **zdarma**. Jsou prostředky Azure [ceny podle produktu](https://azure.microsoft.com/pricing/). Použití [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) odhadnout náklady na provozování prostředky nasazené v tomto příkladu podrobného plánu.

## <a name="artifact-parameters-table"></a>Parametry tabulky artefaktu

Následující tabulka obsahuje seznam podrobný plán artefaktů parametry:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|[Preview]: Auditovat PCI v3.2.1:2018 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování|Přiřazení zásad|Seznam typů prostředků | Auditování nastavení diagnostiky pro vybrané typy prostředků. Výchozí hodnota je, že jsou vybrané všechny prostředky| 
|Povolená umístění|Přiřazení zásad|Seznam povolených umístění|Seznam datacentrum v umístění povolené pro libovolné prostředky nasazované do. Tento seznam je přizpůsobitelný tak, aby požadované umístění Azure globálně. Vyberte umístění, které chcete povolit.| 
|Povolená umístění pro skupiny prostředků|Přiřazení zásad |Povolená umístění |Tato zásada umožňuje omezit umístění, které vaší organizaci můžou vytvářet skupiny prostředků v. Slouží k vynucení požadavků na dodržování geografických předpisů.| 
|Nasazení auditování na serverech SQL|Přiřazení zásad|Počet dnů uchování informací|Data rentention za počet dnů. Výchozí hodnota je 180, ale PCI vyžaduje 365.| 
|Nasazení auditování na serverech SQL|Přiřazení zásad|Název skupiny prostředků pro účet úložiště|Auditování zapisuje databáze k auditu protokolu ve vašem účtu úložiště Azure (účet úložiště se vytvoří v každé oblasti, ve kterém je vytvořena serveru SQL Server, který budou sdílet všechny servery v této oblasti).| 

## <a name="next-steps"></a>Další postup

Teď, když si kroky pro nasazení ukázky PCI-DSS v3.2.1 podrobného plánu, naleznete v následujících článcích se dozvíte o přehled a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Podrobný plán v3.2.1 PCI-DSS – přehled](./index.md)
> [podrobného plánu v3.2.1 PCI-DSS – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
