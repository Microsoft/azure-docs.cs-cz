---
title: 'Kurz: Konfigurace blikání pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů ke blikání.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: d691807f673dcd6c8147c9ff18a95c6ce0c88ae6
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247419"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>Kurz: Konfigurace blikání pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést při blikání a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřízení uživatelů ke blikání.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Předpoklady

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Klient blikání](https://joinblink.com/pricing)
* Uživatelský účet ve blikání s oprávněními správce.

## <a name="assigning-users-to-blink"></a>Přiřazování uživatelů ke blikání

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom členové skupiny Uživatelé nebo skupiny, kteří jsou přiřazeni k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste měli rozhodnout, kteří uživatelé nebo členové skupiny ve službě Azure AD potřebují přístup ke blikání. Po rozhodnutí můžete přiřadit tyto uživatele a skupiny do blikání podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>Důležité tipy pro přiřazení uživatelů ke blikání

* Doporučuje se, aby se k tomu, aby bylo možné otestovat automatickou konfiguraci zřizování uživatelů, přiřadil jeden uživatel Azure AD ke blikání. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele ke blikání musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (je-li k dispozici). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-blink-for-provisioning"></a>Nastavení blikání pro zřizování

1. Zaprotokolujte [případ podpory](https://support.joinblink.com) nebo **podporu blikání** e-mailu na adrese support@joinblink.com a vyžádejte si token SCIM.

2.  Zkopírujte **token ověřování SCIM**. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování vaší aplikace blikání v Azure Portal.

## <a name="add-blink-from-the-gallery"></a>Přidat blikání z Galerie

Před konfigurací blikání pro Automatické zřizování uživatelů pomocí Azure AD je nutné přidat blikání z Galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat blikání z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **blikající**, na panelu výsledků vyberte **blikající** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Blikání v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>Konfigurace automatického zřizování uživatelů ke blikání 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů při blikání na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro blikání jednotné přihlašování založené na SAML, a to podle pokynů uvedených v [kurzu jednotného přihlašování pro blikání](./blink-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce vzájemně přidávají.

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro blikání v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **blikat**.

    ![Odkaz na bliknutí v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://api.joinblink.com/scim` **adresu URL tenanta**. Zadejte hodnotu **SCIM tokenu ověřování** získanou dříve v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit ke blikání. Pokud se připojení nepovede, zajistěte, aby měl váš účet blikání oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelů ke blikání**.

    ![Blikání – mapování uživatelů](media/blink-provisioning-tutorial/User_mappings.png)

9. Kontrola atributů uživatele synchronizovaných z Azure AD pro blikání v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů ve blikat pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|Podporováno pro filtrování|
   |---|---|---|
   |userName|Řetězec|&check;|
   |active|Logická hodnota|
   |title|Řetězec|
   |emails[type eq "work"].value|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |phoneNumbers[type eq "work"].value|Řetězec|
   |phoneNumbers[type eq "mobile"].value|Řetězec|
   |externalId|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Reference|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Blink: 2.0: uživatel: společnost|Řetězec|
   urn: IETF: parametry: SCIM: schémata: rozšíření: Blink: 2.0: uživatel: Popis|Řetězec|
   urn: IETF: parametry: SCIM: schémata: rozšíření: Blink: 2.0: uživatel: umístění|Řetězec|

10. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pokud chcete povolit službu Azure AD Provisioning pro blikání, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

12. Určete uživatele, které chcete zařadit do blikání, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD při blikání.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorování nasazení
Po dokončení konfigurace zřizování můžete své nasazení monitorovat pomocí následujících prostředků:

* S využitím [protokolů zřizování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) můžete zjistit, kteří uživatelé se zřídili úspěšně a kteří neúspěšně.
* Pokud chcete zjistit, jaký je stav cyklu zřizování a jak blízko je dokončení, zkontrolujte [indikátor průběhu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).
* Pokud se zdá, že konfigurace zřizování není v pořádku, aplikace přejde do karantény. Další informace o stavech karantény najdete [tady](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  


## <a name="change-log"></a>Protokol změn

* 01/14/2021 – Přidali jsme vlastní atributy rozšíření **Společnost**, **Popis** a **umístění** .

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../manage-apps/check-status-user-account-provisioning.md).
