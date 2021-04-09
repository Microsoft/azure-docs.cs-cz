---
title: 'Kurz: Konfigurace myPolicies pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro myPolicies.
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
ms.openlocfilehash: 221f63ab9a7eb3f71a4c730a11565dda64c9edc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353579"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Kurz: Konfigurace myPolicies pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v myPolicies a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro myPolicies.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant myPolicies](https://mypolicies.com/index.html#section10)
* Uživatelský účet v myPolicies s oprávněními správce.

## <a name="assigning-users-to-mypolicies"></a>Přiřazování uživatelů k myPolicies

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k myPolicies. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k myPolicies podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Důležité tipy pro přiřazení uživatelů k myPolicies

* Doporučuje se, aby se k myPolicies k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k myPolicies musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-mypolicies-for-provisioning"></a>Nastavení myPolicies pro zřizování

Před konfigurací myPolicies pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na myPolicies.

1. Kontaktujte svého zástupce myPolicies a **support@mypolicies.com** Získejte tajný token potřebný ke konfiguraci zřizování SCIM.

2.  Uložte hodnotu tokenu poskytnutou myPolicies zástupcem. Tato hodnota se zadá do pole **token tajného** kódu na kartě zřizování vaší aplikace myPolicies ve Azure Portal.

## <a name="add-mypolicies-from-the-gallery"></a>Přidání myPolicies z Galerie

Pokud chcete nakonfigurovat myPolicies pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat myPolicies z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat myPolicies z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **myPolicies**, na panelu výsledků vyberte **myPolicies** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![myPolicies v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Konfigurace automatického zřizování uživatelů na myPolicies 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v myPolicies na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro myPolicies podle pokynů uvedených v [kurzu MyPolicies jednotného přihlašování](mypolicies-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro myPolicies ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **myPolicies**.

    ![Odkaz myPolicies v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://<myPoliciesCustomDomain>.mypolicies.com/scim` **adresu URL tenanta** , kde `<myPoliciesCustomDomain>` se nachází vaše myPolicies vlastní doména. Z adresy URL můžete načíst svoji doménu zákazníka myPolicies.
Příklad: `<demo0-qa>` . mypolicies.com.

6. V části **tajný token** zadejte hodnotu tokenu, která byla načtena dříve. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k myPolicies. Pokud se připojení nepovede, ujistěte se, že má váš účet myPolicies oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

7. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé myPolicies**.

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="Snímek obrazovky oddílu mapování V části název je synchronizace Azure Active Directory uživatelů na customappsso viditelná." border="false":::

10. Zkontrolujte atributy uživatele synchronizované z Azure AD do myPolicies v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v myPolicies pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

   |Atribut|Typ|
   |---|---|
   |userName|Řetězec|
   |active|Logická hodnota|
   |emails[type eq "work"].value|Řetězec|
   |name.givenName|Řetězec|
   |name.familyName|Řetězec|
   |název. formátovaný|Řetězec|
   |externalId|Řetězec|
   |adresy [typ EQ "Work"]. Country|Řetězec|
   |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager|Referenční informace|


11. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pokud chcete povolit službu Azure AD Provisioning pro myPolicies, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

13. Definujte uživatele nebo skupiny, které chcete zřídit pro myPolicies, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

14. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v myPolicies.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* myPolicies vždy vyžaduje **uživatelské jméno**, **e-mail** a **externalId**.
* myPolicies nepodporuje pevné odstranění atributů uživatele.

## <a name="change-log"></a>Protokol změn

* 09/15/2020 – pro uživatele byla přidána podpora pro atribut Country.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
