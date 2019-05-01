---
title: Virtuální Desktop Windows tenanta a hostitele Vytvoření fondu – Azure
description: Tom, jak odstraňovat potíže a řešit tenanta a hostitele fondu problémy při nastavení prostředí virtuálního klienta Windows tenanta.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 9f52d1b949310792ffedc6e4723fbfb423fb5dfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928494"
---
# <a name="tenant-and-host-pool-creation"></a>Vytvoření fondu tenanta a hostitele

Tento článek se týká problémy při počátečním nastavení tenanta virtuální plochy Windows a související relace hostitelské fondu infrastruktury.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Můžeme aktuálně nejsou trvá případy podpory virtuální plochy Windows je ve verzi preview. Přejděte [technické komunitě virtuální plochy Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) fattica virtuální plochy Windows service s produktovým týmem a aktivní komunitě členy.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Získání více relací image Windows 10 Enterprise

Použití více relací image Windows 10 Enterprise, přejděte na Azure Marketplace vyberte **Začínáme** > **Microsoft Windows 10** > a [Windows 10 Enterprise pro Virtuálních klientů ve verzi Preview, verze 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Snímek obrazovky s výběrem virtuálních ploch ve verzi Preview 1809 verze Windows 10 Enterprise.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Vytvoření virtuálního klienta Windows tenanta

Tento oddíl řeší potenciální potíže při vytváření tenanta virtuální plochy Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Uživatel nemá oprávnění k dotazování na službu správy

![Snímek obrazovky z prostředí PowerShell okno, ve kterém uživatel nemá oprávnění k dotazování na službu správy.](media/UserNotAuthorizedNewTenant.png)

Příklad nezpracovaná Chyba:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Příčina:** Uživatel, který je přihlášen nebyla přiřazena role TenantCreator v Azure Active Directory.

**Oprava:** Postupujte podle pokynů v [přiřazení role aplikace TenantCreator uživatel ve vašem tenantovi Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Po provedení kroků, budete mít uživatel přiřazený k roli TenantCreator.

![Snímek obrazovky TenantCreator role přiřazená.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Vytvoření virtuálního klienta Windows relace hostování virtuálních počítačů

Relace hostitele virtuálních počítačů lze vytvořit několika způsoby, ale Vzdálená plocha virtuální služby nebo Windows Desktop týmy podporují pouze problémy související s šablony Azure Resource Manageru zřizování virtuálního počítače. Šablony Azure Resource Manageru je k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/) a [Githubu](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problémy s použitím virtuální plochy Windows – poskytování nabídek Azure Marketplace fondu typu host

Virtuální Desktop Windows – zřízení fondu šablonu hostitele je k dispozici na webu Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Chyba: Zobrazí se při použití odkazu z Githubu, zpráva "vytvořte si bezplatný účet"

![Snímek obrazovky Vytvořit bezplatný účet.](media/be615904ace9832754f0669de28abd94.png)

**1. příčina:** Nejsou k dispozici aktivní odběry v účet použitý k přihlášení do Azure nebo použitý účet nemá oprávnění k zobrazení předplatných.

**Oprava 1:** Přihlaste se pomocí účtu, který má přístup přispěvatele (minimálně) k předplatnému, kam relace hostitele virtuálních počítačů k nasazení.

**2. příčina:** Používané předplatné je součástí tenanta Microsoft Cloud Service Provider (CSP).

**Oprava 2:** Přejděte do umístění Githubu pro **vytvoření a zřízení nového fondu hostitele virtuální plochy Windows** a postupujte podle těchto pokynů:

1. Klikněte pravým tlačítkem na **nasadit do Azure** a vyberte **Kopírovat odkaz adresu**.
2. Otevřít **Poznámkový blok** a vložte odkaz.
3. Před znakem # vložte název CSP koncový zákazník tenanta.
4. Otevřete nový odkaz v prohlížeči a na webu Azure portal načte šablonu.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Šablona Azure Resource Manageru a prostředí PowerShell Desired State Configuration (DSC) chyby

Postupujte podle těchto pokynů k řešení neúspěšných nasazení šablon Azure Resource Manageru a prostředí PowerShell DSC.

1. Zkontrolujte chyby v nasazení s použitím [zobrazení operací nasazení pomocí Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Pokud v nasazení nejsou žádné chyby, zkontrolujte chyby v protokolu aktivit pomocí [zobrazení protokolů aktivit pro auditování akcí u prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Po identifikaci chyby použijte chybové zprávy a prostředky v [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) k vyřešení příslušného problému.
4. Odstraňte všechny prostředky vytvořené v rámci předchozího nasazení a opakovat akci nasazení šablony.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Chyba: Selhání vašeho nasazení <hostname> /JoinDomain

![Snímek obrazovky se nepovedlo nasadit.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Příklad nezpracovaná Chyba:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**1. příčina:** Přihlašovací údaje zadané pro připojení virtuálních počítačů k doméně nejsou správné.

**Oprava 1:** Zobrazí chyba "Nesprávné přihlašovací údaje" pro virtuální počítače nejsou připojené k doméně v [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).

**2. příčina:** Název domény se nevyřeší.

**Oprava 2:** Zobrazí chyba "nelze vyřešit název domény" pro virtuální počítače nejsou připojené k doméně v [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).

### <a name="error-vmextensionprovisioningerror"></a>Chyba: VMExtensionProvisioningError

![Snímek obrazovky z vašeho nasazení se nezdařilo s konečným stavem zřizování se nezdařila.](media/7aaf15615309c18a984673be73ac969a.png)

**1. příčina:** Přechodná chyba v prostředí virtuálního klienta Windows.

**2. příčina:** Přechodné chybě připojení.

**Oprava:** Potvrďte, že prostředí virtuálního klienta Windows je v pořádku po přihlášení pomocí Powershellu. Dokončit registraci virtuálního počítače ručně v [vytvořit fond hostitele s prostředím PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Chyba: Zadané uživatelské jméno správce není povolený.

![Což není povoleno zadat správce snímek vašich nasazení se nezdařilo.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Příklad nezpracovaná Chyba:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Příčina:** Zadané heslo obsahuje zakázané podřetězců (admin, administrator, kořenový).

**Oprava:** Aktualizujte uživatelské jméno nebo použijte jiné uživatele.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Chyba: Virtuální počítač nahlásil chybu při zpracování rozšíření

![Snímek obrazovky operace prostředku se dokončila s konečným stavem zřizování v vaše nasazení se nezdařilo.](media/49c4a1836a55d91cd65125cf227f411f.png)

Příklad nezpracovaná Chyba:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Příčina:** Rozšíření PowerShell DSC nebyl schopen získat přístup správce na virtuálním počítači.

**Oprava:** Zkontrolujte uživatelské jméno a heslo mají přístup pro správu na virtuálním počítači a znovu spusťte šablony Azure Resource Manageru.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Chyba: DeploymentFailed – konfigurace DSC Powershellu FirstSessionHost dokončilo s chybami

![Snímek obrazovky se nasazení nezdaří s konfigurací DSC Powershellu FirstSessionHost dokončilo s chybami.](media/64870370bcbe1286906f34cf0a8646ab.png)

Příklad nezpracovaná Chyba:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Příčina:** Rozšíření PowerShell DSC nebyl schopen získat přístup správce na virtuálním počítači.

**Oprava:** Potvrďte, že uživatelské jméno a heslo, které jsou k dispozici máte přístup správce na virtuálním počítači a znovu spusťte šablony Azure Resource Manageru.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Chyba: DeploymentFailed – InvalidResourceReference

Příklad nezpracovaná Chyba:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Příčina:** Část názvu skupiny prostředků se používá k určitým prostředkům vytváří pomocí šablony. Z důvodu názvem odpovídajícím existujících prostředků můžou šablony vybrat existující prostředek v jiné skupině.

**Oprava:** Při spouštění šablony Azure Resource Manageru k nasazení hostitele relace virtuálních počítačů, ujistěte se, první dva znaky pro název skupiny prostředků předplatného jedinečný.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Chyba: DeploymentFailed – InvalidResourceReference

Příklad nezpracovaná Chyba:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Příčina:** Tato chyba je vzhledem k tomu stejný název jako další síťová karta NIC vytvořen pomocí šablony Azure Resource Manageru již má ve virtuální síti.

**Oprava:** Použijte předponu jiného hostitele.

### <a name="error-deploymentfailed--error-downloading"></a>Chyba: DeploymentFailed – Chyba při stahování

Příklad nezpracovaná Chyba:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Příčina:** Tato chyba je způsobená statickou trasu, pravidlo brány firewall nebo NSG blokuje stahování souboru zip vázané na šablonu Azure Resource Manageru.

**Oprava:** Odeberte statickou trasu, pravidlo brány firewall nebo NSG blokuje. Volitelně v textovém editoru otevřete soubor šablony json Azure Resource Manageru, využijte odkaz na soubor zip a stahování prostředku do umístění povolené.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Uživatel nemá oprávnění k dotazování na službu správy

Příklad nezpracovaná Chyba:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Příčina:** Zadaný správcem tenanta virtuální plochy Windows nemá platnou roli přiřazení.

**Oprava:** Uživatel, který vytvořil tenanta virtuální plochy Windows potřebuje k přihlášení do Powershellu virtuální plochy Windows a přiřaďte uživateli pokus o přiřazení role. Pokud používáte GitHub Azure Resource Manageru parametry šablony, postupujte podle těchto pokynů, pomocí příkazů prostředí PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
Set-RdsContext -Name <Windows Virtual Desktop tenant group name>
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Chyba: Uživatel vyžaduje Azure Multi-Factor Authentication (MFA)

![Snímek obrazovky nasazení se nezdařilo z důvodu nedostatku ověřování službou Multi-Factor Authentication (MFA)](media/MFARequiredError.png)

Příklad nezpracovaná Chyba:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Příčina:** Zadaný správcem tenanta virtuální plochy Windows vyžaduje Azure Multi-Factor Authentication (MFA) pro přihlášení.

**Oprava:** Vytvoření instančního objektu a přiřaďte ho roli pro vašeho tenanta virtuální plochy Windows podle postupu v [kurzu: Vytvoření instančních objektů a přiřazení role pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Po ověření, že můžete se přihlásit k virtuálnímu klientovi Windows pomocí instančního objektu, spusťte znovu nabídky Azure Marketplace nebo šablon GitHub Azure Resource Manageru, v závislosti na tom, jakou metodu, kterou používáte. Postupujte podle níže uvedených pokynů zadejte správné parametry pro metodu.

Pokud používáte nabídek Azure Marketplace, zadejte hodnoty následujících parametrů k ověření správně virtuální plochy Windows:

- Virtuální Desktop Windows tenanta vlastníka vzdálené plochy: Instanční objekt
- ID aplikace: Vytvoření aplikace identifikace nový instanční objekt služby
- Heslo a potvrzení hesla: Heslo tajný klíč, který jste vygenerovali pro instanční objekt
- ID klienta Azure AD: Vytvoření Tenanta Azure AD ID instančního objektu

Pokud používáte GitHub Azure Resource Manageru šablony, zadejte hodnoty následujících parametrů k ověření správně virtuální plochy Windows:

- Tenant správce hlavní název uživatele (UPN) nebo ID aplikace: Vytvoření aplikace identifikace nový instanční objekt služby
- Heslo správce tenanta: Heslo tajný klíč, který jste vygenerovali pro instanční objekt
- IsServicePrincipal: **true**
- AadTenantId: Vytvoření Tenanta Azure AD ID instančního objektu

## <a name="next-steps"></a>Další postup

- Přehled o řešení potíží virtuální plochy Windows a sleduje eskalace, naleznete v tématu [řešení potíží s přehled, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Řešení potíží při konfiguraci virtuálního počítače (VM) v virtuální plochy Windows, naleznete v tématu [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Řešení potíží s připojeními klientů virtuální plochy Windows, naleznete v tématu [klienta připojení ke vzdálené ploše](troubleshoot-client-connection.md).
- Při řešení problémů při použití Powershellu s virtuální plochy Windows, naleznete v tématu [Windows Powershellu virtuální plochy](troubleshoot-powershell.md).
- Další informace o služba ve verzi Preview, najdete v článku [prostředí Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Absolvovat kurz řešení potíží, najdete v článku [kurzu: Řešení potíží s nasazení šablon Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Další informace o auditování akcí najdete v tématu [Audit operací pomocí Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích, chcete-li zjistit chyby během nasazení najdete v tématu [zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).