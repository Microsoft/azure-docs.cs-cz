---
title: 'Kurz: Konfigurace LucidChart pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do LucidChart.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9e037e1be36a38631028e5e179e20720d3476a1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190853"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Kurz: Konfigurace LucidChart pro automatické zřizování uživatelů


Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést LucidChart a Azure AD a automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do LucidChart. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory
*   LucidChart tenantovi se [podnikový plán](https://www.lucidchart.com/user/117598685#/subscriptionLevel) nebo lépe povoleno 
*   Uživatelský účet v LucidChart s oprávněními správce 

## <a name="assigning-users-to-lucidchart"></a>Přiřazování uživatelů k LucidChart

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD, kteří potřebují přístup k vaší aplikaci LucidChart představují. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace LucidChart podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Důležité tipy pro přiřazování uživatelů k LucidChart

*   Dále je doporučeno jednoho uživatele Azure AD, je přiřazená LucidChart k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k LucidChart, je nutné vybrat buď **uživatele** roli, nebo jinou platnou specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. **Výchozího přístupu k** role nefunguje pro zřizování a tito uživatelé se přeskočí.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurace zřizování uživatelů pro LucidChart 

Tato část vás provede připojením služby Azure AD vaší LucidChart uživatelského účtu rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v LucidChart podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také pro LucidChart povoleno založené na SAML jednotného přihlašování, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů do LucidChart ve službě Azure AD


1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste už nakonfigurovali LucidChart pro jednotné přihlašování, vyhledejte svoji instanci služby LucidChart pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **LucidChart** v galerii aplikací. Ve výsledcích hledání vyberte LucidChart a přidat do seznamu aplikací.

3. Vyberte instanci LucidChart a potom **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**.

    ![LucidChart zřizování](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. V části **přihlašovacích údajů správce** části, zadejte **tajný klíč tokenu** generovaných LucidChart váš účet (token, který můžete najít v rámci vašeho účtu: **Tým** > **integraci aplikací** > **SCIM**). 

    ![LucidChart zřizování](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci LucidChart. Pokud se nepovede, ujistěte se, že váš účet LucidChart má oprávnění správce a opakujte krok 5.

7. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko "Odeslat e-mailové oznámení, když dojde k chybě."

8. Klikněte na **Uložit**. 

9. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům LucidChart**.

10. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do LucidChart. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v LucidChart pro operace update. Vyberte tlačítko Uložit potvrďte změny.

11. Služba pro LucidChart zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu

12. Klikněte na **Uložit**. 

Tato operace spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k LucidChart v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
