---
title: Ukázka – UK-OFFICIAL a podrobný plán NHS UK - kroky nasazení
description: Kroky UK-OFFICIAL a ukázky UK NHS podrobného plánu nasazení.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 43aae882f27031d3e51ac8a4f5a68d243a973d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453200"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Nasazení ukázky UK NHS podrobného plánu a UK-OFFICIAL

Nasazení UK-OFFICIAL a ukázky podrobného plánu NHS Velká Británie, musí provést následující kroky:

> [!div class="checklist"]
> - Vytvořit nový plán, podle kterého z ukázky
> - Označte si kopii ukázky jako **publikováno**
> - Přiřazení kopie podrobný plán k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

První tak, že vytvoříte nový plán ve vašem prostředí pomocí ukázky jako starter implementaci vzorku podrobného plánu.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Z **Začínáme** stránky na levé straně, vyberte **vytvořit** tlačítko _vytvořit podrobný plán_.

1. Najít **UK-OFFICIAL** nebo **UK NHS** podrobného plánu ukázky v části _Další ukázky_ a vyberte **fungování této ukázky**.

1. Zadejte _Základy_ vzorku podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky podrobného plánu.
   - **Umístění definice**: Použít na tři tečky a vyberte skupiny pro správu k uložení kopie vzorku.

1. Vyberte _artefakty_ kartě v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Projděte si seznam artefakty, které tvoří vzorku podrobného plánu. Řada artefakty, které má parametry, které budeme definovat později. Vyberte **uložit koncept** až dokončíte revize vzorku podrobného plánu.

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopírování

Vaši kopii ukázky podrobného plánu byla vytvořena ve vašem prostředí. Vytvoří se v **koncept** režimu a musí být **publikováno** předtím, než může být přiřazena a nasadit. Kopii ukázky podrobný plán lze přizpůsobit, aby vaše prostředí a požadavky, ale tato změna může přesunout mimo standardní.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru vyhledejte vaši kopii ukázky podrobného plánu a vyberte ji.

1. Vyberte **publikovat podrobný plán** v horní části stránky. Nová stránka na pravé straně, zadejte **verze** vaši kopii ukázky podrobného plánu. Tato vlastnost je užitečná pro, pokud provedete změny později. Zadejte **změnit poznámky** jako "první verze publikovat podrobný plán ukázka UK OFFICIAL nebo NHS Velká Británie." Potom vyberte **publikovat** v dolní části stránky.

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

Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|Podrobný plán iniciativy pro UK-OFFICIAL nebo NHS Velká Británie|Přiřazení zásad |Typy prostředků pro diagnostické protokoly auditu (zásad: Podrobný plán iniciativy pro UK-OFFICIAL nebo NHS Spojené království) |Seznam typů prostředků auditovat, pokud je nastavení diagnostický protokol Poznámka povolena.  Přípustné hodnoty, najdete v části [podporované služby, schémat a kategorie pro diagnostické protokoly Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|[Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem |Přiřazení zásad |Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru (zásady: [Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem) |(Volitelné) Výchozí hodnota je _žádný_. Další informace najdete v tématu [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|[Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Windows |Přiřazení zásad |Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Windows pro přidání do oboru (zásady: [Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Windows) |(Volitelné) Výchozí hodnota je _žádný_. Další informace najdete v tématu [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Další postup

Teď, když si kroky k nasazení UK-OFFICIAL a UK NHS podrobného plánu ukázky naleznete v následujících článcích se dozvíte o přehled a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Plány UK-OFFICIAL a UK NHS - přehled](./index.md)
> [UK-OFFICIAL a plány NHS Velká Británie – ovládací prvek mapování](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
