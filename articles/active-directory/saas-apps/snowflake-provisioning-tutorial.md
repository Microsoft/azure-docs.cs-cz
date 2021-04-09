---
title: 'Kurz: Konfigurace Snowflake pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Snowflake.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539531"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Kurz: Konfigurace Snowflake pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak pomocí Snowflake a Azure Active Directory (Azure AD) nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřízení uživatelů a skupin na [Snowflake](https://www.Snowflake.com/pricing/). Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [co je automatizované zřizování uživatelů aplikací SaaS ve službě Azure AD?](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Tento konektor je aktuálně ve verzi Public Preview. Informace o používání podmínek použití najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Snowflake
> * Odebrat uživatele v Snowflake, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Snowflake
> * Zřizování skupin a členství ve skupinách v Snowflake
> * Povolení [jednotného přihlašování](./snowflake-tutorial.md) k Snowflake (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](../develop/quickstart-create-new-tenant.md)
* Uživatelský účet ve službě Azure AD s [oprávněním](../roles/permissions-reference.md) ke konfiguraci zřizování (správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce)
* [Tenant Snowflake](https://www.Snowflake.com/pricing/)
* Uživatelský účet v Snowflake s oprávněními správce

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1: plánování nasazení zřizování
1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Krok 2: Konfigurace Snowflake pro podporu zřizování pomocí Azure AD

Než nakonfigurujete Snowflake pro Automatické zřizování uživatelů pomocí Azure AD, musíte povolit systém pro zřizování SCIM (Domain Identity Management) na Snowflake.

1. Přihlaste se ke konzole pro správu Snowflake. Do zvýrazněného listu zadejte následující dotaz a pak vyberte **Spustit**.

    ![Snímek obrazovky s konzolou pro správu Snowflake s tlačítkem pro dotaz a spuštění](media/Snowflake-provisioning-tutorial/image00.png)

2.  Pro vašeho tenanta Snowflake se vygeneruje přístupový token SCIM. Pokud ho chcete načíst, vyberte odkaz zvýrazněný na následujícím snímku obrazovky.

    ![Snímek obrazovky listu ve Snowflake U I s přístupovým tokenem S C I M s názvem.](media/Snowflake-provisioning-tutorial/image01.png)

3. Zkopírujte hodnotu vygenerovaného tokenu a vyberte **Hotovo**. Tato hodnota se zadá do pole **tajný token** na kartě **zřizování** aplikace Snowflake ve Azure Portal.

    ![Snímek obrazovky s oddílem podrobností zobrazující token zkopírovaný do textového pole a možnost Hotovo s názvem.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Krok 3: Přidání Snowflake z Galerie aplikací Azure AD

Přidejte Snowflake z Galerie aplikací Azure AD a začněte spravovat zřizování pro Snowflake. Pokud jste dříve nastavili Snowflake pro jednotné přihlašování (SSO), můžete použít stejnou aplikaci. Při počátečním testování integrace však doporučujeme vytvořit samostatnou aplikaci. [Přečtěte si další informace o přidání aplikace z Galerie](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: definování toho, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení k aplikaci, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete použít [postup k přiřazení uživatelů a skupin k aplikaci](../manage-apps/assign-user-or-group-access-portal.md). Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete [použít filtr oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Pamatujte na tyto tipy:

* Když přiřazujete uživatele a skupiny do Snowflake, musíte vybrat jinou roli než výchozí přístup. Uživatelé s rolí Výchozí přístup jsou vyloučeni ze zřizování a v protokolech zřizování se označí příznakem neplatného nároku. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role. 

* Začněte v malém. Než se pustíte do zavádění pro všechny, proveďte testování s malou skupinou uživatelů a skupin. Když je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Krok 5: Konfigurace automatického zřizování uživatelů na Snowflake 

Tato část vás provede postupem konfigurace služby zřizování Azure AD pro vytváření, aktualizaci a zakázání uživatelů a skupin v Snowflake. V Azure AD můžete založit konfiguraci pro přiřazení uživatelů a skupin.

Konfigurace automatického zřizování uživatelů pro Snowflake ve službě Azure AD:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Snímek obrazovky, který zobrazuje podokno podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **Snowflake**.

    ![Snímek obrazovky, který zobrazuje seznam aplikací.](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností Automatická možnost byla vyvolána.](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte základní adresu url SCIM 2,0 a ověřovací token, který jste dříve získali na **adrese URL tenanta** a v polích **tajného tokenu** (v uvedeném pořadí). 

   Vyberte **Test připojení** , aby se služba Azure AD mohla připojit k Snowflake. Pokud se připojení nepovede, ujistěte se, že má váš účet Snowflake oprávnění správce, a zkuste to znovu.

    ![Snímek obrazovky, který zobrazuje pole pro klienta U R L a tajný token, společně s tlačítkem test Connection.](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování. Pak zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Snímek obrazovky, který zobrazuje pole pro oznamovací e-mail](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Snowflake**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Snowflake v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Snowflake pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |active|Logická hodnota|
   |displayName|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |userName|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: defaultRole|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: defaultWarehouse|Řetězec|

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny do Snowflake**.

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do Snowflake v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Snowflake pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    |Atribut|Typ|
    |---|---|
    |displayName|Řetězec|
    |členy|Referenční informace|

12. Pokud chcete nakonfigurovat filtry oborů, přečtěte si pokyny v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Snowflake, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

     ![Snímek obrazovky, který zobrazuje stav zřizování zapnuto.](common/provisioning-toggle-on.png)

14. Definujte uživatele a skupiny, které chcete zřídit pro Snowflake, výběrem požadovaných hodnot v části **Rozsah** **Nastavení** . 

    Pokud tato možnost není k dispozici, nakonfigurujte požadovaná pole v části **pověření správce**, vyberte **Uložit** a aktualizujte stránku. 

     ![Snímek obrazovky zobrazující možnosti pro obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni zřídit, vyberte **Uložit**.

     ![Snímek obrazovky s tlačítkem pro uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. K dalším synchronizacím dochází každých 40 minut, pokud je služba zřizování Azure AD spuštěná.

## <a name="step-6-monitor-your-deployment"></a>Krok 6: sledování nasazení
Po dokončení konfigurace zřizování použijte následující prostředky k monitorování nasazení:

- Pomocí [protokolů zřizování](../reports-monitoring/concept-provisioning-logs.md) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili.
- Zkontrolujte indikátor [průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) a zobrazte stav cyklu zřizování a způsob, jakým se má dokončit.
- Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. [Přečtěte si další informace o stavech karantény](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Omezení konektoru

Snowflake vygenerované tokeny SCIM vyprší za 6 měsíců. Mějte na paměti, že před vypršením platnosti těchto tokenů je potřeba tyto tokeny aktualizovat, aby zajištěné synchronizace pokračovaly v práci. 

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Služba zřizování Azure AD aktuálně funguje v rámci konkrétních [rozsahů IP adres](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). V případě potřeby můžete omezit další rozsahy IP adres a přidat tyto konkrétní rozsahy IP adres do seznamu povolených aplikací. Tato technika umožní do vaší aplikace tok provozu ze služby zřizování Azure AD.

## <a name="change-log"></a>Protokol změn

* 07/21/2020: povoleno obnovitelné odstranění pro všechny uživatele (prostřednictvím aktivního atributu).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky
* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
