---
title: Vytvoření prostředí z ukázky podrobného plánu
description: Vytvořit definici podrobného plánu, který vytvoří dvě skupiny prostředků a nakonfiguruje pro jednotlivé přiřazení role pomocí vzorového podrobného plánu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b0d5d96ff897ac1710206eb49bca785e8809cb7d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65758857"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Kurz: Vytvoření prostředí z ukázky podrobného plánu

Ukázková plány poskytnout příklady, co se dá dělat pomocí Azure podrobné plány. Každá je tady je příklad s specifické cílem nebo účely, ale nepodporuje vytvoření kompletního prostředí samy o sobě. Každá je určena jako výchozí bod k prozkoumání Azure plány pomocí různých kombinací zahrnuté artefakty, návrhy a parametry.

V následujícím kurzu se používá **skupin prostředků pomocí RBAC** ukázka podrobný plán k prezentaci různé aspekty plány služby. Jsou zahrnuty následující kroky:

> [!div class="checklist"]
> - Vytvořte novou definici podrobného plánu z ukázky
> - Označte si kopii ukázky jako **publikováno**
> - Přiřazení kopie podrobný plán k existujícímu předplatnému
> - Kontrola přiřazení nasazených prostředků
> - Zrušit přiřazení podrobného plánu se odebrat zámky.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, je potřeba předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-blueprint-definition-from-sample"></a>Vytvořit definici podrobného plánu z ukázky

První implementaci vzorku podrobného plánu. Import vytvoří nový plán ve vašem prostředí založené na vzorku.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Z **Začínáme** stránky na levé straně, vyberte **vytvořit** tlačítko _vytvořit podrobný plán_.

1. Najít **skupin prostředků pomocí RBAC** podrobného plánu ukázky v části _Další ukázky_ a vyberte **fungování této ukázky**.

1. Zadejte _Základy_ vzorku podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky podrobného plánu. V tomto kurzu použijeme název _dvě rgs s--přiřazení rolí_.
   - **Umístění definice**: Použít na tři tečky a vyberte skupiny pro správu nebo předplatného, uložte si kopii vzorku.

1. Vyberte _artefakty_ kartě v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Projděte si seznam artefakty, které tvoří vzorku podrobného plánu. Tento příklad definuje dvě skupiny prostředků s názvy zobrazení _ProdRG_ a _PreProdRG_. Poslední název a umístění každé skupině prostředků se nastavují při přiřazení podrobného plánu. _ProdRG_ přiřazené skupiny prostředků _Přispěvatel_ role a _PreProdRG_ přiřazené skupiny prostředků _vlastníka_ a  _Čtenáři_ role. Přiřazené v definici role jsou statické, ale je během přiřazení podrobného plánu nastavit uživatele, aplikace nebo skupina, která je přiřazena role.

1. Vyberte **uložit koncept** až dokončíte revize vzorku podrobného plánu.

Tento krok vytvoří kopii ukázková definice podrobného plánu v vybraná skupina pro správu nebo předplatného. Definice podrobného plánu uložené se spravuje stejně jako jakékoli podrobného plánu vytvářet úplně od začátku. Ukázku můžete uložit do vaší skupiny pro správu nebo předplatného tolikrát, kolikrát podle potřeby. Každá kopie však třeba zadat jedinečný název.

Jakmile **ukládá definice podrobného plánu se úspěšně** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopírování

Vaši kopii ukázky podrobného plánu byla vytvořena ve vašem prostředí. Vytvoří se v **koncept** režimu a musí být **publikováno** předtím, než může být přiřazena a nasadit. Kopii ukázky podrobný plán lze přizpůsobit, aby vaše prostředí a požadavky. Pro účely tohoto kurzu jsme neprovede žádné změny.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru k vyhledání _dvě rgs s--přiřazení rolí_ podrobný plán definice a potom ho vyberte.

1. Vyberte **publikovat podrobný plán** v horní části stránky. V novém podokně na pravé straně zadejte **verze** jako _1.0_ vaši kopii ukázky podrobného plánu. Tato vlastnost je užitečná pro, pokud provedete změny později. Zadejte **změnit poznámky** jako "první verze publikovat skupiny prostředků s ukázkou podrobného plánu RBAC." Potom vyberte **publikovat** v dolní části stránky.

Tento krok umožňuje přiřadit podrobný plán k předplatnému. Po publikování se můžete stále provádět změny. Další změny, které vyžadují publikování s novou **verze** hodnotu ke sledování rozdíly mezi různé verze stejné definice podrobného plánu.

Jednou **publikování podrobný plán definici úspěšné** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="assign-the-sample-copy"></a>Přiřazení kopie vzorku

Jakmile kopii ukázky podrobný plán byl úspěšně **publikováno**, je možné přiřadit na předplatné v rámci se uložila do skupiny pro správu. Tento krok je, kde jsou k dispozici parametry aby každého nasazení kopii ukázky podrobného plánu jedinečný.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru k vyhledání _dvě rgs s--přiřazení rolí_ podrobný plán definice a potom ho vyberte.

1. Vyberte **přiřazení podrobného plánu** v horní části stránky definice podrobného plánu.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná:** Vyberte jednu nebo několik předplatných, které jsou ve skupině pro správu můžete uložit kopii ukázku podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: Název je předem vyplněna můžete vycházet z názvu definice podrobného plánu.
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořen v. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Pro účely tohoto kurzu vyberte _USA – východ 2_.
     - **Verze definice podrobného plánu**: Vyberte si **publikováno** verze _1.0_ kopie ukázková definice podrobného plánu.

   - Uzamknout přiřazení

     Vyberte _jen pro čtení_ režim zámku podrobného plánu. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí nastavení _přiřazenou systémem_ možnost. Další informace najdete v tématu [spravovaných identit](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, pod kterým je definována. Tyto parametry jsou [dynamických parametrů](../concepts/parameters.md#dynamic-parameters) protože máte definovaný během přiřazování podrobný plán. Pro každý artefakt, nastavte hodnotu parametru definovaný v **hodnotu** sloupce. Pro `{Your ID}`, vyberte váš uživatelský účet Azure.

     |Název artefaktu|Typ artefaktu|Název parametru|Hodnota|Popis|
     |-|-|-|-|-|
     |Skupina prostředků ProdRG|Skupina prostředků|Název|ProductionRG|Definuje název první skupinu prostředků.|
     |Skupina prostředků ProdRG|Skupina prostředků|Location|Západní USA 2|Nastaví umístění první skupinu prostředků.|
     |Přispěvatel|Přiřazení role|Uživatel nebo skupina|{ID}|Definuje, které uživateli nebo skupině udělit _Přispěvatel_ přiřazení rolí v rámci první skupinu prostředků.|
     |Skupina prostředků PreProdRG|Skupina prostředků|Název|PreProductionRG|Definuje název skupiny prostředků, druhém.|
     |Skupina prostředků PreProdRG|Skupina prostředků|Location|Západní USA|Nastaví umístění, z druhé skupiny prostředků.|
     |Vlastník|Přiřazení role|Uživatel nebo skupina|{ID}|Definuje, které uživateli nebo skupině udělit _vlastníka_ přiřazení role v rámci druhé skupiny prostředků.|
     |Čtenáři|Přiřazení role|Uživatel nebo skupina|{ID}|Definuje, které uživateli nebo skupině udělit _čtenáři_ přiřazení role v rámci druhé skupiny prostředků.|

1. Po zadání všech parametrů se vyberte **přiřadit** v dolní části stránky.

Tento krok definované prostředky nasadí a nakonfiguruje vybraného **zámek přiřazení**. Zámky podrobného plánu může trvat až 30 minut použít.

Jednou **přiřazení definice podrobného plánu se úspěšně** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Zkontrolovat prostředky nasazené podle přiřazení

Přiřazení podrobného plánu se vytvoří a sleduje artefakty definované v definici podrobného plánu. Můžeme vidět stav prostředků ze stránky přiřazení podrobného plánu a prohlédněte prostředky přímo.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **přiřazené podrobné plány** na levé straně stránky. Pomocí filtru k vyhledání _Assignment-two-rgs-with-role-assignments_ podrobný plán přiřazení a pak ho vyberte.

   Na této stránce můžeme vidět seznam vytvořených prostředků spolu s jejich stav zámku podrobného plánu a přiřazení proběhlo úspěšně. Pokud aktualizaci přiřazení **operace přiřazení** rozevíracím seznamu zobrazí podrobnosti o nasazení každé definici verze. Každý uvedené prostředek, který byl vytvořen dá kliknout a otevře se stránka vlastností této zdrojů.

1. Vyberte **ProductionRG** skupinu prostředků.

   Vidíme, že je název skupiny prostředků **ProductionRG** a nikoli zobrazovaný název artefaktu _ProdRG_. Tento název odpovídá hodnotě nastavit během přiřazení podrobného plánu.

1. Vyberte **řízení přístupu (IAM)** stránky na levé straně a pak **přiřazení rolí** kartu.

   Tady vidíme, že bylo uděleno účtu _Přispěvatel_ role na rozsahu _tento prostředek_. _Assignment-two-rgs-with-role-assignments_ se přiřazení podrobného plánu _vlastníka_ role, protože byla použita k vytvoření skupiny prostředků. Tato oprávnění se také používají ke správě prostředků pomocí zámků nakonfigurované podrobného plánu.

1. Azure portal navigace s popisem cesty, vyberte **Assignment-two-rgs-with-role-assignments** vrátit jednu stránku, vyberte **PreProductionRG** skupinu prostředků.

1. Vyberte **řízení přístupu (IAM)** stránky na levé straně a pak **přiřazení rolí** kartu.

   Tady vidíme, že váš účet bylo uděleno i _vlastníka_ a _čtečky_ role, na rozsahu _tento prostředek_. Přiřazení podrobného plánu se má také _vlastníka_ role jako první skupinu prostředků.

1. Vyberte **zamítnout přiřazení** kartu.

   Přiřazení podrobného plánu, který je vytvořen [zamítnout přiřazení](../../../role-based-access-control/deny-assignments.md) nasazený prostředek skupiny k vynucení _jen pro čtení_ režim zámku podrobného plánu. Někdo s příslušná oprávnění přiřazení odepřít brání na _přiřazení rolí_ kartu z konkrétní akci. Přiřazení odepřít ovlivňuje _všechny objekty zabezpečení_.

1. Vyberte přiřazení Odepřít a potom **odepření oprávnění** na levé straně stránky.

   Přiřazení odepřít brání všechny operace s **\*** a **akce** konfigurace, ale umožňuje přístup pro čtení vyloučením  **\* /čtení**prostřednictvím **NotActions**.

1. Azure portal navigace s popisem cesty, vyberte **PreProductionRG – řízení přístupu (IAM)**. Vyberte **přehled** stránky na levé straně a pak **odstranit skupinu prostředků** tlačítko. Zadejte název _PreProductionRG_ potvrďte odstranění a vyberte **odstranit** v dolní části podokna.

   Na portálu oznámení **odstranit skupinu prostředků se nepovedlo PreProductionRG** se zobrazí. Chyba, která uvádí, zatímco váš účet má oprávnění k odstranění skupiny prostředků, přístup se zamítl podle přiřazení podrobného plánu. Mějte na paměti, že jsme vybrali _jen pro čtení_ režim zámku podrobného plánu během přiřazení podrobného plánu. Podrobný plán zámek je chrání účtu s oprávněním, dokonce i _vlastníka_, z příslušný prostředek odstraníte. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

Tyto kroky ukazují, že naše prostředky se vytvořily, jak jsou definovány a podrobného plánu zámky brání nežádoucí odstranění, dokonce i z účtu s oprávněním.

## <a name="unassign-the-blueprint"></a>Zrušit přiřazení podrobný plán

Posledním krokem je odebrat přiřazení podrobný plán a prostředky, které se nasadit.
Odebrání přiřazení neodebere nasazené artefakty.

1. Vyberte **všechny služby** v levém podokně. Vyhledejte a vyberte **plány**.

1. Vyberte **přiřazené podrobné plány** na levé straně stránky. Pomocí filtru k vyhledání _Assignment-two-rgs-with-role-assignments_ podrobný plán přiřazení a pak ho vyberte.

1. Vyberte **zrušit přiřazení podrobného plánu** tlačítko v horní části stránky. Přečtěte si upozornění v dialogovém okně potvrzení a pak vyberte **OK**.

   Přiřazení podrobného plánu odebrat se taky odeberou zámky podrobného plánu. Vytvořené prostředky můžete odstranit znovu pomocí účtu s oprávněními.

1. Vyberte **skupiny prostředků** z nabídky Azure vyberte **ProductionRG**.

1. Vyberte **řízení přístupu (IAM)** stránky na levé straně a pak **přiřazení rolí** kartu.

Zabezpečení pro každé skupiny prostředků má stále přiřazení nasazené rolí, ale už se přiřazení podrobného plánu _vlastníka_ přístup.

Jakmile **přiřazení podrobného plánu odebrání proběhlo úspěšně.** portálu oznámení se zobrazí, přejít k dalšímu kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu, odstraňte následující prostředky:

- Skupina prostředků _ProductionRG_
- Skupina prostředků _PreProductionRG_
- Podrobný plán definice _dvě rgs s--přiřazení rolí_

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Zjistěte, jak používat [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)