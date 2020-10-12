---
title: Vytvoření fondu hostitelů prostředí virtuálních počítačů s Windows – Azure
description: Řešení potíží a řešení problémů s fondem klientů a hostitelů během instalace prostředí virtuálních počítačů s Windows
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d02642b49951b4b116eaae6dbea490ef2720c15d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084409"
---
# <a name="host-pool-creation"></a>Vytvoření fondu hostitelů

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

Tento článek popisuje problémy při počátečním nastavení tenanta virtuální plochy Windows a související infrastruktury fondu hostitelů relací.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Získání bitové kopie Windows 10 Enterprise s více relacemi

Pokud chcete použít bitovou kopii Windows 10 Enterprise s více relacemi, **Přečtěte si Azure Marketplace, vyberte Začínáme**s  >  **Microsoft Windows 10** > a [Windows 10 Enterprise multi-session verze 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problémy s používáním Azure Portal k vytváření fondů hostitelů

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Chyba: při přístupu ke službě se zobrazí zpráva vytvořit bezplatný účet.

> [!div class="mx-imgBorder"]
> ![Obrázek znázorňující Azure Portal se zobrazením zprávy vytvořit bezplatný účet](media/create-new-account.png)

**Příčina**: v účtu, ke kterému jste se přihlásili do Azure, nejsou aktivní předplatná, nebo účet nemá oprávnění k zobrazení předplatných.

**Oprava**: Přihlaste se k předplatnému, kde nasadíte virtuální počítače hostitele relace s účtem, který má alespoň přístup na úrovni přispěvatele.

### <a name="error-exceeding-quota-limit"></a>Chyba: překročení limitu kvóty

Pokud vaše operace překročí limit kvóty, můžete provést jednu z následujících akcí:

- Vytvořte nový fond hostitelů se stejnými parametry, ale méně virtuálních počítačů a jader virtuálních počítačů.

- Otevřete odkaz, který vidíte v poli statusMessage v prohlížeči, abyste odeslali žádost o zvýšení kvóty pro vaše předplatné Azure pro zadanou SKU virtuálního počítače.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Chyba: nelze zobrazit přiřazení uživatelů ve skupinách aplikací.

Příčina: k této chybě obvykle dochází po přesunu předplatného z klienta 1 Azure Active Directory (AD) do jiného. Pokud jsou stará přiřazení stále vázaná na starý tenant služby Azure AD, Azure Portal je bude sledovat.

Oprava: bude nutné znovu přiřadit uživatele ke skupinám aplikací.

## <a name="azure-resource-manager-template-errors"></a>Chyby šablon Azure Resource Manager

Při řešení neúspěšného nasazení Azure Resource Manager šablon a prostředí PowerShell DSC postupujte podle těchto pokynů.

1. Projděte si chyby v nasazení pomocí [Zobrazení operací nasazení s Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Pokud v nasazení nejsou žádné chyby, zkontrolujte chyby v protokolu aktivit pomocí [zobrazení protokolů aktivit a proveďte audit akcí u prostředků](../azure-resource-manager/resource-group-audit.md).
3. Po identifikaci chyby použijte chybovou zprávu a prostředky v tématu [řešení běžných chyb nasazení Azure pomocí Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) k vyřešení problému.
4. Odstraňte všechny prostředky vytvořené během předchozího nasazení a zkuste šablonu znovu nasadit.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Chyba: nasazení nebylo úspěšné... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Nasazování se nepovedlo snímek obrazovky.](media/failure-joindomain.png)

Příklad nezpracované chyby:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Příčina 1:** Přihlašovací údaje zadané pro připojení virtuálních počítačů k doméně nejsou správné.

**Oprava 1:** Pokud virtuální počítače nejsou připojené k doméně v [konfiguraci virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md), Projděte si chybu "nesprávné přihlašovací údaje".

**Příčina 2:** Název domény není přeložen.

**Oprava 2:** Viz [Chyba: název domény není přeložen](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) v [konfiguraci virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).

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
> ![Snímek obrazovky vašeho nasazení selhal se stavem zřizování terminálu selhalo.](media/failure-vmextensionprovisioning.png)

**Příčina 1:** Přechodná chyba v prostředí virtuálního počítače s Windows

**Příčina 2:** Přechodná chyba se spojením.

**Oprava:** Ověřte, jestli je prostředí virtuálních počítačů s Windows v pořádku, když se přihlásíte pomocí PowerShellu. Dokončete registraci virtuálního počítače ručně v části [Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Chyba: Zadané uživatelské jméno správce není povolené

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s vaším nasazením se nepovedl, protože zadaný správce není povolený.](media/failure-username.png)

Příklad nezpracované chyby:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Příčina:** Zadané heslo obsahuje zakázané podřetězce (admin, Administrator, root).

**Oprava:** Aktualizujte uživatelské jméno nebo použijte jiné uživatele.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Chyba: Virtuální počítač nahlásil chybu při zpracování rozšíření

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s operací prostředku dokončeným se stavem zřizování terminálu v nasazení se nezdařil.](media/failure-processing.png)

Příklad nezpracované chyby:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Příčina:** Rozšíření PowerShell DSC nebylo schopné získat přístup správce na virtuálním počítači.

**Oprava:** Ujistěte se, že uživatelské jméno a heslo mají na virtuálním počítači přístup správce a znovu spusťte šablonu Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Chyba: DeploymentFailed – konfigurace prostředí PowerShell DSC ' FirstSessionHost ' se dokončila s chybami

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s nasazením se nepovedl s konfigurací PowerShellu pro DSC FirstSessionHost se dokončil s chybami.](media/failure-dsc.png)

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

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Chyba: po odstranění virtuálního počítače nejde odstranit hostitele relace z fondu hostitelů.

**Příčina:** Před odstraněním virtuálního počítače je nutné odstranit hostitele relace.

**Oprava:** Vložte hostitele relace do režimu vyprázdnění, odhlaste všechny uživatele z hostitele relace a pak odstraňte hostitele.

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](troubleshoot-client.md) .
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
