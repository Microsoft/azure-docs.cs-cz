---
title: Konektor proxy Aplikace Azure AD pro tichou instalaci | Microsoft Docs
description: Popisuje, jak provést bezobslužnou instalaci konektoru služby Azure Proxy aplikací služby AD, která poskytuje zabezpečený vzdálený přístup k místním aplikacím.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258146"
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

> [!IMPORTANT]
> Pokud instalujete konektor pro Azure Government Cloud zkontrolujte [požadavky](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) a [kroky instalace](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). K tomu je potřeba povolit přístup k jiné sadě adres URL a další parametr ke spuštění instalace.

## <a name="install-the-connector"></a>Instalace konektoru
K instalaci konektoru bez jeho registrace použijte následující postup:

1. Otevřete příkazový řádek.
2. Spusťte následující příkaz, ve kterém parametr/q znamená tichou instalaci. Po tiché instalaci se zobrazí výzva, abyste přijali Licenční smlouvu End-User.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Registrace konektoru ve službě Azure AD
Existují dvě metody, které můžete použít k registraci konektoru:

* Zaregistrujte konektor pomocí objektu přihlašovacích údajů prostředí Windows PowerShell.
* Registrace konektoru pomocí vytvořeného tokenu offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Zaregistrujte konektor pomocí objektu přihlašovacích údajů prostředí Windows PowerShell.
1. Vytvořte objekt přihlašovacích údajů prostředí Windows PowerShell `$cred` , který obsahuje uživatelské jméno a heslo pro váš adresář. Spusťte následující příkaz a nahraďte *\<username\>* a *\<password\>* :

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Přejít na **C:\Program Files\Microsoft AAD App proxy Connector** a spusťte následující skript pomocí `$cred` objektu, který jste vytvořili:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrace konektoru pomocí vytvořeného tokenu offline
1. Vytvořte offline token pomocí třídy AuthenticationContext pomocí hodnot v tomto fragmentu kódu nebo rutin PowerShellu níže:

   **Pomocí jazyka C#:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **Použití PowerShellu:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Jakmile máte token, vytvořte SecureString pomocí tokenu:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Spusťte následující příkaz prostředí Windows PowerShell a nahraďte \<tenant GUID\> ID vašeho adresáře:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Další kroky
* [Publikování aplikací s použitím vlastního názvu domény](application-proxy-configure-custom-domain.md)
* [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
* [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)