---
title: Zabezpečení služby RESTful pomocí klientských certifikátů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zabezpečené výměny deklarací identity vaše vlastní rozhraní REST API ve vaší Azure AD B2C s využitím klientské certifikáty
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0832b3b8e0b2b6d7459eeddb8d8e5a93a7f17d09
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448345"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Zabezpečení služby RESTful pomocí klientských certifikátů

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V příslušném článku vám [vytvořit služba RESTful](active-directory-b2c-custom-rest-api-netfw.md) , který komunikuje se službou Azure Active Directory B2C (Azure AD B2C).

V tomto článku se dozvíte, jak omezit přístup k vaší webové aplikace Azure (rozhraní RESTful API) pomocí certifikátu klienta. Tento mechanismus je volána vzájemného ověřování TLS nebo *ověření klientského certifikátu*. Pouze služby, které mají správné certifikáty, jako je například Azure AD B2C, můžete přístup ke službě.

>[!NOTE]
>Můžete také zabezpečení služby RESTful pomocí [základního ověřování protokolu HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Ale základního ověřování protokolu HTTP se považuje za méně zabezpečené přes klientský certifikát. Naše doporučení je zabezpečit službu RESTful pomocí ověření klientského certifikátu, jak je popsáno v tomto článku.

Tento článek podrobně popisuje postup:
* Nastavení webové aplikace pro použití ověřování pomocí certifikátu klienta.
* Nahrajte certifikát do klíče zásad Azure AD B2C.
* Nakonfigurujte vlastní zásady pro použití certifikátu klienta.

## <a name="prerequisites"></a>Požadavky
* Proveďte kroky v [integrace rozhraní REST API deklarací výměny](active-directory-b2c-custom-rest-api-netfw.md) článku.
* Získáte platný certifikát (soubor .pfx s privátním klíčem).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Krok 1: Konfigurace webové aplikace pro ověření klientského certifikátu
K nastavení **služby Azure App Service** tak, aby vyžadovala certifikáty klientů, nastavte webové aplikaci `clientCertEnabled` lokality nastavení *true*. K provedení této změny na webu Azure Portal, otevřete stránce vaší webové aplikace. V levém navigačním panelu v části **nastavení** vyberte **nastavení SSL**. V **klientské certifikáty** části, zapněte **příchozí klientský certifikát** možnost.

>[!NOTE]
>Ujistěte se, že váš plán služby App Service je standardní nebo vyšší. Další informace najdete v tématu [podrobný přehled plánů služby Azure App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

>[!NOTE]
>Další informace o nastavení **povoleném certifikátu klienta** vlastnost, naleznete v tématu [vzájemného ověřování TLS konfigurace pro službu web apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Krok 2: Nahrajte certifikát do klíče zásad Azure AD B2C
Po nastavení `clientCertEnabled` k *true*, komunikace pomocí rozhraní RESTful API vyžaduje klientský certifikát. Získat, nahrávat a ukládat certifikát klienta ve vašem tenantovi Azure AD B2C, postupujte takto: 
1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **architekturu rozhraní identit**.

2. Chcete-li zobrazit klíče, které jsou k dispozici ve vašem tenantovi, vyberte **klíče zásad**.

3. Vyberte **Přidat**.  
    **Vytvořte klíč** otevře se okno.

4. V **možnosti** vyberte **nahrát**.

5. V **název** zadejte **B2cRestClientCertificate**.  
    Předpona, která *B2C_1A_* je automaticky přidán.

6. V **nahrání souboru** , vyberte soubor .pfx vašeho certifikátu s privátním klíčem.

7. V **heslo** zadejte heslo k certifikátu.

    ![Nahrát klíče zásad](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Vyberte **Vytvořit**.

8. Zobrazení klíče, které jsou k dispozici ve vašem tenantovi a potvrďte, že jste vytvořili `B2C_1A_B2cRestClientCertificate` klíč, vyberte **klíče zásad**.

## <a name="step-3-change-the-technical-profile"></a>Krok 3: Změna technický profil
Pro podporu ověřování pomocí certifikátu klienta ve vlastních zásadách, změňte technický profil následujícím způsobem:

1. V pracovním adresáři, otevřete *TrustFrameworkExtensions.xml* soubor rozšíření zásad.

2. Hledat `<TechnicalProfile>` uzel, který zahrnuje `Id="REST-API-SignUp"`.

3. Vyhledejte `<Metadata>` elementu.

4. Změnit *AuthenticationType* k *ClientCertificate*, následujícím způsobem:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Okamžitě po uplynutí `<Metadata>` prvku, přidejte následující fragment kódu XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Po přidání fragmentu kódu, technický profil by měl vypadat jako v následujícím kódu XML:

    ![Nastavena ClientCertificate ověřování XML elementů](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Krok 4: Nahrání zásad do vašeho tenanta

1. V [webu Azure portal](https://portal.azure.com), přepněte [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak vyberte **Azure AD B2C**.

2. Vyberte **architekturu rozhraní identit**.

3. Vyberte **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkExtensions.xml* souboru a poté se ujistěte, že projde úspěšně ověřovacím.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Krok 5: Testování spustit pomocí vlastních zásad
1. Otevřít **nastavení Azure AD B2C**a pak vyberte **architekturu rozhraní identit**.

    >[!NOTE]
    >Spustit nyní vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli a pak vyberte **spustit nyní**.

3. Testování procesu tak, že zadáte **testovací** v **křestní jméno** pole.  
    Azure AD B2C zobrazí chybovou zprávu v horní části okna.    

    ![Otestování rozhraní API vaší identity](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. V **křestní jméno** zadejte název (jiné než "Test").  
    Azure AD B2C zaregistruje uživatele a potom číslo věrnostní odesílá do vaší aplikace. Poznamenejte si číslo v tomto příkladu JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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
   >Pokud se zobrazí chybová zpráva *název není platný, zadejte prosím platný název*, znamená to, že Azure AD B2C úspěšně volat služby RESTful, zatímco zobrazí klientský certifikát. Dalším krokem je ověření certifikátu.

## <a name="step-6-add-certificate-validation"></a>Krok 6: Přidání ověření certifikátu
Klientský certifikát, který Azure AD B2C pošle do vaší služby RESTful nejdou dělit ověřování podle platformy Azure Web Apps, s výjimkou a zkontrolujte, zda existuje certifikát. Ověřování certifikátu zodpovídá za webové aplikace. 

V této části přidejte ukázkový kód ASP.NET, která ověřuje vlastnosti certifikátu pro účely ověřování.

> [!NOTE]
>Další informace o konfiguraci služby Azure App Service pro ověření klientského certifikátu naleznete v tématu [vzájemného ověřování TLS konfigurace pro službu web apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 přidáte nastavení aplikace do souboru web.config vašeho projektu
V projektu sady Visual Studio, který jste vytvořili dříve, přidejte následující nastavení aplikace, které *web.config* souboru po `appSettings` element:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Nahraďte tento certifikát **název subjektu**, **název vystavitele**, a **kryptografický otisk certifikátu** hodnoty hodnotami vašeho certifikátu.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 přidat IsValidClientCertificate – funkce
Otevřít *Controllers\IdentityController.cs* souboru a potom přidat do `Identity` řadič třídy následující funkce: 

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

V předchozím ukázkovém kódu můžeme přijmout certifikát jako platný pouze v případě, že jsou splněny všechny následující podmínky:
* Certifikát není prošlý a aktuální čas na serveru je aktivní.
* `Subject` Název certifikátu je rovna `ClientCertificate:Subject` hodnoty nastavení aplikace.
* `Issuer` Název certifikátu je rovna `ClientCertificate:Issuer` hodnoty nastavení aplikace.
* `thumbprint` Certifikátu je rovna `ClientCertificate:Thumbprint` hodnoty nastavení aplikace.

>[!IMPORTANT]
>V závislosti na citlivosti vaši službu můžete potřebovat přidat další ověření. Například můžete potřebovat k ověření, zda certifikát je zřetězený důvěryhodnou kořenovou autoritou, ověření názvu vystavitele organizace a tak dále.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 volání IsValidClientCertificate – funkce
Otevřít *Controllers\IdentityController.cs* souboru a potom na začátku `SignUp()` funkci, přidejte následující fragment kódu: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Po přidání fragmentu kódu, vaše `Identity` řadič by měl vypadat jako v následujícím kódu:

![Přidejte kód pro ověření certifikátu](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Krok 7: Publikování projektu do Azure a testování
1. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **Contoso.AADB2C.API** projektu a pak vyberte **publikovat**.

2. Opakujte "Krok 6" a znovu otestujte vaše vlastní zásada vypadat pomocí ověřování certifikátu. Zkuste spustit zásady a ujistěte se, že vše funguje po přidání ověření.

3. Ve vaší *web.config* souboru, změňte hodnotu vlastnosti `ClientCertificate:Subject` k **neplatný**. Znovu spusťte zásady a zobrazí se chybová zpráva.

4. Změnit hodnotu zpět na **platný**a ujistěte se, že zásady můžete volat rozhraní REST API.

Pokud potřebujete řešit tento krok, přečtěte si téma [shromažďování protokolů pomocí Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Volitelné) Stažení kompletní zásady souborů a kódu
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návodu, doporučujeme vám vytvořit váš scénář s využitím vlastních zásad pro soubory. Pro srovnání si uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
