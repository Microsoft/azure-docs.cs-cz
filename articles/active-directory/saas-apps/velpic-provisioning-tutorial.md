---
title: 'Kurz: Konfigurace VELPIC pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro VELPIC.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: fbed4b888c48a518d9f10a91ff0494aa7bdc1843
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88532360"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Kurz: Konfigurace VELPIC pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v VELPIC a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až VELPIC.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenanta Azure Active Directory.
* Tenant VELPIC s [plánem Enterprise](https://www.velpic.com/pricing.html) nebo lepším povoleným
* Uživatelský účet v VELPIC s oprávněními správce

## <a name="assigning-users-to-velpic"></a>Přiřazování uživatelů k VELPIC

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů budou synchronizováni pouze uživatelé a skupiny, které byly přiřazeny do aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte službu zřizování, budete se muset rozhodnout, co můžou uživatelé a skupiny v Azure AD zastupovat s uživateli, kteří potřebují přístup k vaší aplikaci VELPIC. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace VELPIC podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Důležité tipy pro přiřazení uživatelů k VELPIC

* Doporučuje se, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD VELPIC. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k VELPIC, musíte vybrat buď roli **uživatele** , nebo jinou platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Všimněte si, že **výchozí role přístupu** nefunguje pro zřizování, a tito uživatelé se přeskočí.

## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurace zřizování uživatelů na VELPIC

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v VELPIC na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro VELPIC jednu Sign-On založenou na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na VELPIC ve službě Azure AD:

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace**  .

2. Pokud jste už nakonfigurovali VELPIC pro jednotné přihlašování, vyhledejte vaši instanci VELPIC pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **VELPIC** v galerii aplikací. Ve výsledcích hledání vyberte VELPIC a přidejte je do seznamu aplikací.

3. Vyberte svou instanci VELPIC a pak vyberte kartu **zřizování** .

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Zřizování VELPIC](./media/velpic-provisioning-tutorial/Velpic1.png)

5. V části **přihlašovací údaje správce** zadejte **adresu URL klienta&tajného tokenu** VELPIC. (Tyto hodnoty najdete pod účtem VELPIC: **Správa**  >  **Integrace**  >  **Modul plug-in**  >  **SCIM**)

    ![Autorizační hodnoty](./media/velpic-provisioning-tutorial/Velpic2.png)

6. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci VELPIC. Pokud se připojení nepovede, ujistěte se, že má váš účet VELPIC oprávnění správce, a zkuste krok 5 znovu.

7. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko níže.

8. Klikněte na **Uložit**.

9. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé VELPIC**.

10. V části **mapování atributů** zkontrolujte atributy uživatele, které se budou synchronizovat z Azure AD do VELPIC. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity ke spárování uživatelských účtů v VELPIC pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

11. Pokud chcete povolit službu Azure AD Provisioning pro VELPIC, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

12. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených VELPIC v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než další synchronizace, ke kterým dojde přibližně každých 40 minut, pokud je služba spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následné odkazy na sestavy aktivit zřizování, které popisují všechny akce prováděné službou zřizování.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).