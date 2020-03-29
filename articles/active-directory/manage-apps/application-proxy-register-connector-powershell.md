---
title: Silent install Konektor proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Popisuje, jak provést bezobslužnou instalaci konektoru proxy aplikací Azure AD, který poskytuje zabezpečený vzdálený přístup k místním aplikacím.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b43d2de0a366d7e69a025b2e4e2998dccda2038e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756207"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Vytvoření skriptu bezobslužné instalace pro konektor proxy aplikací Azure AD

Toto téma vám pomůže vytvořit skript prostředí Windows PowerShell, který umožňuje bezobslužnou instalaci a registraci konektoru proxy aplikací Azure AD.

Tato funkce je užitečná, pokud chcete:

* Nainstalujte konektor na servery windows, které nemají povolené uživatelské rozhraní nebo ke kterým nemáte přístup pomocí vzdálené plochy.
* Nainstalujte a zaregistrujte mnoho konektorů najednou.
* Integrujte instalaci a registraci konektoru jako součást jiného postupu.
* Vytvořte standardní bit serveru, který obsahuje bity konektoru, ale není registrován.

Aby [konektor proxy aplikace](application-proxy-connectors.md) fungoval, musí být zaregistrován v adresáři Azure AD pomocí správce aplikace a hesla. Obvykle se tyto informace zadávají během instalace konektoru v rozbalovacím dialogovém okně, ale místo toho můžete tento proces automatizovat pomocí prostředí PowerShell.

Existují dva kroky pro bezobslužnou instalaci. Nejprve nainstalujte konektor. Za druhé zaregistrujte konektor s Azure AD. 

## <a name="install-the-connector"></a>Instalace konektoru
Pomocí následujících kroků nainstalujte konektor bez registrace:

1. Otevřete příkazový řádek.
2. Spusťte následující příkaz, ve kterém /q znamená tichou instalaci. Tichá instalace vás nevyzve k přijetí licenční smlouvy s koncovým uživatelem.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrace konektoru pomocí Azure AD
Existují dvě metody, které můžete použít k registraci konektoru:

* Registrace konektoru pomocí objektu pověření prostředí Windows PowerShell
* Registrace konektoru pomocí tokenu vytvořeného offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrace konektoru pomocí objektu pověření prostředí Windows PowerShell
1. Vytvořte objekt `$cred` pověření prostředí Windows PowerShell, který obsahuje uživatelské jméno a heslo pro správce vašeho adresáře. Spusťte následující příkaz, který nahradí * \<uživatelské jméno\> * a * \<heslo\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Přejděte na **c:\Program Files\Microsoft AAD App Proxy** `$cred` Connector a spusťte následující skript pomocí vytvořeného objektu:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrace konektoru pomocí tokenu vytvořeného offline
1. Vytvořte offline token pomocí třídy AuthenticationContext pomocí hodnot v tomto fragmentu kódu nebo rutinách prostředí PowerShell níže:

    **Použití jazyka C#:**

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

    **Použití Prostředí PowerShell:**

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

2. Jakmile budete mít token, vytvořte SecureString pomocí tokenu:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Spusťte následující příkaz prostředí \<Windows PowerShell, který nahradí identifikátor GUID klienta\> id adresáře:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Další kroky 
* [Publikování aplikací s použitím vlastního názvu domény](application-proxy-configure-custom-domain.md)
* [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
* [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)


