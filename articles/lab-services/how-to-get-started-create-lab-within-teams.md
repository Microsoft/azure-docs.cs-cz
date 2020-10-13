---
title: Začínáme a vytváření Azure Lab Services testovacího prostředí z týmů
description: Naučte se, jak začít a vytvořit Azure Lab Services testovací prostředí z týmů.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946664"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Začínáme a vytvoření testovacího prostředí testovacích služeb z týmů

Tento článek ukazuje, jak přidat aplikaci Azure Lab Services do týmů. Pak vytvořte testovací prostředí z týmů.

## <a name="add-a-lab-services-app-to-teams"></a>Přidání aplikace testovacích služeb do týmů

Testovací služby můžete přidat přímo do kanálů týmů a pak je aplikace k dispozici pro všechny v týmu, které budou používat. Postupujte podle těchto tří kroků:

1. Přejděte na kanál týmy, kam chcete aplikaci přidat, a výběrem možnosti **+** Přidat kartu kliknutím na "...". v horní části pravého okna. 
1. V možnostech karty vyhledejte **Azure Lab Services** a přidejte tuto aplikaci. 

    > [!NOTE]
    > Pouze **vlastníci** týmu budou moci vytvářet cvičení pro tým.
1. Vyberte účet služby testovacího prostředí, který chcete použít pro vytváření cvičení učebny v tomto týmu. 

    Azure Lab Services používá jednotné přihlašování do [Azure Lab Services webu](https://labs.azure.com) a vyžádá si všechny účty testovacího prostředí, ke kterým máte přístup. 

    Zobrazí se účty, které jsou ve stejném tenantovi jako týmy a pro které máte oprávnění **vlastníka**, **přispěvatele**nebo **Tvůrce** . 

   ![Vítá vás funkce ALS](./media/integrate-with-teams/welcome.png) 
1. Stiskněte **Uložit** a aplikace se přidá do týmů a karta se přidá do kanálu. 

    Nyní můžete z kanálu vybrat kartu **Azure Lab Services** a začít spravovat laboratoře, jak je popsáno v následujícím kroku.

    Jakmile jeden člen týmu přidá kartu, zobrazí se pro všechny v kanálu. Všichni uživatelé, kteří mají přístup k aplikaci, získají přístup s jednotným přihlašováním pomocí přihlašovacích údajů, které používají pro Microsoft Teams. Všichni uživatelé, kteří nemají přístup k aplikaci, mohou zobrazit kartu v týmech, ale budou blokovány, dokud jim neudělíte oprávnění k místní aplikaci a Azure Portal publikované verzi aplikace.

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

Po výběru účtu testovacího prostředí budou moci vlastníci týmu vytvářet cvičení pro tým. Celý proces vytvoření testovacího prostředí a všechny úlohy na úrovni testovacího prostředí je možné provádět v rámci týmů. Uživatelé budou mít možnost vytvořit několik testovacích prostředí v rámci stejného týmu a vlastníka týmu, přičemž příslušný přístup na úrovni účtu testovacího prostředí uvidí jenom cvičení spojená s konkrétním týmem.

## <a name="giving-access-to-users-of-the-lab-account"></a>Poskytnutí přístupu uživatelům účtu testovacího prostředí

Přístup k uživatelům na úrovni účtu testovacího prostředí musí být zajištěn na webu [Azure](https://ms.portal.azure.com/) Portal.

1. V Azure Portal přejděte na účet Azure Lab Services. 
1. Na stránce **účet testovacího prostředí** vyberte **řízení přístupu (IAM)**, vyberte **+ Přidat** na panelu nástrojů a pak na panelu nástrojů vyberte **+ Přidat přiřazení role** . 

    ![Access Control – tlačítko Přidat přiřazení role >](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stránce **Přidat přiřazení role** vyberte možnost **Autor testovacího prostředí** pro **role**, vyberte uživatele, kterého chcete přidat do role testovací tvůrci, a vyberte **Uložit**. 

    ![Přidat autora testovacího prostředí](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Vytváření prostředí učeben Labs

Proces vytváření testovacích cvičení je stejný, ať už vytváříte cvičení z týmů nebo z [webu služby Lab Services](https://labs.azure.com). 

Podrobnosti o nastavení tohoto článku postup tvorby vytváření návrhových laboratoří: [Správa prostředí učebny v Azure Lab Services](how-to-manage-classroom-labs.md).

## <a name="deleting-classroom-labs"></a>Odstraňování testovacích cvičení

Testovací prostředí vytvořené v rámci týmů je možné odstranit na [webu testovacích služeb](https://labs.azure.com) , a to odstraněním testovacího prostředí přímo, jak je popsáno v tématu [Správa učebnních cvičení v Azure Lab Services](how-to-manage-classroom-labs.md). 

Odstranění testovacího prostředí se také aktivuje při odstranění týmu. Pokud se tým, ve kterém testovací prostředí vytváří, odstraní, testovací prostředí se automaticky odstraní 24 hodin po aktivaci automatického seznamu uživatelů. 

Odstraněním karty nebo odinstalováním aplikace nebudete mít za následek odstranění testovacího prostředí. Pokud je karta Odstraněná, uživatelé v seznamu členství v týmu budou mít i nadále přístup k virtuálním počítačům na [webu testovacích služeb](https://labs.azure.com) , pokud se odstranění testovacího prostředí explicitně neaktivuje odstraněním testovacího prostředí na webu nebo odstraněním týmu. 

## <a name="next-steps"></a>Další kroky

Při vytvoření testovacího prostředí v rámci týmů se seznam uživatelů testovacího prostředí automaticky vyplní a synchronizuje s členstvím v týmu. Všichni členové týmu, včetně vlastníků, členů a hostů budou automaticky přidáni do seznamu uživatelů testovacího prostředí. Služba Azure Lab Services bude udržovat synchronizaci se členstvím v týmu a Automatická synchronizace se spustí každých 24 hodin. Podrobnosti najdete tady:

[Správa seznamů uživatelů testovacích služeb z týmů](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Viz také

Viz také následující články:

- [Přehled použití Azure Lab Services v rámci týmů](lab-services-within-teams-overview.md)
- [Správa fondu virtuálních počítačů ve službě Lab Services z týmů](how-to-manage-vm-pool-within-teams.md)
- [Vytváření plánů testovacích služeb z týmů](how-to-create-schedules-within-teams.md)
- [Přístup k virtuálnímu počítači (zobrazení student) ve službě Lab Services z týmů](how-to-access-vm-for-students-within-teams.md)

