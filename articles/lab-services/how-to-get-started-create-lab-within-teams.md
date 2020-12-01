---
title: Začínáme a vytvoření testovacího prostředí Azure Lab Services v rámci týmů
description: Naučte se, jak začít a vytvořit Azure Lab Services testovací prostředí v rámci týmů.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433967"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Začínáme a vytvoření testovacího prostředí testovacích služeb v rámci týmů

Tento článek ukazuje, jak přidat aplikaci **Azure Lab Services** do týmu a jak vytvořit testovací prostředí v prostředí MS Teams.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu nastavíte testovací prostředí s virtuálními počítači pro váš tým. Pokud chcete nastavit testovací prostředí v účtu testovacího prostředí, musíte být členem jedné z těchto rolí v účtu testovacího prostředí: vlastník, autor testovacího prostředí nebo Přispěvatel. Účet, který jste použili k vytvoření účtu testovacího prostředí, je automaticky přidán do role vlastníka. K vytvoření testovacího prostředí můžete použít uživatelský účet, který jste použili k vytvoření účtu testovacího prostředí.

Toto je typický pracovní postup při použití Azure Lab Services v rámci týmů.

1. Uživatel [vytvoří účet testovacího prostředí](tutorial-setup-lab-account.md#create-a-lab-account) na Azure Portal.
1. [Tvůrce účtu testovacího prostředí přidá další uživatele](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) do role **testovacího prostředí** . Například autor nebo správce účtu testovacího prostředí přidává pedagogy do role **Tvůrce testovacího prostředí** , aby mohli vytvářet Labs pro své třídy.
1. Pak pedagogy vytvoří Labs, předem nakonfigurují šablonu virtuálního počítače a publikují testovací prostředí, aby se virtuální počítač zřídil všem členům týmu.
1. Po publikování testovacího prostředí se virtuální počítač přiřadí všem uživatelům v seznamu členství v týmu při jejich prvním Azure Lab Services přihlášení, a to kliknutím na kartu obsahující **Azure Lab Services** aplikace v rámci týmů (SSO) nebo přístupem na [Web Labs](https://labs.azure.com). Uživatelé pak můžou použít virtuální počítač k tomu, aby mohli pracovat s pracovními a nedokončenými úkoly.

> [!IMPORTANT]
> Azure Lab Services lze v rámci týmů použít pouze v případě, že účty testovacího prostředí jsou vytvořeny ve stejném tenantovi jako týmy.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Přidání aplikace Azure Lab Services jako karty týmu

Jako vlastník týmu můžete přidat **Azure Lab Services** aplikaci přímo do kanálů týmů a pak je aplikace k dispozici pro všechny v týmu, aby mohli používat. Postupujte podle následujících tří kroků:

1. Přejděte na kanál týmy, kam chcete aplikaci přidat, a vyberte možnost **+** Přidat kartu. 
1. V možnostech karty vyhledejte **Azure Lab Services** a přidejte tuto aplikaci. 

    > [!NOTE]
    > Pouze **vlastníci** týmu budou moci vytvářet cvičení pro tým.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Přidat kartu":::
1. Vyberte účet služby testovacího prostředí, který chcete použít pro vytváření cvičení v tomto týmu. 

    Azure Lab Services používá jednotné přihlašování do [Azure Lab Services webu](https://labs.azure.com) a vyžádá si všechny účty testovacího prostředí, ke kterým máte přístup. 

    Zobrazí se účty, které jsou ve stejném tenantovi jako týmy a pro které máte oprávnění **vlastníka**, **přispěvatele** nebo **Tvůrce** . 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Vítá vás funkce ALS":::
1. Stiskněte **Uložit** a karta se přidá do kanálu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="Byla vytvořena karta ALS":::

    Nyní můžete z kanálu vybrat kartu **Azure Lab Services** a začít spravovat laboratoře, jak je popsáno v následujících článcích.

Po výběru účtu testovacího prostředí budou moci vlastníci týmu vytvářet cvičení pro tým. Celý proces vytvoření testovacího prostředí a všechny úlohy na úrovni testovacího prostředí je možné provádět v rámci týmů. Uživatelé budou mít možnost vytvořit několik testovacích prostředí v rámci stejného týmu a vlastníka týmu, přičemž příslušný přístup na úrovni účtu testovacího prostředí uvidí jenom cvičení spojená s konkrétním týmem.

## <a name="next-steps"></a>Další kroky

Při vytvoření testovacího prostředí v rámci týmů se seznam uživatelů testovacího prostředí automaticky vyplní a synchronizuje s členstvím v týmu. Všichni členové týmu, včetně vlastníků, členů a hostů budou automaticky přidáni do seznamu uživatelů testovacího prostředí. Služba Azure Lab Services bude udržovat synchronizaci se členstvím v týmu a Automatická synchronizace se spustí každých 24 hodin. Podrobnosti najdete tady:

[Správa seznamů uživatelů testovacích služeb v rámci týmů](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Viz také

Viz také následující články:

- [Přehled použití Azure Lab Services v rámci týmů](lab-services-within-teams-overview.md)
- [Správa fondu virtuálních počítačů v testovacím prostředí v rámci týmů](how-to-manage-vm-pool-within-teams.md)
- [Vytváření a Správa plánů testovacího prostředí v rámci týmů](how-to-create-schedules-within-teams.md)
- [Přístup k virtuálnímu počítači v rámci týmů – zobrazení studenta](how-to-access-vm-for-students-within-teams.md)
- [Odstranění cvičení v rámci týmů](how-to-delete-lab-within-teams.md)
