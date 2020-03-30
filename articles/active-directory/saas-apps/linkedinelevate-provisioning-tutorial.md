---
title: 'Kurz: Zřizování uživatelů pro LinkedIn Elevate – Azure AD'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty na LinkedIn Elevate.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0a26eaeac431ed2c78c5bd938bbbe7dff14e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057409"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Kurz: Konfigurace linkedinu Elevate pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v LinkedIn Elevate a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD na LinkedIn Elevate.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Tenanta Azure Active Directory.
* Tenant LinkedIn Elevate
* Účet správce na LinkedIn Elevate s přístupem k Centru účtů LinkedIn

> [!NOTE]
> Azure Active Directory se integruje s LinkedIn Elevate pomocí protokolu [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-elevate"></a>Přiřazení uživatelů ke zvýšení oprávnění LinkedIn

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů budou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se budete muset rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup ke službě LinkedIn Elevate. Jakmile se rozhodnete, můžete přiřadit tyto uživatele k LinkedIn Elevate podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Důležité tipy pro přiřazení uživatelů na LinkedIn Elevate

* Doporučuje se, aby jeden uživatel Azure AD přiřazena LinkedIn Elevate k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke zvýšení oprávnění LinkedIn je nutné vybrat roli **uživatele** v dialogovém okně přiřazení. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurace zřizování uživatelů na LinkedIn Elevate

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů SCIM společnosti LinkedIn Elevate a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů na LinkedIn Elevate na základě přiřazení uživatelů a skupin na základě přiřazení uživatelů a skupin ve službě Azure AD.

**Tip:** Můžete se také rozhodnout, že chcete povolit jednotné přihlašování na základě SAML pro LinkedIn Elevate podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na LinkedIn Elevate ve službě Azure AD:

Prvním krokem je načtení přístupového tokenu LinkedIn. Pokud jste správce rozlehlé sítě, můžete zřídit přístupový token sami. V centru účtů přejděte do **globálního nastavení nastavení &gt; ** a otevřete panel Nastavení **SCIM.**

> [!NOTE]
> Pokud přistupujete k centru účtů přímo, nikoli prostřednictvím odkazu, můžete se k němu dostat pomocí následujících kroků.

1. Přihlaste se do Centra účtů.

2. Vyberte **Nastavení &gt; správce** .

3. Na levém postranním panelu klikněte na **Rozšířené integrace.** Budete přesměrováni do centra účtů.

4. Klikněte na **+ Přidat novou konfiguraci SCIM** a postupujte podle postupu vyplněním každého pole.

    > [!NOTE]
    > Pokud není povoleno automatické přiřazování licencí, znamená to, že jsou synchronizována pouze uživatelská data.

    ![LinkedIn zvýšit zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Pokud je povoleno přiřazení automatických licencí, je třeba poznamenat instanci aplikace a typ licence. Licence jsou přiřazovány podle toho, kdo dřív přijde, je dřív na řadě, dokud nebudou všechny licence odebrány.

    ![LinkedIn zvýšit zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Klepněte na **tlačítko Generovat token**. V poli **tokenu přístupu** by se měl zobrazit váš přístupový token.

6. Před opuštěním stránky uložte přístupový token do schránky nebo počítače.

7. Dále se přihlaste k [portálu Azure](https://portal.azure.com)a přejděte do části **Azure Active Directory > Enterprise Apps > Všechny aplikace.**

8. Pokud jste již nakonfigurovali LinkedIn Elevate pro jednotné přihlašování, vyhledejte svou instanci LinkedIn Elevate pomocí vyhledávacího pole. V opačném případě vgalerii aplikací vyberte **Přidat** a vyhledejte **LinkedIn Elevate.** Ve výsledcích hledání vyberte LinkedIn Elevate a přidejte je do seznamu aplikací.

9. Vyberte svou instanci LinkedIn Elevate a pak vyberte kartu **Zřizování.**

10. Nastavte **režim zřizování** na **automatické**.

    ![LinkedIn zvýšit zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Vyplňte následující pole v části **Přihlašovací údaje správce** :

    * Do pole Adresa URL `https://api.linkedin.com` **klienta** zadejte .

    * Do pole **Tajný token** zadejte přístupový token, který jste vygenerovali v kroku 1, a klepněte na tlačítko **Testovat připojení** .

    * Oznámení o úspěchu byste měli vidět na pravé horní straně portálu.

12. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko níže.

13. Klikněte na **Uložit**.

14. V části **Mapování atributů** zkontrolujte atributy uživatele a skupiny, které budou synchronizovány z Azure AD na LinkedIn Elevate. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity tak, aby odpovídaly uživatelským účtům a skupinám v LinkedIn Elevate pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

    ![LinkedIn zvýšit zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Chcete-li povolit službu zřizování Azure AD pro LinkedIn Elevate, změňte **stav zřizování** **na Zapnuto** v části **Nastavení**

16. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů a/nebo skupin přiřazených linkedinu Elevate v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci LinkedIn Elevate.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
