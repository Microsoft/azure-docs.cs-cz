---
title: Kurz konfigurace Azure Active Directory B2C s nástrojem Nevis
titleSuffix: Azure AD B2C
description: Informace o tom, jak integrovat Azure AD B2C ověřování s nástrojem Nevis pro ověřování bez hesla
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 19aa847ce7ce1a6ba727a4733aefcdfad845e8c6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629387"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Kurz konfigurace Nevisu pomocí Azure Active Directory B2C pro ověřování bez hesla

V tomto ukázkovém kurzu se naučíte, jak Azure AD B2C s nástrojem  [Nevis](https://www.nevis.net/solution/authentication-cloud) a povolit ověřování bez hesla. Nevis nabízí mobilní a plně náročné prostředí koncového uživatele s aplikací Nevis Access, které poskytuje silné ověřování zákazníků a dodržuje požadavky na transakce 2 (PSD2) pro platební služby.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- [Zkušební účet](https://www.nevis-security.com/aadb2c/) Nevis

- Předplatné služby Azure AD. Pokud ho nemáte, Získejte [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , který je propojený s vaším předplatným Azure.

- Nakonfigurované Azure AD B2C prostředí pro použití [vlastních zásad](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started), pokud chcete do svého toku zásad registrace integrovat Nevis.

## <a name="scenario-description"></a>Popis scénáře

V tomto scénáři přidejte do back-endové aplikace plně se značkou Access pro ověřování bez hesla. Řešení tvoří následující součásti:

- Tenant Azure AD B2C s kombinovaným přihlašováním a zásadami registrace k back-endu
- Instance Nevis a její REST API pro vylepšení Azure AD B2C
- Vaše vlastní aplikace pro přístup přes značku

V diagramu se zobrazuje implementace.

![Tok přihlašování k heslům vysoké úrovně pomocí Azure AD B2C a Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel se pokusí přihlásit nebo zaregistrovat do aplikace prostřednictvím Azure AD B2C zásady přihlašování a registrace.
| 2. | Při registraci se aplikace Nevis Access zaregistruje na zařízení uživatele pomocí kódu QR. Na uživatelském zařízení se vygeneruje privátní klíč, který se používá k podepsání uživatelských požadavků.
| 3. |  Azure AD B2C používá technický profil RESTful ke spuštění přihlášení pomocí řešení Nevis.
| 4. | Žádost o přihlášení se pošle do aplikace pro přístup, buď jako nabízená zpráva, kód QR nebo jako přímý odkaz.
| 5. | Uživatel schválí pokus o přihlášení s jejich biometrika. Zpráva se pak vrátí do Nevis, která ověří přihlášení pomocí uloženého veřejného klíče.
| 6. | Azure AD B2C pošle poslední žádost na Nevis, aby se ověřilo, že se přihlášení úspěšně dokončilo.
| 7. |Na základě zprávy o úspěchu/neúspěchu od Azure AD B2C má uživatel povolen nebo odepřen přístup k aplikaci.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrace Azure AD B2Cho tenanta

### <a name="onboard-to-nevis"></a>Zprovoznění na Nevis 

[Zaregistrujte si účet Nevis](https://www.nevis-security.com/aadb2c/).
Dostanete dvě e-maily:

1. Oznámení účtu pro správu

2. Pozvání mobilní aplikace.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Přidejte svého tenanta Azure AD B2C do svého účtu Nevis

1. Z zkušebního e-mailu účtu pro správu Nevis zkopírujte svůj klíč pro správu do schránky.

2. Otevřete https://console.nevis.cloud/ v prohlížeči.

3. Přihlaste se ke konzole pro správu pomocí svého klíče.

4. Vybrat **přidat instanci**

5. Vyberte nově vytvořenou instanci a otevřete ji.

6. Na bočním navigačním panelu vyberte **vlastní integrace** .

7. Vyberte **Přidat vlastní integraci**.

8. Jako název integrace zadejte Azure AD B2C název tenanta.

9. Jako adresu URL nebo doménu zadejte `https://yourtenant.onmicrosoft.com`

10. Vyberte **Další**.

>[!NOTE]
>Přístupový token Nevis budete potřebovat později.

11. Vyberte **Hotovo**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Instalace aplikace pro Nevis Access do telefonu

1. Z zkušebního e-mailové zprávy pro Nevis mobilní aplikace otevřete žádost o **test letové aplikace** .

2. Nainstalujte aplikaci.

3. Postupujte podle pokynů uvedených pro instalaci aplikace Nevis Access.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integrace Azure AD B2C s nástrojem Nevis

1. Otevřete web [Azure Portal](https://portal.azure.com/).

2. Přepněte na svého tenanta Azure AD B2C. Ujistěte se, že jste vybrali správného tenanta, protože tenant Azure AD B2C obvykle je v samostatném tenantovi.

3. V nabídce vyberte možnost **Architektura prostředí identity (IEF)** .

4. Vybrat **klíče zásad**

5. Vyberte **Přidat** a vytvořte nový klíč s následujícím nastavením:

      a. Vybrat **Ruční** v možnostech

      b. Nastavte název na **AuthCloudAccessToken** .

      c. Vložte do pole tajného klíče dříve uložený **přístupový token pro Nevis** .

      d. Pro použití klíče vyberte **šifrování** .

      e. Vyberte **Vytvořit**.

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Konfigurace a nahrání nevis.html do úložiště objektů BLOB v Azure

1. V prostředí identity (IDE), přejít do složky [**zásad**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otevřete soubor  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) .

3. Nahraďte  **authentication_cloud_url** adresou URL konzoly Správce služby Nevis – `https://<instance_id>.mauth.nevis.cloud` .

4. **Uložte** změny do souboru.

5. Postupujte podle [pokynů](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#2-create-an-azure-blob-storage-account) a nahrajte soubor **nevis.html** do úložiště objektů BLOB v Azure.

6. Postupujte podle [pokynů](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#3-configure-cors) a povolte sdílení prostředků mezi zdroji (CORS) pro tento soubor.

7. Po dokončení nahrávání a povolení CORS vyberte v seznamu soubor **nevis.html** .

8. Na kartě **Přehled** vedle pole **Adresa URL** vyberte ikonu **Kopírovat odkaz** .

9. Otevřete odkaz na nové kartě prohlížeče, abyste se ujistili, že se zobrazí šedé pole.

>[!NOTE]
>Odkaz na objekt BLOB budete potřebovat později.

### <a name="customize-your-trustframeworkbasexml"></a>Přizpůsobení TrustFrameworkBase.xml

1. V integrovaném vývojovém prostředí přejdete do složky [**zásad**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otevřete soubor [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) .

3. V **TenantId** nahraďte **yourtenant** názvem vašeho účtu tenanta Azure.

4. Nahraďte **yourtenant** názvem vašeho účtu tenanta Azure v **PublicPolicyURI**.

5. Nahraďte všechny **authentication_cloud_url** instance adresou URL vaší konzole pro správu Nevis.

6. **Uložte** změny do souboru.

### <a name="customize-your-trustframeworkextensionsxml"></a>Přizpůsobení TrustFrameworkExtensions.xml

1. V integrovaném vývojovém prostředí přejdete do složky [**zásad**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otevřete soubor [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) .

3. V **TenantId** nahraďte **yourtenant** názvem vašeho účtu tenanta Azure.

4. Nahraďte **yourtenant** názvem vašeho účtu tenanta Azure v **PublicPolicyURI**.

5. V části **BasePolicy** v **TenantId** také nahraďte _Yourtenant_ názvem vašeho účtu tenanta Azure.

6. V části **BuildingBlocks** nahraďte **LoadUri** adresou URL odkazu na objekt BLOB vaší _nevis.html_ v účtu BLOB Storage.

7. **Uložte** soubor.

### <a name="customize-your-signuporsigninxml"></a>Přizpůsobení SignUpOrSignin.xml

1. V integrovaném vývojovém prostředí přejdete do složky [**zásad**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otevřete soubor [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) .

3. V **TenantId** nahraďte **yourtenant** názvem vašeho účtu tenanta Azure.

4. Nahraďte **yourtenant** názvem vašeho účtu tenanta Azure v **PublicPolicyUri**.

5. V části **BasePolicy** v **TenantId** taky nahraďte **Yourtenant** názvem vašeho účtu tenanta Azure.

6. **Uložte** soubor.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Nahrajte vlastní zásady a Azure AD B2C

1. Otevřete domovskou stránku [tenanta Azure AD B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Vyberte **architekturu prostředí identity**.

3. Vyberte **Odeslat vlastní zásadu**.

4. Vyberte soubor **TrustFrameworkBase.xml** , který jste změnili.

5. Zaškrtněte políčko **Přepsat vlastní zásadu, pokud již existuje** .
6. Vyberte **Nahrát**.

7. Opakujte kroky 5 a 6 pro **TrustFrameworkExtensions.xml**.

8. Opakujte kroky 5 a 6 pro **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Instalace testovacího účtu a přístup k aplikaci Nevis

1. Otevřete domovskou stránku [tenanta Azure AD B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Vyberte **architekturu prostředí identity**.

3. Přejděte dolů na vlastní zásady a vyberte **B2C_1A_signup_signin**.

4. Vyberte **Spustit nyní**.

5. V místním okně vyberte zaregistrovat se **hned teď**.

6. Přidejte svou e-mailovou adresu.

7. Vyberte **Odeslat ověřovací kód**.

8. Zkopírujte přes e-mail ověřovací kód.

9. Vyberte **Ověřit**.

10. Vyplňte formulář pomocí nového hesla a zobrazovaného jména.

11. Vyberte **Vytvořit**.

12. Přejdete na stránku skenování kódů QR.

13. V telefonu otevřete **aplikaci Nevis Access**.

14. Vyberte **ID obličeje**.

15. Když obrazovka říká **registraci ověřovatele**, vyberte **pokračovat**.

16. Na telefonu se znovu ověřte u svého obličeje.

17. Přejdete na úvodní stránku [JWT.MS](https://jwt.ms) , která zobrazí podrobnosti o dekódování tokenu.

### <a name="test-the-pure-passwordless-sign-in"></a>Otestování čistě přihlašování se neúplnými hesly

1. V části **Architektura prostředí identity** vyberte **B2C_1A_signup_signin**.

2. Vyberte **Spustit nyní**.

3. V místním okně vyberte ověřování bez **hesla**.

4. Zadejte e-mailovou adresu.

5. Vyberte **Pokračovat**.

6. V nabídce oznámení vyberte v oznámeních možnost aplikace **Nevis přístup k aplikaci**.

7. Ověřte se u svého obličeje.

8. Automaticky přejdete na úvodní stránku [JWT.MS](https://jwt.ms) , která zobrazí vaše tokeny.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích.

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
