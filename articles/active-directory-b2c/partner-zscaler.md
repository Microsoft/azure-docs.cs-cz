---
title: Kurz – konfigurace Azure Active Directory B2C pomocí Zscaler
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 254f8da74a187e88cfb973da7100fe5654c84bb6
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732442"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Kurz: konfigurace privátního přístupu Zscaler pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak integrovat ověřování Azure Active Directory B2C (Azure AD B2C) s [privátním přístupem Zscaler (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA zajišťuje zabezpečený přístup k soukromým aplikacím a prostředkům na základě zásad, aniž by došlo k nákladům, nepříjemnostem nebo bezpečnostním rizikům virtuální privátní sítě (VPN). Nabídka Zscaler Secure Hybrid Access nabízí prostor pro vypínání s nulovým přístupem pro aplikace, které jsou v kombinaci s Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

- Předplatné Azure. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).  
- [Klienta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , který je propojený s vaším předplatným Azure.  
- [Předplatné ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Popis scénáře

Integrace ZPA zahrnuje tyto komponenty:

- **Azure AD B2C**: poskytovatel identity (IDP), který je zodpovědný za ověření přihlašovacích údajů uživatele. Zodpovídá také za registraci nového uživatele.  
- **ZPA**: služba odpovědná za zabezpečení webové aplikace vynuceným [přístupem s nulovou důvěryhodností](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **Webová aplikace**: hostuje službu, ke které se uživatel pokouší získat přístup.

Následující diagram znázorňuje, jak se ZPA integruje s Azure AD B2C.

![Diagram architektury Zscaler znázorňující, jak se ZPA integruje s Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

Sekvence je popsána v následující tabulce:

|Krok | Popis |
| :-----:| :-----------|
| 1 | Uživatel přirazí na portál ZPA User Portal nebo na aplikaci pro přístup přes prohlížeč ZPA.
| 2 | ZPA vyžaduje informace o kontextu uživatele předtím, než se rozhodne, zda uživateli povolí přístup k webové aplikaci. K ověření uživatele ZPA provede přesměrování SAML na přihlašovací stránku Azure AD B2C.  
| 3 | Uživatel dorazí na přihlašovací stránku Azure AB B2C. Nový uživatel si zaregistruje a vytvoří účet a stávající uživatelé se přihlásí pomocí svých stávajících přihlašovacích údajů. Azure AD B2C ověří identitu uživatele.
| 4 | Po úspěšném ověření Azure AD B2C přesměruje uživatele zpět na ZPA spolu s kontrolním výrazem SAML. ZPA ověří kontrolní výraz SAML a nastaví kontext uživatele.
| 5 | ZPA vyhodnocuje zásady přístupu pro uživatele. Pokud má uživatel povolený přístup k webové aplikaci, připojení může projít.

## <a name="onboard-to-zpa"></a>Připojit k ZPA

V tomto kurzu se předpokládá, že už máte fungující nastavení pro ZPA. Pokud začínáte s ZPA, přečtěte si [podrobný průvodce konfigurací pro ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Integrace ZPA s Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Krok 1: konfigurace Azure AD B2C jako IdP na ZPA

Pokud chcete nakonfigurovat Azure AD B2C jako [IDP v ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign), postupujte takto:

1. Přihlaste se na [portál pro správu ZPA](https://admin.private.zscaler.com).

1. Přejít na   >  **Konfigurace IDP** pro správu.

1. Vyberte **Přidat konfiguraci IDP**.

   Otevře se podokno **Přidat konfiguraci IDP** .

   ![Snímek obrazovky s kartou informace o IdP v podokně Přidat konfiguraci IdP](media/partner-zscaler/add-idp-configuration.png)

1. Vyberte kartu **informace o IDP** a pak postupujte takto:

   a. Do pole **název** zadejte **Azure AD B2C**.  
   b. V části **jednotné přihlašování** vyberte **uživatel**.  
   c. V rozevíracím seznamu **domény** vyberte domény ověřování, které chcete k tomuto IDP přidružit.

1. Vyberte **Další**.

1. Vyberte kartu **metadata SP** a pak postupujte takto:

   a. V části **Adresa URL poskytovatele služeb** zkopírujte nebo poznamenejte hodnotu pro pozdější použití.  
   b. V části **ID entity poskytovatele služeb** zkopírujte nebo poznamenejte hodnotu pro pozdější použití.

   ![Snímek obrazovky karty metadata SP v podokně Přidat konfiguraci IdP](media/partner-zscaler/sp-metadata.png)

1. Vyberte **pozastavit**.

Po dokončení konfigurace Azure AD B2C se zbytek konfigurace IdP obnoví.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Krok 2: Konfigurace vlastních zásad v Azure AD B2C

>[!Note]
>Tento krok je nutný jenom v případě, že jste ještě nenakonfigurovali vlastní zásady. Pokud již máte jednu nebo více vlastních zásad, můžete tento krok přeskočit.

Informace o konfiguraci vlastních zásad v tenantovi Azure AD B2C najdete v tématu Začínáme [s vlastními zásadami v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Krok 3: registrace ZPA jako aplikace SAML v Azure AD B2C

Postup konfigurace aplikace SAML v Azure AD B2C najdete v tématu [Registrace aplikace SAML v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers). 

V kroku ["3,2 Nahrajte a otestujte metadata zásad"](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata), zkopírujte nebo Poznamenejte si adresu URL metadat SAML IDP, kterou používá Azure AD B2C. Budete ho potřebovat později.

Postupujte podle pokynů v kroku ["4,2 aktualizace manifestu aplikace"](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). V kroku 4,2 aktualizujte vlastnosti manifestu aplikace následujícím způsobem:

- Pro **identifierUris**: použijte ID entity poskytovatele služby, které jste zkopírovali nebo poznamenali dříve v kroku 1.6. b.  
- Pro **samlMetadataUrl**: tuto vlastnost můžete přeskočit, protože ZPA nehostuje adresu URL metadat SAML.  
- Pro **replyUrlsWithType**: použijte adresu URL poskytovatele služeb, kterou jste zkopírovali nebo poznamenali dříve v kroku 1.6. a.  
- Pro **logoutUrl**: tuto vlastnost můžete přeskočit, protože ZPA nepodporuje ODHLAŠOVACÍ adresu URL.

Zbývající kroky nejsou pro tento kurz relevantní.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Krok 4: extrakce metadat SAML IdP z Azure AD B2C

Dál je potřeba získat adresu URL metadat SAML v následujícím formátu:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Všimněte si, že `<tenant-name>` je název vašeho tenanta Azure AD B2C a `<policy-name>` je název vlastní zásady SAML, který jste vytvořili v předchozím kroku.

Adresa URL může být například `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` .

Otevřete webový prohlížeč a přejít na adresu URL metadat SAML. Klikněte pravým tlačítkem na libovolné místo na stránce, vyberte **Uložit jako** a potom soubor uložte do počítače pro použití v dalším kroku.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Krok 5: dokončení konfigurace IdP na ZPA

Dokončete [konfiguraci IDP na portálu pro správu ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) , který jste částečně nakonfigurovali dříve v části "krok 1: konfigurace Azure AD B2C jako IDP na ZPA".

1. Na [portálu pro správu ZPA](https://admin.private.zscaler.com)přejít na **Správa**  >  **Konfigurace IDP**.

1. Vyberte IdP, který jste nakonfigurovali v kroku 1, a pak vyberte **pokračovat**.

1. V podokně **Přidat konfiguraci IDP** vyberte kartu **vytvořit IDP** a pak postupujte takto:

   a. V části **soubor metadat IDP** nahrajte soubor metadat, který jste předtím uložili v kroku 4: extrakce metadat SAML IdP z Azure AD B2C.  
   b. Ověřte, že je **povolený** **stav** konfigurace IDP.  
   c. Vyberte **Uložit**.

   ![Snímek obrazovky s kartou vytvořit IdP v podokně Přidat konfiguraci IdP](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testování řešení

Přejděte na ZPA User Portal nebo aplikace pro přístup přes prohlížeč a otestujte proces registrace nebo přihlašování. Test by měl mít za následek úspěšné ověření SAML.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Registrace aplikace SAML v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)
- [Podrobný průvodce konfigurací pro ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Konfigurace IdP pro jednotné přihlašování](https://help.zscaler.com/zpa/configuring-idp-single-sign)
