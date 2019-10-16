---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: aba3dd34383edbfb555adcc2063e4a2b76af1959
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389388"
---
## <a name="for-users-in-your-directory"></a>Pro uživatele ve vašem adresáři

Postupujte podle těchto kroků, pokud chcete, aby uživatelé ve vašem adresáři mohli požadovat tento balíček přístupu. Při definování zásad žádosti můžete zadat jednotlivé uživatele nebo skupiny uživatelů, kteří jsou často. Vaše organizace například už může mít skupinu, jako je například **Všichni zaměstnanci**.  Pokud se tato skupina přidá v zásadách pro uživatele, kteří můžou požádat o přístup, pak může každý člen této skupiny požádat o přístup.

1. V seznamu **Uživatelé, kteří můžou vyžádat přístup** vyberte **pro uživatele v adresáři**.

    Když vyberete tuto možnost, zobrazí se nové možnosti, které vám pomůžou lépe upřesnit, kdo v adresáři může tento přístupový balíček požádat.

    ![Přístup k balíčku – požadavky – pro uživatele ve vašem adresáři](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  |  |
    | --- | --- |
    | **Konkrétní uživatelé a skupiny** | Tuto možnost vyberte, pokud chcete, aby se tento balíček přístupu mohl vyžádat jenom pro uživatele a skupiny v adresáři, který jste zadali. |
    | **Všichni členové (kromě hostů)** | Tuto možnost vyberte, pokud chcete, aby všichni členové členských uživatelů v adresáři mohli požádat o tento balíček přístupu. Tato možnost nezahrnuje žádné uživatele typu Host, které jste mohli pozvat do adresáře. |
    | **Všichni uživatelé (včetně hostů)** | Tuto možnost vyberte, pokud chcete, aby se tento balíček přístupu mohl vyžádat všichni členové a uživatelé typu Host v adresáři. |

    Uživatelé typu Host odkazují na externí uživatele, kteří byli pozváni do vašeho adresáře pomocí [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Informace o rozdílech mezi členskými uživateli a uživateli typu Host najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Pokud jste vybrali **konkrétní uživatele a skupiny**, klikněte na **Přidat uživatele a skupiny**.

1. V podokně vybrat uživatele a skupiny vyberte uživatele a skupiny, které chcete přidat.

    ![Přístup k balíčku – požadavky – výběr uživatelů a skupin](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klikněte na **Vybrat** a přidejte uživatele a skupiny.

1. Přeskočte dolů k části [schválení](#approval) .

## <a name="for-users-not-in-your-directory"></a>Pro uživatele, kteří nejsou ve vašem adresáři

Postupujte podle těchto kroků, pokud chcete, aby uživatelé, kteří nejsou ve vašem adresáři, mohli získat tento balíček přístupu. Uživatelé, kteří **nejsou ve vašem adresáři** , odkazují na uživatele, kteří jsou v jiné službě nebo adresáři služby Azure AD, a nemusí být do vašeho adresáře pozváni. Je potřeba nakonfigurovat adresáře Azure AD tak, aby umožňovaly pozvánky v **omezeních spolupráce**. Další informace najdete v tématu [Povolení externí spolupráce B2B a Správa toho, kdo může pozvat hosty](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Uživatelský účet hosta se vytvoří pro uživatele, který ještě není ve vašem adresáři, jehož žádost je schválená nebo automaticky schválená. Host bude pozván, ale neobdrží e-mail s pozvánkou. Místo toho obdrží e-mail po doručení jejich přiřazení balíčku přístupu. Ve výchozím nastavení platí, že pokud už uživatel typu host již nemá žádná přiřazení balíčku přístupu, protože jejich poslední přiřazení vypršela nebo byla zrušena, bude tento uživatelský účet hosta zablokován a následně odstraněn. Pokud chcete, aby uživatelé typu Host zůstali ve vašem adresáři po neomezenou dobu, a to i v případě, že nemají žádná přiřazení balíčků přístupu, můžete změnit nastavení pro konfiguraci správy nároků. Další informace o objektu uživatele hosta najdete v tématu [vlastnosti Azure Active Directoryho uživatele spolupráce B2B](../articles/active-directory/b2b/user-properties.md).

1. V seznamu **Uživatelé, kteří můžou vyžádat přístup** vyberte **pro uživatele, kteří nejsou ve vašem adresáři**.

    Když vyberete tuto možnost, zobrazí se nové možnosti.

    ![Přístup k balíčku – požadavky – pro uživatele, kteří nejsou ve vašem adresáři](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  |  |
    | --- | --- |
    | **Konkrétní propojené organizace** | Tuto možnost vyberte, pokud chcete vybrat ze seznamu organizací, které jste přidali jako správce. Všichni uživatelé z vybraných organizací budou moct požádat o tento balíček přístupu. |
    | **Všechny připojené organizace** | Tuto možnost vyberte, pokud chcete, aby všichni uživatelé ze všech připojených organizací mohli požádat o tento balíček přístupu. |

    Připojená organizace je externí adresář služby Azure AD nebo doména, se kterou často spolupracujete.

1. Pokud jste vybrali **konkrétní připojené organizace**, klikněte na **Přidat adresáře** a vyberte si ze seznamu připojených organizací, které jste přidali jako správce.

1. Zadejte název domény, ve kterém chcete vyhledat připojenou organizaci s názvem domény.

    ![Přístup k balíčku – požadavky – výběr adresářů](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Pokud organizace, se kterou chcete spolupracovat, není v seznamu, můžete požádat správce, aby ho přidal jako připojenou organizaci. 

1. Po výběru všech připojených organizací klikněte na **Vybrat**.

    > [!NOTE]
    > Všichni uživatelé z vybraných připojených organizací budou moct požádat o tento balíček přístupu. To zahrnuje uživatele ze všech subdomén přidružených k připojeným organizacím.

1. Přeskočte dolů k části [schválení](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Žádné (pouze přímé přiřazení správce)

Postupujte podle těchto kroků, pokud chcete vynechat žádosti o přístup a umožníte správcům, aby k tomuto balíčku přístupu přiřadili přímo konkrétní uživatele. Uživatelé nebudou muset vyžadovat balíček přístupu. Pořád můžete nastavit nastavení životního cyklu, ale neexistují žádná nastavení žádostí.

1. V seznamu **Uživatelé, kteří můžou vyžádat přístup** , vyberte možnost **žádný (pouze přímá přiřazení správců**).

    ![Přístup k balíčku – požadavky – pouze přímá přiřazení správců](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Po vytvoření balíčku pro přístup můžete k balíčku pro přístup přímo přiřadit konkrétní interní a externí uživatele. Pokud zadáte externího uživatele, vytvoří se ve vašem adresáři uživatelský účet hosta. Informace o tom, jak přímo přiřazovat uživatele, najdete v tématu [zobrazení a změna přiřazení balíčku pro přístup](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Přeskočte dolů do oddílu [Povolit žádosti](#enable-requests) .

## <a name="approval"></a>Schválení

V části schvalování určíte, jestli se vyžaduje schválení, když si uživatelé vyžádají tento přístupový balíček. Nastavení schválení fungují následujícím způsobem:

- Pouze jeden z vybraných schvalovatelů nebo záložní schvalovatelé musí žádost schválit. Schválení ze všech schvalovatelů se nevyžaduje.
- Rozhodnutí o schválení vychází z toho, kdo žádost nejprve zkontroluje.

1. Pokud chcete pro žádosti od vybraných uživatelů vyžadovat schválení, nastavte u přepínače **vyžadovat schválení** **hodnotu Ano**. Chcete-li automaticky schvalovat požadavky, nastavte přepínač na **ne**.

    ![Přístup k balíčku – nastavení schválení](./media/active-directory-entitlement-management-request-policy/approval.png)

1. Pokud chcete, aby uživatelé zadali odůvodnění pro přístup k balíčku pro přístup, nastavte u přepínače **vyžadovat změnu zarovnání** na **hodnotu Ano**.

1. Určete, zda požadavek bude vyžadovat jedno nebo více fází schválení. Nastavte, **kolik fází** se má pro jednu fázi přepnout na hodnotu **1** .

1. U schvalovatelů vyberte **manažera jako schvalovatele** nebo **zvolte konkrétní schvalovatele**.

    Správce je určený atributem **správce** v profilu uživatele Azure AD. Další informace najdete v tématu [Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

    ![Azure Active Directory atributu User Profile Manager](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. Pokud jste vybrali manažera jako schvalovatele, klikněte na **Přidat zálohu** a vyberte jednoho nebo více uživatelů nebo skupin ve vašem adresáři jako záložního schvalovatele v případě, že správa nároků nemůže najít správce.

1. Pokud jste vybrali možnost zvolit konkrétní schvalovatele, klikněte na **Přidat schvalovatele** a vyberte jednoho nebo více uživatelů nebo skupin v adresáři, které mají být schvalovatelé.

1. V části **rozhodnutí se musí**zadat počet dní, po které má schvalovatel zkontrolovat požadavek na tento balíček přístupu.

    Pokud žádost není v tomto časovém období schválena, bude automaticky odepřena. Uživatel bude muset odeslat další žádost o přístup k balíčku.

1. Pokud chcete, aby uživatelé měli oprávnění k vyžádání balíčku pro přístup, nastavte **vyžadovat odůvodnění** na **Ano**.

    Odůvodnění se zobrazí ostatním schvalovatelům a žadateli.

## <a name="enable-requests"></a>Povolit žádosti

1. Pokud chcete, aby byl balíček přístupu hned dostupný pro uživatele v zásadách žádosti, klikněte na **Ano** , aby se aktivovala žádost.

    Po dokončení vytváření balíčku pro přístup ho můžete v budoucnu kdykoli povolit.

    Pokud jste vybrali **možnost žádné (pouze oprávnění správce)** a nastavíte možnost povolit na **ne**, správci nebudou moci přiřadit tento balíček přístupu přímo.

    ![Přístup k balíčku – nastavení zásad – Povolit zásadu](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klikněte na **Další**.
