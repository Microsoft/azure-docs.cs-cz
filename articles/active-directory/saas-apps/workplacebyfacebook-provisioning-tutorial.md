---
title: 'Kurz: Konfigurace pracoviště na Facebooku pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a na pracovišti pomocí Facebooku.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: 4b4953cbca4d08e911fd043413387a1602bd8e08
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006070"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Kurz: Konfigurace pracoviště na Facebooku pro Automatické zřizování uživatelů

Tento kurz popisuje kroky, které je třeba provést na pracovišti na Facebooku a Azure Active Directory (Azure AD) ke konfiguraci automatického zřizování uživatelů. Po nakonfigurování Azure AD automaticky zřídí a odzřídí uživatele a skupiny na [pracovišti pomocí Facebooku](https://work.workplace.com/) pomocí služby zřizování Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů na pracovišti pomocí Facebooku
> * Odebrat uživatele na pracovišti pomocí Facebooku, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a pracovištěm pomocí Facebooku
> * [Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) k pracovní ploše na Facebooku (doporučeno)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce)
* Pracoviště s povoleným jednorázovým přihlášením na Facebooku

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí.

K otestování kroků v tomto kurzu byste měli postupovat podle těchto doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [si ho stáhnout](https://azure.microsoft.com/pricing/free-trial/)na měsíc zdarma.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a na pracovišti pomocí Facebooku](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace pracoviště na Facebooku pro podporu zřizování pomocí Azure AD

Než nakonfigurujete a povolíte službu zřizování, musíte určit, kteří uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k vašemu pracovišti pomocí aplikace Facebook. Až se rozhodnete, můžete těmto uživatelům přiřadit aplikaci na Facebooku pomocí následujících pokynů:

*   Doporučuje se, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD k pracovní ploše na Facebooku. Další uživatele a skupiny můžete přiřadit později.

*   Když přiřadíte uživatele k pracovišti na Facebooku, musíte vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání pracoviště pomocí Facebooku z Galerie aplikací Azure AD

Přidejte pracovní plochu z Galerie aplikací Azure AD na Facebooku a začněte spravovat zřizování na pracovišti pomocí Facebooku. Pokud jste dříve nastavili pracovní plochu na webu Facebook pro jednotné přihlašování (SSO), můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4: Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin na pracoviště prostřednictvím Facebooku je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **pracovní plocha na Facebooku**.

    ![Odkaz na pracoviště podle Facebooku v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** klikněte na **autorizovat**. Na stránce pro autorizaci budete přesměrováni na pracovní plochu. Zadejte své pracoviště pomocí uživatelského jména na Facebooku a klikněte na tlačítko **pokračovat** . Klikněte na **Test připojení** a ujistěte se, že se Azure AD může připojit k pracovišti pomocí Facebooku. Pokud se připojení nepovede, ujistěte se, že na pracovišti má účet Facebook oprávnění správce, a zkuste to znovu.

    ![zřizování](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Autorizace](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování a zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Vyberte **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatelů na pracovišti pomocí Facebooku**.

9. Zkontrolujte atributy uživatele, které se synchronizují z Azure AD po pracovní plochu na Facebooku v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů na pracovišti pomocí Facebooku pro operace aktualizace. Pokud se rozhodnete změnit [odpovídající cílový atribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), budete muset zajistit, aby pracoviště na Facebooku API podporuje filtrování uživatelů na základě tohoto atributu. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |displayName|Řetězec|
   |aktivně|Logická hodnota|
   |title|Logická hodnota|
   |e-maily [typ EQ "Work"]. Value|Řetězec|
   |název. křestní jméno|Řetězec|
   |název. rodina|Řetězec|
   |název. formátovaný|Řetězec|
   |adresy [Type EQ "Work"]. formátovaný|Řetězec|
   |adresy [Type EQ "]. streetAddress|Řetězec|
   |adresy [typ EQ "Work"].|Řetězec|
   |adresy [typ EQ "Work"]. region|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |adresy [typ EQ "Work"]. postalCode|Řetězec|
   |adresy [Type EQ "ostatní"]. formátovaný|Řetězec|
   |phoneNumbers [typ EQ "Work"]. Value|Řetězec|
   |phoneNumbers [Type EQ "mobilní"]. Value|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |externalId|Řetězec|
   |preferredLanguage|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. Manager|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. Department|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. divize|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. Organization|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. costCenter|Řetězec|
   |urn: SCIM: schemas: rozšíření: Enterprise: 1.0. employeeNumber|Řetězec|
   |urn: SCIM: schemas: rozšíření: Facebook: auth_method: 1.0: auth_method|Řetězec|
   |urn: SCIM: schemas: Extension: Facebook: prvotní: 1.0. is_frontline|Logická hodnota|
   |urn: SCIM: schemas: Extension: Facebook: starttermdates: 1.0. startDate|Integer|


10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu zřizování Azure AD pro pracovní plochu na Facebooku, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit na pracovišti pomocí Facebooku, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční cyklus synchronizace všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Počáteční cyklus trvá déle než u dalších cyklů, ke kterým dojde přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
*  Pokud se uživateli zobrazí zpráva, že uživatel nebyl úspěšně vytvořen a existuje událost protokolu auditu s kódem "1789003", znamená to, že uživatel pochází z neověřené domény.

## <a name="change-log"></a>Protokol změn

* 09/10/2020 – Přidali jsme podporu pro podnikové atributy "region", "Organization", "costCenter" a "employeeNumber". Přidání podpory pro vlastní atributy "startDate", "auth_method" a "prvotní"

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)
