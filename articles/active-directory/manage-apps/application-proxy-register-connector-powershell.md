---
title: Bezobslužnou instalaci konektoru Proxy aplikace Azure AD | Microsoft Docs
description: Popisuje, jak provést bezobslužnou instalaci konektoru Proxy aplikace Azure AD poskytnout zabezpečený vzdálený přístup k místní aplikace.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7cc51a3e16c476385fc360ea7f40826e21daaebc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292598"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Vytvořit skript bezobslužné instalace pro konektor proxy aplikace služby Azure AD

Toto téma vám pomůže vytvořit skript prostředí Windows PowerShell, která umožňuje bezobslužné instalace a registrace pro vaše konektoru proxy aplikace služby Azure AD.

Tato možnost je užitečná, když chcete:

* Konektor nainstalujte na serverech Windows, který není k dispozici uživatelské rozhraní, které jsou povolené nebo, ke kterému nelze přistupovat pomocí vzdálené plochy.
* Nainstalujte a zaregistrujte mnoho konektory najednou.
* Integrate instalace konektoru a registraci v rámci jiného režimu.
* Vytvořte standardní serverovou bitovou kopii, která obsahuje službu bits konektor, ale není registrovaný.

Pro [konektor Proxy aplikace](application-proxy-connectors.md) pracovat, má být registrováno v adresáři služby Azure AD pomocí globálního správce a hesla. Obvykle se tyto informace zadá během instalace konektoru v dialogovém okně automaticky otevírané okno, ale můžete použít PowerShell k automatizaci tohoto procesu místo.

Existují dva kroky pro bezobslužnou instalaci. Nejprve nainstalujte konektor. Za druhé registraci konektoru k Azure AD. 

## <a name="install-the-connector"></a>Instalace konektoru
Při instalaci konektoru bez registrace ho použijte následující kroky:

1. Otevřete příkazový řádek.
2. Spusťte následující příkaz, ve kterém /q znamená tichou instalaci. Bezobslužnou instalaci nevyzve přijmout licenční smlouvu s koncovým uživatelem.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrace konektoru s Azure AD
Existují dvě metody, které můžete použít k registraci konektoru:

* Registrace konektoru pomocí objekt pověření prostředí Windows PowerShell
* Registrace konektoru pomocí token vytvořený v režimu offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrace konektoru pomocí objekt pověření prostředí Windows PowerShell
1. Vytvořit objekt přihlašovacích údajů Windows Powershellu `$cred` obsahující správu uživatelské jméno a heslo pro svůj adresář. Spusťte následující příkaz, nahraďte *\<uživatelské jméno\>* a  *\<heslo\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Přejděte na **konektoru Proxy C:\Program Files\Microsoft AAD aplikace** a spusťte následující skript pomocí `$cred` objekt, který jste vytvořili:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrace konektoru pomocí token vytvořený v režimu offline
1. Vytvořte token offline pomocí třídy kontextu AuthenticationContext pomocí hodnot v tomto fragmentu kódu nebo rutiny prostředí PowerShell níže:

    **Pomocí jazyka C#:**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **Pomocí prostředí PowerShell:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Jakmile máte token, vytvořte SecureString pomocí tokenu:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Spusťte následující příkaz prostředí Windows PowerShell, nahraďte \<klienta GUID\> s ID vašeho adresáře:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Další postup 
* [Publikování aplikací s použitím vlastního názvu domény](application-proxy-configure-custom-domain.md)
* [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
* [Řešení problémů, které máte s pomocí Proxy aplikace](application-proxy-troubleshoot.md)


