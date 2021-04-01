---
title: 'Kurz: Konfigurace PlayVox pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po PlayVox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96862475"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Kurz: Konfigurace PlayVox pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak postupovat v PlayVox i Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele nebo skupiny k [PlayVox](https://www.playvox.com) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá a jak funguje, a nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v PlayVox.
> * Odeberte uživatele v PlayVox, když k nim nepotřebují přístup.
> * Udržujte atributy uživatele synchronizované mezi Azure AD a PlayVox.

## <a name="prerequisites"></a>Požadavky

Scénář v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování. Například účet může mít roli správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce.
* Uživatelský účet v [PlayVox](https://www.playvox.com) s oprávněními správce superuživatele.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1: plánování nasazení zřizování

1. Přečtěte si [, jak služba zřizování funguje](../app-provisioning/user-provisioning.md).

2. Určete, kdo bude [v oboru pro zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Určete, jaká data se mají [mapovat mezi Azure AD a PlayVox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Krok 2: Konfigurace PlayVox pro podporu zřizování pomocí Azure AD

1. Přihlaste se ke konzole pro správu PlayVox a pokračujte na **nastavení > klíče rozhraní API**.

2. Vyberte **vytvořit klíč rozhraní API**.

    ![Částečný snímek obrazovky znázorňující umístění tlačítka pro vytvoření klíče rozhraní API v uživatelském rozhraní PlayVox](media/playvox-provisioning-tutorial/create.png)

3. Zadejte smysluplný název pro klíč rozhraní API a pak vyberte **Uložit**. Po vygenerování klíče rozhraní API vyberte **Zavřít**.

4. Na klíč rozhraní API, který jste vytvořili, vyberte ikonu **Podrobnosti** .

    ![Částečný snímek obrazovky znázorňující umístění ikony podrobností, což je Lupa, v uživatelském rozhraní PlayVox.](media/playvox-provisioning-tutorial/api.png)

5. Zkopírujte a uložte hodnotu **klíče Base64** . Později v Azure Portal zadáte tuto hodnotu do textového pole **token tajného** kódu na kartě **zřizování** aplikace PlayVox.

    ![Snímek obrazovky s klíčem rozhraní API pro podrobnosti se zvýrazněnou hodnotou klíče BASE64](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Krok 3: Přidání PlayVox z Galerie aplikací Azure AD

Pokud chcete začít spravovat zřizování pro PlayVox, přidejte PlayVox do svého tenanta Azure AD z Galerie aplikací. Další informace najdete v tématu [rychlý Start: Přidání aplikace do tenanta Azure Active Directory (Azure AD)](../manage-apps/add-application-portal.md).

Pokud jste dříve nastavili PlayVox pro jednotné přihlašování (SSO), můžete použít stejnou aplikaci. Doporučujeme ale při počátečním testování integrace vytvořit samostatnou aplikaci.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: definování toho, kdo bude v oboru pro zřizování

Službu zřizování Azure AD můžete použít k určení oboru, který se zřídí, a to na základě přiřazení aplikace nebo atributů uživatele nebo skupiny. Informace o tom, jak do aplikace přiřazovat uživatele nebo skupiny, najdete v tématu věnovaném [správě přiřazení uživatelů pro aplikaci v Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) k určení oboru, který se bude zřizovat na základě přiřazení. Chcete-li určit obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, použijte filtr oboru, jak je popsáno v tématu [zřizování aplikace založené na atributech s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Zapamatujte si tyto body:

* Při přiřazování uživatelů k PlayVox je nutné vybrat jinou roli než výchozí přístup. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je výchozí přístup jedinou rolí dostupnou v aplikaci, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat další role.

* Začněte v malém. Než se pustíte do všech uživatelů, otestujte je pomocí malé skupiny uživatelů nebo skupin. Pokud je rozsah zřizování založen na přiřazených uživatelích nebo skupinách, můžete nastavit velikost sady tím, že aplikaci přiřadíte pouze jednoho nebo dva uživatele nebo skupiny. Když rozsah zřizování zahrnuje všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) pro omezení velikosti sady testů.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Krok 5: Konfigurace automatického zřizování uživatelů na PlayVox

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

Konfigurace automatického zřizování uživatelů pro PlayVox ve službě Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Částečný snímek obrazovky Azure Portal s vybranými podnikovými aplikacemi a všemi položkami aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyhledejte a vyberte **PlayVox**.

    ![Částečný snímek obrazovky se seznamem aplikací se zvýrazněným polem pro hledání aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Částečný snímek obrazovky znázorňující položku nabídky zřizování](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Částečný snímek obrazovky s kartou zřizování, který zobrazuje automatickou možnost vybranou v rozevíracím seznamu režim zřizování.](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL tenanta** PlayVox jako:

    `https://{tenant}.playvox.com/scim/v1`

    Zadejte **tajný token** , který jste zkopírovali dříve v kroku 2. Pak vyberte **Test připojení** , aby se služba Azure AD mohla připojit k PlayVox. Pokud se připojení nepovede, ujistěte se, že má váš účet PlayVox oprávnění správce, a zkuste to znovu.

    ![Částečný snímek obrazovky s oddílem přihlašovací údaje správce, včetně adresy URL tenanta a textových polí tajného tokenu a zvýrazněným odkazem test Connection](common/provisioning-testconnection-tenanturltoken.png)

6. Do textového pole **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, kterým se budou zobrazovat oznámení o chybách zřizování. Potom zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Částečný snímek obrazovky s textovým polem oznámení a e-mailovým oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé PlayVox**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do PlayVox v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v PlayVox pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), ujistěte se, že rozhraní PlayVox API podporuje filtrování uživatelů na základě tohoto atributu. Vyberte **Uložit** a potvrďte všechny změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |displayName|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |externalId|Řetězec|

10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro PlayVox, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Částečný snímek obrazovky oddílu nastavení s informacemi o stavu zřizování nastaveném na zapnuto](common/provisioning-toggle-on.png)

12. Pořád v **Nastavení** definujte uživatele nebo skupiny, které chcete zřídit pro PlayVox, a to výběrem požadovaných hodnot v **oboru**.

    ![Částečný snímek obrazovky oddílu nastavení, který zobrazuje rozevírací seznam rozsah.](common/provisioning-scope.png)

13. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Částečný snímek obrazovky znázorňující možnosti uložení a zahození](common/provisioning-configuration-save.png)

Tato operace zahájí cyklus počáteční synchronizace všech uživatelů a skupin definovaných v nabídce **Rozsah** v části **Nastavení**. Počáteční cyklus trvá déle než v pozdějších cyklech. K dalším cyklům dochází přibližně každých 40 minut za předpokladu, že služba zřizování Azure AD běží.

## <a name="step-6-monitor-your-deployment"></a>Krok 6: sledování nasazení

Po dokončení konfigurace zřizování použijte následující prostředky k monitorování nasazení:

* Pomocí [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
* Zkontrolujte indikátor [průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [v tématu zřizování aplikací ve stavu karantény](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).