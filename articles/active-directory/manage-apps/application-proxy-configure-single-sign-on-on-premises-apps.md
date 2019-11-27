---
title: Jednotné přihlašování SAML pro místní aplikace s Aplikace Azure AD proxy
description: Naučte se, jak zajistit jednotné přihlašování k místním aplikacím, které jsou zabezpečené pomocí ověřování SAML. Poskytněte vzdálený přístup k místním aplikacím pomocí proxy aplikací.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59e4e527f25a32c8a5274db5eab5d33a7843bc3c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275585"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Jednotné přihlašování SAML pro místní aplikace s proxy aplikací

Můžete poskytnout jednotné přihlašování (SSO) k místním aplikacím, které jsou zabezpečené pomocí ověřování SAML, a poskytovat vzdálený přístup k těmto aplikacím prostřednictvím proxy aplikací. Při jednotném přihlašování SAML se Azure Active Directory (Azure AD) ověřuje v aplikaci pomocí účtu Azure AD uživatele. Azure AD komunikuje informace přihlašování k aplikaci pomocí připojení protokolu. Uživatele můžete také namapovat na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML. Povolením proxy aplikací kromě jednotného přihlašování SAML budou mít uživatelé externí přístup k aplikaci a bezproblémové možnosti jednotného přihlašování.

Aplikace musí být schopné využívat tokeny SAML vydané **Azure Active Directory**. Tato konfigurace se nevztahuje na aplikace, které používají místní zprostředkovatele identity. V těchto scénářích doporučujeme zkontrolovat [prostředky pro migraci aplikací do Azure AD](migration-resources.md).

Jednotné přihlašování SAML s proxy aplikací funguje i s funkcí šifrování tokenu SAML. Další informace najdete v tématu [Konfigurace šifrování tokenů SAML v Azure AD](howto-saml-token-encryption.md).

Níže uvedené diagramy protokolu popisují posloupnost jednotného přihlašování pro tok inicializovaný poskytovatelem služeb (iniciované v rámci SP) i pro tok iniciované poskytovatelem identity (IdP). Proxy aplikace spolupracuje se službou SAML SSO tím, že ukládá požadavek SAML a odpověď do místní aplikace do a z ní.

  ![Tok SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Tok SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Vytvoření aplikace a nastavení jednotného přihlašování SAML

1. V Azure Portal vyberte **Azure Active Directory > podnikové aplikace** a vyberte **Nová aplikace**.

2. V části místní **aplikace** vyberte **Přidat místní aplikaci**.

3. Zadejte zobrazovaný název pro novou aplikaci a pak vyberte **Přidat**.

4. Na stránce **Přehled** aplikace vyberte **jednotné přihlašování**.

5. Jako metodu jednotného přihlašování vyberte **SAML** .

6. Nejdřív nastavte jednotné přihlašování SAML pro práci v podnikové síti. Na stránce **nastavit jednotné přihlašování pomocí SAML** otevřete záhlaví **základní konfigurace SAML** a vyberte jeho ikonu pro **Úpravy** (tužka). Použijte postup v části [zadání základní konfigurace SAML](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) pro konfiguraci ověřování založeného na SAML pro aplikaci.

7. Přidejte do aplikace alespoň jednoho uživatele a ujistěte se, že testovací účet má přístup k aplikaci. Když jste připojení k podnikové síti, použijte testovací účet a zjistěte, jestli máte k aplikaci jednotné přihlašování. 

   > [!NOTE]
   > Po nastavení proxy aplikace se vrátíte zpátky a aktualizujete **adresu URL odpovědi**SAML.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publikování místní aplikace pomocí proxy aplikací

Před poskytnutím jednotného přihlašování pro místní aplikace je potřeba povolit proxy aplikace a nainstalovat konektor. Podívejte se na kurz [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure AD](application-proxy-add-on-premises-application.md) , kde se dozvíte, jak připravit místní prostředí, jak nainstalovat a zaregistrovat konektor a otestovat konektor. Pak pomocí těchto kroků publikujte novou aplikaci s využitím proxy aplikací. Další nastavení, která nejsou uvedená níže, najdete v části [Přidání místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) v tomto kurzu.

1. Když je aplikace v Azure Portal stále otevřená, vyberte **proxy aplikace**. Zadejte **interní adresu URL** pro aplikaci. Pokud používáte vlastní doménu, musíte také odeslat certifikát SSL pro vaši aplikaci. 
   > [!NOTE]
   > Osvědčeným postupem je použití vlastních domén, kdykoli je to možné pro optimalizované uživatelské prostředí. Přečtěte si další informace o [práci s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).

2. Jako metodu **předběžného ověření** pro aplikaci vyberte **Azure Active Directory** .

3. Zkopírujte **externí adresu URL** pro aplikaci. Tuto adresu URL budete potřebovat k dokončení konfigurace SAML.

4. Pomocí testovacího účtu se pokuste otevřít aplikaci s **externí adresou URL** a ověřit, jestli je správně nastavená proxy aplikace. Pokud se vyskytnou problémy, přečtěte si téma [řešení problémů se službou Application proxy a chybovými zprávami](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Aktualizace konfigurace SAML

1. Když je aplikace v Azure Portal stále otevřená, vyberte **jednotné přihlašování**. 

2. Na stránce **nastavit jednotné přihlašování pomocí SAML** otevřete záhlaví **základní konfigurace SAML** a vyberte jeho ikonu pro **Úpravy** (tužka). **Externí adresa URL** , kterou jste nakonfigurovali v proxy aplikaci, automaticky vyplní pole **identifikátor**, **Adresa URL odpovědi**a **Adresa URL pro odhlášení** . Tyto adresy URL neupravujte, protože jsou potřebné k tomu, aby proxy aplikací fungovalo správně.

3. Upravte **adresu URL odpovědi** nakonfigurovanou dříve, aby její doména byla dosažitelná proxy aplikací. Pokud se například vaše **externí adresa url** `https://contosotravel-f128.msappproxy.net` a původní **adresa url odpovědi** byla `https://contosotravel.com/acs`, bude nutné aktualizovat původní **adresu URL odpovědi** na `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Zadejte základní konfigurační data SAML.](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Zaškrtněte políčko vedle aktualizované **adresy URL odpovědi** a označte ji jako výchozí.

   * Pokud je požadovaná **Adresa URL odpovědi** již uvedena, označte tuto **adresu URL odpovědi** jako výchozí a odstraňte dříve konfigurovanou **adresu URL odpovědi**.

   * Pro tok iniciované v rámci SP se ujistěte, že aplikace back-end určuje správnou **adresu URL odpovědi** nebo adresu URL služby kontrolního výrazu pro příjem ověřovacího tokenu.

    > [!NOTE]
    > Pokud back-end aplikace očekává, že **Adresa URL odpovědi** bude interní adresou URL, budete muset použít [vlastní domény](application-proxy-configure-custom-domain.md) , aby odpovídaly interním a externím adresám URL, nebo na zařízení uživatelů nainstalovat rozšíření pro zabezpečené přihlašování k aplikacím. Toto rozšíření se automaticky přesměruje na příslušnou službu proxy aplikace. Pokud chcete nainstalovat rozšíření, přečtěte si téma [Moje zabezpečené přihlašovací rozšíření pro moje aplikace](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testování aplikace

Po dokončení těchto kroků, by vaše aplikace do provozu. Testování aplikace:

1. Otevřete prohlížeč a přejděte na **externí adresu URL** , kterou jste vytvořili při publikování aplikace. 
1. Přihlaste se pomocí testovacího účtu, který jste přiřadili do aplikace. Měli byste být schopni načíst aplikaci a mít v aplikaci jednotné přihlašování.

## <a name="next-steps"></a>Další kroky

- [Jak Azure Proxy aplikací služby AD poskytuje jednotné přihlašování?](application-proxy-single-sign-on.md)
- [Řešení potíží s proxy aplikací](application-proxy-troubleshoot.md)
