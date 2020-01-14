---
title: Zabezpečení služby RESTful pomocí klientských certifikátů
titleSuffix: Azure AD B2C
description: Zabezpečte svoje vlastní REST APIé výměny deklarací identity v Azure AD B2C pomocí klientských certifikátů.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0201c0947c2f6052182721294e8972e29479825d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930639"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Zabezpečte službu RESTful pomocí klientských certifikátů.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V souvisejícím článku [vytvoříte službu RESTful](active-directory-b2c-custom-rest-api-netfw.md) , která komunikuje s Azure Active Directory B2C (Azure AD B2C).

V tomto článku se dozvíte, jak omezit přístup k webové aplikaci Azure (RESTful API) pomocí klientského certifikátu. Tento mechanismus se nazývá vzájemné ověřování TLS nebo *ověřování klientským certifikátem*. K vaší službě mají přístup jenom služby, které mají správné certifikáty, například Azure AD B2C.

>[!NOTE]
>Službu RESTful můžete zabezpečit i pomocí [základního ověřování http](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Základní ověřování HTTP se ale za klientský certifikát považuje za méně bezpečné. Naším doporučením je zabezpečit službu RESTful pomocí ověřování klientského certifikátu, jak je popsáno v tomto článku.

Tento článek podrobně popisuje, jak:
* Nastavte webovou aplikaci tak, aby používala ověřování klientským certifikátem.
* Nahrajte certifikát do klíčů zásad Azure AD B2C.
* Nakonfigurujte vlastní zásady na používání klientského certifikátu.

## <a name="prerequisites"></a>Požadavky
* Proveďte kroky popsané v článku [integrace REST APIch deklarací identity](active-directory-b2c-custom-rest-api-netfw.md) .
* Získejte platný certifikát (soubor. pfx s privátním klíčem).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Krok 1: Konfigurace webové aplikace pro ověřování klientských certifikátů
Chcete-li nastavit **Azure App Service** pro vyžadování klientských certifikátů, nastavte nastavení webu webové aplikace `clientCertEnabled` na *hodnotu true*. Tuto změnu provedete tak, že v Azure Portal otevřete stránku webové aplikace. V levém navigačním panelu v části **Nastavení** vyberte **Nastavení SSL**. V části **klientské certifikáty** zapněte možnost **příchozí klientský certifikát** .

>[!NOTE]
>Ujistěte se, že je váš plán Azure App Service Standard nebo vyšší. Další informace najdete v tématu Podrobný [Přehled plánů Azure App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Další informace o nastavení vlastnosti **clientCertEnabled** najdete v tématu [Konfigurace vzájemného ověřování TLS pro webové aplikace](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Krok 2: nahrání certifikátu do klíčů zásad Azure AD B2C
Po nastavení `clientCertEnabled` na *hodnotu true*vyžaduje komunikace s rozhraním API RESTful klientský certifikát. Pokud chcete získat, nahrát a uložit certifikát klienta v Azure AD B2C tenanta, udělejte toto:
1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **Architektura prostředí identit**.

2. Pokud chcete zobrazit klíče, které jsou k dispozici ve vašem tenantovi, vyberte **klíče zásad**.

3. Vyberte **Přidat**.
    Otevře se okno **vytvořit klíč** .

4. V poli **Možnosti** vyberte **Odeslat**.

5. Do pole **název** zadejte **B2cRestClientCertificate**.
    *B2C_1A_* předpony se přidají automaticky.

6. V poli **nahrávání souboru** vyberte soubor. pfx vašeho certifikátu s privátním klíčem.

7. Do pole **heslo** zadejte heslo certifikátu.

    ![Odeslat klíč zásad na stránce vytvořit klíč v Azure Portal](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Vyberte **Vytvořit**.

8. Pokud chcete zobrazit klíče, které jsou k dispozici ve vašem tenantovi, a potvrďte, že jste vytvořili `B2C_1A_B2cRestClientCertificate` klíč, vyberte **klíče zásad**.

## <a name="step-3-change-the-technical-profile"></a>Krok 3: Změna technického profilu
Pro podporu ověřování klientských certifikátů ve vlastních zásadách změňte technický profil tak, že provedete následující akce:

1. V pracovním adresáři otevřete soubor zásad rozšíření *TrustFrameworkExtensions. XML* .

2. Vyhledejte `<TechnicalProfile>` uzel, který obsahuje `Id="REST-API-SignUp"`.

3. Vyhledejte element `<Metadata>`.

4. Změňte *AuthenticationType* na *ClientCertificate*, a to následujícím způsobem:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Ihned po ukončení `<Metadata>` elementu přidejte následující fragment kódu XML:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Po přidání fragmentu by váš technický profil měl vypadat jako v následujícím kódu XML:

    ![Nastavení elementů XML ověřování v ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Krok 4: nahrání zásady do tenanta

1. V [Azure Portal](https://portal.azure.com)na panelu nástrojů portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

1. Vyberte **architekturu prostředí identity**.

1. Vyberte **všechny zásady**.

1. Vyberte **Odeslat zásadu**.

1. Zaškrtněte políčko **přepsat zásadu, pokud existuje** .

1. Nahrajte soubor *TrustFrameworkExtensions. XML* a ověřte, zda projde ověřením.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Krok 5: Vyzkoušejte si vlastní zásady pomocí rutiny spustit hned
1. Otevřete **Azure AD B2C nastavení**a pak vyberte **Architektura prostředí identity**.

    >[!NOTE]
    >Spustit teď vyžaduje, aby se v tenantovi předem zaregistrovala aspoň jedna aplikace. Informace o tom, jak zaregistrovat aplikace, najdete v článku Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) nebo v článku věnovaném [registraci aplikace](active-directory-b2c-app-registration.md) .

2. Otevřete **B2C_1A_signup_signin**, vlastní zásady předávající strany (RP), které jste nahráli, a pak vyberte **Spustit nyní**.

3. Otestujte proces zadáním příkazu **test** do **daného pole název** .
    Azure AD B2C zobrazí v horní části okna chybovou zprávu.

    ![Bylo zobrazeno zvýrazněné textové pole název a chyba ověření vstupu.](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Do pole **daný název** zadejte název (jiný než "test").
    Azure AD B2C uživatele odhlásí a pak pošle věrnostní číslo do vaší aplikace. Poznamenejte si číslo v tomto příkladu JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Pokud se zobrazí chybová zpráva, *název není platný, zadejte prosím platný název*. znamená to, že Azure AD B2C úspěšně volal službu RESTful, zatímco prezentuje certifikát klienta. Dalším krokem je ověření certifikátu.

## <a name="step-6-add-certificate-validation"></a>Krok 6: Přidání ověření certifikátu
Certifikát klienta, který Azure AD B2C odesílá službě RESTful, neprovádí ověřování pomocí Azure App Service platformy, s výjimkou ověření, zda certifikát existuje. Ověřování certifikátu je zodpovědností webové aplikace.

V této části přidáte vzorový ASP.NET kód, který ověřuje vlastnosti certifikátu pro účely ověřování.

> [!NOTE]
>Další informace o konfiguraci Azure App Service pro ověřování klientských certifikátů najdete v tématu [Konfigurace vzájemného ověřování TLS pro webové aplikace](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6,1 přidejte nastavení aplikace do souboru Web. config vašeho projektu.
V projektu sady Visual Studio, který jste vytvořili dříve, přidejte do souboru *Web. config* následující nastavení aplikace za prvkem `appSettings`:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Hodnoty certifikátu nahraďte **názvem subjektu**certifikátu, **názvem vystavitele**a **kryptografickým otiskem certifikátu** .

### <a name="62-add-the-isvalidclientcertificate-function"></a>6,2 přidejte funkci IsValidClientCertificate
Otevřete soubor *Controllers\IdentityController.cs* a přidejte do třídy kontroleru `Identity` následující funkci:

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

V předchozím ukázkovém kódu přijímáme certifikát jako platný, pouze pokud jsou splněné všechny následující podmínky:
* Platnost certifikátu nevypršela a je aktivní pro aktuální čas na serveru.
* `Subject` název certifikátu se rovná hodnotě nastavení `ClientCertificate:Subject` aplikace.
* `Issuer` název certifikátu se rovná hodnotě nastavení `ClientCertificate:Issuer` aplikace.
* `thumbprint` certifikátu se rovná hodnotě nastavení `ClientCertificate:Thumbprint` aplikace.

>[!IMPORTANT]
>V závislosti na citlivosti služby možná budete muset přidat další ověřování. Například může být nutné otestovat, zda je certifikát zřetězený s důvěryhodnou kořenovou autoritou, ověřit název organizace vystavitele atd.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6,3 volání funkce IsValidClientCertificate
Otevřete soubor *Controllers\IdentityController.cs* a potom na začátku `SignUp()` funkce přidejte následující fragment kódu:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Po přidání fragmentu by měl váš řadič `Identity` vypadat jako v následujícím kódu:

![Přidat kód pro ověření certifikátu](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Krok 7: publikování projektu do Azure a jeho otestování
1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **contoso. AADB2C. API** a pak vyberte **publikovat**.

2. Opakujte krok 6 a znovu otestujte vlastní zásadu s ověřením certifikátu. Pokuste se spustit tuto zásadu a ujistěte se, že vše funguje po přidání ověření.

3. V souboru *Web. config* změňte hodnotu `ClientCertificate:Subject` na **neplatné**. Spusťte zásady znovu a měla by se zobrazit chybová zpráva.

4. Změňte hodnotu na **platná**a ujistěte se, že zásada může zavolat REST API.

Pokud potřebujete tento krok vyřešit, přečtěte si téma [shromažďování protokolů pomocí Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>Volitelné Stažení úplných souborů a kódu zásad
* Po dokončení návodu [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) doporučujeme sestavit svůj scénář pomocí vlastních souborů zásad. Pro váš odkaz jsme zadali [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Můžete si stáhnout kompletní kód z [ukázkového řešení sady Visual Studio pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).
