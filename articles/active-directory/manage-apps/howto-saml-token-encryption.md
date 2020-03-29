---
title: Šifrování tokenů SAML ve službě Azure Active Directory
description: Přečtěte si, jak nakonfigurovat šifrování tokenů Azure Active Directory SAML.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0082d841faf22745e609d38444f4a97553b3c867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365862"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Postup: Konfigurace šifrování tokenů Azure AD SAML

> [!NOTE]
> Šifrování tokenů je prémiová funkce Azure Active Directory (Azure AD). Další informace o edicích, funkcích a cenách Azure AD najdete v [tématu Ceny Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Šifrování tokenů SAML umožňuje použití šifrovaných kontrolních výrazů SAML s aplikací, která jej podporuje. Při konfiguraci pro aplikaci Azure AD zašifruje kontrolní výrazy SAML, které pro tuto aplikaci vydává, pomocí veřejného klíče získaného z certifikátu uloženého ve službě Azure AD. Aplikace musí použít odpovídající soukromý klíč k dešifrování tokenu, než jej lze použít jako důkaz ověření pro přihlášeného uživatele.

Šifrování kontrolních výrazů SAML mezi službou Azure AD a aplikací poskytuje další záruku, že obsah tokenu nelze zachytit a ohrožena osobní nebo podniková data.

I bez šifrování tokenů tokeny Azure AD SAML tokeny jsou nikdy předány v síti v vymazat. Azure AD vyžaduje, aby se výměny žádostí o odpověď tokenů a odpovědí uskutečňovaly přes šifrované kanály HTTPS/TLS, aby komunikace mezi IDP, prohlížečem a aplikací probíhala přes šifrované odkazy. Zvažte hodnotu šifrování tokenu pro vaši situaci ve srovnání s režií správy dalších certifikátů.   

Chcete-li nakonfigurovat šifrování tokenů, musíte nahrát soubor certifikátu X.509, který obsahuje veřejný klíč do aplikačního objektu Azure AD, který představuje aplikaci. Chcete-li získat certifikát X.509, můžete jej stáhnout ze samotné aplikace nebo jej získat od dodavatele aplikace v případech, kdy dodavatel aplikace poskytuje šifrovací klíče, nebo v případech, kdy aplikace očekává, že poskytnete soukromý klíč, může být vytvořenpomocí kryptografických nástrojů, část soukromého klíče nahrané do úložiště klíčů aplikace a odpovídající certifikát veřejného klíče nahraný do Azure AD.

Azure AD používá AES-256 k šifrování saml kontrolní data.

## <a name="configure-saml-token-encryption"></a>Konfigurace šifrování tokenů SAML

Chcete-li nakonfigurovat šifrování tokenů SAML, postupujte takto:

1. Získejte certifikát veřejného klíče, který odpovídá soukromému klíči, který je nakonfigurován v aplikaci.

    Vytvořte asymetrický pár klíčů, který chcete použít pro šifrování. Nebo pokud aplikace poskytuje veřejný klíč pro šifrování, postupujte podle pokynů aplikace ke stažení certifikátu X.509.

    Veřejný klíč by měl být uložen v souboru certifikátu X.509 ve formátu CER.

    Pokud aplikace používá klíč, který vytvoříte pro vaši instanci, postupujte podle pokynů poskytnutých vaší aplikací pro instalaci privátního klíče, který aplikace použije k dešifrování tokenů z vašeho klienta Azure AD.

1. Přidejte certifikát do konfigurace aplikace ve službě Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Konfigurace šifrování tokenů na webu Azure Portal

Veřejný certifikát můžete přidat do konfigurace aplikace v rámci portálu Azure.

1. Přejděte na [portál Azure](https://portal.azure.com).

1. Přejděte do okna **aplikace Azure Active Directory > enterprise** a vyberte aplikaci, pro kterou chcete nakonfigurovat šifrování tokenů.

1. Na stránce aplikace vyberte **šifrování tokenu**.

    ![Možnost šifrování tokenů na webu Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Možnost **šifrování tokenu** je k dispozici jenom pro aplikace SAML, které byly nastaveny z okna **podnikové aplikace** na portálu Azure, buď z Galerie aplikací nebo z aplikace mimo galerii. U jiných aplikací je tato možnost nabídky zakázána. Pro aplikace registrované prostřednictvím prostředí **registrace aplikací** na webu Azure Portal můžete nakonfigurovat šifrování tokenů SAML pomocí manifestu aplikace, prostřednictvím Microsoft Graphu nebo přes PowerShell.

1. Na stránce **šifrování tokenu** vyberte **importovat certifikát,** chcete-li importovat soubor CER obsahující veřejný certifikát X.509.

    ![Import souboru CER obsahujícího certifikát X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Po importu certifikátu a nakonfigurován soukromý klíč pro použití na straně aplikace, aktivujte šifrování výběrem **...** vedle stavu kryptografického otisku a pak z možností v rozbalovací nabídce vyberte **Aktivovat šifrování tokenu.**

1. Výběrem **možnosti Ano** potvrďte aktivaci šifrovacího certifikátu tokenu.

1. Zkontrolujte, zda jsou výrazy SAML vysílané pro aplikaci šifrovány.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Deaktivace šifrování tokenů na webu Azure Portal

1. Na webu Azure Portal přejděte na **Azure Active Directory > podnikové aplikace**a vyberte aplikaci, která má povoleno šifrování tokenů SAML.

1. Na stránce aplikace vyberte **Šifrování tokenu**, vyhledejte certifikát a pak vyberte možnost **...** pro zobrazení rozbalovací nabídky.

1. Vyberte **Možnost Deaktivovat šifrování tokenu**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurace šifrování tokenů SAML pomocí rozhraní Graph API, PowerShellu nebo manifestu aplikace

Šifrovací certifikáty jsou uložené na objektu aplikace ve službě Azure AD se značkou `encrypt` využití. Můžete nakonfigurovat více šifrovacích certifikátů a ten, který `tokenEncryptionKeyID` je aktivní pro šifrování tokenů, je identifikován atributem.

K konfiguraci šifrování tokenů pomocí rozhraní Microsoft Graph API nebo PowerShellu budete potřebovat ID objektu aplikace. Tuto hodnotu můžete najít programově nebo na stránce **Vlastnosti** aplikace na webu Azure Portal a s uvedením hodnoty **ID objektu.**

Při konfiguraci keyCredential pomocí graph, PowerShell nebo v manifestu aplikace, měli byste vygenerovat IDENTIFIKÁTOR GUID použít pro keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Konfigurace šifrování tokenů pomocí aplikace Microsoft Graph

1. Aktualizujte aplikace `keyCredentials` pomocí certifikátu X.509 pro šifrování. Následující příklad ukazuje, jak to provést.

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

1. Použijte nejnovější modul Azure AD PowerShell pro připojení k tenantovi.

1. Nastavte nastavení šifrování tokenů pomocí příkazu **[Set-AzureApplication.](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Přečtěte si nastavení šifrování tokenů pomocí následujících příkazů.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Konfigurace šifrování tokenů pomocí manifestu aplikace

1. Na webu Azure najdete v **registračních službách služby Azure Active Directory > .**

1. V rozevíracím souboru vyberte **Všechny aplikace,** chcete-li zobrazit všechny aplikace, a pak vyberte podnikovou aplikaci, kterou chcete nakonfigurovat.

1. Na stránce aplikace vyberte **Manifest,** chcete-li upravit [manifest aplikace](../develop/reference-app-manifest.md).

1. Nastavte hodnotu `tokenEncryptionKeyId` atributu.

    Následující příklad ukazuje manifest aplikace nakonfigurovaný se dvěma šifrovacími certifikáty a druhý vybraný jako aktivní pomocí tokenenryptionKeyId.

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

* Zjistěte, [jak Azure AD používá protokol SAML](../develop/active-directory-saml-protocol-reference.md)
* Naučte se formát, charakteristiky zabezpečení a obsah [tokenů SAML ve službě Azure AD](../develop/reference-saml-tokens.md)