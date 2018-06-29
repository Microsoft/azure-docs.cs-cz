---
title: Zabezpečení služby RESTful pomocí klientských certifikátů v Azure Active Directory B2C | Microsoft Docs
description: Zabezpečit vaše vlastní výměnu REST API deklarace identity ve vašem Azure AD B2C pomocí klientských certifikátů
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fc95974fb7db856d0a255d4a5d1d754649b71eca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098442"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Zabezpečení služby RESTful pomocí klientských certifikátů

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V souvisejícím článku jste [vytvoření RESTful služby](active-directory-b2c-custom-rest-api-netfw.md) který komunikuje se službou Azure Active Directory B2C (Azure AD B2C).

V tomto článku zjistěte, jak omezit přístup k vaší webové aplikace Azure (RESTful API) pomocí certifikátu klienta. Tento mechanismus se nazývá vzájemné ověřování TLS, nebo *ověřování certifikátu klienta*. Pouze služby, které mají správnou certifikáty, jako je například Azure AD B2C, můžete přístup ke službě.

>[!NOTE]
>Můžete také zabezpečení služby RESTful pomocí [základní ověřování protokolu HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Základní ověřování protokolu HTTP se považuje však méně bezpečná přes klientský certifikát. Naše doporučení je služba RESTful zabezpečit pomocí ověření klientského certifikátu, jak je popsáno v tomto článku.

Tento článek podrobnosti o tom, jak:
* Nastavení webové aplikace k použití ověřování pomocí certifikátu klienta.
* Nahrajte certifikát do Azure AD B2C zásad klíče.
* Nakonfigurujte vaše vlastní zásady pro použití certifikátu klienta.

## <a name="prerequisites"></a>Požadavky
* Proveďte kroky v [integrovat REST API deklarací výměnu](active-directory-b2c-custom-rest-api-netfw.md) článku.
* Získáte platný certifikát (soubor .pfx s privátní klíč).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Krok 1: Konfigurace webové aplikace pro ověření certifikátu klienta
Nastavit **Azure App Service** vyžadování klientských certifikátů, nastavení webové aplikace `clientCertEnabled` lokality nastavení *true*. Chcete-li tuto změnu na portálu Azure, otevřete stránku své webové aplikace. V levém navigačním panelu v části **nastavení** vyberte **nastavení SSL**. V **klientské certifikáty** část, zapněte **příchozí klientský certifikát** možnost.

>[!NOTE]
>Ujistěte se, že plán služby Azure App Service je standardní nebo vyšší. Další informace najdete v tématu [podrobný přehled plánů služby Azure App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

>[!NOTE]
>Další informace o nastavení **clientCertEnabled** vlastnost, najdete v části [konfigurace TLS vzájemné ověřování pro webové aplikace](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Krok 2: Nahrajte certifikát do Azure AD B2C zásad klíče
Po nastavení `clientCertEnabled` k *true*, komunikaci se službou rozhraní RESTful API, vyžaduje klientský certifikát. Získat, odeslání a uložte certifikát klienta v klientovi služby Azure AD B2C, postupujte takto: 
1. Ve vašem klientu Azure AD B2C, vyberte **nastavení B2C** > **Identity rozhraní Framework**.

2. Chcete-li zobrazit klíčů, které jsou k dispozici ve vašem klientovi, vyberte **zásad klíče**.

3. Vyberte **Přidat**.  
    **Vytvořte klíč** otevře se okno.

4. V **možnosti** vyberte **nahrát**.

5. V **název** zadejte **B2cRestClientCertificate**.  
    Předpona *B2C_1A_* je automaticky přidán.

6. V **nahrávání souborů** , vyberte soubor .pfx vašeho certifikátu s privátním klíčem.

7. V **heslo** zadejte heslo k certifikátu.

    ![Odešlete klíč zásad](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Vyberte **Vytvořit**.

8. K zobrazení klíčů, které jsou k dispozici ve vašem klientovi a ověřte, že jste vytvořili `B2C_1A_B2cRestClientCertificate` klíč, vyberte **zásad klíče**.

## <a name="step-3-change-the-technical-profile"></a>Krok 3: Změna technické profilu
Pro podporu ověřování certifikátu klienta ve vlastních zásadách, změna technické profil následujícím způsobem:

1. V pracovním adresáři, otevřete *TrustFrameworkExtensions.xml* soubor rozšíření zásad.

2. Vyhledejte `<TechnicalProfile>` uzlu, který zahrnuje `Id="REST-API-SignUp"`.

3. Vyhledejte `<Metadata>` elementu.

4. Změna *AuthenticationType* k *ClientCertificate*, a to takto:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Ihned po zavření `<Metadata>` elementu, přidejte následující fragment kódu XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Po přidání fragmentu váš technické profil by měl vypadat podobně jako následující kód XML:

    ![Nastavit ClientCertificate ověřování XML elementy](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Krok 4: Nahrání zásady klienta

1. V [portál Azure](https://portal.azure.com), přepnout [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a potom vyberte **Azure AD B2C**.

2. Vyberte **Identity rozhraní Framework**.

3. Vyberte **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkExtensions.xml* souboru a pak se ujistěte, že předává ověření.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Krok 5: Testování spustit pomocí vlastních zásad
1. Otevřete **nastavení Azure AD B2C**a potom vyberte **Identity rozhraní Framework**.

    >[!NOTE]
    >Spustit nyní vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, které jste nahráli a potom vyberte **spustit nyní**.

3. Otestujte proces zadáním **Test** v **křestní jméno** pole.  
    Azure AD B2C zobrazí chybovou zprávu v horní části okna.    

    ![Testování vaší identity rozhraní API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. V **křestní jméno** zadejte název (jiné než "Test").  
    Azure AD B2C zaregistruje uživatele a pak odešle číslo věrného do vaší aplikace. Poznamenejte si číslo v tomto příkladu JWT:

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
   >Pokud se zobrazí chybová zpráva *název není platný, zadejte platný název*, znamená to, že Azure AD B2C úspěšně volat služby RESTful, dokud se zobrazí certifikát klienta. Dalším krokem je ověřit certifikát.

## <a name="step-6-add-certificate-validation"></a>Krok 6: Přidání ověření certifikátu
Certifikát klienta, který Azure AD B2C odešle do služby RESTful nepodléhají ověření platformou Azure Web Apps, s výjimkou a zkontrolujte, zda certifikát existuje. Ověřování certifikátu odpovídá webové aplikace. 

V této části přidejte ukázkový kód ASP.NET, která ověřuje vlastnosti certifikátu pro účely ověření.

> [!NOTE]
>Další informace o konfiguraci Azure App Service pro ověření certifikátu klienta najdete v tématu [konfigurace TLS vzájemné ověřování pro webové aplikace](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 přidáte nastavení aplikace do souboru web.config vašeho projektu
V sadě Visual Studio projekt, který jste vytvořili dříve, přidejte následující nastavení aplikace, které *web.config* souboru po `appSettings` element:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Nahraďte tento certifikát **název subjektu**, **název vystavitele**, a **kryptografický otisk certifikátu** hodnoty hodnotami vašeho certifikátu.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 přidat funkci IsValidClientCertificate
Otevřete *Controllers\IdentityController.cs* souboru a pak přidejte do `Identity` řadiče třídy následující funkce: 

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

V předchozím ukázkovém kódu můžeme přijmout si certifikát platný, pouze pokud jsou splněny všechny následující podmínky:
* Certifikát není vypršela a je aktivní pro aktuální čas na serveru.
* `Subject` Název certifikátu je rovno `ClientCertificate:Subject` hodnota nastavení aplikace.
* `Issuer` Název certifikátu je rovno `ClientCertificate:Issuer` hodnota nastavení aplikace.
* `thumbprint` Certifikátu je rovno `ClientCertificate:Thumbprint` hodnota nastavení aplikace.

>[!IMPORTANT]
>V závislosti na velkých a malých písmen služby může být nutné přidat další ověření. Například může být potřeba otestovat, zda certifikát je zřetězený k důvěryhodné kořenové autority, Vystavitel ověření názvu organizace a tak dále.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 volání funkce IsValidClientCertificate
Otevřete *Controllers\IdentityController.cs* souboru a potom na začátku `SignUp()` fungovat, přidejte následující fragment kódu: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Po přidání fragmentu, vaše `Identity` řadič by měl vypadat jako následující kód:

![Přidat kód pro ověření certifikátu](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Krok 7: Publikování projektu v Azure a otestovat ji
1. V **Průzkumníku řešení**, klikněte pravým tlačítkem myši **Contoso.AADB2C.API** projektu a potom vyberte **publikovat**.

2. Opakujte "Krok 6" a znovu otestovat vaše vlastní zásada s ověření certifikátu. Pokuste se spustit zásady a ujistěte se, že vše funguje po přidání ověření.

3. Ve vaší *web.config* souboru, změňte hodnotu `ClientCertificate:Subject` k **neplatný**. Znovu spusťte zásady a měli byste vidět chybovou zprávu.

4. Změnit hodnotu zpět do **platný**a ujistěte se, že zásady můžete volat rozhraní REST API.

Pokud potřebujete, chcete-li vyřešit tento krok, přečtěte si [shromažďování protokolů pomocí Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Volitelné) Stáhnout soubory dokončení zásad a kódu
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návod, doporučujeme vám vytvořit váš scénář pomocí vlastních zásad pro soubory. Pro vaši informaci uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Si můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
