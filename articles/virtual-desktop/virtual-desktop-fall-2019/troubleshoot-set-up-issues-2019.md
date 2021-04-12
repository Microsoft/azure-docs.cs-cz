---
title: Vytvoření fondu hostitelů tenanta pro Windows Virtual Desktop (Classic) – Azure
description: Řešení potíží a řešení problémů s fondem klientů a hostitelů během instalace klientského prostředí pro virtuální počítače s Windows (Classic)
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 12be4611f0583c6da630b9e5a0e4b5a82ebfa650
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444271"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Vytvoření fondu tenanta a hostitele ve virtuální ploše Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../troubleshoot-set-up-issues.md).

Tento článek popisuje problémy při počátečním nastavení tenanta virtuální plochy Windows a související infrastruktury fondu hostitelů relací.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Získání bitové kopie Windows 10 Enterprise s více relacemi

Pokud chcete použít bitovou kopii Windows 10 Enterprise s více relacemi, **Přečtěte si Azure Marketplace, vyberte Začínáme** s  >  **Microsoft Windows 10** > a [Windows 10 Enterprise pro virtuální plochy verze 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s výběrem Windows 10 Enterprise pro virtuální plochy verze 1809](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant&quot;></a>Vytváří se tenant virtuální plochy Windows.

Tato část se věnuje potenciálním problémům při vytváření tenanta virtuálních klientů Windows.

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>Chyba: AADSTS650052 aplikace potřebuje přístup ke službě.

Příklad nezpracované chyby:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Příčina:** V instanci Azure Active Directory se neudělí souhlas s virtuálním počítačem s Windows.

**Oprava:** [podle tohoto průvodce](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) udělíte souhlas.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Uživatel nemá oprávnění k dotazování služby správy

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky okna prostředí PowerShell, ve kterém uživatel nemá oprávnění pro dotaz na službu správy.](../media/UserNotAuthorizedNewTenant.png)

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

**Příčina:** Uživatel, který je přihlášený, nemá přiřazenou roli TenantCreator ve svých Azure Active Directory.

**Oprava:** Postupujte podle pokynů v tématu [přiřazení role aplikace TenantCreator uživateli ve vašem tenantovi Azure Active Directory](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Po provedení těchto pokynů budete mít přiřazeného uživatele k roli TenantCreator.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky přiřazené role TenantCreator](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Vytváření virtuálních počítačů hostitele relace virtuálních počítačů s Windows

Virtuální počítače hostitele relace je možné vytvořit několika způsoby, ale tým virtuálních ploch Windows podporuje jenom problémy zřizování virtuálních počítačů, které souvisejí s nabídkou [Azure Marketplace](https://azuremarketplace.microsoft.com/) . Další informace najdete v tématu [problémy s použitím virtuálního počítače s Windows – zřízení fondu hostitelů Azure Marketplace nabídky](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problémy s používáním virtuálního počítače s Windows – zřízení fondu hostitelů Azure Marketplace nabídky

Virtuální počítač s Windows – zřízení šablony fondu hostitelů je dostupné z Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Chyba: při použití odkazu z GitHubu se zobrazí zpráva "vytvořit bezplatný účet".

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s vytvořením bezplatného účtu.](../media/be615904ace9832754f0669de28abd94.png)

**Příčina 1:** V účtu, který se používá k přihlášení k Azure, nejsou aktivní předplatná, nebo účet nemá oprávnění k zobrazení předplatných.

**Oprava 1:** Přihlaste se pomocí účtu, který má přístup přispěvatele (minimálně) k předplatnému, kde budou nasazeny virtuální počítače hostitele relace.

**Příčina 2:** Používané předplatné je součástí tenanta poskytovatele Microsoft Cloudch služeb (CSP).

**Oprava 2:** Přejděte do umístění GitHubu pro **Vytvoření a zřízení nového fondu hostitelů virtuálních počítačů s Windows** a postupujte podle těchto pokynů:

1. Klikněte pravým tlačítkem na **nasadit do Azure** a vyberte **Kopírovat adresu odkazu**.
2. Otevřete **Poznámkový blok** a vložte odkaz.
3. Před znakem # vložte název tenanta koncového zákazníka CSP.
4. Otevře nový odkaz v prohlížeči a Azure Portal načte šablonu.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Chyba: obdržíte chybu nasazení šablony není platné.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s názvem nasazení šablony... není platný, chyba](../media/troubleshooting-marketplace-validation-error-generic.png)

Než provedete konkrétní akci, budete muset zjistit protokol aktivit a zobrazit podrobnou chybu při ověřování nasazení.

Zobrazení chyby v protokolu aktivit:

1. Ukončí aktuální nabídku nasazení Azure Marketplace.
2. V horním panelu vyhledávání vyhledejte a vyberte **Protokol aktivit**.
3. Vyhledejte aktivitu s názvem **ověřit nasazení** , která má stav **selhání** a vyberte aktivitu.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky jednotlivých * * ověřit aktivitu nasazení * * s * * neúspěšným * * stavem](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Vyberte JSON a potom se posuňte dolů k dolní části obrazovky, dokud se nezobrazí pole "statusMessage".

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s neúspěšnou aktivitou s červeným polem kolem vlastnosti statusMessage textu JSON](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Pokud vaše šablona operace překročí limit kvóty, můžete opravit jednu z následujících akcí:

 - Spusťte Azure Marketplace s parametry, které jste použili poprvé, ale tentokrát se používá méně virtuálních počítačů a jader virtuálních počítačů.
 - Otevřete odkaz, který vidíte v poli **statusMessage** v prohlížeči, abyste odeslali žádost o zvýšení kvóty pro vaše předplatné Azure pro zadanou SKU virtuálního počítače.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Chyby konfigurace Azure Resource Manager a prostředí PowerShell pro konfiguraci požadovaného stavu (DSC)

Při řešení neúspěšného nasazení Azure Resource Manager šablon a prostředí PowerShell DSC postupujte podle těchto pokynů.

1. Projděte si chyby v nasazení pomocí [Zobrazení operací nasazení s Azure Resource Manager](../../azure-resource-manager/templates/deployment-history.md).
2. Pokud v nasazení nejsou žádné chyby, zkontrolujte chyby v protokolu aktivit pomocí [zobrazení protokolů aktivit a proveďte audit akcí u prostředků](../../azure-resource-manager/management/view-activity-logs.md).
3. Po identifikaci chyby použijte chybovou zprávu a prostředky v tématu [řešení běžných chyb nasazení Azure pomocí Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) k vyřešení problému.
4. Odstraňte všechny prostředky vytvořené během předchozího nasazení a zkuste šablonu znovu nasadit.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Chyba: nasazení nebylo úspěšné... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Nasazování se nepovedlo snímek obrazovky.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Příklad nezpracované chyby:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Příčina 1:** Přihlašovací údaje zadané pro připojení virtuálních počítačů k doméně nejsou správné.

**Oprava 1:** Pokud virtuální počítače nejsou připojené k doméně v [konfiguraci virtuálního počítače hostitele relace](troubleshoot-vm-configuration-2019.md), Projděte si chybu "nesprávné přihlašovací údaje".

**Příčina 2:** Název domény není přeložen.

**Oprava 2:** Viz [Chyba: název domény není přeložen](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) v [konfiguraci virtuálního počítače hostitele relace](troubleshoot-vm-configuration-2019.md).

**Příčina 3:** Konfigurace DNS virtuální sítě (VNET) je nastavená na **výchozí**.

Chcete-li tento problém vyřešit, proveďte následující akce:

1. Otevřete Azure Portal a přejdete na kartu **virtuální sítě** .
2. Najděte virtuální síť a pak vyberte **servery DNS**.
3. V pravé části obrazovky by se měla zobrazit nabídka servery DNS. V této nabídce vyberte možnost **vlastní**.
4. Ujistěte se, že servery DNS uvedené v části vlastní odpovídají vašemu řadiči domény nebo doméně služby Active Directory. Pokud server DNS nevidíte, můžete ho přidat zadáním jeho hodnoty do pole **Přidat server DNS** .

### <a name="error-your-deployment-failedunauthorized"></a>Chyba: Vaše nasazení selhalo...\Neautorizováno

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Příčina:** Předplatné, které používáte, je typ, který nemůže získat přístup k požadovaným funkcím v oblasti, kde se zákazník pokouší nasadit. Tato chyba se může zobrazit například v předplatných MSDN, Free nebo školství.

**Oprava:** Změňte typ nebo oblast předplatného na jednu, která má přístup k požadovaným funkcím.

### <a name="error-vmextensionprovisioningerror"></a>Chyba: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky vašeho nasazení selhal se stavem zřizování terminálu selhalo.](../media/7aaf15615309c18a984673be73ac969a.png)

**Příčina 1:** Přechodná chyba v prostředí virtuálního počítače s Windows

**Příčina 2:** Přechodná chyba se spojením.

**Oprava:** Ověřte, jestli je prostředí virtuálních počítačů s Windows v pořádku, když se přihlásíte pomocí PowerShellu. Dokončete registraci virtuálního počítače ručně v části [Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell-2019.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Chyba: Zadané uživatelské jméno správce není povolené

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s vaším nasazením se nepovedl, protože zadaný správce není povolený.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Příklad nezpracované chyby:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Příčina:** Zadané heslo obsahuje zakázané podřetězce (admin, Administrator, root).

**Oprava:** Aktualizujte uživatelské jméno nebo použijte jiné uživatele.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Chyba: Virtuální počítač nahlásil chybu při zpracování rozšíření

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s operací prostředku dokončeným se stavem zřizování terminálu v nasazení se nezdařil.](../media/49c4a1836a55d91cd65125cf227f411f.png)

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

**Příčina:** Rozšíření PowerShell DSC nebylo schopné získat přístup správce na virtuálním počítači.

**Oprava:** Ujistěte se, že uživatelské jméno a heslo mají na virtuálním počítači přístup správce a znovu spusťte šablonu Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Chyba: DeploymentFailed – konfigurace prostředí PowerShell DSC ' FirstSessionHost ' se dokončila s chybami

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s nasazením se nepovedl s konfigurací PowerShellu pro DSC FirstSessionHost se dokončil s chybami.](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

**Příčina:** Rozšíření PowerShell DSC nebylo schopné získat přístup správce na virtuálním počítači.

**Oprava:** Ověřte, že zadané uživatelské jméno a heslo mají na virtuálním počítači přístup správce a znovu spusťte šablonu Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Chyba: DeploymentFailed – InvalidResourceReference

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

**Příčina:** Část názvu skupiny prostředků se používá pro některé prostředky, které šablona vytváří. Vzhledem k názvu, který odpovídá stávajícím prostředkům, může šablona vybrat existující prostředek z jiné skupiny.

**Oprava:** Když spouštíte šablonu Azure Resource Manager pro nasazení virtuálních počítačů hostitele relace, nastavte první dva znaky jako jedinečné pro název skupiny prostředků předplatného.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Chyba: DeploymentFailed – InvalidResourceReference

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

**Příčina:** Tato chyba je způsobená tím, že síťová karta vytvořená pomocí šablony Azure Resource Manager má stejný název jako jiná síťová karta, která už je ve virtuální síti.

**Oprava:** Použijte jinou předponu hostitele.

### <a name="error-deploymentfailed--error-downloading"></a>Chyba: DeploymentFailed – stahování chyby

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

**Příčina:** Příčinou této chyby je statická trasa, pravidlo brány firewall nebo NSG blokující stažení souboru ZIP svázaného se šablonou Azure Resource Manager.

**Oprava:** Odebere blokující statickou trasu, pravidlo brány firewall nebo NSG. Případně můžete otevřít soubor JSON šablony Azure Resource Manager v textovém editoru, vytvořit odkaz na soubor zip a stáhnout prostředek do povoleného umístění.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Uživatel nemá oprávnění k dotazování služby správy

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

**Příčina:** Zadaný správce tenanta virtuálních počítačů s Windows nemá platné přiřazení role.

**Oprava:** Uživatel, který vytvořil tenanta virtuálních klientů Windows, musí se přihlásit k prostředí PowerShell virtuální plochy Windows a přiřadit pokusy o přiřazení role uživateli. Pokud používáte parametry šablony Azure Resource Manager GitHubu, postupujte podle těchto pokynů pomocí příkazů PowerShellu:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Chyba: uživatel vyžaduje Azure AD Multi-Factor Authentication (MFA).

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky nasazení se nezdařil z důvodu nedostatku Multi-Factor Authentication (MFA)](../media/MFARequiredError.png)

Příklad nezpracované chyby:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Příčina:** Zadaný správce tenanta virtuálních počítačů s Windows vyžaduje přihlášení k Azure AD Multi-Factor Authentication (MFA).

**Oprava:** Pomocí kroků v tomto kurzu vytvořte instanční objekt a přiřaďte mu roli pro vašeho tenanta virtuálních klientů Windows. v [PowerShellu vytvořte instanční objekty a přiřazení rolí](create-service-principal-role-powershell.md). Po ověření, že se můžete přihlásit k virtuální ploše Windows pomocí instančního objektu, spusťte znovu nabídku Azure Marketplace nebo šablonu GitHub Azure Resource Manager, podle toho, kterou metodu používáte. Podle následujících pokynů zadejte správné parametry pro vaši metodu.

Pokud používáte nabídku Azure Marketplace, zadejte hodnoty pro následující parametry pro správné ověření pro virtuální plochu Windows:

- Vlastník vzdálené plochy klienta Windows: instanční objekt
- ID aplikace: Identifikace aplikace nového instančního objektu, který jste vytvořili.
- Heslo/potvrzení hesla: tajný kód pro heslo, který jste vygenerovali pro instanční objekt.
- ID tenanta Azure AD: ID tenanta služby Azure AD pro instanční objekt, který jste vytvořili

Pokud používáte šablonu Azure Resource Manager GitHubu, zadejte hodnoty pro následující parametry pro správné ověření na virtuálním počítači s Windows:

- Hlavní název uživatele (UPN) správce tenanta nebo ID aplikace: Identifikace aplikace nového instančního objektu, který jste vytvořili.
- Heslo správce tenanta: tajný kód pro heslo, který jste vygenerovali pro instanční objekt.
- IsServicePrincipal: **true**
- AadTenantId: ID tenanta Azure AD vytvořeného objektu služby

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Chyba: vmSubnet není k dispozici při konfiguraci virtuálních sítí

**Příčina:** V šabloně virtuálního počítače s Windows se v uživatelském rozhraní zobrazí jenom podsítě, které mají aspoň tolik IP adres, kolik jich je dostupných jako celkový počet virtuálních počítačů zadaných v šabloně. Skutečný počet dostupných IP adres v podsíti musí být roven počtu nově nasazených virtuálních počítačů, které však nelze vypočítat pomocí aktuálního uživatelského rozhraní.

**Oprava:** Můžete zadat podsíť s aspoň tolik IP adresami, kolik je k dispozici jako počet virtuálních počítačů, které se přidávají pomocí uživatelského rozhraní Marketplace. to se dá udělat zadáním názvu podsítě v parametru "**existingSubnetName**" při opětovném [nasazení stávajícího nasazení](expand-existing-host-pool-2019.md#redeploy-from-azure) nebo [nasazení pomocí základní šablony ARM z GitHubu](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview-2019.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration-2019.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection-2019.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](../troubleshoot-client.md) .
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell-2019.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup-2019.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md).