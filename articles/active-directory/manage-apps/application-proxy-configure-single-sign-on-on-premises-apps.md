---
title: Jednotné přihlašování SAML pro místní aplikace pomocí proxy aplikací Azure AD
description: Zjistěte, jak zajistit jednotné přihlašování pro místní aplikace, které jsou zabezpečené pomocí ověřování SAML. Poskytněte vzdálený přístup k místním aplikacím pomocí proxy aplikace.
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
ms.openlocfilehash: ccf34b52e06e369fe4dd459ff9dfa2880596fb35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481343"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Jednotné přihlášení SAML pro místní aplikace s proxy aplikací

Můžete poskytnout jednotné přihlašování (SSO) do místních aplikací, které jsou zabezpečené pomocí ověřování SAML a poskytují vzdálený přístup k těmto aplikacím prostřednictvím proxy aplikace. S jediným přihlašování saml, Azure Active Directory (Azure AD) ověřuje do aplikace pomocí účtu Azure AD uživatele. Azure AD sděluje přihlašovací informace do aplikace prostřednictvím protokolu připojení. Můžete také mapovat uživatele na konkrétní role aplikace na základě pravidel, která definujete v deklaracích SAML. Povolením proxy aplikace kromě jednotného přihlašovacího systému SAML budou mít vaši uživatelé externí přístup k aplikaci a bezproblémové prostředí jednotného přihlašovacího zařízení.

Aplikace musí být schopny využívat tokeny SAML vydané službou **Azure Active Directory**. Tato konfigurace se nevztahuje na aplikace, které používají místního zprostředkovatele identity. V těchto scénářích doporučujeme zkontrolovat [prostředky pro migraci aplikací do Služby Azure AD](migration-resources.md).

Saml SSO s proxy aplikací také pracuje s funkcí šifrování tokenu SAML. Další informace najdete [v tématu Konfigurace šifrování tokenů Azure AD SAML](howto-saml-token-encryption.md).

Diagramy protokolu níže popisují pořadí jednotného přihlašování pro tok iniciovaný poskytovatelem služeb (spuštěná aktualizace SP) i tok iniciovaný zprostředkovatelem identity. Proxy aplikace pracuje s objektem SSO SAML ukládáním požadavku SAML a odpovědi na a z místní aplikace.

  ![Tok SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Tok SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Vytvoření aplikace a nastavení samoautomatického přihlašovat.

1. Na webu Azure Portal vyberte **Azure Active Directory > podnikové aplikace** a vyberte Nová **aplikace**.

2. Zadejte zobrazovaný název nové aplikace, vyberte **Integrovat jakoukoli jinou aplikaci, kterou v galerii nenajdete**, a pak vyberte **Vytvořit**.

3. Na stránce **Přehled** aplikace vyberte **Jednotné přihlašování**.

4. Jako metodu jednotného přihlašování vyberte **SAML.**

5. Nejprve nastavte samolo přiřazovat k práci v podnikové síti. Na stránce **Nastavit jednotné přihlašování pomocí saml** přejděte na nadpis **Základní konfigurace SAML** a vyberte jeho ikonu **Upravit** (tužku). Postupujte podle pokynů v [části Zadejte základní konfiguraci SAML](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) a nakonfigurujte ověřování na základě saml pro aplikaci.

6. Přidejte alespoň jednoho uživatele do aplikace a ujistěte se, že testovací účet má přístup k aplikaci. Při připojení k podnikové síti použijte testovací účet a zjistěte, zda máte jednotné přihlášení k aplikaci. 

   > [!NOTE]
   > Po nastavení proxy aplikace se vrátíte a aktualizujete **adresu URL odpovědi**SAML .

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publikování místní aplikace pomocí proxy aplikace

Před poskytnutím služby SSO pro místní aplikace je třeba povolit proxy aplikace a nainstalovat konektor. V kurzu [se dozvíte,](application-proxy-add-on-premises-application.md) jak připravit místní prostředí, nainstalovat a zaregistrovat konektor a otestovat konektor. Potom postupujte podle následujících kroků publikovat novou aplikaci s proxy aplikací. Další nastavení, která nejsou uvedena níže, najdete v části [Přidání místní aplikace do azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) části v kurzu.

1. Pokud je aplikace stále otevřená na webu Azure Portal, vyberte **Proxy aplikace**. Zadejte **interní adresu URL** aplikace. Pokud používáte vlastní doménu, musíte také nahrát certifikát TLS/SSL pro vaši aplikaci. 
   > [!NOTE]
   > Jako osvědčený postup použijte vlastní domény, kdykoli je to možné, pro optimalizované uživatelské prostředí. Další informace o [práci s vlastními doménami v proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).

2. Vyberte **službu Azure Active Directory** jako metodu **předběžného ověřování** pro vaši aplikaci.

3. Zkopírujte **externí adresu URL** aplikace. Tuto adresu URL budete potřebovat k dokončení konfigurace SAML.

4. Pomocí testovacího účtu se pokuste otevřít aplikaci s **externí adresou URL,** abyste ověřili, že je server Proxy aplikace správně nastaven. Pokud se vyskytly problémy, [přečtěte si článek Poradce při potížích s proxy aplikací a chybových zprávách](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Aktualizace konfigurace SAML

1. Když je aplikace stále otevřená na webu Azure Portal, vyberte **Jedno přihlašování**. 

2. Na stránce **Nastavit jednotné přihlašování pomocí saml** přejděte na nadpis **Základní konfigurace SAML** a vyberte jeho ikonu **Upravit** (tužku). Zkontrolujte, zda je **externí adresa URL** nakonfigurovaná v polích Proxy aplikace vyplněna v polích **Identifikátor**, Adresa URL **pro odpověď**a Adresa **URL odhlášení.** Tyto adresy URL jsou vyžadovány pro server Proxy aplikace, aby fungoval správně. 

3. Upravte dříve nakonfigurovanou **adresu URL odpovědi** tak, aby její doména byla dostupná pomocí proxy aplikace. Pokud `https://contosotravel-f128.msappproxy.net` je například **externí adresa URL** a původní adresa URL **odpovědi** byla `https://contosotravel.com/acs`, bude nutné aktualizovat původní adresu URL pro **odpověď** na adresu `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Zadání základních konfiguračních dat SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Zaškrtnutím políčka vedle aktualizované **adresy URL odpovědi** označíte jako výchozí.

   * Pokud je požadovaná **adresa URL odpovědi** již uvedena, označte tuto adresu URL pro **odpověď** jako výchozí a odstraňte dříve nakonfigurovanou adresu URL **pro odpověď**.

   * Pro tok iniciovaný sp, ujistěte se, že back-end aplikace určuje správnou **adresu URL pro odpověď** nebo adresu URL služby Assertion Consumer Service pro příjem ověřovacího tokenu.

    > [!NOTE]
    > Pokud back-endová aplikace očekává, že **adresa URL odpovědi** bude interní adresou URL, budete muset buď použít vlastní [domény,](application-proxy-configure-custom-domain.md) abyste měli odpovídající interní a externí adresy URL, nebo nainstalovat rozšíření zabezpečeného přihlášení Moje aplikace na zařízení uživatelů. Toto rozšíření bude automaticky přesměrovat na příslušnou službu proxy aplikace. Pokud chcete rozšíření nainstalovat, přečtěte si informace [o zabezpečeném přihlašovacím rozšíření Moje aplikace](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testování aplikace

Po dokončení všech těchto kroků by vaše aplikace měla být zprovozněná. Testování aplikace:

1. Otevřete prohlížeč a přejděte na **externí adresu URL,** kterou jste vytvořili při publikování aplikace. 
1. Přihlaste se pomocí testovacího účtu, který jste aplikaci přiřadili. Měli byste být schopni načíst aplikaci a mít do aplikace s vyrovnávání s el.

## <a name="next-steps"></a>Další kroky

- [Jak azure ad proxy aplikace poskytují jednotné přihlašování?](application-proxy-single-sign-on.md)
- [Řešení potíží s proxy aplikací](application-proxy-troubleshoot.md)
