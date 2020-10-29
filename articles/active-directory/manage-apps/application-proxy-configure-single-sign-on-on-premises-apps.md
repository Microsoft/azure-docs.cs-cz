---
title: Jednotné přihlašování SAML pro místní aplikace s Aplikace Azure AD proxy
description: Naučte se, jak zajistit jednotné přihlašování k místním aplikacím, které jsou zabezpečené pomocí ověřování SAML. Poskytněte vzdálený přístup k místním aplikacím pomocí proxy aplikací.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90838b0b613c043ae41a71c76b5e9023d21df3a6
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93025846"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Jednotné přihlašování SAML pro místní aplikace s proxy aplikací

Můžete poskytnout jednotné přihlašování (SSO) k místním aplikacím, které jsou zabezpečené pomocí ověřování SAML, a poskytovat vzdálený přístup k těmto aplikacím prostřednictvím proxy aplikací. Při jednotném přihlašování SAML se Azure Active Directory (Azure AD) ověřuje v aplikaci pomocí účtu Azure AD uživatele. Azure AD komunikuje s přihlašovacími informacemi k aplikaci prostřednictvím protokolu připojení. Uživatele můžete také namapovat na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML. Povolením proxy aplikací kromě jednotného přihlašování SAML budou mít uživatelé externí přístup k aplikaci a bezproblémové možnosti jednotného přihlašování.

Aplikace musí být schopné využívat tokeny SAML vydané **Azure Active Directory** . Tato konfigurace se nevztahuje na aplikace, které používají místní zprostředkovatele identity. V těchto scénářích doporučujeme zkontrolovat [prostředky pro migraci aplikací do Azure AD](migration-resources.md).

Jednotné přihlašování SAML s proxy aplikací funguje i s funkcí šifrování tokenu SAML. Další informace najdete v tématu [Konfigurace šifrování tokenů SAML v Azure AD](howto-saml-token-encryption.md).

Níže uvedené diagramy protokolu popisují posloupnost jednotného přihlašování pro tok inicializovaný poskytovatelem služeb (iniciované v rámci SP) i pro tok iniciované poskytovatelem identity (IdP). Proxy aplikace spolupracuje se službou SAML SSO tím, že ukládá požadavek SAML a odpověď do místní aplikace do a z ní.

  ![Diagram znázorňuje interakce aplikace, proxy aplikace, klienta a Azure A D pro jednotné přihlašování S Iniciováným voláním P.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Diagram znázorňuje interakce aplikace, proxy aplikace, klienta a Azure A D pro jednotné přihlašování, které bylo iniciováno v P/v.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Vytvoření aplikace a nastavení jednotného přihlašování SAML

1. V Azure Portal vyberte **Azure Active Directory > podnikové aplikace** a vyberte **Nová aplikace** .

2. Zadejte zobrazovaný název pro novou aplikaci, vyberte možnost **integrace jakékoli jiné aplikace, kterou v galerii nenajdete** , a pak vyberte **vytvořit** .

3. Na stránce **Přehled** aplikace vyberte **jednotné přihlašování** .

4. Jako metodu jednotného přihlašování vyberte **SAML** .

5. Nejdřív nastavte jednotné přihlašování SAML pro práci v podnikové síti, v části základní konfigurace SAML v tématu [Konfigurace jednotného přihlašování založeného na](configure-saml-single-sign-on.md) SAML pro konfiguraci ověřování založeného na SAML pro aplikaci.

6. Přidejte do aplikace alespoň jednoho uživatele a ujistěte se, že testovací účet má přístup k aplikaci. Když jste připojení k podnikové síti, použijte testovací účet a zjistěte, jestli máte k aplikaci jednotné přihlašování. 

   > [!NOTE]
   > Po nastavení proxy aplikace se vrátíte zpátky a aktualizujete **adresu URL odpovědi** SAML.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publikování místní aplikace pomocí proxy aplikací

Před poskytnutím jednotného přihlašování pro místní aplikace je potřeba povolit proxy aplikace a nainstalovat konektor. Podívejte se na kurz [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure AD](application-proxy-add-on-premises-application.md) , kde se dozvíte, jak připravit místní prostředí, jak nainstalovat a zaregistrovat konektor a otestovat konektor. Pak pomocí těchto kroků publikujte novou aplikaci s využitím proxy aplikací. Další nastavení, která nejsou uvedená níže, najdete v části [Přidání místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) v tomto kurzu.

1. Když je aplikace v Azure Portal stále otevřená, vyberte **proxy aplikace** . Zadejte **interní adresu URL** pro aplikaci. Pokud používáte vlastní doménu, budete také muset nahrát certifikát TLS/SSL pro vaši aplikaci. 
   > [!NOTE]
   > Osvědčeným postupem je použití vlastních domén, kdykoli je to možné pro optimalizované uživatelské prostředí. Přečtěte si další informace o [práci s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).

2. Jako metodu **předběžného ověření** pro aplikaci vyberte **Azure Active Directory** .

3. Zkopírujte **externí adresu URL** pro aplikaci. Tuto adresu URL budete potřebovat k dokončení konfigurace SAML.

4. Pomocí testovacího účtu se pokuste otevřít aplikaci s **externí adresou URL** a ověřit, jestli je správně nastavená proxy aplikace. Pokud se vyskytnou problémy, přečtěte si téma [řešení problémů se službou Application proxy a chybovými zprávami](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Aktualizace konfigurace SAML

1. Když je aplikace v Azure Portal stále otevřená, vyberte **jednotné přihlašování** . 

2. Na stránce **nastavit jeden Sign-On se** stránkou SAML přejdete do **základního záhlaví konfigurace SAML** a vyberete jeho ikonu pro **Úpravy** (tužka). Ujistěte se, že **externí adresa URL** , kterou jste nakonfigurovali v proxy aplikaci, se naplní v polích **identifikátor** , **Adresa URL odpovědi** a **Adresa URL pro odhlášení** . Tyto adresy URL jsou vyžadovány pro správné fungování proxy aplikací. 

3. Upravte **adresu URL odpovědi** nakonfigurovanou dříve, aby byla doména dostupná na internetu prostřednictvím proxy aplikací. Pokud je například vaše **externí adresa URL** `https://contosotravel-f128.msappproxy.net` a původní **Adresa URL odpovědi** , bude `https://contosotravel.com/acs` nutné aktualizovat původní **adresu URL odpovědi** na `https://contosotravel-f128.msappproxy.net/acs` .

    ![Zadejte základní konfigurační data SAML.](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Zaškrtněte políčko vedle aktualizované **adresy URL odpovědi** a označte ji jako výchozí.

   * Po označení požadované **adresy URL odpovědi** jako výchozí můžete také odstranit dříve NAKONFIGUROVANOU **adresu URL odpovědi** , která použila interní adresu URL.

   * Pro tok iniciované v rámci SP se ujistěte, že aplikace back-end určuje správnou **adresu URL odpovědi** nebo adresu URL služby kontrolního výrazu pro příjem ověřovacího tokenu.

    > [!NOTE]
    > Pokud back-end aplikace očekává, že **Adresa URL odpovědi** bude interní adresou URL, budete muset použít [vlastní domény](application-proxy-configure-custom-domain.md) , aby odpovídaly interním a externím adresám URL, nebo na zařízení uživatelů nainstalovat rozšíření pro zabezpečené přihlašování k aplikacím. Toto rozšíření se automaticky přesměruje na příslušnou službu proxy aplikace. Pokud chcete nainstalovat rozšíření, přečtěte si téma [Moje zabezpečené přihlašovací rozšíření pro moje aplikace](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testování aplikace

Po dokončení všech těchto kroků by vaše aplikace měla být spuštěná. Testování aplikace:

1. Otevřete prohlížeč a přejděte na **externí adresu URL** , kterou jste vytvořili při publikování aplikace. 
1. Přihlaste se pomocí testovacího účtu, který jste přiřadili k aplikaci. Měli byste být schopni načíst aplikaci a mít v aplikaci jednotné přihlašování.

## <a name="next-steps"></a>Další kroky

- [Jak Azure Proxy aplikací služby AD poskytuje jednotné přihlašování?](application-proxy-single-sign-on.md)
- [Řešení potíží s proxy aplikací](application-proxy-troubleshoot.md)
