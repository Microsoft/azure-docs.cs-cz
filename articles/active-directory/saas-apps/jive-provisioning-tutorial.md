---
title: 'Kurz: Konfigurace Jive pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Seznamte se s kroky, které je třeba provést v Jive a Azure AD pro automatické zřízení a zrušení zřízení uživatelských účtů z Azure AD až Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: fa45ece76499b50694d366ed2dcdd57cc1e0c7dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312092"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Kurz: Konfigurace Jive pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v Jive a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až Jive.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

*   Tenant Azure Active Directory.
*   Předplatné s povoleným jedním přihlašováním (Jive).
*   Uživatelský účet v Jive s oprávněními správce týmu.

## <a name="assigning-users-to-jive"></a>Přiřazování uživatelů k Jive

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci Jive. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace Jive podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Důležité tipy pro přiřazení uživatelů k Jive

*   Doporučuje se, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD Jive. Další uživatele a skupiny můžete přiřadit později.

*   Při přiřazování uživatele k Jive je nutné vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v Jive na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Jive, a to podle pokynů uvedených v tématu [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je vysvětlit, jak povolit uživatelům zřizování uživatelských účtů služby Active Directory pro Jive.
V rámci tohoto postupu je potřeba poskytnout token zabezpečení uživatele, který potřebujete pro vyžádání z Jive.com.

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

1. Pokud jste už nakonfigurovali Jive pro jednotné přihlašování, vyhledejte vaši instanci Jive pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Jive** v galerii aplikací. Ve výsledcích hledání vyberte Jive a přidejte je do seznamu aplikací.

1. Vyberte svou instanci Jive a pak vyberte kartu **zřizování** .

1. Nastavte **Režim zřizování** na hodnotu **Automaticky**. 

    ![Snímek obrazovky se zobrazí stránka zřizování Jive s režimem zřizování nastaveným na automatické a jiné hodnoty, které můžete nastavit.](./media/jive-provisioning-tutorial/provisioning.png)

1. V části **přihlašovací údaje správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **uživatelské jméno správce Jive** zadejte název účtu Jive, který má přiřazený profil **správce systému** v Jive.com.
   
    b. Do textového pole **heslo správce Jive** zadejte heslo pro tento účet.
   
    c. Do textového pole **Adresa URL tenanta Jive** zadejte adresu URL tenanta Jive.
      
      > [!NOTE]
      > Adresa URL tenanta Jive je adresa URL, kterou vaše organizace používá k přihlášení k Jive.  
      > Adresa URL má obvykle následující formát: **www. \<organization\> . jive.com**.          

1. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci Jive.

1. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko níže.

1. Klikněte na **Uložit.**

1. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé Jive.**

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do Jive. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Jive pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

1. Pokud chcete povolit službu Azure AD Provisioning pro Jive, změňte **stav zřizování** na **zapnuto** v části nastavení.

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených Jive v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování ve vaší aplikaci Jive.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](jive-tutorial.md)
