---
title: Šifrování tokenu SAML v Azure Active Directory
description: Zjistěte, jak nakonfigurovat šifrování tokenu Azure Active Directory SAML.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: celested
ms.reviewer: paulgarn
ms.openlocfilehash: 21bd83511f09c3049c396e13161e02dead6e3459
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100002"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Postup: Konfigurace šifrování tokenu Azure AD SAML (Preview)

> [!NOTE]
> Šifrování tokenu je funkce Azure Active Directory (Azure AD) premium. Další informace o službě Azure AD edice, funkce a ceny najdete v tématu [ceny služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Šifrování tokenu SAML umožňuje použití šifrovaných kontrolní výrazy SAML s aplikací, která ho podporuje. Při konfiguraci pro aplikaci Azure AD bude šifrovat kontrolní výrazy SAML, které vydává pro příslušnou aplikaci pomocí veřejného klíče získané z certifikátu uloženého v Azure AD. Aplikace musí používat odpovídající privátní klíč pro dešifrování token předtím, než může sloužit jako doklad o ověřování pro přihlášeného uživatele.

Šifrování kontrolní výrazy SAML mezi službami Azure AD a aplikace poskytuje další záruku, že nemůže dojít k jejich zachycení obsah tokenu a dojde k ohrožení bezpečnosti osobní nebo podniková data.

I bez šifrování tokenů jsou v síti v nešifrované podobě nebyl nikdy předán tokeny Azure AD SAML. Azure AD vyžaduje token požadavku nebo odpovědi výměny uskutečnit přes šifrované kanály HTTPS/TLS, aby komunikace mezi zprostředkovatele identity, prohlížeč a aplikaci probíhat přes šifrované odkazy. Vezměte v úvahu hodnota šifrování tokenu pro vaši situaci ve srovnání s režijní náklady správy dalších certifikátů.   

Pokud chcete nakonfigurovat šifrování tokenů, budete muset nahrát soubor certifikátu X.509, který obsahuje veřejný klíč do objektu aplikace Azure AD, který reprezentuje aplikaci. Pokud chcete získat certifikát X.509, můžete ji stáhnout z aplikace sám nebo ho od dodavatele aplikace v případech, kde na dodavatele aplikace obsahuje šifrovací klíče nebo v případech, kde ji aplikace očekává, abychom vám poskytli privátní klíč, může být get vytvořené pomocí nástroje pro šifrování, část privátního klíče nahráli do úložiště klíčů vaší aplikace a nahrát odpovídající certifikátu veřejného klíče do služby Azure AD.

Azure AD používá AES-256 k šifrování dat kontrolního výrazu SAML.

## <a name="configure-saml-token-encryption"></a>Konfigurace šifrování tokenu SAML

Pokud chcete nakonfigurovat šifrování tokenu SAML, postupujte takto:

1. Získání certifikátu veřejného klíče, který odpovídá privátní klíč, který je nakonfigurovaný v aplikaci.

    Vytvoření asymetrický pár klíčů má použít pro šifrování. Nebo, pokud aplikace poskytuje veřejného klíče pro šifrování, postupujte podle pokynů vaší aplikace ke stažení certifikátu X.509.

    Veřejný klíč by měl být uložený v soubory ve formátu .cer certifikátu X.509.

    Pokud aplikace používá klíč, který vytvoříte pro vaše instance, postupujte podle pokynů uvedených v aplikaci pro instalaci privátní klíč, který bude aplikace používat k dešifrování tokenů ve svém tenantovi Azure AD.

1. Přidání certifikátu do konfigurace aplikace ve službě Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Ke konfiguraci šifrování tokenů na webu Azure Portal

Veřejný certifikát můžete přidat konfiguraci aplikace na webu Azure portal.

1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Přejděte **Azure Active Directory > podnikové aplikace** okna a pak vyberte aplikaci, kterou chcete nakonfigurovat šifrování tokenů pro.

1. Na stránce aplikace vyberte **Token šifrování**.

    ![Možnost šifrování tokenů na webu Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > **Token šifrování** možnost je dostupná jenom pro aplikace SAML, které byly vytvořeny z **podnikové aplikace** okna portálu Azure portal, buď z Galerie aplikací nebo Aplikace mimo galerii. U ostatních aplikací se tato možnost nabídky je zakázaná. Pro aplikace registrovaný prostřednictvím metody **registrace aplikací** prostředí na webu Azure Portal, můžete nakonfigurovat šifrování pro manifest tokeny SAML pomocí aplikace, prostřednictvím Microsoft Graphu, nebo přes PowerShell.

1. Na **Token šifrování** stránce **importovat certifikát** importovat soubor .cer, který obsahuje váš veřejný certifikát X.509.

    ![Importovat soubor .cer, který obsahuje certifikát X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Po importu certifikátu a privátního klíče je nakonfigurován pro použití na straně aplikace, aktivujte šifrování tak, že vyberete **...**  další stav kryptografický otisk a pak vyberte **aktivovat šifrování tokenů** z možnosti v rozevírací nabídce.

1. Vyberte **Ano** potvrďte aktivace token šifrovací certifikát.

1. Potvrďte, že jsou šifrovaná kontrolní výrazy SAML pro aplikaci.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Deaktivace šifrování tokenů na webu Azure Portal

1. Na webu Azure Portal, přejděte na **Azure Active Directory > podnikové aplikace**a pak vyberte aplikaci, která má povolené šifrování tokenu SAML.

1. Na stránce aplikace vyberte **Token šifrování**, vyhledejte certifikát a pak vyberte **...**  možnost zobrazit rozevírací nabídky.

1. Vyberte **deaktivovat šifrování tokenů**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurace šifrování tokenu SAML pomocí rozhraní Graph API, Powershellu nebo manifestu aplikace

Šifrovací certifikáty se ukládají na objekt aplikace ve službě Azure AD pomocí `encrypt` využití značek. Můžete nakonfigurovat více šifrovací certifikáty a ten, který je aktivní pro šifrování tokenů je identifikován `tokenEncryptionKeyID` atribut.

Budete potřebovat ID objektu vaší aplikace ke konfiguraci šifrování tokenů, použijte PowerShell nebo rozhraní Microsoft Graph API. Tuto hodnotu lze najít prostřednictvím kódu programu, nebo tak, že přejdete do vaší aplikace **vlastnosti** stránku webu Azure portal a zobrazí **ID objektu** hodnotu.

Když konfigurujete keyCredential pomocí grafu, PowerShell, nebo v manifestu aplikace, byste měli generovat identifikátor GUID pro účely ID klíče.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Ke konfiguraci šifrování tokenu pomocí Microsoft Graphu

1. Aktualizace aplikace `keyCredentials` pomocí certifikátu X.509 pro šifrování. Následující příklad ukazuje, jak to provést.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identifikujte šifrovací certifikát, který je aktivní pro šifrování tokenů. Následující příklad ukazuje, jak to provést.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Ke konfiguraci šifrování tokenu pomocí Powershellu

Tato funkce je již brzy. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials “<KeyCredentialsObject>”  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```
-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Ke konfiguraci šifrování tokenu pomocí manifest aplikace

1. Na webu Azure Portal, přejděte na **Azure Active Directory > Registrace aplikací**.

1. Vyberte **všechny aplikace** z rozevíracího seznamu zobrazit všechny aplikace, a potom vyberte podniková aplikace, kterou chcete konfigurovat.

1. Na stránce aplikace vyberte **Manifest** upravit [manifest aplikace](../develop/reference-app-manifest.md).

1. Nastavte hodnotu `tokenEncryptionKeyId` atribut.

    Následující příklad ukazuje manifest aplikace nakonfigurované dva šifrovací certifikáty a s druhým vybrané jako aktivní pomocí tokenEnryptionKeyId.

    ```
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Další postup

* Přečtěte si [jak služby Azure AD používá protokol SAML](../develop/active-directory-saml-protocol-reference.md)
* Informace o formátu, vlastnosti zabezpečení a obsah [tokeny SAML ve službě Azure AD](../develop/reference-saml-tokens.md)
