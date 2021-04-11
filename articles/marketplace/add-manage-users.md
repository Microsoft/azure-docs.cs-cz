---
title: Přidání a Správa uživatelů pro komerční Marketplace – Azure Marketplace
description: Naučte se spravovat uživatele v programu komerčního tržiště pro účet Microsoft Commercial Marketplace v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108168"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Přidání a Správa uživatelů pro komerční tržiště

**Příslušné role**

- Vlastník
- Manažer

Část **Uživatelé** partnerského centra (v části **Nastavení účtu**) vám umožní spravovat uživatele, skupiny a aplikace Azure AD, které mají přístup k vašemu účtu partnerského centra, pomocí Azure AD. Váš účet musí mít oprávnění na úrovni manažera pro [pracovní účet (tenanta Azure AD)](company-work-accounts.md) , ve kterém chcete přidat nebo upravit uživatele. Pokud chcete spravovat uživatele v rámci jiného pracovního účtu nebo tenanta, budete se muset odhlásit a pak znovu přihlásit jako uživatel s oprávněním **správce** pro tento pracovní účet nebo tenanta.

Až budete přihlášeni pomocí svého pracovního účtu (tenant Azure AD), můžete přidat a spravovat uživatele.

## <a name="add-existing-users"></a>Přidat existující uživatele

Pokud chcete přidat uživatele k vašemu účtu partnerského centra, který už existuje v [pracovním účtu vaší společnosti (tenant Azure AD)](company-work-accounts.md):

1. Pokračujte na **Uživatelé** (v části **Nastavení účtu**) a vyberte **Přidat uživatele**.
1. Vyberte jednoho nebo více uživatelů ze seznamu, který se zobrazí. Pomocí vyhledávacího pole můžete vyhledat konkrétní uživatele. * Pokud vyberete více než jednoho uživatele, který chcete přidat do svého účtu partnerského centra, musíte jim přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více uživatelů s různými rolemi a oprávněními, opakujte tento postup pro každou roli nebo sadu vlastních oprávnění.
1. Až budete s volbou uživatelé hotovi, vyberte **Přidat vybrané**.
1. V části **role** určete role nebo přizpůsobená oprávnění pro vybrané uživatele.
1. Vyberte **Uložit**.

## <a name="create-new-users"></a>Vytvoření nových uživatelů

Chcete-li vytvořit nové uživatelské účty, musíte mít účet s oprávněními [globálního správce](/azure/active-directory/roles/permissions-reference) .

1. V nabídce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat uživatele** a pak zvolte **vytvořit nové uživatele**.
1. Zadejte jméno, příjmení a uživatelské jméno pro každého nového uživatele.
1. Pokud chcete, aby měl nový uživatel účet globálního správce v adresáři vaší organizace, zaškrtněte políčko Označit **tohoto uživatele jako globální správce ve službě Azure AD s úplnou kontrolou nad všemi prostředky adresáře**. Uživatel tak bude mít úplný přístup ke všem funkcím pro správu v Azure AD vaší společnosti. Budou moct přidávat a spravovat uživatele v pracovním účtu vaší organizace (tenant Azure AD), ale ne v partnerském centru, pokud neudělíte účtu příslušnou roli nebo oprávnění.
1. Pokud jste zaškrtli políčko, pokud chcete, aby **Tento uživatel byl globálním správcem**, budete muset zadat *E-mail pro obnovení hesla* , aby uživatel mohl v případě potřeby obnovit heslo.
1. V části **členství ve skupině** vyberte všechny skupiny, do kterých má nový uživatel patřit.
1. V části **role** určete role nebo přizpůsobená oprávnění pro uživatele.
1. Vyberte **Uložit**.

Vytvořením nového uživatele v partnerském centru se taky vytvoří účet pro tohoto uživatele v pracovním účtu (tenant Azure AD), ke kterému jste přihlášení. Změna jména uživatele v partnerském centru provede stejné změny v pracovním účtu vaší organizace (tenant Azure AD).

## <a name="invite-new-users-by-email"></a>Pozvat nové uživatele e-mailem

Pokud chcete pozvat uživatele, kteří nejsou aktuálně součástí pracovního účtu vaší společnosti (tenant Azure AD) prostřednictvím e-mailu, musíte mít účet s oprávněními [globálního správce](/azure/active-directory/roles/permissions-reference) .

1. Přejít na **uživatele** (v části **Nastavení účtu**) vyberte **Přidat uživatele** a pak zvolte **pozvat uživatele e-mailem**.
1. Zadejte jednu nebo více e-mailových adres (až 10), které jsou odděleny čárkami nebo středníkem.
1. V části **role** určete role nebo přizpůsobená oprávnění pro uživatele.
1. Vyberte **Uložit**.

Uživatelé, které jste pozvali, obdrží e-mailovou pozvánku k připojení k vašemu účtu partnerského centra. V pracovním účtu (tenant Azure AD) se vytvoří nový účet uživatele Guest. Každý uživatel bude muset přijmout svou pozvánku, aby mohli získat přístup k vašemu účtu.

Pokud potřebujete znovu odeslat pozvánku, navštivte stránku *Uživatelé* , vyhledejte pozvánku v seznamu uživatelů, vyberte jejich e-mailovou adresu (nebo text, který říká *pozvání čeká na vyřízení*). Potom v dolní části stránky vyberte znovu **Odeslat pozvánku**.

Pokud vaše organizace používá [integraci adresářů](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) k synchronizaci místní adresářové služby s vaší službou Azure AD, nebudete moct vytvářet nové uživatele, skupiny nebo aplikace Azure AD v partnerském centru. Vy (nebo jiný správce v místním adresáři) ho budete muset vytvořit přímo v místním adresáři, abyste je mohli zobrazit a přidat v partnerském centru.

## <a name="remove-a-user"></a>Odebrání uživatele

Pokud chcete odebrat uživatele z pracovního účtu (tenant Azure AD), pokračujte na **Uživatelé** (v části **Nastavení účtu**), vyberte uživatele, kterého chcete odebrat, pomocí zaškrtávacího políčka ve sloupci úplně vpravo a pak z dostupných akcí zvolte **Odebrat** . Zobrazí se automaticky otevírané okno s potvrzením, že chcete vybrané uživatele odebrat.

## <a name="change-a-user-password"></a>Změna hesla uživatele

Pokud některý z uživatelů potřebuje změnit heslo, může to udělat sami, pokud jste při vytváření uživatelského účtu zadali *e-mail pro obnovení hesla* . Pomocí následujících kroků můžete také aktualizovat heslo uživatele. Pokud chcete změnit heslo uživatele v pracovním účtu vaší společnosti (tenant Azure AD), musíte být přihlášeni pomocí účtu s oprávněními [globálního správce](/azure/active-directory/roles/permissions-reference) . Tím se změní heslo uživatele v tenantovi Azure AD spolu s heslem, které používají pro přístup k partnerskému centru.

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte název uživatelského účtu, který chcete upravit.
1. V dolní části stránky vyberte tlačítko **resetovat heslo** .
1. Zobrazí se stránka s potvrzením, na které se zobrazí přihlašovací informace pro uživatele, včetně dočasného hesla. Nezapomeňte vytisknout nebo zkopírovat tyto informace a poskytnout ji uživateli, protože po opuštění této stránky nebudete mít přístup k dočasnému heslu.
