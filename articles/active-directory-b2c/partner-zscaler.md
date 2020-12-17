---
title: Kurz konfigurace Azure Active Directory B2C pomocí Zscaler
titleSuffix: Azure AD B2C
description: Informace o tom, jak integrovat Azure AD B2C ověřování pomocí Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 223f8acd6aad7aaf4c37e0b2eae2df882ed2ad1d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629370"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Kurz konfigurace Zscaler privátního přístupu pomocí Azure Active Directory B2C pro zabezpečený hybridní přístup

V tomto kurzu se dozvíte, jak integrovat Azure AD B2C ověřování pomocí [privátního přístupu Zscaler (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA zajišťuje zabezpečený přístup k soukromým aplikacím a prostředkům na základě zásad, aniž by došlo k nákladům, nepříjemnostem nebo bezpečnostním rizikům virtuální privátní sítě (VPN). Nabídka zabezpečeného hybridního přístupu pro Zscaler umožňuje pro aplikace určené uživatelům v kombinaci s Azure AD B2C prostor pro útoky s nulovým přístupem.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné Azure. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , který je propojený s vaším předplatným Azure.

- [Předplatné ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Popis scénáře

Integrace ZPA zahrnuje tyto komponenty:

- Azure AD B2C – poskytovatel identity (IdP) zodpovědný za ověření přihlašovacích údajů uživatele Zodpovídá také za registraci nového uživatele.

- ZPA – služba odpovědná za zabezpečení webové aplikace tím, že vynucuje [přístup s nulovým vztahem důvěryhodnosti](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Webová aplikace – hostuje službu, ke které se uživatel pokouší získat přístup.

Následující diagram znázorňuje, jak se ZPA integruje s Azure AD B2C.

![Obrázek znázorňuje diagram architektury Zscaler.](media/partner-zscaler/zscaler-architecture-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel přirazí na portál ZPA User Portal nebo na aplikaci ZPA Browser Access.
| 2. | ZPA vyžaduje informace o kontextu uživatele předtím, než se rozhodne, zda uživateli povolí přístup k webové aplikaci. K ověření uživatele ZPA provede přesměrování SAML na přihlašovací stránku Azure AD B2C.  
| 3. | Uživatel dorazí na přihlašovací stránku Azure AB B2C. Pokud se jedná o nového uživatele, uživatel se přihlásí k vytvoření nového účtu. Stávající uživatel se přihlásí pomocí svých stávajících přihlašovacích údajů. Azure AD B2C ověří identitu uživatele.
| 4. | Po úspěšném ověření Azure AD B2C přesměruje uživatele zpět na ZPA spolu s kontrolním výrazem SAML. ZPA ověří kontrolní výraz SAML a nastaví kontext uživatele.
| 5. | ZPA vyhodnotí zásady přístupu pro uživatele. Pokud má uživatel povolený přístup k webové aplikaci, může připojení projít.

## <a name="onboard-to-zpa"></a>Připojit k ZPA

V tomto kurzu se předpokládá, že už máte funkční nastavení pro ZPA. Pokud začínáte s ZPA, přečtěte si [podrobný průvodce konfigurací pro ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Část 1 – konfigurace Azure AD B2C jako IdP na ZPA

Postup konfigurace Azure AD B2C jako [zprostředkovatele identity (IDP) na ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Přihlaste se na [portál pro správu ZPA](https://admin.private.zscaler.com) .

2. Přejít na **správu**  >  **IDP Configuration**

3. Vyberte **Přidat konfiguraci IDP**

4. Pro **1 IDP informace** zadejte následující:

   a. **Název**: Azure AD B2C

   b. **Jednotné přihlašování**: vybrat uživatele

   c. **Domény**: zvolte domény ověřování, které chcete k tomuto IDP přidružit, a potom vyberte **Hotovo** .

   ![Obrázek zobrazuje IDP informace](media/partner-zscaler/add-idp-configuration.png)

5. Vyberte **Další**.

6. Pro **2 metadata SP**:

   a. Zkopírujte adresu URL poskytovatele služby a poznamenejte si ji pro pozdější použití.

   b. Zkopírujte ID entity poskytovatele služby a poznamenejte si je pro pozdější použití.

   ![Obrázek ukazuje informace o metadatech SP](media/partner-zscaler/sp-metadata.png)

7. Vybrat **pozastavit**

Zbytek konfigurace IdP bude pokračovat po konfiguraci Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Část 2 – Konfigurace vlastních zásad v Azure AD B2C

>[!Note]
>Tento krok je nutný jenom v případě, že ještě nemáte vlastní zásady. Pokud již máte jednu nebo více vlastních zásad, můžete tento krok přeskočit.

Článek [Začínáme s vlastními zásadami v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) poskytuje pokyny ke konfiguraci vlastních zásad v tenantovi Azure AD B2C.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Část 3 – registrace ZPA jako aplikace SAML v Azure AD B2C

Článek [Registrace aplikace SAML v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) poskytuje pokyny ke konfiguraci aplikace saml v Azure AD B2C. V [kroku 3,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)se vám poskytne adresa URL metadat SAML IDP, kterou používá Azure AD B2C. Budete ho potřebovat pro pozdější použití.

Postupujte podle pokynů v [kroku 4,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). V kroku 4,2 instrukce budete muset aktualizovat manifest aplikace. Aktualizujte vlastnosti následujícím způsobem:

- **identifierUris**: použijte ID entity poskytovatele služeb, které jste si poznamenali v **části 1, krok 6a**.

- **samlMetadataUrl**: Tato vlastnost se přeskočí, protože ZPA nehostuje adresu URL metadat SAML.

- **replyUrlsWithType**: použijte adresu URL poskytovatele služeb, kterou jste si poznamenali v **části 1, krok 6b**.

- **logoutUrl**: Tato vlastnost se přeskočí, protože ZPA nepodporuje ODHLAŠOVACÍ adresu URL.

Zbývající kroky nejsou pro tento kurz relevantní.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Část 4 – extrakce metadat SAML IdP z Azure AD B2C

Z předchozího kroku je potřeba získat adresu URL metadat SAML v následujícím formátu:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

kde `<tenant-name>` je název vašeho tenanta Azure AD B2C a `<policy-name>` je název vlastní zásady SAML, kterou jste vytvořili v posledním kroku.

Například " https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata "

Otevřete webový prohlížeč a přejděte na adresu URL metadat SAML. Po načtení stránky klikněte pravým tlačítkem myši kdekoli na stránce. Vyberte **Uložit stránku jako** a uložte soubor na svém počítači. použijete ho v další části.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Část 5 – dokončení konfigurace IdP na ZPA

Dokončete [konfiguraci IDP na portálu pro správu ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) , který byl částečně nakonfigurovaný v části 1:

1. Přihlaste se na [portál pro správu ZPA](https://admin.private.zscaler.com) .

2. Přejít na   >  **Konfigurace IDP** pro správu.

3. Vyberte IdP, který byl nakonfigurován v části 1 a vyberte možnost **pokračovat**.

4. Pro **3 vytvoření IDP**

   a. V **souboru metadat IDP**  >  **Vyberte soubor** a nahrajte soubor metadat, který jste uložili v části 4.

   b. Ověřte, že je **povolený** **stav** konfigurace IDP.

   c. Vyberte **Uložit**.

      ![Obrázek ukazuje informace o vytvoření IDP](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testování řešení

Přejděte na portál ZPA User Portal nebo aplikace pro přístup k prohlížeči a otestujte proces registrace nebo přihlašování. Mělo by to mít za následek úspěšné ověření SAML.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Začínáme s vlastními zásadami v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Registrace aplikace SAML v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Podrobný průvodce konfigurací pro ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Konfigurace IdP pro jednotné přihlašování](https://help.zscaler.com/zpa/configuring-idp-single-sign)
