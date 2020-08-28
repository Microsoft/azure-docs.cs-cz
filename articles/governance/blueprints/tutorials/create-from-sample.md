---
title: 'Kurz: Ukázka podrobného plánu do nového prostředí'
description: V tomto kurzu použijete ukázku podrobného plánu k vytvoření definice podrobného plánu, která nastaví dvě skupiny prostředků a nakonfiguruje přiřazení role pro každou z nich.
ms.date: 08/27/2020
ms.topic: tutorial
ms.openlocfilehash: 891240dcafe0cbfbfab4ff1fd415cba4abf682a5
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048728"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Kurz: vytvoření prostředí z ukázky podrobného plánu

Ukázkové modrotisky obsahují příklady toho, co můžete udělat pomocí Azure modrotisky. Každá z nich je ukázkou s konkrétním záměrem nebo účelem, ale nevytváří celé prostředí sami. Každá z nich je určena jako počáteční místo pro zkoumání pomocí Azure modrotisky s různými kombinacemi zahrnutých artefaktů, návrhů a parametrů.

V následujícím kurzu se k předvedení různých aspektů služby Azure modrotisky používá ukázka **skupiny prostředků s nástrojem RBAC** . plán. Postup je popsaný v následujících krocích:

> [!div class="checklist"]
> - Vytvořit novou definici podrobného plánu z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému
> - Kontrola nasazených prostředků pro přiřazení
> - Zrušení přiřazení podrobného plánu k odebrání zámků

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-blueprint-definition-from-sample"></a>Vytvořit definici podrobného plánu z ukázky

Nejdřív implementujte ukázku podrobného plánu. Import vytvoří ve svém prostředí nový podrobný plán založený na ukázce.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte **skupiny prostředků s** ukázkovým plánem RBAC a vyberte je.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázky podrobného plánu. V tomto kurzu použijeme název _dvou-RGS-s přiřazení role_.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu nebo předplatné, do které chcete kopii ukázky Uložit.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **Další: artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Tato ukázka definuje dvě skupiny prostředků s zobrazovanými názvy _ProdRG_ a _PreProdRG_. Konečný název a umístění jednotlivých skupin prostředků se nastaví během přiřazení podrobného plánu. Skupině prostředků _ProdRG_ se přiřadí role _Přispěvatel_ a pro skupinu prostředků _PreProdRG_ se přiřazují role _vlastník_ a _čtenáři_ . Role přiřazené v definici jsou statické, ale uživatel, aplikace nebo skupina, které mají přiřazenou roli, se nastaví během přiřazení podrobného plánu.

1. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

Tento krok vytvoří kopii vzorové definice podrobného plánu ve vybrané skupině nebo předplatném pro správu. Uložená definice podrobného plánu se spravuje stejně jako každý podrobný plán vytvořený od začátku. Ukázku můžete uložit do vaší skupiny pro správu nebo předplatného tolikrát, kolikrát je potřeba. Pro každou kopii je však nutné zadat jedinečný název.

Po úspěšném zobrazení oznámení na portálu pro **uložení definice** podrobného plánu přejděte k dalšímu kroku.

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit pro vaše prostředí a potřeby. Pro tento kurz nebudeme dělat žádné změny.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů vyhledejte definici podrobného plánu _přiřazení dvou RGS-s-role_ a pak ji vyberte.

1. V horní části stránky vyberte **publikovat podrobný plán** . V pravém podokně na pravé straně zadejte pro kopii ukázky podrobného plánu **verzi** jako _1,0_ . Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , jako je například "první verze publikovaná ze skupin prostředků s ukázkou podrobného plánu RBAC". Potom v dolní části stránky vyberte **publikovat** .

Tento krok umožňuje přiřadit podrobný plán k předplatnému. Po publikování můžou být změny pořád připravené. Další změny vyžadují publikování s novou hodnotou **verze** ke sledování rozdílů mezi různými verzemi stejné definice podrobného plánu.

Po **úspěšném** zobrazení oznámení na portálu definice podrobného plánu publikování přejděte k dalšímu kroku.

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů vyhledejte definici podrobného plánu _přiřazení dvou RGS-s-role_ a pak ji vyberte.

1. V horní části stránky definice podrobného plánu vyberte **přiřadit podrobný plán** .

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu definice podrobného plánu.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Pro tento kurz vyberte _východní USA 2_.
     - **Verze definice**podrobného plánu: vyberte **publikovanou** verzi _1,0_ kopie definice ukázkového podrobného plánu.

   - Zamknout přiřazení

     Vyberte režim zámku _podrobného_ plánu. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí možnost _přiřazenou systému_ . Další informace najdete v tématu [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktu

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Pro každý artefakt nastavte hodnotu parametru na to, co je definováno ve sloupci **hodnota** . V případě `{Your ID}` Vyberte svůj uživatelský účet Azure.

     |Název artefaktu|Typ artefaktu|Název parametru|Hodnota|Popis|
     |-|-|-|-|-|
     |Skupina prostředků ProdRG|Skupina prostředků|Name|ProductionRG|Definuje název první skupiny prostředků.|
     |Skupina prostředků ProdRG|Skupina prostředků|Umístění|Západní USA 2|Nastaví umístění první skupiny prostředků.|
     |Přispěvatel|Přiřazení role|Uživatel nebo skupina|{ID}|Definuje uživatele nebo skupinu, kterým chcete udělit přiřazení role _přispěvatele_ v rámci první skupiny prostředků.|
     |Skupina prostředků PreProdRG|Skupina prostředků|Name|PreProductionRG|Definuje název druhé skupiny prostředků.|
     |Skupina prostředků PreProdRG|Skupina prostředků|Umístění|USA – západ|Nastaví umístění druhé skupiny prostředků.|
     |Vlastník|Přiřazení role|Uživatel nebo skupina|{ID}|Definuje uživatele nebo skupinu, kterým udělíte přiřazení role _vlastníka_ v rámci druhé skupiny prostředků.|
     |Čtenáři|Přiřazení role|Uživatel nebo skupina|{ID}|Definuje uživatele nebo skupinu pro udělení přiřazení role _čtenáři_ v rámci druhé skupiny prostředků.|

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** .

Tento krok nasadí definované prostředky a nakonfiguruje vybrané **přiřazení zámku**. Použití zámků podrobného plánu může trvat až 30 minut.

Jakmile se zobrazí oznámení o **úspěšném přiřazení** na portál definice podrobného plánu, přejděte k dalšímu kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Kontrola prostředků nasazených přiřazením

Přiřazení podrobného plánu vytvoří a sleduje artefakty definované v definici podrobného plánu. Stav prostředků můžeme zobrazit na stránce přiřazení podrobného plánu a prohlížející si prostředky přímo.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **přiřazené plány** . Použijte filtry k vyhledání přiřazení _podrobného plánu přiřazení-Two-RGS-with-role_ a pak ho vyberte.

   Z této stránky vidíte úspěšné přiřazení a seznam vytvořených prostředků spolu s jejich stavem zámku podrobného plánu. Pokud je přiřazení aktualizováno, rozevírací seznam **operace přiřazení** zobrazí podrobnosti o nasazení každé verze definice. Každý uvedený prostředek, který byl vytvořen, lze vybrat a otevře se stránka vlastností prostředky.

1. Vyberte skupinu prostředků **ProductionRG** .

   Ukážeme, že název skupiny prostředků je **ProductionRG** , a ne zobrazovaný název artefaktu _ProdRG_. Tento název se shoduje s hodnotou nastavenou během přiřazení podrobného plánu.

1. Na levé straně vyberte stránku **řízení přístupu (IAM)** a pak kartu **přiřazení rolí** .

   Tady vidíte, že váš účet měl přiřazenou roli _Přispěvatel_ v oboru _tohoto prostředku_. Přiřazení _podrobného plánu přiřazení (dvou-RGS-with-role_ ) má roli _Owner_ , protože se použila k vytvoření skupiny prostředků. Tato oprávnění slouží také ke správě prostředků s konfigurovanými zámky podrobného plánu.

1. Z Azure Portal s popisem cesty vyberte **přiřazení – dvě-RGS-s-role – přiřazení** se vrátí zpět jedna stránka a pak vyberte skupinu prostředků **PreProductionRG** .

1. Na levé straně vyberte stránku **řízení přístupu (IAM)** a pak kartu **přiřazení rolí** .

   Tady vidíte, že váš účet měl v oboru _tohoto prostředku_udělena role _vlastník_ i _Čtenář_ . Přiřazení podrobného plánu má také roli _vlastníka_ jako první skupinu prostředků.

1. Vyberte kartu **Odepřít přiřazení** .

   Přiřazení podrobného plánu vytvořilo [přiřazení odepřít](../../../role-based-access-control/deny-assignments.md) pro nasazenou skupinu prostředků, aby se vynutil režim zámku _podrobného plánu pro čtení_ . Přiřazení zamítnutí brání osobě s odpovídajícími právy na kartě _přiřazení rolí_ v tom, aby převzal konkrétní akce. Přiřazení zamítnutí má vliv na _všechny objekty zabezpečení_.

1. Vyberte přiřazení odepřít a pak na levé straně vyberte stránku **Zamítnutá oprávnění** .

   Přiřazení zamítnutí brání všem operacím s **\*** konfigurací **Akce** a, ale umožňuje přístup pro čtení s výjimkou ** \* /Read** prostřednictvím **NotActions**.

1. Z Azure Portal s popisem cesty vyberte **PreProductionRG-Access Control (IAM)**. Pak na levé straně vyberte stránku **Přehled** a pak klikněte na tlačítko **Odstranit skupinu prostředků** . Zadejte název _PreProductionRG_ a potvrďte odstranění a vyberte **Odstranit** v dolní části podokna.

   Zobrazuje **se skupina prostředků odstranit** oznámení na portálu PreProductionRG. Chyba uvádí, že zatímco váš účet má oprávnění k odstranění skupiny prostředků, přístup je odepřen přiřazením podrobného plánu. Mějte na paměti, že při přiřazení podrobného plánu jsme vybrali režim zámku podrobný plán _pro čtení_ . Zámek podrobného plánu zabraňuje účtu s oprávněním, dokonce i _vlastníkem_, z odstranění prostředku. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

Tyto kroky ukazují, že se naše prostředky vytvořily podle definice a zámky podrobného plánu zabraňují nechtěnému odstranění, a to i z účtu s oprávněním.

## <a name="unassign-the-blueprint"></a>Zrušit přiřazení podrobného plánu

Posledním krokem je odebrání přiřazení podrobného plánu a prostředků, které byly nasazeny.
Odebráním přiřazení nedojde k odebrání nasazených artefaktů.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **přiřazené plány** . Použijte filtry k vyhledání přiřazení _podrobného plánu přiřazení-Two-RGS-with-role_ a pak ho vyberte.

1. V horní části stránky vyberte tlačítko zrušit **přiřazení** podrobného plánu. Přečtěte si upozornění v potvrzovacím dialogovém okně a pak vyberte **OK**.

   Po odebrání přiřazení podrobného plánu jsou také odebrány zámky podrobného plánu. Vytvořené prostředky můžete znovu odstranit pomocí účtu s oprávněními.

1. V nabídce Azure vyberte **skupiny prostředků** a pak vyberte **ProductionRG**.

1. Na levé straně vyberte stránku **řízení přístupu (IAM)** a pak kartu **přiřazení rolí** .

Zabezpečení pro každou skupinu prostředků má stále nasazená přiřazení rolí, ale přiřazení podrobného plánu už nemá přístup _vlastníka_ .

Jakmile se zobrazí oznámení o **úspěšném odebrání přiřazení** podrobného plánu, přejděte k dalšímu kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu odstraňte následující prostředky:

- _ProductionRG_ skupiny prostředků
- _PreProductionRG_ skupiny prostředků
- Definice _podrobného plánu 2 – RGS-s-role-přiřazení_

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit nový podrobný plán z ukázkové definice. Další informace o plánech Azure najdete v článku o životním cyklu podrobného plánu.

> [!div class="nextstepaction"]
> [Další informace o životním cyklu podrobného plánu](../concepts/lifecycle.md)