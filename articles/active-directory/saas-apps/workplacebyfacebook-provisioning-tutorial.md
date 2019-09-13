---
title: 'Kurz: Konfigurace pracoviště na Facebooku pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a na pracovišti pomocí Facebooku.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f040ff4c8e59f764676aa6fdd9460ec94641684a
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881795"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Kurz: Konfigurace pracoviště na Facebooku pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést na pracovišti z Facebooku a Azure AD, a automaticky zřizovat a zrušit zřizování uživatelských účtů z Azure AD až po Facebook.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s pracovištěm na Facebooku, budete potřebovat následující položky:

- Předplatné Azure AD
- Pracoviště s povoleným jednorázovým přihlášením na Facebooku

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [si ho stáhnout](https://azure.microsoft.com/pricing/free-trial/)na měsíc zdarma.

## <a name="assigning-users-to-workplace-by-facebook"></a>Přiřazení uživatelů k pracovišti pomocí Facebooku

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte určit, kteří uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k vašemu pracovišti pomocí aplikace Facebook. Až se rozhodnete, můžete těmto uživatelům přiřadit aplikaci na Facebooku pomocí následujících pokynů:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Důležité tipy pro přiřazení uživatelů k pracovišti na Facebooku

*   Doporučuje se, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD k pracovní ploše na Facebooku. Další uživatele a skupiny můžete přiřadit později.

*   Když přiřadíte uživatele k pracovišti na Facebooku, musíte vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojením služby Azure AD k pracovišti pomocí rozhraní API zřizování uživatelských účtů na Facebooku a konfigurací zřizovací služby k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů na pracovišti pomocí Facebooku na základě uživatelů a skupin. přiřazení ve službě Azure AD.

>[!Tip]
>Můžete se také rozhodnout, že povolíte jednotné přihlašování založené na SAML na pracovišti na Facebooku, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Konfigurace zřizování uživatelských účtů na pracovišti pomocí Facebooku ve službě Azure AD:

Cílem této části je pořídit, jak na Facebooku povolit zřizování uživatelských účtů služby Active Directory na pracovišti.

Azure AD podporuje možnost automaticky synchronizovat podrobnosti účtu přiřazených uživatelů na pracovišti pomocí Facebooku. Tato automatická synchronizace umožňuje pracovní ploše na Facebooku získat data, která potřebuje k autorizaci uživatelů pro přístup, předtím, než se pokusí o přihlášení poprvé. Také odzřídí uživatele z pracoviště na Facebooku, když se v Azure AD odvolá přístup.

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** .

2. Pokud jste už nakonfigurovali pracoviště na Facebooku pro jednotné přihlašování, vyhledejte vaši instanci pracoviště pomocí Facebooku pomocí vyhledávacího pole. V opačném případě vyberte v galerii aplikací možnost **Přidat** a vyhledat **pracovní plochu na Facebooku** . Vyberte možnost pracovní plocha z výsledků hledání z Facebooku a přidejte ji do seznamu aplikací.

3. Vyberte svou instanci pracoviště na Facebooku a pak vyberte kartu **zřizování** .

4. Nastavte **režim zřizování** na **automaticky**. 

    ![zřizování](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. V části **přihlašovací údaje správce** zadejte přístupový token z pracoviště správcem Facebooku a nastavte hodnotu adresy URL tenanta na `https://www.facebook.com/scim/v1/` . Podívejte se na tyto [pokyny](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) k vytvoření přístupového tokenu pro práci na pracovišti. 

6. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se Azure AD může připojit k vašemu pracovišti pomocí aplikace Facebook. Pokud se připojení nepovede, ujistěte se, že váš pracovní prostor na Facebooku má oprávnění správce týmu.

7. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko.

8. Klikněte na **Uložit.**

9. V části mapování vyberte možnost **synchronizovat Azure Active Directory uživatelů na pracovišti pomocí Facebooku.**

10. V části **mapování atributů** zkontrolujte atributy uživatelů, které jsou synchronizované z Azure AD, do pracoviště pomocí Facebooku. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů na pracovišti pomocí Facebooku pro operace aktualizace. Vyberte tlačítko Uložit potvrďte změny.

11. Pokud chcete povolit službu zřizování Azure AD pro pracovní plochu na Facebooku, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

12. Klikněte na **Uložit.**

Další informace o tom, jak nakonfigurovat automatické zřizování, najdete v tématu.[https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Nyní můžete vytvořit testovací účet. Počkejte až 20 minut, než ověříte, že je účet synchronizovaný na pracovišti pomocí Facebooku.

> [!NOTE]
> Úzce spolupracujeme s pracovištěm na Facebooku, abychom zajistili, že aplikace Azure AD je schválená a splňuje nové pokyny.   

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](workplacebyfacebook-tutorial.md)
