---
title: 'Kurz: Ukázka podrobného plánu do nového prostředí'
description: V tomto kurzu použijete ukázku podrobného plánu k vytvoření definice podrobného plánu, která nastaví dvě skupiny prostředků a nakonfiguruje přiřazení role pro každou z nich.
ms.date: 03/25/2020
ms.topic: tutorial
ms.openlocfilehash: c4230282223b0a64f6254448fe069bf8f7ab9a15
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80282014"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Kurz: Vytvoření prostředí z ukázky podrobného plánu

Ukázkové podrobné plány poskytují příklady toho, co se dá dělat pomocí Azure Blueprints. Každý je ukázka s konkrétním záměrem nebo účelem, ale nevytváří úplné prostředí sami. Každý z nich je určen jako výchozí místo pro zkoumání pomocí Azure Blueprints s různými kombinacemi zahrnuté artefakty, návrhy a parametry.

Následující kurz používá **skupiny prostředků s** ukázkou podrobného plánu RBAC k prezentaci různých aspektů služby Podrobné plány. Jsou uvedeny následující kroky:

> [!div class="checklist"]
> - Vytvoření nové definice podrobného plánu z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému
> - Kontrola nasazených prostředků pro přiřazení
> - Zrušení přiřazení podrobného plánu k odebrání zámků

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu je potřeba předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-blueprint-definition-from-sample"></a>Vytvoření definice podrobného plánu z ukázky

Nejprve implementujte ukázku podrobného plánu. Import vytvoří nový podrobný plán ve vašem prostředí na základě ukázky.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce **Začínáme** vlevo vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. Najděte **skupiny prostředků s** ukázkou podrobného plánu RBAC v části Další _ukázky_ a vyberte ji.

1. Zadejte _základy ukázky_ podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro kopii ukázky podrobného plánu. Pro účely tohoto kurzu použijeme název _dva rgs-with-role-assignments_.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu nebo předplatné pro uložení kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Tato ukázka definuje dvě skupiny prostředků se zobrazovanými názvy _ProdRG_ a _PreProdRG_. Konečný název a umístění každé skupiny prostředků jsou nastaveny během přiřazení podrobného plánu. Skupině prostředků _ProdRG_ je přiřazena role _přispěvatele_ a skupině prostředků _PreProdRG_ jsou přiřazeny role _Vlastník_ a _Čtenáři._ Role přiřazené v definici jsou statické, ale uživatel, aplikace nebo skupina, která je přiřazena role je nastavena během přiřazení podrobného plánu.

1. Po dokončení revize ukázky podrobného plánu vyberte **Uložit koncept.**

Tento krok vytvoří kopii ukázkové definice podrobného plánu ve vybrané skupině pro správu nebo předplatné. Uložená definice podrobného plánu je spravována jako jakýkoli podrobný plán vytvořený od začátku. Ukázku můžete uložit do skupiny pro správu nebo předplatného tolikrát, kolikrát je potřeba. Každá kopie však musí mít jedinečný název.

Jakmile se zobrazí **definice podrobného plánu ukládání úspěšné** hodování, přejděte k dalšímu kroku.

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopie

Vaše kopie ukázky podrobného plánu byla nyní vytvořena ve vašem prostředí. Je vytvořen v režimu **konceptu** a musí být **publikovánpřed** jeho přiřazením a nasazením. Kopii ukázky podrobného plánu lze přizpůsobit vašemu prostředí a potřebám. V tomto kurzu neprovedeme žádné změny.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte definici podrobného plánu _se dvěma rgs-with-role-assignments_ a vyberte ji.

1. V horní části stránky vyberte **Publikovat podrobný plán.** V novém podokně vpravo zadejte **verzi** jako _1.0_ pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud provedete změnu později. Zadejte **poznámky ke změně,** například "První verze publikovaná ze skupin prostředků s ukázkou podrobného plánu RBAC". Pak vdolní části stránky vyberte **Publikovat.**

Tento krok umožňuje přiřadit podrobný plán k odběru. Po publikování lze změny stále provést. Další změny vyžadují publikování s novou hodnotou **Version** ke sledování rozdílů mezi různými verzemi stejné definice podrobného plánu.

Jakmile se zobrazí **definice podrobného plánu publikování úspěšné** hodování, přejděte k dalšímu kroku.

## <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Jakmile je kopie ukázky podrobného plánu úspěšně **publikována**, lze ji přiřadit k předplatnému v rámci skupiny pro správu, do které byla uložena. Tento krok je, kde jsou k dispozici parametry, aby každé nasazení kopie ukázky podrobného plánu jedinečné.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte definici podrobného plánu _se dvěma rgs-with-role-assignments_ a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán.**

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Odběry:** Vyberte jedno nebo více předplatných, které jsou ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, bude pro každé z nich vytvořeno přiřazení pomocí zadaných parametrů.
     - **Název přiřazení**: Název je předem vyplněný na základě názvu definice podrobného plánu.
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořena. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       V tomto kurzu vyberte _možnost Východní USA 2_.
     - **Verze definice podrobného plánu**: Vyberte **publikovanou** verzi _1.0_ kopie ukázkové definice podrobného plánu.

   - Přiřazení zámku

     Vyberte režim uzamčení podrobného plánu _jen pro čtení._ Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí možnost _Systému přiřazenou._ Další informace naleznete v tématu [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktů

     Parametry definované v této části platí pro artefakt, pod kterým je definován. Tyto parametry jsou [dynamické parametry,](../concepts/parameters.md#dynamic-parameters) protože jsou definovány během přiřazení podrobného plánu. Pro každý artefakt nastavte hodnotu parametru na co je definováno ve sloupci **Hodnota.** Vyberte `{Your ID}`v aplikaci uživatelský účet Azure.

     |Název artefaktu|Typ artefaktu|Název parametru|Hodnota|Popis|
     |-|-|-|-|-|
     |Skupina prostředků ProdRG|Skupina prostředků|Name (Název)|ProductionRG|Definuje název první skupiny prostředků.|
     |Skupina prostředků ProdRG|Skupina prostředků|Umístění|USA – západ 2|Nastaví umístění první skupiny prostředků.|
     |Přispěvatel|Přiřazení role|Uživatel nebo skupina|{Vaše ID}|Definuje, který uživatel nebo skupina udělí přiřazení role _přispěvatele_ v rámci první skupiny prostředků.|
     |Skupina prostředků PreProdRG|Skupina prostředků|Name (Název)|PreProductionRG|Definuje název druhé skupiny prostředků.|
     |Skupina prostředků PreProdRG|Skupina prostředků|Umístění|USA – západ|Nastaví umístění druhé skupiny prostředků.|
     |Vlastník|Přiřazení role|Uživatel nebo skupina|{Vaše ID}|Definuje, který uživatel nebo skupina udělí přiřazení role _vlastníka_ v rámci druhé skupiny prostředků.|
     |Čtenáři|Přiřazení role|Uživatel nebo skupina|{Vaše ID}|Definuje, který uživatel nebo skupina udělit přiřazení role _Čtenáři_ v rámci druhé skupiny prostředků.|

1. Po zadání všech parametrů **vyberte** Přiřadit v dolní části stránky.

Tento krok nasadí definované prostředky a nakonfiguruje vybrané **přiřazení zámku**. Použití zámků podrobného plánu může trvat až 30 minut.

Jakmile se zobrazí **definice přiřazení podrobného plánu úspěšné** oznámení portálu, přejděte k dalšímu kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Kontrola prostředků nasazených přiřazením

Přiřazení podrobného plánu vytvoří a sleduje artefakty definované v definici podrobného plánu. Můžete zobrazit stav zdrojů ze stránky přiřazení podrobného plánu a při pohledu na zdroje přímo.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Přiřazené podrobné plány** vlevo. Pomocí filtrů vyhledejte přiřazení podrobného plánu _Přiřazení-dva-rgs-s rolí_ a vyberte ho.

   Na této stránce můžeme vidět přiřazení úspěšné a seznam vytvořených prostředků spolu s jejich stav zámku podrobného plánu. Pokud je přiřazení aktualizováno, rozevírací seznam **Operace přiřazení** zobrazuje podrobnosti o nasazení každé verze definice. Na každý prostředek uvedený v seznamu lze klepnout a otevře stránku vlastností prostředků.

1. Vyberte skupinu prostředků **ProductionRG.**

   Vidíme, že název skupiny prostředků je **ProductionRG** a nikoli název zobrazení artefaktu _ProdRG_. Tento název odpovídá hodnotě nastavené během přiřazení podrobného plánu.

1. V levé části vyberte stránku **ovládacího prvku Přístup (IAM)** a potom na kartě **Přiřazení rolí.**

   Zde vidíme, že vašemu účtu byla udělena role _přispěvatele_ v rozsahu _tohoto prostředku_. Přiřazení _-two-rgs-with-role-assignments_ přiřazení podrobného plánu přiřazení má roli _vlastníka,_ jak byl použit k vytvoření skupiny prostředků. Tato oprávnění se také používají ke správě prostředků s nakonfigurovanými zámky podrobného plánu.

1. Na webu Azure portal drobečková navigace vyberte **Přiřazení-dvě-rgs-s role-přiřazení** pro návrat o jednu stránku a pak vyberte skupinu prostředků **PreProductionRG.**

1. V levé části vyberte stránku **ovládacího prvku Přístup (IAM)** a potom na kartě **Přiřazení rolí.**

   Zde vidíme, že váš účet byl udělen vlastník _a_ _čtenář_ role, a to jak na rozsah _tohoto prostředku_. Přiřazení podrobného plánu má také roli _vlastníka_ jako první skupinu prostředků.

1. Vyberte kartu **Odepřít přiřazení.**

   Přiřazení podrobného plánu vytvořilo [přiřazení odepření](../../../role-based-access-control/deny-assignments.md) v nasazené skupině prostředků k vynucení režimu uzamčení podrobného plánu _jen pro čtení._ Odepřít přiřazení zabrání uživateli s příslušnými _právy_ na kartě Přiřazení rolí v přijetí konkrétních akcí. Přiřazení odepřít ovlivňuje _všechny objekty zabezpečení_.

1. Vyberte odepřít přiřazení a potom vyberte stránku **Odepřená oprávnění** vlevo.

   Přiřazení odepřít brání všechny operace **\*** s a **akce** konfigurace, ale umožňuje přístup pro čtení vyloučením ** \*/read** via **NotActions**.

1. Na webu Azure portal s popisem cesty vyberte **PreProductionRG – řízení přístupu (IAM).** Pak vyberte stránku **Přehled** vlevo a potom tlačítko **Odstranit skupinu prostředků.** Zadejte název _PreProductionRG,_ chcete-li potvrdit odstranění, a v dolní části podokna vyberte **Odstranit.**

   Zobrazí se oznámení **portálu Odstranit skupinu prostředků PreProductionRG se nezdařilo.** Chyba uvádí, že zatímco váš účet má oprávnění k odstranění skupiny prostředků, přístup je odepřen přiřazení podrobného plánu. Nezapomeňte, že jsme během přiřazení podrobného plánu vybrali režim uzamčení podrobného plánu _jen pro čtení._ Zámek podrobného plánu zabrání účtu s oprávněním, a to i _Vlastník_, odstranění prostředku. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

Tyto kroky ukazují, že naše prostředky byly vytvořeny podle definice a podrobné ho zamykání zabránit nechtěnému odstranění, a to i z účtu s oprávněním.

## <a name="unassign-the-blueprint"></a>Zrušení přiřazení podrobného plánu

Posledním krokem je odebrání přiřazení podrobného plánu a prostředků, které nasadil.
Odebrání přiřazení neodebere nasazené artefakty.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Přiřazené podrobné plány** vlevo. Pomocí filtrů vyhledejte přiřazení podrobného plánu _Přiřazení-dva-rgs-s rolí_ a vyberte ho.

1. V horní části stránky vyberte tlačítko **Zrušit přiřazení podrobného** plánu. Přečtěte si upozornění v potvrzovacím dialogovém okně a pak vyberte **OK**.

   S přiřazení podrobného plánu odebrány, jsou odebrány také zámky podrobného plánu. Vytvořené prostředky lze opět odstranit pomocí účtu s oprávněními.

1. V nabídce Azure vyberte **skupiny prostředků** a pak vyberte **ProductionRG**.

1. V levé části vyberte stránku **ovládacího prvku Přístup (IAM)** a potom na kartě **Přiřazení rolí.**

Zabezpečení pro každou skupinu prostředků stále má nasazené přiřazení rolí, ale přiřazení podrobného plánu již nemá přístup _vlastníka._

Jakmile se zobrazí **oznámení o odebrání podrobného plánu,** přejděte k dalšímu kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu odstraňte následující zdroje:

- Skupina prostředků _ProductionRG_
- Skupina prostředků _PreProductionRG_
- Definice podrobného plánu _dva rgs-with-role-assignments_

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit nový podrobný plán z ukázkové definice. Další informace o Azure Blueprints, pokračujte v článku životního cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](../concepts/lifecycle.md)