---
title: 'Kurz: Konfigurace obsahu pro Automatické zřizování uživatelů pomocí Azure Active Directory'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů od Azure Active Directory (Azure AD) k obsahu.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516332"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Kurz: Konfigurace obsahu pro Automatické zřizování uživatelů

Tento článek popisuje kroky, které je třeba provést v obsahu a v Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny k [obsahu](https://www.contentful.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá a jak funguje, a nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Podporované funkce

> [!div class="checklist"]
> * Vytváření uživatelů v obsahu
> * Odebrat uživatele v obsahu, když už nepotřebují přístup
> * Udržování uživatelských atributů synchronizovaných mezi službou Azure AD a obsahem
> * Zřizování skupin a členství ve skupinách v obsahu
> * [Jednotné přihlašování](contentful-tutorial.md) k obsahu (doporučeno)

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenanta Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uživatelský účet ve službě Azure AD, který má [oprávnění](../roles/permissions-reference.md) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce). 
* Účet organizace s obsahem, který má předplatné podporující systém pro zřizování pro správu identit mezi doménami (SCIM). Pokud máte dotazy týkající se předplatného vaší organizace, obraťte se na [podporu obsahu](mailto:support@contentful.com).
 
## <a name="plan-your-provisioning-deployment"></a>Plánování nasazení zřizování

1. Seznamte se s [fungováním služby zřizování](../app-provisioning/user-provisioning.md).
2. Zjistěte, kdo bude v [rozsahu zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Určete, jaká data se mají [mapovat mezi Azure AD a obsahem](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Konfigurace obsahu pro podporu zřizování pomocí Azure AD

1. V rámci Contentd vytvořte **uživatelský účet služby** . K dispozici jsou všechna oprávnění k zřizování pro Azure prostřednictvím tohoto účtu. Pro tento účet doporučujeme zvolit možnost **vlastník** jako role organizace.

2. Přihlaste se k obsahu jako **uživatel služby**.

3. V nabídce vlevo vyberte **Nastavení organizace**  >  **přístupové nástroje**  >  .**zřizování uživatelů**.

   ![Snímek obrazovky s nabídkou nastavení organizace v obsahu se zvýrazněným zřizováním uživatelů v nabídce Nástroje pro přístup.](media/contentful-provisioning-tutorial/access.png)

4. Zkopírujte a uložte **adresu URL SCIM**. Tuto hodnotu zadáte do Azure Portal na kartě **zřizování** vaší aplikace s obsahem.

5. Vyberte **Generovat osobní přístupový token**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. V modálním okně zadejte název vašeho osobního přístupového tokenu a potom vyberte **Generovat**.

7. Je vygenerována adresa URL SCIM a tajný token. Zkopírujte a uložte tyto hodnoty. Tyto hodnoty zadáte na kartě **zřizování** aplikace s obsahem v Azure Portal.

    ![Snímek obrazovky s podoknem tokenu osobního přístupu a zvýrazněným znakem C F P a a názvem zástupného symbolu tokenu](media/contentful-provisioning-tutorial/token.png)


Pokud máte dotazy při konfiguraci zřizování v konzole pro správu obsahu, obraťte se na [podporu obsahu](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Přidání obsahu z Galerie aplikací Azure AD

Pokud chcete spravovat zřizování obsahu, přidejte do něj obsah z Galerie aplikací Azure AD. Pokud jste dříve nastavili obsah pro jednotné přihlašování, můžete použít stejnou aplikaci. Nicméně doporučujeme vytvořit samostatnou aplikaci pro prvotní testování integrace. Naučte se, jak [Přidat aplikaci do galerie](../manage-apps/add-application-portal.md). 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Definování uživatelů, kteří budou v rozsahu zřizování 

Službu zřizování Azure AD můžete použít k určení oboru, který se zřídí v závislosti na přiřazení aplikace, nebo na základě atributů uživatele nebo skupiny. 

Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, proveďte kroky k [přiřazení uživatelů a skupin k aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, použijte filtr oborů k [Definování podmíněných pravidel pro zřizování uživatelských účtů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Při přiřazování uživatelů a skupin k obsahu je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé, kteří mají výchozí roli přístupu, jsou z zřizování vyloučeni a jsou uvedeni v protokolech zřizování jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) a přidat tak další role. 
* Začněte v malém. Před zavedením všech uživatelů proveďte test pomocí malé sady uživatelů a skupin. Když je obor zřizování nastavený na přiřazené uživatele a skupiny, můžete ho řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Konfigurace automatického zřizování uživatelů pro obsah 

V této části se seznámíte s postupem nastavení služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v testovací aplikaci na základě přiřazení uživatelů nebo skupin v Azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro obsahy ve službě Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.

   ![Snímek obrazovky, který zobrazuje nabídku podnikových aplikací v Azure Portal se zvýrazněnými všemi aplikacemi.](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **contentd**.

   ![Snímek obrazovky zobrazující prvních 20 výsledků vrácených v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

   ![Snímek karty zřizování zvýrazněný v části Správa v levé nabídce](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

   ![Snímek obrazovky zobrazující možnosti režimu zřizování s automatickým zvýrazněním](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte adresu URL klienta s obsahem a tajný token. Pokud chcete zajistit, aby se služba Azure AD mohla připojit k obsahu, vyberte **Test připojení**. Pokud se připojení nepovede, ujistěte se, že účet s obsahem má oprávnění správce, a zkuste to znovu.

   ![Snímek obrazovky se zvýrazněnými textovými poli tenant U R L a tajného tokenu se zvýrazněným tlačítkem pro test připojení.](common/provisioning-testconnection-tenanturltoken.png)

6. V části **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a pak zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

   ![Snímek obrazovky, který zobrazuje textové pole pro oznamovací E-mail.](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelů k obsahu**.

9. V sekci **mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizované z Azure AD, do obsahu. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v rámci obsahu pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](../app-provisioning/customize-application-attributes.md), je nutné zajistit, aby contentd API podporoval filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny s obsahem**.

11. V sekci **mapování atributů** zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD, do obsahu. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v obsahu pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    |Atribut|Typ|Podporováno pro filtrování|
    |---|---|---|
    |displayName|Řetězec|&check;|
    |členy|Referenční informace|

12. Pokud chcete nastavit filtry oborů, proveďte kroky popsané v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu zřizování Azure AD pro obsah, vyberte v části **nastavení u možnosti** **stav zřizování** možnost **zapnuto**.

    ![Snímek obrazovky, který zobrazuje stav zřizování v přepínači a vypnuto.](common/provisioning-toggle-on.png)

14. Chcete-li definovat uživatele nebo skupiny, u kterých chcete vytvořit obsah, v části **Nastavení** pro možnost **obor** vyberte příslušnou možnost.

    ![Snímek obrazovky zobrazující možnosti, které můžete vybrat v podokně rozsah.](common/provisioning-scope.png)

15. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Snímek obrazovky zobrazující tlačítko Uložit a tlačítko Storno](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizační cyklus všech uživatelů a skupin definovaných v **oboru** **Nastavení**. Počáteční cyklus trvá déle než další cykly, které se provádějí přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="monitor-your-deployment"></a>Monitorování nasazení

Po nakonfigurování zřizování použijte následující prostředky k monitorování nasazení:

* Pokud chcete zjistit, kteří uživatelé se úspěšně zřídili nebo neúspěšně zřídili, zobrazte si [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md).
* Pokud chcete zobrazit stav cyklu zřizování a jak se má dokončit, zkontrolujte indikátor [průběhu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, aplikace přejde do karantény. Přečtěte si další informace o [stavech karantény](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
