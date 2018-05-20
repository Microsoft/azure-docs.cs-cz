---
title: 'Kurz: Konfigurace LucidChart pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do LucidChart.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: ccc876b2421641271e73ad72ec0c00a10792f8b3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Kurz: Konfigurace LucidChart pro zřizování automatické uživatelů


Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v LucidChart a Azure AD a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do LucidChart. 

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klienta služby Azure Active directory
*   LucidChart klienta s [plánu podnikového](https://www.lucidchart.com/user/117598685#/subscriptionLevel) nebo lépe povolena. 
*   Uživatelský účet v LucidChart s oprávněními správce 

## <a name="assigning-users-to-lucidchart"></a>Přiřazení uživatelů k LucidChart

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu se synchronizují pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Před konfigurací a povolení zřizování služby, musíte rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci LucidChart. Jakmile se rozhodli, můžete přiřadit těmto uživatelům aplikace LucidChart podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Důležité tipy pro přiřazování uživatelů do LucidChart

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke LucidChart a otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k LucidChart, je nutné vybrat buď **uživatele** role nebo jinou platnou specifické pro aplikaci rolí (Pokud je k dispozici) v dialogovém okně přiřazení. **Výchozího přístupu k** role nefunguje pro zřizování a tito uživatelé se přeskočí.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfiguraci zřizování uživatelů k LucidChart 

Tato část vás provede připojení k LucidChart na uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakažte přiřazené uživatelské účty v LucidChart podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také pro LucidChart povoleno na základě SAML jednotné přihlašování, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurace automatického účet zřizování uživatelů k LucidChart ve službě Azure AD


1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste již nakonfigurovali LucidChart pro jednotné přihlašování, vyhledejte instanci LucidChart pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **LucidChart** v galerii aplikací. Vyberte LucidChart ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci LucidChart a pak vyberte **zřizování** kartě.

4. Nastavte **režimu zřizování** k **automatické**.

    ![Zřizování LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart1.png)

5. V části **přihlašovací údaje správce** části, zadejte **tajný klíč tokenu** generované vaší LucidChart účtu (token můžete najít v rámci vašeho účtu: **Team** > **integrace aplikací** > **SCIM**). 

    ![Zřizování LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart2.png)

6. Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k aplikaci LucidChart. Pokud se nepovede připojit, zajistěte, aby byl váš účet LucidChart oprávnění správce a opakujte krok 5.

7. Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole a zaškrtněte políčko "Odesílat e-mailové oznámení, pokud dojde k chybě."

8. Klikněte na **Uložit**. 

9. V části mapování vyberte **synchronizaci Azure Active Directory uživatelům LucidChart**.

10. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizované z Azure AD LucidChart. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v LucidChart pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

11. Povolit zřizování služby pro LucidChart Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části

12. Klikněte na **Uložit**. 

Tato operace spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k LucidChart v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud se službou provést. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity, které popisují všechny akce prováděné při zřizování služby.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](active-directory-enterprise-apps-manage-provisioning.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](active-directory-saas-provisioning-reporting.md)
