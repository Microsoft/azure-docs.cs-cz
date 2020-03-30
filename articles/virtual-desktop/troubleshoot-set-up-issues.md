---
title: Vytvoření fondu hostitele klienta Virtuální plochy Windows – Azure
description: Jak řešit a řešit problémy s klientem a fondem hostitelů během instalace prostředí klienta Virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371592"
---
# <a name="tenant-and-host-pool-creation"></a>Vytvoření tenanta a fondu hostitelů

Tento článek popisuje problémy během počátečníinstalace klienta virtuální plochy Windows a související infrastruktury fondu hostitelů relací.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu virtuálníplochy windows a](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) prodiskutujte službu Windows Virtual Desktop s produktovým týmem a aktivními členy komunity.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Získání image windows 10 enterprise pro více relací

Pokud chcete použít image windows 10 enterprise s více relacemi, přejděte na Azure Marketplace a vyberte **Začínáme** > **Microsoft Windows 10** > a Windows [10 Enterprise for Virtual Desktops verze 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Snímek obrazovky s výběrem Windows 10 Enterprise for Virtual Desktops verze 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Vytvoření klienta virtuální plochy Windows

Tato část popisuje potenciální problémy při vytváření klienta virtuální plochy Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Uživatel není oprávněn dotazovat se na službu pro správu.

![Snímek obrazovky s oknem PowerShellu, ve kterém uživatel nemá oprávnění k dotazování na službu pro správu.](media/UserNotAuthorizedNewTenant.png)

Příklad nezpracované chyby:

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

**Příčina:** Uživateli, který je přihlášen, nebyla přiřazena role TenantCreator ve službě Azure Active Directory.

**Oprava:** Postupujte podle pokynů v [části Přiřazení role aplikace TenantCreator uživateli v tenantovi služby Azure Active Directory](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Po postupujte podle pokynů, budete mít uživatele přiřazena role TenantCreator.

![Snímek obrazovky s přiřazenou rolí TenantCreator](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Vytváření hostitelských virtuálních počítačů relací virtuální plochy virtuální plochy systému Windows

Virtuální počítače hostitele relací se můžou vytvořit několika způsoby, ale tým Virtuální plochy Windows podporuje jenom problémy se zřizováním virtuálních počítačů související s nabídkou [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Další informace najdete [v tématu Problémy s windows virtual desktop – zřízení hostitelského fondu Azure Marketplace nabízí](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problémy s používáním Windows Virtual Desktop – zřízení nabídky hostitelského fondu Azure Marketplace

Windows Virtual Desktop – Zřízení šablony fondu hostitele je k dispozici na Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Chyba: Při použití odkazu z GitHubu se zobrazí zpráva "Vytvořit bezplatný účet"

![Snímek obrazovky pro vytvoření bezplatného účtu](media/be615904ace9832754f0669de28abd94.png)

**Příčina 1:** V účtu, který se používá k přihlášení k Azure, nejsou aktivní předplatná nebo použitý účet nemá oprávnění k zobrazení předplatných.

**Oprava 1:** Sign in with an account that has contributor access (at a minimum) to the subscription where session host VMs are going to be deployed.

**Příčina 2:** Používané předplatné je součástí klienta Zprostředkovatele cloudových služeb (CSP) společnosti Microsoft.

**Oprava 2:** Přejděte do umístění GitHub u **vytvoření a zřízení nového fondu hostitelů virtuální plochy Windows** a postupujte podle následujících pokynů:

1. Klikněte pravým tlačítkem myši **na Nasadit do Azure** a vyberte Kopírovat adresu **odkazu**.
2. Otevřete **Poznámkový blok** a vložte odkaz.
3. Před znak # vložte název koncového klienta koncového zákazníka CSP.
4. Otevřete nový odkaz v prohlížeči a portál Azure načte šablonu.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Chyba: Zobrazí se chyba "Nasazení šablony není platné"

![Snímek obrazovky s "nasazení šablony ... není platná" chyba](media/troubleshooting-marketplace-validation-error-generic.png)

Před přijetím konkrétní akce budete muset zkontrolovat protokol aktivit, abyste viděli podrobnou chybu pro ověření neúspěšného nasazení.

Chcete-li zobrazit chybu v protokolu aktivit:

1. Ukončete aktuální nabídku nasazení Na Azure Marketplace.
2. Na horním řádku hledání vyhledejte a vyberte **protokol aktivit**.
3. Najděte aktivitu s názvem **Ověřit nasazení,** která má stav **Se nezdařilo,** a vyberte aktivitu.
   ![Snímek obrazovky s individuální **Ověřit aktivitu nasazení** se stavem **Failed**](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Vyberte JSON a pak se posuňte dolů do dolní části obrazovky, dokud se nezobrazí pole "statusMessage".
   ![Snímek obrazovky s neúspěšnou aktivitou s červeným polem kolem vlastnosti statusMessage textu JSON.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Pokud šablona operace překračuje limit kvóty, můžete ji opravit jedním z následujících:

 - Spusťte Azure Marketplace s parametry, které jste použili poprvé, ale tentokrát použijte méně virtuálních počítačů a virtuálních stránek.
 - Otevřete odkaz, který se zobrazí v poli **statusMessage** v prohlížeči, abyste odeslali žádost o zvýšení kvóty pro vaše předplatné Azure pro zadanou skladovou položku virtuálního počítače.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Chyby šablony Azure Resource Manager a konfigurace požadovaného stavu prostředí PowerShell (DSC)

Podle těchto pokynů můžete řešit potíže s neúspěšnými nasazeními šablon Azure Resource Manager a PowerShellU DSC.

1. Zkontrolujte chyby v nasazení pomocí [zobrazení operací nasazení pomocí Správce prostředků Azure](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Pokud v nasazení nejsou žádné chyby, zkontrolujte chyby v protokolu aktivit pomocí [zobrazení protokolů aktivit k auditování akcí na prostředcích](../azure-resource-manager/resource-group-audit.md).
3. Jakmile je chyba identifikována, použijte chybovou zprávu a prostředky v [řešení běžných chyb nasazení Azure s Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) k vyřešení problému.
4. Odstraňte všechny prostředky vytvořené během předchozího nasazení a opakujte nasazení šablony znovu.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Chyba: Nasazení se\<nezdařilo.... název_hostitele>/joindomain

![Snímek obrazovky s nasazením se nezdařil.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Příklad nezpracované chyby:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Příčina 1:** Pověření pro připojení virtuálních připojení k doméně jsou nesprávné.

**Oprava 1:** Podívejte se na chybu "Nesprávné pověření" pro virtuální počítače nejsou připojeny k doméně v [konfiguraci hosta relace .](troubleshoot-vm-configuration.md)

**Příčina 2:** Název domény se nevyřeší.

**Oprava 2:** Viz Chyba: Název domény se v [konfiguraci virtuálního](troubleshoot-vm-configuration.md)počítače hostitele relace [nevyřeší.](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)

**Příčina 3:** Konfigurace DNS virtuální sítě (VNET) je nastavena na **výchozí**.

Chcete-li tento problém vyřešit, proveďte následující kroky:

1. Otevřete portál Azure a přejděte na kartu **Virtuální sítě.**
2. Najděte virtuální síť a vyberte **servery DNS**.
3. Nabídka serverů DNS by se měla zobrazit na pravé straně obrazovky. V této nabídce vyberte **vlastní**.
4. Zkontrolujte, zda servery DNS uvedené v části Vlastní odpovídají řadiči domény nebo doméně služby Active Directory. Pokud dns server nevidíte, můžete ho přidat zadáním jeho hodnoty do pole **Přidat server DNS.**

### <a name="error-your-deployment-failedunauthorized"></a>Chyba: Nasazení se nezdařilo...\Neautorizováno.

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Příčina:** Předplatné, které používáte, je typ, který nemá přístup k požadovaným funkcím v oblasti, kde se zákazník pokouší nasadit. Například msdn, zdarma nebo vzdělávání odběry můžete zobrazit tuto chybu.

**Oprava:** Změňte typ předplatného nebo oblast na ten, který má přístup k požadovaným funkcím.

### <a name="error-vmextensionprovisioningerror"></a>Chyba: Chyba vrozšíření v donášce v.v.

![Snímek obrazovky s nasazením se nezdařil o stavu zřizování terminálu se nezdařil.](media/7aaf15615309c18a984673be73ac969a.png)

**Příčina 1:** Přechodná chyba v prostředí Virtuální plocha systému Windows.

**Příčina 2:** Přechodná chyba s připojením.

**Oprava:** Potvrďte, že prostředí Virtuální plochy systému Windows je v pořádku, pokud se přihlásíte pomocí prostředí PowerShell. Dokončete registraci virtuálního zařízení ručně v [části Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Chyba: Zadané uživatelské jméno správce není povoleno.

![Snímek obrazovky vašeho nasazení se nezdařil, ve kterém není povolen zadaný správce.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Příklad nezpracované chyby:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Příčina:** Poskytnuté heslo obsahuje zakázané podřetězce (admin, správce, root).

**Oprava:** Aktualizujte uživatelské jméno nebo použijte různé uživatele.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Chyba: Virtuální měkce při zpracování rozšíření ohlásila chyba.

![Snímek obrazovky operace prostředku dokončené se stavem zřizování terminálu ve vašem nasazení se nezdařilo.](media/49c4a1836a55d91cd65125cf227f411f.png)

Příklad nezpracované chyby:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**Příčina:** Rozšíření PowerShell DSC se nepodařilo získat přístup správce na virtuálním počítači.

**Oprava:** Potvrďte, že uživatelské jméno a heslo mají přístup pro správu na virtuálním počítači a znovu spusťte šablonu Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Chyba: DeploymentFailed – Konfigurace DSC prostředí PowerShell 'FirstSessionHost' dokončena s chybou (y)

![Snímek obrazovky s nasazením se nezdaří s konfigurací DSC prostředí PowerShell 'FirstSessionHost' dokončena chyba (y).](media/64870370bcbe1286906f34cf0a8646ab.png)

Příklad nezpracované chyby:

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

**Příčina:** Rozšíření PowerShell DSC se nepodařilo získat přístup správce na virtuálním počítači.

**Oprava:** Potvrďte, že uživatelské jméno a heslo mají přístup pro správu na virtuálním počítači a znovu spusťte šablonu Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Chyba: Nasazení se nezdařilo – neplatný odkaz na prostředky.

Příklad nezpracované chyby:

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

**Příčina:** Část názvu skupiny prostředků se používá pro určité prostředky vytvořené šablonou. Vzhledem k názvu odpovídajícíexistující prostředky, šablona může vybrat existující prostředek z jiné skupiny.

**Oprava:** Při spuštění šablony Azure Resource Manager k nasazení hostitelských virtuálních počítačů relace, aby první dva znaky jedinečné pro název skupiny prostředků předplatného.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Chyba: Nasazení se nezdařilo – neplatný odkaz na prostředky.

Příklad nezpracované chyby:

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

**Příčina:** Tato chyba je, protože nic vytvořené pomocí šablony Azure Resource Manager má stejný název jako jiná síťová konekve již ve virtuální síti.

**Oprava:** Použijte jinou předponu hostitele.

### <a name="error-deploymentfailed--error-downloading"></a>Chyba: DeploymentFailed – při stahování chyb

Příklad nezpracované chyby:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Příčina:** Tato chyba je způsobena statickou trasou, pravidlem brány firewall nebo skupinou zabezpečení sítě, která blokuje stahování souboru zip vázaného na šablonu Správce prostředků Azure.

**Oprava:** Odeberte blokování statické trasy, pravidla brány firewall nebo souboru nsg. Volitelně můžete otevřít soubor json šablony Azure Resource Manager v textovém editoru, převzít odkaz na soubor ZIP a stáhnout prostředek do povoleného umístění.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Uživatel není oprávněn dotazovat se na službu pro správu.

Příklad nezpracované chyby:

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

**Příčina:** Zadaný správce klienta Virtuální plochy Windows nemá platné přiřazení role.

**Oprava:** Uživatel, který vytvořil klienta virtuální plochy windows, se musí přihlásit k prostředí Windows Virtual Desktop PowerShell a přiřadit pokusu o přiřazení role. Pokud používáte parametry šablony GitHub Azure Resource Manager, postupujte podle následujících pokynů pomocí příkazů PowerShellu:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Chyba: Uživatel vyžaduje azure vícefaktorové ověřování (MFA)

![Snímek obrazovky s nasazením se nezdařil z důvodu nedostatku vícefaktorového ověřování (MFA)](media/MFARequiredError.png)

Příklad nezpracované chyby:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Příčina:** Zadaný správce klienta Virtuální plochy Windows vyžaduje k přihlášení Azure Multi-Factor Authentication (MFA).

**Oprava:** Vytvořte instanční objekt a přiřaďte mu roli pro vašeho klienta virtuální plochy Windows podle kroků v [kurzu: Vytvoření instančních objektů a přiřazení rolí pomocí prostředí PowerShell](create-service-principal-role-powershell.md). Po ověření, že se můžete přihlásit k virtuální ploše Windows pomocí instančního objektu, znovu spusťte nabídku Azure Marketplace nebo šablonu GitHub Azure Resource Manager v závislosti na tom, kterou metodu používáte. Podle následujících pokynů zadejte správné parametry pro vaši metodu.

Pokud používáte nabídku Azure Marketplace, zadejte hodnoty pro následující parametry, které chcete správně ověřit na Virtuální desktopwindows:

- Vlastník klienta virtuální plochy Windows RDS: Instanční objekt service
- ID aplikace: Identifikace aplikace nového instančního objektu, který jste vytvořili
- Heslo/Potvrdit heslo: Tajný klíč hesla, který jste vygenerovali pro instanční objekt služby
- ID klienta Azure AD: ID klienta Azure AD instančního objektu, který jste vytvořili

Pokud používáte šablonu GitHub Azure Resource Manager, zadejte hodnoty pro následující parametry, které chcete správně ověřit na virtuální ploše Windows:

- Hlavní jméno uživatele správce klienta (UPN) nebo ID aplikace: Identifikace aplikace nového instančního objektu, který jste vytvořili
- Heslo správce klienta: Tajný klíč hesla, který jste vygenerovali pro instanční objekt služby
- IsServicePrincipal: **true**
- AadTenantId: ID klienta Azure AD instančního objektu, který jste vytvořili

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuální plochou Windows a traseskalace najdete [v tématu Přehled řešení potíží, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače (VM) ve Windows Virtual Desktop najdete v [tématu Konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta Virtuální plocha systému Windows naleznete v [tématu Připojení služby Windows Virtual Desktop](troubleshoot-service-connection.md).
- Informace o řešení problémů s klienty vzdálené plochy naleznete [v tématu Poradce při potížích s klientem Vzdálené plochy.](troubleshoot-client.md)
- Informace o řešení problémů při používání PowerShellu s Windows Virtual Desktop najdete v [tématu Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Další informace o službě naleznete v tématu [Windows Virtual Desktop environment](environment-setup.md).
- Chcete-li projít kurz řešení potíží, [přečtěte si článek Návod k řešení potíží s nasazením šablon Správce prostředků](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o auditování akcí naleznete v [tématu Audit operace se Správcem prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Informace o akcích k určení chyb během nasazení najdete v [tématu Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
