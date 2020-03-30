---
title: 'Kurz: Konfigurace workplace by Facebook pro automatické zřizování uživatelů pomocí Služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workplace podle Facebooku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603208"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Kurz: Konfigurace workplace by Facebook pro automatické zřizování uživatelů

Tento kurz popisuje kroky, které je potřeba provést na Workplace by Facebook a Azure Active Directory (Azure AD) ke konfiguraci automatickézřivačné nastavení uživatelů. Když je Azure AD nakonfigurováno, automaticky zřazuje a deprovisionzuje uživatele a skupiny na [Workplace facebookem](https://work.workplace.com/) pomocí služby Azure AD Provisioning. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrace na nové pracoviště pomocí aplikace Facebook
Pokud máte existující integraci s Workplace by Facebook, přečtěte si níže uvedenou část o přicházejících změnách. Pokud poprvé nastavujete Workplace by Facebook, můžete tuto část přeskočit a přejít na podporované funkce. 

#### <a name="whats-changing"></a>Co se mění?
* Změny na straně Azure AD: Metoda autorizace pro zřizování uživatelů na workplace byla historicky dlouho trvající tajný token. Brzy uvidíte autorizační metodu změněnou na udělení autorizace OAuth. 
* Změny na straně Pracoviště: Dříve byla aplikace Azure AD vlastní integrací na Workplace od Facebooku. Teď uvidíte Azure AD v adresáři integrace workplace jako aplikace třetí strany. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Co musím udělat, abych mohl migrovat stávající vlastní integraci do nové aplikace?
Pokud máte existující integraci workplace s platným tokenem, **není vyžadována žádná akce**. Každý týden automaticky migrujeme zákazníky do nové aplikace. To se provádí zcela v zákulisí. Pokud nemůžete čekat a chcete přejít na novou aplikaci ručně, můžete přidat novou instanci Workplace z galerie a znovu nakonfigurovat zřizování. Všechny nové instance Workplace budou automaticky používat novou verzi aplikace. 

 
Pokud je vaše integrace na Workplace v karanténě, budete muset znovu zadat platný token, abychom vás mohli migrovat. Oddíl přihlašovacích údajů správce bude zašedlý, ale můžete připojit následující (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)** na adresu URL pro další uložení přihlašovacích údajů. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Jak poznám, že moje aplikace byla migrována? 
Při migraci aplikace bude odstraněn nápis v části autorizace o změnách upcomming a pole tajného tokenu bude nahrazeno modrým tlačítkem autorizovat. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>Oddíl přihlašovacích údajů správce je v aplikaci zašedlý a nemohu je uložit. Proč?
Uzamkli jsme oddíl přihlašovacích údajů správce pro stávající zákazníky na Workplace. Po migraci vašeho tenanta do nové aplikace Workplace budete moct znovu aktualizovat oddíl s přihlašovacími údaji správce. Pokud nemůžete čekat, můžete použít adresu URL výše k úpravě aplikace. 

 
#### <a name="when-will-these-changes-happen"></a>Kdy k těmto změnám dojde?
Všechny nové instance Workplace již budou používat novou metodu integrace / autorizace. Stávající integrace budou postupně migrovány do května. Tým na pracovišti poskytl prodloužení lhůty od února-28 do května-1. 

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů na Workplace podle Facebooku
> * Odebrání uživatelů na Workplace facebookem, když už nevyžadují přístup
> * Zachování synchronizace atributů uživatelů mezi Azure AD a Workplace podle Facebooku
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) k Workplace od Facebooku (doporučeno)

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* [Klient Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikací, správce cloudových aplikací, vlastník aplikace nebo globální správce)
* Předplatné s povoleným přihlášením k zařízení Workplace by Facebook

> [!NOTE]
> Chcete-li otestovat kroky v tomto kurzu, nedoporučujeme používat produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si o [tom, jak funguje služba zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se [mají mapovat mezi Azure AD a Workplace podle Facebooku](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace workplace by Facebook pro podporu zřizování pomocí Azure AD

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Workplace pomocí Facebooku. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci Workplace by Facebook podle pokynů zde:

*   Doporučuje se, aby jeden uživatel Azure AD je přiřazen k workplace Facebook emitovat konfiguraci zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

*   Při přiřazování uživatele k Workplace facebookem musíte vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání pracoviště podle Facebooku z galerie aplikací Azure AD

Přidejte Workplace by Facebook z galerie aplikací Azure AD a začněte spravovat zřizování na Workplace pomocí Facebooku. Pokud jste dříve nastavili Workplace facebookem pro ssebou, můžete použít stejnou aplikaci. Doporučujeme však vytvořit samostatnou aplikaci při testování integrace zpočátku. Další informace o přidání aplikace z galerie [naleznete zde](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování 

Služba zřizování Azure AD umožňuje obor, který bude zřízen na základě přiřazení k aplikaci a nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete obor, kdo bude zřízen do vaší aplikace na základě přiřazení, můžete použít následující [kroky](../manage-apps/assign-user-or-group-access-portal.md) k přiřazení uživatelů a skupin k aplikaci. Pokud se rozhodnete obor, který bude zřízen výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oborů, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k workplace podle Facebooku musíte vybrat jinou roli než **výchozí přístup**. Uživatelé s rolí výchozí přístup jsou vyloučeni z zřizování a budou označeny jako neefektivně oprávněné v protokolech zřizování. Pokud je k dispozici pouze role v aplikaci výchozí přístupová role, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte v malém. Vyzkoušejte s malou sadou uživatelů a skupin před zavedením pro všechny. Pokud je obor zřizování nastaven na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin do aplikace. Pokud je obor nastaven na všechny uživatele a skupiny, můžete určit [filtr oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Workplace by Facebook**.

    ![Odkaz Pracoviště podle Facebooku v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** klikněte na **autorizovat**. Na stránce autorizace Facebooku budete přesměrováni na Workplace. Zadejte své pracoviště podle uživatelského jména facebooku a klikněte na tlačítko **Pokračovat.** Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k Workplace od Facebooku. Pokud se připojení nezdaří, ujistěte se, že váš účet Workplace by Facebook má oprávnění správce a zkuste to znovu.

    ![Zajišťování](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Autorizace](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části Mapování vyberte **Synchronizovat uživatele Služby Azure active directory na Workplace podle Facebooku**. **Mappings**

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Workplace facebookem v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů na workplace facebookem pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby rozhraní API Workplace by Facebook podporovalo filtrování uživatelů na základě tohoto atributu. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |displayName|Řetězec|
   |Aktivní|Logická hodnota|
   |title|Logická hodnota|
   |e-maily[zadejte eq "práce"].hodnota|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název.formátován|Řetězec|
   |adresy[zadejte eq "work"].formátované|Řetězec|
   |addresses[zadejte eq "work"].streetAddress|Řetězec|
   |adresy[zadejte eq "práce"].lokalita|Řetězec|
   |adresy[zadejte eq "work"].region|Řetězec|
   |adresy[zadejte eq "work"].země|Řetězec|
   |adresy[zadejte eq "práce"].PSČ|Řetězec|
   |adresy[zadejte eq "other"].formátované|Řetězec|
   |phoneNumbers[zadejte eq "work"].value|Řetězec|
   |phoneNumbers[zadejte eq "mobile"].value|Řetězec|
   |phoneNumbers[zadejte eq "fax"].value|Řetězec|
   |externalId|Řetězec|
   |preferredLanguage|Řetězec|
   |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:manažer|Řetězec|
   |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:oddělení|Řetězec|

10. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu zřizování Azure AD pro Workplace by Facebook, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit Workplace by Facebook, výběrem požadovaných hodnot v **části Obor** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

13. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Počáteční cyklus trvá déle než následující cykly, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po konfiguraci zřizování můžete ke sledování nasazení použít následující prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé byli zřízeni úspěšně nebo neúspěšně
2. Zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) zobrazíte stav cyklu zřizování a jak blízko je k dokončení
3. Pokud se zdá, že konfigurace zřizování je v nestavu, aplikace přejde do karantény. Další informace o stavech karantény naleznete [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
*  Pokud se zobrazí uživatel neúspěšně vytvořen a dojde k události protokolu auditu s kódem "1789003", znamená to, že uživatel pochází z neověřené domény.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../manage-apps/check-status-user-account-provisioning.md)
