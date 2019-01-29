---
title: 'Kurz: Konfigurace síti na pracovišti ve službě Facebook pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a síti na pracovišti ve službě Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 6544143feccb3fe24460d6f8a369b241181b5646
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194504"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Kurz: Konfigurace síti na pracovišti ve službě Facebook pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v síti na pracovišti Facebooku a Azure AD, aby automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD k síti na pracovišti ve službě Facebook.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s síti na pracovišti ve službě Facebook, potřebujete následující položky:

- Předplatné Azure AD
- Firemní síti pomocí Facebooku jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Přiřazování uživatelů k síti na pracovišti ve službě Facebook.

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší síti na pracovišti aplikace pro Facebook. Jakmile se rozhodli, můžete přiřadit tito uživatelé k pracovní ploše aplikace pro Facebook podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Důležité tipy pro přiřazování uživatelů k síti na pracovišti ve službě Facebook.

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazena k síti na pracovišti ve službě Facebook, k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Když přiřadíte uživatele k síti na pracovišti ve službě Facebook, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojení služby Azure AD k síti na pracovišti, když uživatelský účet na Facebooku rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v síti na pracovišti ve službě Facebook na základě uživatele a skupiny přiřazení ve službě Azure AD.

>[!Tip]
>Můžete také povolit založené na SAML jednotného přihlašování k síti na pracovišti ve službě Facebook, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Postup konfigurace zřizování uživatelských účtů k síti na pracovišti ve službě Facebook ve službě Azure AD:

Cílem této části se popisují, jak povolit zřizování uživatelských účtů služby Active Directory k síti na pracovišti ve službě Facebook.

Azure AD podporuje možnost automatickou synchronizaci účtu podrobnosti o přiřazení uživatelé k síti na pracovišti ve službě Facebook. Tato automatická synchronizace umožňuje síti na pracovišti ve službě Facebook. Chcete-li získat data, která potřebuje k autorizaci uživatelů pro přístup, než je pokus o přihlášení prvním. Také zrušení zřizuje uživatele v síti na pracovišti ve službě Facebook při odvolal(a) přístup ve službě Azure AD.

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory** > **podnikové aplikace** > **všechnyaplikace** oddílu.

2. Pokud jste už nakonfigurovali síti na pracovišti ve službě Facebook pro jednotné přihlašování, hledání instance této síti na pracovišti ve službě Facebook, pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **síti na pracovišti ve službě Facebook** v galerii aplikací. Ve výsledcích hledání vyberte síti na pracovišti tak Facebook a přidat do seznamu aplikací.

3. Vyberte instanci pracoviště pomocí Facebooku a potom **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**. 

    ![zřizování](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. V části **přihlašovacích údajů správce** části, zadejte Token přístupu ze svého pracoviště správcem sítě Facebook a nastavte hodnotu adresy URL Tenanta `https://www.facebook.com/scim/v1/` . Informace najdete v těchto [pokyny](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) týkající se vytvoření Token k přístupu k síti na pracovišti. 

6. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD se můžete připojit k pracovní ploše pomocí aplikace Facebook. Pokud se nepovede, zajistěte, aby že pracovišti ve Facebookový účet má oprávnění správce týmu.

7. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko.

8. Klikněte na tlačítko **uložit.**

9. V oddílu mapování, vyberte **synchronizace Azure Active Directory Users k síti na pracovišti ve službě Facebook.**

10. V **mapování atributů** , projděte si atributy uživatele, které jsou synchronizovány ze služby Azure AD k firemní síti pomocí sítě Facebook. Atributy vybrané jako **odpovídající** vlastnosti jsou používány tak, aby odpovídaly uživatelské účty v síti na pracovišti Facebooku pro operace update. Vyberte tlačítko Uložit potvrďte změny.

11. Služba pro pracoviště ve službě Facebook zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu

12. Klikněte na tlačítko **uložit.**

Další informace o tom, jak nakonfigurovat automatické zřizování najdete v tématu [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Nyní můžete vytvořit zkušební účet. Počkejte, až 20 minut, než k ověření, že účet byl synchronizován k síti na pracovišti ve službě Facebook.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](workplacebyfacebook-tutorial.md)
