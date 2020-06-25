---
title: 'Kurz: Konfigurace Smartsheet pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 699eb37176d6737744fb0ba01f9f3f4a2d4e55b1
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318743"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Kurz: Konfigurace Smartsheet pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Smartsheet a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro [Smartsheet](https://www.smartsheet.com/pricing). Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Podporované funkce
> [!div class="checklist"]
> * Vytváření uživatelů v Smartsheet
> * Odebrat uživatele v Smartsheet, když už nevyžadují přístup
> * Udržování uživatelských atributů synchronizovaných mezi Azure AD a Smartsheet
> * Jednotné přihlašování k Smartsheet (doporučeno)

> [!NOTE]
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadované součásti

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* [Tenant služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uživatelský účet ve službě Azure AD s [oprávněním](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) ke konfiguraci zřizování (například správce aplikace, správce cloudové aplikace, vlastník aplikace nebo globální správce).
* [Tenant Smartsheet](https://www.smartsheet.com/pricing)
* Uživatelský účet v plánu Smartsheet Enterprise nebo Enterprise Premier s oprávněními správce systému.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Plánování nasazení zřizování
1. Přečtěte si [, jak služba zřizování funguje](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Určete, kdo bude v [oboru pro zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Určete, jaká data se mají [mapovat mezi Azure AD a Smartsheet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurace Smartsheet pro podporu zřizování pomocí Azure AD

Před konfigurací Smartsheet pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Smartsheet.

1. Přihlaste se jako **správce** systému na **[portálu Smartsheet](https://app.smartsheet.com/b/home)** a přejděte na **správce účtu**.

    ![Správce účtu Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Přejít na **ovládací prvky zabezpečení > Automatické zřizování uživatelů > upravit**.

    ![Ovládací prvky zabezpečení Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Přidejte a ověřte e-mailové domény pro uživatele, které chcete zřídit z Azure AD do Smartsheet. Pokud chcete zajistit, aby všechny akce zřizování pocházely jenom z Azure AD, a taky zajistit, aby byl váš seznam uživatelů Smartsheet synchronizovaný s přiřazeními Azure AD, vyberte **Nepovoleno** .

    ![Zřizování uživatelů Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po dokončení ověření bude nutné doménu aktivovat. 

    ![Smartsheet aktivovat doménu](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Vygenerujte **tajný token** potřebný ke konfiguraci automatického zřizování uživatelů pomocí Azure AD tak, že přejdete na **aplikace a integrace**.

    ![Instalace Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Vyberte možnost **přístup přes rozhraní API**. Klikněte na **vygenerovat nový přístupový token**.

    ![Instalace Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zadejte název přístupového tokenu rozhraní API. Klikněte na **OK**.

    ![Instalace Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Zkopírujte přístupový token rozhraní API a uložte ho tak, jak se to bude zobrazovat jenom v takovém případě. To se vyžaduje v poli **tajný token** v Azure AD.

    ![Token Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Krok 3. Přidání Smartsheet z Galerie aplikací Azure AD

Přidejte Smartsheet z Galerie aplikací Azure AD a začněte spravovat zřizování pro Smartsheet. Pokud jste dříve nastavili Smartsheet pro jednotné přihlašování, můžete použít stejnou aplikaci. Doporučuje se ale při počátečním testování integrace vytvořit samostatnou aplikaci. Další informace o přidání aplikace z Galerie [najdete tady](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definujte, kdo bude v oboru pro zřizování. 

Služba zřizování Azure AD umožňuje obor, který se zřídí na základě přiřazení do aplikace, nebo na základě atributů uživatele nebo skupiny. Pokud se rozhodnete určit rozsah, který se zřídí pro vaši aplikaci na základě přiřazení, můžete k přiřazení uživatelů a skupin k aplikaci použít následující [postup](../manage-apps/assign-user-or-group-access-portal.md) . Pokud se rozhodnete obor, který se zřídí výhradně na základě atributů uživatele nebo skupiny, můžete použít filtr oboru, jak je popsáno [zde](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Při přiřazování uživatelů a skupin k Smartsheet je nutné vybrat jinou roli než **výchozí přístup**. Uživatelé s výchozí rolí přístupu se z zřizování vylučují a v protokolech zřizování se označí jako neefektivně. Pokud je jedinou rolí dostupnou v aplikaci výchozí role přístupu, můžete [aktualizovat manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) a přidat další role. 

* Aby se zajistila parita v přiřazování rolí uživatelů mezi Smartsheet a Azure AD, doporučuje se využít stejné přiřazení rolí, které jsou naplněné v seznamu úplných uživatelů Smartsheet. Pokud chcete načíst tento seznam uživatelů z Smartsheet, přejděte na **správce účtu > Správa uživatelů > další akce > stáhnout seznam uživatelů (CSV)**.

* Pro přístup k určitým funkcím aplikace Smartsheet vyžaduje, aby uživatel měl více rolí. Další informace o uživatelských typech a oprávněních v Smartsheet najdete v [uživatelském typu a oprávnění](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Pokud má uživatel v Smartsheet přiřazených víc rolí, **musíte** zajistit, aby se tato přiřazení rolí replikoval ve službě Azure AD, aby nedocházelo k situaci, kdy uživatelé můžou trvale ztratit přístup k objektům Smartsheet. Každá jedinečná role v Smartsheet **musí** být přiřazena jiné skupině v Azure AD. Pak je **nutné** přidat uživatele do všech skupin odpovídajících rolím, které jsou požadovány. 

* Začněte malým. Než se pustíte do všech uživatelů, testujte je s malou sadou uživatelů a skupin. Pokud je obor pro zřizování nastavený na přiřazené uživatele a skupiny, můžete to řídit přiřazením jednoho nebo dvou uživatelů nebo skupin k aplikaci. Pokud je obor nastavený na všechny uživatele a skupiny, můžete zadat [Filtr oboru založený na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Krok 5. Konfigurace automatického zřizování uživatelů na Smartsheet 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Smartsheet na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Smartsheet ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Smartsheet**.

    ![Odkaz Smartsheet v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **zřizování** .

    ![Karta zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automaticky**.

    ![Karta zřizování](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte **základní adresu URL SCIM 2,0 a hodnoty přístupového tokenu** , které jste dříve získali z Smartsheet v **adrese URL tenanta** a **tajném tokenu** , v uvedeném pořadí. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Smartsheet. Pokud se připojení nepovede, ujistěte se, že váš účet Smartsheet má oprávnění správce systému, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Smartsheet**.

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Smartsheet v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Smartsheet pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |aktivně|Logická hodnota|
   |title|Řetězec|
   |userName|Řetězec|
   |název. křestní jméno|Řetězec|
   |název. rodina|Řetězec|
   |phoneNumbers [typ EQ "Work"]. Value|Řetězec|
   |phoneNumbers [Type EQ "mobilní"]. Value|Řetězec|
   |phoneNumbers [typ EQ "fax"]. hodnota|Řetězec|
   |externalId|Řetězec|
   |role [Primary EQ "true"]. Display|Řetězec|
   |role [Primary EQ "true"]. Type|Řetězec|
   |role [primární EQ "true"]. Value|Řetězec|
   |role|Řetězec|
   urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: divize|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: costCenter|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Řetězec|


10. Pokud chcete nakonfigurovat filtry oborů, přečtěte si následující pokyny uvedené v [kurzu filtr oboru](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro Smartsheet, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý stav zřizování](common/provisioning-toggle-on.png)

12. Definujte uživatele nebo skupiny, které chcete zřídit pro Smartsheet, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

13. Až budete připraveni zřídit, klikněte na **Uložit**.

    ![Ukládá se konfigurace zřizování.](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Jakmile nakonfigurujete zřizování, použijte k monitorování nasazení tyto prostředky:

1. Pomocí [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) určete, kteří uživatelé se úspěšně zřídili nebo neúspěšně nastavili.
2. Podívejte se na [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , kde se zobrazí stav cyklu zřizování a jak se má dokončit.
3. Pokud se zdá, že konfigurace zřizování je ve stavu není v pořádku, bude aplikace přejít do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Omezení konektoru

* Smartsheet nepodporuje obnovitelné odstranění. Když je **aktivní** atribut uživatele nastaven na hodnotu false, Smartsheet odstraní uživatele trvale.

## <a name="change-log"></a>Protokol změn

* 06/16/2020 – Přidali jsme podporu pro atributy podnikového rozšíření "cost Center", "divize", "manažer" a "oddělení" pro uživatele.

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
