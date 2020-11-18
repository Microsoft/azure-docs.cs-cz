---
title: Šifrování tokenu SAML v Azure Active Directory
description: Přečtěte si, jak nakonfigurovat Azure Active Directory šifrování tokenu SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 820e2cb0d422597f0e649e6934fd8bb11c1521db
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659025"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Postupy: Konfigurace šifrování tokenů SAML v Azure AD

> [!NOTE]
> Šifrování tokenu je funkce Premium služby Azure Active Directory (Azure AD). Další informace o edicích, funkcích a cenách Azure AD najdete v tématu [ceny služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Šifrování tokenu SAML umožňuje použití šifrovaných kontrolních výrazů SAML v aplikaci, která ji podporuje. Při konfiguraci pro aplikaci Azure AD zašifruje kontrolní výrazy SAML, které tato aplikace vygeneruje, pomocí veřejného klíče získaného z certifikátu uloženého v Azure AD. Aby bylo možné token použít jako legitimaci ověřování pro přihlášeného uživatele, musí aplikace použít stejný privátní klíč k dešifrování.

Šifrování kontrolních výrazů SAML mezi Azure AD a aplikací poskytuje další jistotu, že obsah tokenu nejde zachytit, a osobní nebo firemní data budou ohrožená.

Bez šifrování tokenů se tokeny SAML služby Azure AD nikdy neúspěšně předávají v síti v jasném případě. Služba Azure AD vyžaduje, aby výměna požadavků a odpovědí na tokeny probíhat prostřednictvím šifrovaných kanálů HTTPS/TLS, aby byla komunikace mezi IDP, prohlížečem a aplikací prováděna prostřednictvím šifrovaných odkazů. Vezměte v úvahu hodnotu šifrování tokenu pro vaši situaci v porovnání s režií při správě dalších certifikátů.   

Ke konfiguraci šifrování tokenů je potřeba nahrát soubor certifikátu X. 509, který obsahuje veřejný klíč, do objektu aplikace Azure AD, který představuje aplikaci. Pokud chcete získat certifikát X. 509, můžete si ho stáhnout ze samotné aplikace nebo ho získat od dodavatele aplikace v případech, kdy dodavatel aplikace poskytne šifrovací klíče, nebo v případech, kdy aplikace očekává, že jste zadali privátní klíč, můžete ho vytvořit pomocí kryptografických nástrojů, část privátního klíče nahraného do úložiště klíčů aplikace a odpovídajícího certifikátu veřejného klíče odeslaného do Azure AD.

Azure AD používá algoritmus AES-256 k šifrování dat kontrolního výrazu SAML.

## <a name="configure-saml-token-encryption"></a>Konfigurace šifrování tokenu SAML

Při konfiguraci šifrování tokenů SAML postupujte takto:

1. Získejte certifikát veřejného klíče, který odpovídá privátnímu klíči nakonfigurovanému v aplikaci.

    Vytvořte dvojici asymetrických klíčů, která se má použít pro šifrování. Nebo pokud aplikace poskytuje veřejný klíč, který se má použít pro šifrování, Stáhněte si certifikát X. 509 podle pokynů aplikace.

    Veřejný klíč by měl být uložený v souboru certifikátu X. 509 ve formátu. cer.

    Pokud aplikace používá klíč, který vytvoříte pro instanci, postupujte podle pokynů, které vám poskytuje aplikace pro instalaci privátního klíče, který aplikace použije k dešifrování tokenů z vašeho tenanta Azure AD.

1. Přidejte certifikát do konfigurace aplikace ve službě Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Konfigurace šifrování tokenů v Azure Portal

Veřejný certifikát můžete přidat do konfigurace aplikace v rámci Azure Portal.

1. Přejděte na web [Azure Portal](https://portal.azure.com).

1. V okně **Azure Active Directory > podnikových aplikací** vyberte aplikaci, pro kterou chcete nakonfigurovat šifrování tokenu.

1. Na stránce aplikace vyberte **šifrování tokenu**.

    ![Možnost šifrování tokenu v Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Možnost **šifrování tokenu** je dostupná jenom pro aplikace SAML, které jste nastavili v okně **podnikové aplikace** v Azure Portal, a to buď z Galerie aplikací, nebo z aplikace mimo galerii. Pro jiné aplikace je tato možnost nabídky zakázána. Pro aplikace zaregistrované v prostředí **Registrace aplikací** v Azure Portal můžete nakonfigurovat šifrování pro tokeny SAML pomocí manifestu aplikace, prostřednictvím Microsoft Graph nebo prostřednictvím PowerShellu.

1. Na stránce **šifrování tokenů** vyberte **importovat certifikát** , abyste importovali soubor. CER, který obsahuje váš veřejný certifikát X. 509.

    ![Importujte soubor. CER, který obsahuje certifikát X. 509.](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Po importu certifikátu a zadání privátního klíče pro použití na straně aplikace aktivujte šifrování tak, že vyberete **...** vedle stavu kryptografických otisků, a pak vyberte **aktivovat šifrování tokenů** z možností v rozevírací nabídce.

1. Výběrem **Ano** potvrďte aktivaci certifikátu pro šifrování tokenu.

1. Ověřte, zda jsou výrazy SAML vydávané pro aplikaci zašifrované.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Deaktivace šifrování tokenu v Azure Portal

1. V Azure Portal klikněte na **Azure Active Directory > podnikové aplikace** a pak vyberte aplikaci s povoleným šifrováním tokenu SAML.

1. Na stránce aplikace vyberte **šifrování tokenu**, Najděte certifikát a potom vyberte možnost **...** . zobrazí se rozevírací nabídka.

1. Vyberte **deaktivovat šifrování tokenu**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurace šifrování tokenu SAML pomocí Graph API, PowerShellu nebo manifestu aplikace

Šifrovací certifikáty se ukládají v aplikačním objektu v Azure AD se `encrypt` značkou použití. Můžete nakonfigurovat několik šifrovacích certifikátů a tu, která je aktivní pro šifrování tokenů, je identifikována `tokenEncryptionKeyID` atributem.

K nakonfigurování šifrování tokenů pomocí rozhraní Microsoft Graph API nebo prostředí PowerShell budete potřebovat ID objektu aplikace. Tuto hodnotu můžete najít programově nebo na stránce **vlastností** aplikace v Azure Portal a zaznamenání hodnoty **ID objektu** .

Když konfigurujete přihlašovací údaje pomocí grafu, PowerShellu nebo v manifestu aplikace, měli byste vygenerovat identifikátor GUID, který se má použít pro keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Konfigurace šifrování tokenů pomocí Microsoft Graph

1. Aktualizujte aplikaci `keyCredentials` pomocí certifikátu X. 509 pro šifrování. Následující příklad ukazuje, jak to provést.

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

### <a name="to-configure-token-encryption-using-powershell"></a>Konfigurace šifrování tokenů pomocí PowerShellu

1. K připojení k vašemu tenantovi použijte nejnovější modul Azure AD PowerShell.

1. Nastavení šifrování tokenu nastavte pomocí příkazu **[set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** .

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Přečtěte si nastavení šifrování tokenu pomocí následujících příkazů.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Konfigurace šifrování tokenů pomocí manifestu aplikace

1. V Azure Portal **Azure Active Directory > registrace aplikací**.

1. Výběrem možnost **všechny aplikace** z rozevíracího seznamu zobrazíte všechny aplikace a pak vyberte podnikovou aplikaci, kterou chcete nakonfigurovat.

1. Na stránce aplikace vyberte **manifest** pro úpravu [manifestu aplikace](../develop/reference-app-manifest.md).

1. Nastavte hodnotu `tokenEncryptionKeyId` atributu.

    Následující příklad ukazuje manifest aplikace nakonfigurovaný pomocí dvou šifrovacích certifikátů a druhý vybraný jako aktivní pomocí tokenEnryptionKeyId.

    ```json
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

## <a name="next-steps"></a>Další kroky

* Zjistěte [, jak Azure AD používá protokol SAML](../develop/active-directory-saml-protocol-reference.md) .
* Naučte se formát, charakteristiky zabezpečení a obsah [tokenů SAML v Azure AD](../develop/reference-saml-tokens.md) .