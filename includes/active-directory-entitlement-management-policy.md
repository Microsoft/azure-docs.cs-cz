---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 07/31/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: db16a2f122da1bf6c767e0a47c93c22f1882c406
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817207"
---
### <a name="policy-for-users-in-your-directory"></a>Zásady: Pro uživatele ve vašem adresáři

Postupujte podle těchto kroků, pokud chcete, aby byly zásady pro uživatele ve vašem adresáři, které mohou vyžadovat tento balíček přístupu.  **Uživatelé ve vašem adresáři** odkazují jak na interní uživatele, tak i na externí uživatele, kteří byli předtím pozváni do adresáře, a to prostřednictvím nich, kteří požadují správu nároků s jiným balíčkem přístupu nebo pozvaní s Azure AD B2B. Při definování zásady můžete zadat jednotlivé uživatele nebo skupiny uživatelů, kteří jsou často. Vaše organizace například už může mít skupinu, jako je například **Všichni zaměstnanci**.  Pokud se tato skupina přidá v zásadách pro uživatele, kteří můžou požádat o přístup, pak může každý člen této skupiny požádat o přístup.

1. V části **Uživatelé, kteří můžou požádat o přístup** , vyberte **pro uživatele v adresáři**.

    Všimněte si, že nastavení **pro uživatele v adresáři** zahrnuje členské uživatele i uživatele typu Host, kteří byli přidáni do vašeho adresáře. Pokud chcete zahrnout pouze členy uživatelů a nikoli uživatele typu Host, vyberte možnost **pro uživatele v adresáři** a pak vyberte skupinu členů uživatelů. V případě potřeby můžete vytvořit dynamickou skupinu členů členských uživatelů (User. userType-EQ "Member"). Další informace najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](../articles/active-directory/users-groups-roles/groups-dynamic-membership.md).

1. V části **Vybrat uživatele a skupiny** klikněte na **Přidat uživatele a skupiny**.

1. V podokně vybrat uživatele a skupiny vyberte uživatele a skupiny, které chcete přidat.

    ![Přístup k balíčku – zásady – výběr uživatelů a skupin](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Klikněte na **Vybrat** a přidejte uživatele a skupiny.

1. Přeskočit dolů k [zásadě: Oddíl](#policy-request) žádosti.

### <a name="policy-for-users-not-in-your-directory"></a>Zásady: Pro uživatele, kteří nejsou ve vašem adresáři

Postupujte podle těchto kroků, pokud chcete, aby byly zásady pro uživatele, kteří nejsou ve vašem adresáři, který může vyžadovat tento balíček přístupu. Uživatelé, kteří **nejsou ve vašem adresáři** , odkazují na uživatele, kteří jsou v jiném adresáři služby Azure AD, a nemusí být do vašeho adresáře pozváni.  V nastavení **omezení spolupráce organizačních vztahů** se musí adresáře nakonfigurovat tak, aby byly povolené.

> [!NOTE]
> Pro uživatele, který ještě není ve vašem adresáři, jehož požadavek je schválen nebo automaticky schválen, se vytvoří účet externího uživatele hosta. Host bude pozván, ale neobdrží e-mail s pozvánkou. Místo toho obdrží e-mail po doručení jejich přiřazení balíčku přístupu. Ve výchozím nastavení platí, že pokud už uživatel typu host již nemá žádná přiřazení balíčku přístupu, protože jejich poslední přiřazení vypršela nebo byla zrušena, bude tento uživatelský účet hosta zablokován a následně odstraněn. Pokud chcete, aby uživatelé typu Host zůstali ve vašem adresáři po neomezenou dobu, a to i v případě, že nemají žádná přiřazení balíčků přístupu, můžete změnit nastavení pro konfiguraci správy nároků.

1. V části **Uživatelé, kteří můžou požádat o přístup** , vyberte **pro uživatele, kteří nejsou ve vašem adresáři**.

1. V části **vybrat externí adresář služby Azure AD** klikněte na **Přidat adresáře**.

1. Zadejte název domény a vyhledejte adresář služby Azure AD s tímto názvem domény.

1. Ověřte, že se jedná o správný adresář pomocí zadaného názvu adresáře a počáteční domény.

    > [!NOTE]
    > Všichni uživatelé z adresáře budou moct požádat o tento přístupový balíček. To zahrnuje uživatele ze všech subdomén přidružených k adresáři, nikoli jenom doménu použitou ve vyhledávání.

    ![Přístup k balíčku – zásady – výběr adresářů](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Kliknutím na **Přidat** Přidejte adresář.

1. Opakováním tohoto kroku přidejte další adresáře.

1. Po přidání všech adresářů, které chcete zahrnout do zásad, klikněte na **Vybrat**.

1. Přeskočit dolů k [zásadě: Oddíl](#policy-request) žádosti.

### <a name="policy-none-administrator-direct-assignments-only"></a>Zásady: Žádné (pouze přímé přiřazení správce)

Postupujte podle těchto kroků, pokud chcete, aby vaše zásada nepoužívala žádosti o přístup a aby správci mohli přímo přiřadit konkrétní uživatele k balíčku přístupu. Uživatelé nebudou muset vyžadovat balíček přístupu. Pořád můžete nastavit nastavení vypršení platnosti, ale neexistují žádná nastavení žádostí.

1. V části **Uživatelé, kteří můžou požádat o přístup** , vyberte možnost **žádný (pouze přímá přiřazení správců**).

    Po vytvoření balíčku pro přístup můžete k balíčku pro přístup přímo přiřadit konkrétní interní a externí uživatele. Pokud zadáte externího uživatele, vytvoří se ve vašem adresáři uživatelský účet hosta.

1. Přeskočit dolů k [zásadě: Část](#policy-expiration) vypršení platnosti.

### <a name="policy-request"></a>Zásady: Žádost

V části Request (žádost) určíte nastavení schválení, když si uživatelé vyžádají balíček pro přístup.

1. Pokud chcete pro žádosti od vybraných uživatelů vyžadovat schválení, nastavte u přepínače **vyžadovat schválení** **hodnotu Ano**. Chcete-li automaticky schvalovat požadavky, nastavte přepínač na **ne**.

1. Pokud požadujete schválení, v části **Vybrat schvalovatele** klikněte na **Přidat schvalovatele**.

1. V podokně vybrat schvalovatele vyberte jednoho nebo více uživatelů nebo skupin, které mají být schvalovatelé.

    Pouze jeden z vybraných schvalovatelů potřebuje schválit žádost. Schválení ze všech schvalovatelů se nevyžaduje. Rozhodnutí o schválení vychází z toho, kdo žádost nejprve zkontroluje.

    ![Přístup k balíčku – zásady – výběr schvalovatelů](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Kliknutím na **Vybrat** přidejte schvalovatele.

1. Kliknutím na **Zobrazit upřesňující nastavení žádostí** zobrazíte další nastavení.

    ![Přístup k balíčku – zásady – výběr adresářů](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Pokud chcete, aby uživatelé měli oprávnění k vyžádání balíčku pro přístup, nastavte **vyžadovat odůvodnění** na **Ano**.

1. Pokud chcete, aby schvalovatel mohl poskytnout odůvodnění pro schválení žádosti o přístup k balíčku, nastavte **vyžadovat odůvodnění schvalovatele** na **Ano**.

1. V poli **časový limit žádosti o schválení (dny)** zadejte dobu, kterou schvalovatelé musí ke kontrole žádosti. Pokud je v tomto počtu dnů žádní schvalovatelé nekontrolují, platnost žádosti vyprší a uživatel bude muset odeslat další žádost o přístup k balíčku.

### <a name="policy-expiration"></a>Zásady: Konec platnosti

V části vypršení platnosti zadejte, kdy vyprší platnost přiřazení uživatele k balíčku přístupu.

1. V části **vypršení platnosti** nastavte **přístup k balíčku platnost** na **Datum**, **počet dní**nebo **nikdy**.

    V poli pro **Datum**vyberte datum vypršení platnosti v budoucnosti.

    V **části počet dnů**zadejte číslo mezi 0 a 3660 dny.

    Na základě vašeho výběru vyprší přiřazení uživatele k balíčku pro přístup k určitému datu, určitému počtu dní od jejich schválení nebo nikdy.

1. Kliknutím na **Zobrazit upřesňující nastavení vypršení platnosti** zobrazíte další nastavení.

1. Chcete-li uživateli dovolit, aby rozšířili přiřazení, nastavte možnost **dovolit uživatelům** , aby rozšířili přístup k **Ano**.

    Pokud jsou v zásadách povolené přípony, uživatel dostane e-mail 14 dní a také 1 den před tím, než je přiřazení balíčku přístupu nastavené na vypršení platnosti, aby se toto přiřazení rozšířilo.

    ![Přístup k balíčku-zásady – nastavení vypršení platnosti](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Zásady: Povolení zásady

1. Pokud chcete, aby byl balíček přístupu hned dostupný pro uživatele v zásadě, klikněte na **Ano** , aby se zásady povolily.

    Po dokončení vytváření balíčku pro přístup ho můžete v budoucnu kdykoli povolit.

    ![Přístup k balíčku – nastavení zásad – Povolit zásadu](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Klikněte na tlačítko **Další** nebo **vytvořit**.
