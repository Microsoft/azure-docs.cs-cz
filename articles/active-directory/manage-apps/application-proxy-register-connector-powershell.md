---
title: Bezobslužná instalace konektoru Proxy aplikace Azure AD | Dokumentace Microsoftu
description: Popisuje, jak provést bezobslužnou instalaci konektoru Proxy aplikace Azure AD poskytovat zabezpečený vzdálený přístup k místním aplikacím.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee35fbfff676e8618a4e5ae02086193c503f482
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170818"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Vytvořit skript bezobslužné instalace pro Azure AD Application Proxy connector

Toto téma vám pomůže vytvořit skript prostředí Windows PowerShell, který umožňuje bezobslužné instalace a registrace konektoru Proxy aplikací Azure AD.

Tato možnost je užitečná, když chcete:

* Konektor nainstalujte na servery Windows, které nemají uživatelské rozhraní povolená, nebo pomocí vzdálené plochy nelze získat přístup.
* Instalace a registrace najednou celou řadu konektorů.
* Integrace konektoru instalační a registrační jako součást jiného procesu.
* Vytvořte standardní serverovou bitovou kopii, která obsahuje službu bits konektoru, ale není registrovaná.

Pro [konektor Proxy aplikací](application-proxy-connectors.md) fungovat, musí být registrována pomocí adresáře Azure AD pomocí globální správce a hesla. Obvykle se tyto informace zadat během instalace konektoru v místním dialogovém, ale můžete použít PowerShell k automatizaci tohoto procesu místo.

Existují dva kroky pro bezobslužnou instalaci. Nejdřív nainstalujte konektor. Za druhé registrace konektoru v Azure AD. 

## <a name="install-the-connector"></a>Instalace konektoru
Použijte následující postup k instalaci konektoru i bez registrace je:

1. Otevřete příkazový řádek.
2. Spusťte následující příkaz, ve kterém /q znamená bezobslužnou instalaci. Bezobslužnou instalaci nevyzve přijmout podmínky licenční smlouvy s koncovým uživatelem.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrace konektoru v Azure AD
Existují dvě metody, které lze použít k registraci konektoru:

* Registrace konektoru pomocí přihlašovacích údajů objektu prostředí Windows PowerShell
* Registrace konektoru pomocí tokenu vytvořený v režimu offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrace konektoru pomocí přihlašovacích údajů objektu prostředí Windows PowerShell
1. Vytvořte objekt přihlašovacích údajů Windows Powershellu `$cred` , který obsahuje uživatelské jméno správce a heslo pro svůj adresář. Spusťte následující příkaz a nahraďte *\<uživatelské jméno\>* a  *\<heslo\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Přejděte na **C:\Program Files\Microsoft AAD App proxy serveru konektoru** a spusťte následující skript využívající `$cred` objekt, který jste vytvořili:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrace konektoru pomocí tokenu vytvořený v režimu offline
1. Vytvoření offline tokenu horizontálních oddílů pomocí třídy kontextu AuthenticationContext s použitím hodnot v tomto fragmentu kódu nebo rutiny prostředí PowerShell níže:

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

3. Spuštěním následujícího příkazu Windows Powershellu nahraďte \<tenanta GUID\> své ID adresáře:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Další postup 
* [Publikování aplikací s použitím vlastního názvu domény](application-proxy-configure-custom-domain.md)
* [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
* [Řešení potíží s problémy, se kterými máte potíže s Proxy aplikací](application-proxy-troubleshoot.md)


