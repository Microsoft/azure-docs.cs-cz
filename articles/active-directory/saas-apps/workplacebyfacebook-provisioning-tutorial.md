---
title: 'Kurz: Konfigurace síti na pracovišti ve službě Facebook pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a síti na pracovišti ve službě Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 40eee26e1cc685576c08a6bfef0fbe152916090e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35904041"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Kurz: Konfigurace síti na pracovišti ve službě Facebook pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v síti na pracovišti Facebook a Azure AD a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD k firemní síti pomocí sítě Facebook.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se na pracovišti ve službě Facebook, potřebujete následující položky:

- Předplatné služby Azure AD
- Firemní síti pomocí sítě Facebook jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Přiřazování uživatelů k síti na pracovišti ve službě Facebook.

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu se synchronizují pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování služby, musíte rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k pracovní ploše aplikace Facebook. Jakmile se rozhodli, můžete přiřadit těmto uživatelům k pracovní ploše aplikace Facebook podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Důležité tipy pro přiřazování uživatelů k síti na pracovišti ve službě Facebook.

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazena k síti na pracovišti ve službě Facebook otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k firemní síti pomocí sítě Facebook, musíte vybrat platné uživatelské role. Roli "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojení k síti na pracovišti uživatelský účet na Facebooku pro zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakažte přiřazené uživatelské účty v síti na pracovišti ve službě Facebook podle přiřazení uživatelů a skupin ve službě Azure AD.

>[!Tip]
>Můžete také povolit na základě SAML jednotného přihlašování k síti na pracovišti ve službě Facebook, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Konfigurace účtu zřizování uživatelů k firemní síti pomocí sítě Facebook v Azure AD:

Cílem této části se popisují postup povolení zřizování uživatelských účtů služby Active Directory k firemní síti pomocí sítě Facebook.

Azure AD podporuje možnost automaticky synchronizovat Podrobnosti účtu přiřazené uživatelů k firemní síti pomocí sítě Facebook. Toto automatické synchronizace umožňuje síti na pracovišti ve službě Facebook se získat data, je nutné autorizovat uživatele pro přístup, než je pokus o přihlášení za poprvé. Také zrušte technologii uživatelům v síti na pracovišti ve službě Facebook při odvolání přístupu ve službě Azure AD.

1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** části.

2. Pokud jste již nakonfigurovali síti na pracovišti ve službě Facebook pro jednotné přihlašování, vyhledejte instanci síti na pracovišti ve službě Facebook pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **síti na pracovišti ve službě Facebook** v galerii aplikací. Vyberte síti na pracovišti ve službě Facebook ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci síti na pracovišti ve službě Facebook a pak vyberte **zřizování** kartě.

4. Nastavte **režimu zřizování** k **automatické**. 

    ![zřizování](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. V části **přihlašovací údaje správce** části, zadejte Token přístupu ze svého pracoviště správcem sítě Facebook a nastavte hodnotu URL klienta `https://www.facebook.com/scim/v1/` . Podívejte na tyto [pokyny](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) na vytváření Token k přístupu k síti na pracovišti. 

6. Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k pracovní ploše aplikace Facebook. Pokud se nepovede připojit, zajistěte, aby že vaše pracoviště Facebook účet má oprávnění správce týmu.

7. Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole a zaškrtnutím políčka.

8. Klikněte na tlačítko **uložit.**

9. V části mapování vyberte **synchronizaci Azure Active Directory Users k firemní síti pomocí sítě Facebook.**

10. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizovány z Azure AD k firemní síti pomocí sítě Facebook. Atributy vybrán jako **párování** vlastnosti jsou používány tak, aby odpovídaly uživatelské účty v síti na pracovišti Facebook pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

11. Povolit Azure AD zřizování služby pro pracoviště ve službě Facebook, změňte **Stav zřizování** k **na** v **nastavení** části

12. Klikněte na tlačítko **uložit.**

Další informace o tom, jak nakonfigurovat automatické zřizování najdete v tématu [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Nyní můžete vytvořit testovací účet. Chcete-li ověřit, že účet byl synchronizován k síti na pracovišti ve službě Facebook Počkejte až 20 minut.

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](workplacebyfacebook-tutorial.md)
