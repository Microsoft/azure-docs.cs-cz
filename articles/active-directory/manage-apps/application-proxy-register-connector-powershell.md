---
title: Konektor proxy Aplikace Azure AD pro tichou instalaci | Microsoft Docs
description: Popisuje, jak provést bezobslužnou instalaci konektoru služby Azure Proxy aplikací služby AD, která poskytuje zabezpečený vzdálený přístup k místním aplikacím.
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
ms.date: 01/23/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca40e9717394690374f5ca289a69e5c22551eb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712005"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Vytvoření skriptu bezobslužné instalace pro konektor Azure Proxy aplikací služby AD

Toto téma vám pomůže vytvořit skript prostředí Windows PowerShell, který umožňuje bezobslužnou instalaci a registraci konektoru služby Azure Proxy aplikací služby AD.

Tato funkce je užitečná v případě, že chcete:

* Nainstalujte konektor na Windows servery, které nemají povolené uživatelské rozhraní, nebo jestli nemáte přístup pomocí vzdálené plochy.
* Nainstalujte a zaregistrujte mnoho konektorů najednou.
* Integrujte instalaci konektoru a registraci jako součást jiného postupu.
* Vytvořte standardní image serveru, která obsahuje bity konektoru, ale není zaregistrovaná.

Aby [konektor proxy aplikací](application-proxy-connectors.md) fungoval, musí být zaregistrovaný v adresáři služby Azure AD pomocí správce aplikace a hesla. Tyto informace se obvykle zadávají při instalaci konektoru v automaticky otevíraném dialogovém okně, ale k automatizaci tohoto procesu můžete použít PowerShell.

Existují dva kroky pro bezobslužnou instalaci. Nejdřív nainstalujte konektor. Za druhé Zaregistrujte konektor u služby Azure AD. 

## <a name="install-the-connector"></a>Instalace konektoru
K instalaci konektoru bez jeho registrace použijte následující postup:

1. Otevřete příkazový řádek.
2. Spusťte následující příkaz, ve kterém parametr/q znamená tichou instalaci. Tichá instalace vás nevyzve, abyste přijali Licenční smlouvu s koncovým uživatelem.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrace konektoru ve službě Azure AD
Existují dvě metody, které můžete použít k registraci konektoru:

* Zaregistrujte konektor pomocí objektu přihlašovacích údajů prostředí Windows PowerShell.
* Registrace konektoru pomocí vytvořeného tokenu offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Zaregistrujte konektor pomocí objektu přihlašovacích údajů prostředí Windows PowerShell.
1. Vytvořte objekt přihlašovacích údajů prostředí Windows PowerShell `$cred`, který obsahuje uživatelské jméno a heslo pro váš adresář. Spusťte následující příkaz a nahraďte *\<username\>* a *\<hesla\>* :
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Přejít na **C:\Program Files\Microsoft AAD App proxy Connector** a spusťte následující skript pomocí objektu `$cred`, který jste vytvořili:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrace konektoru pomocí vytvořeného tokenu offline
1. Vytvořte offline token pomocí třídy AuthenticationContext pomocí hodnot v tomto fragmentu kódu nebo rutin PowerShellu níže:

    **Používá C#se:**

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
        static readonly Uri ConnectorRedirectAddress = new Uri("https://login.microsoftonline.com/common/oauth2/nativeclient");

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

    **Použití PowerShellu:**

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
        [uri]$ConnectorRedirectAddress = "https://login.microsoftonline.com/common/oauth2/nativeclient" 
        
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

3. Spusťte následující příkaz prostředí Windows PowerShell a nahraďte \<identifikátor GUID klienta\> s vaším ID adresáře:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Další kroky 
* [Publikování aplikací s použitím vlastního názvu domény](application-proxy-configure-custom-domain.md)
* [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
* [Řešení potíží s problémy, se kterými máte potíže s Proxy aplikací](application-proxy-troubleshoot.md)


