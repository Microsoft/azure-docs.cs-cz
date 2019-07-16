---
title: Ukázka - SP NIST 800-53 R4 podrobného plánu - kroky nasazení
description: Nasaďte kroky NIST SP 800-53 R4 podrobného plánu ukázku.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 206763e2d17f4ad711ff5fd897f1429814e61837
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228902"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Nasadit NIST SP 800-53 R4 podrobného plánu ukázku

Chcete-li nasadit SP Azure plány NIST 800-53 R4 podrobného plánu ukázku, musí provést následující kroky:

> [!div class="checklist"]
> - Vytvořit nový plán, podle kterého z ukázky
> - Označte si kopii ukázky jako **publikováno**
> - Přiřazení kopie podrobný plán k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

První tak, že vytvoříte nový plán ve vašem prostředí pomocí ukázky jako starter implementaci vzorku podrobného plánu.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Z **Začínáme** stránky na levé straně, vyberte **vytvořit** tlačítko _vytvořit podrobný plán_.

1. Najít **SP NIST 800-53 R4** podrobného plánu ukázky v části _Další ukázky_ a vyberte **fungování této ukázky**.

1. Zadejte _Základy_ vzorku podrobného plánu:

   - **Název podrobného plánu**: Zadejte název vaší kopii ukázky NIST SP 800-53 R4 podrobného plánu.
   - **Umístění definice**: Použít na tři tečky a vyberte skupiny pro správu k uložení kopie vzorku.

1. Vyberte _artefakty_ kartě v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Projděte si seznam artefakty, které tvoří vzorku podrobného plánu. Řada artefakty, které má parametry, které budeme definovat později. Vyberte **uložit koncept** až dokončíte revize vzorku podrobného plánu.

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopírování

Vaši kopii ukázky podrobného plánu byla vytvořena ve vašem prostředí. Vytvoří se v **koncept** režimu a musí být **publikováno** předtím, než může být přiřazena a nasadit. Kopii ukázky podrobný plán lze přizpůsobit, aby vaše prostředí a požadavky, ale tato změna může přesun ho od soulad s NIST SP 800-53 ovládacích prvků.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru vyhledejte vaši kopii ukázky podrobného plánu a vyberte ji.

1. Vyberte **publikovat podrobný plán** v horní části stránky. Nová stránka na pravé straně, zadejte **verze** vaši kopii ukázky podrobného plánu. Tato vlastnost je užitečná pro, pokud provedete změny později. Zadejte **změnit poznámky** jako "první verze publikovat NIST SP 800-53 R4 podrobného plánu vzorku." Potom vyberte **publikovat** v dolní části stránky.

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
|\[Ve verzi Preview\]: Auditovat NIST SP 800-53 R4 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování|Přiřazení zásad|ID pracovního prostoru analýzy protokolů, které virtuální počítače by měl být nakonfigurovaný pro|Toto je pracovního prostoru Log Analytics, virtuální počítače by měl být nakonfigurovaný pro ID (GUID).|
|\[Ve verzi Preview\]: Auditovat NIST SP 800-53 R4 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování|Přiřazení zásad|Seznam typů prostředků, které by měly být povolené diagnostické protokoly|Seznam typů prostředků pro audit, jestli není zapnutá diagnostických protokolů. Přípustné hodnoty lze nalézt v [Azure Monitor diagnostické protokoly, schémata](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Ve verzi Preview\]: Auditovat NIST SP 800-53 R4 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování|Přiřazení zásad|Seznam uživatelů, které mají být vyloučeny ze skupiny Administrators virtuálních počítačů Windows|Středníkem oddělený seznam členů, které by se měly vyloučit v místní skupině Administrators. Příklad: Správce; myUser1; myUser2|
|\[Ve verzi Preview\]: Auditovat NIST SP 800-53 R4 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování|Přiřazení zásad|Seznam uživatelů, kteří by měl být součástí skupiny Administrators virtuálních počítačů Windows|Středníkem oddělený seznam členů, které by měl být součástí místní skupiny Administrators. Příklad: Správce; myUser1; myUser2|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS)|Přiřazení zásad|Pracovní prostor log Analytics pro virtuální počítač Škálovací sady (VMSS v Linuxu)|Pokud tento pracovní prostor je mimo rozsah přiřazení je nutné ručně udělit oprávnění "Přispěvatel Log Analytics" (nebo podobnou) ID objektu zabezpečení toto přiřazení zásady|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do rozsahu|Prázdné pole slouží k označení žádné volitelné parametry: \[\]|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem|Přiřazení zásad|Pracovní prostor log Analytics pro virtuální počítače s Linuxem|Pokud tento pracovní prostor je mimo rozsah přiřazení je nutné ručně udělit oprávnění "Přispěvatel Log Analytics" (nebo podobnou) ID objektu zabezpečení toto přiřazení zásady|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do rozsahu|Prázdné pole slouží k označení žádné volitelné parametry: \[\]|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS)|Přiřazení zásad|Pracovní prostor log Analytics pro Windows virtuální počítač Škálovací sady (škálovací sada)|Pokud tento pracovní prostor je mimo rozsah přiřazení je nutné ručně udělit oprávnění "Přispěvatel Log Analytics" (nebo podobnou) ID objektu zabezpečení toto přiřazení zásady|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Windows pro přidání do rozsahu|Prázdné pole slouží k označení žádné volitelné parametry: \[\]|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Pracovní prostor log Analytics pro virtuální počítače s Windows|Pokud tento pracovní prostor je mimo rozsah přiřazení je nutné ručně udělit oprávnění "Přispěvatel Log Analytics" (nebo podobnou) ID objektu zabezpečení toto přiřazení zásady|
|\[Ve verzi Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Windows pro přidání do rozsahu|Prázdné pole slouží k označení žádné volitelné parametry: \[\]|
|Nasazení pokročilou ochranu před hrozbami pro účty úložiště.|Přiřazení zásad|Efekt|Informace o účinky zásad najdete v [pochopit účinky zásad Azure](../../../policy/concepts/effects.md)|
|Nasazení auditování na serverech SQL|Přiřazení zásad|Ve dnech doba uchování (0 určuje neomezenou dobu)|Počet dní uchování (volitelné, 180 dnů Pokud tento parametr zadán)|
|Nasazení auditování na serverech SQL|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro auditování služby SQL server|Auditování zapisuje databáze k auditu protokolu ve vašem účtu úložiště Azure (účet úložiště se vytvoří v každé oblasti, ve kterém je vytvořena serveru SQL Server, který budou sdílet všechny servery v této oblasti). Důležité – správné fungování auditování odstranit nebo přejmenovat skupiny prostředků nebo účty úložiště.|
|Nasazení nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Předpona účtu úložiště diagnostiky skupiny zabezpečení sítě|Tuto předponu se zkombinuje s umístěním skupiny zabezpečení sítě k názvu účtu úložiště vytvořený.|
|Nasazení nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Název skupiny prostředků pro účet úložiště diagnostiky skupiny zabezpečení sítě (musí existovat)|Vytvoří se účet úložiště ve skupině prostředků. Tato skupina prostředků už musí existovat.|

## <a name="next-steps"></a>Další postup

Teď, když si kroky k nasazení NIST SP 800-53 R4 podrobného plánu vzorku naleznete v následujících článcích se dozvíte o podrobného plánu a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Podrobný plán R4 SP NIST 800-53 – přehled](./index.md)
> [podrobného plánu R4 SP NIST 800-53 – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
