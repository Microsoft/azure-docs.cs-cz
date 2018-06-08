---
title: Poznámky k verzi Microsoft Azure zásobníku Development Kit | Microsoft Docs
description: Vylepšení, opravy a známé problémy pro Azure zásobníku Development Kit.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 185cd6f20cdb19531777ccb58e72be14ea656549
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850116"
---
# <a name="azure-stack-development-kit-release-notes"></a>Poznámky k verzi Azure zásobníku Development Kit  
Tyto poznámky k verzi obsahují informace o vylepšení, opravy a známých problémů v Azure zásobníku Development Kit. Pokud si nejste jistí, kterou verzi používáte, můžete [použití portálu ke kontrole](.\.\azure-stack-updates.md#determine-the-current-version).

> Vždy aktuální s co je nového v ASDK se přihlásíte k odběru [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanálu](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Sestavení 1.1805.1.47

> [!TIP]  
> Na základě názorů zákazníků, dojde k aktualizaci verze schématu používán pro Microsoft Azure zásobníku. Počínaje touto aktualizací 1805, nové schéma lépe představuje aktuální verze cloudu.  
> 
> Verze schématu je nyní *Version.YearYearMonthMonth.MinorVersion.BuildNumber* kde sady druhý a třetí znamenat verze a verze. Například 1805.1 představuje *verze výrobní* (RTM) verzi 1805.  


### <a name="new-features"></a>Nové funkce 
Toto sestavení obsahuje následující vylepšení a opravy pro Azure zásobníku.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack now includes a *Syslog* client** as a *preview feature*. This client allows the forwarding of audit and security logs related to the Azure Stack infrastructure to a Syslog server or security information and event management (SIEM) software that is external to Azure Stack. Currently, the Syslog client only supports unauthenticated UDP connections over default port 514. The payload of each Syslog message is formatted in Common Event Format (CEF). 

  Chcete-li nakonfigurovat klienta Syslog, použijte **Set-SyslogServer** rutiny v privilegované koncový bod. 

  S touto verzí preview může se zobrazit následující tři výstrahy. Když sada protokolů Azure, patří tyto výstrahy *popisy* a *nápravy* pokyny. 
  - TITLE: Integrity kódu vypnuto  
  - TITLE: Integrity kódu v režimu auditování 
  - TITLE: Uživatelský účet vytvořený

  Když tato funkce je ve verzi preview, nemělo by v produkčním prostředí spoléhat.   


### <a name="fixed-issues"></a>Opravené problémy

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán Azure zásobníku


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- <!-- 2551834 - IS, ASDK --> When you select **Overview** for a storage account in either the admin or user portals, the information from the *Essentials* pane does not display.  The Essentials pane displays information about the account like its *Resource group*, *Location*, and *Subscription ID*.  Other options for Overview  are accessible, like *Services* and *Monitoring*, as well as options to *Open in Explorer* or to *Delete storage account*.  

  Chcete-li zobrazit informace k dispozici, použijte [Get azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) rutiny prostředí PowerShell. 

- <!-- TBD - IS ASDK --> Do not use the new administrative subscription types of *Metering subscription*, and *Consumption subscription*. These new subscription types were introduced with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Popis cesty](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.


#### <a name="health-and-monitoring"></a>Sledování stavu a
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Výstrahy #1:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu prezenčního signálu skener není k dispozici. To může ovlivnit sestavy o stavu a metrik.  

  Výstrahy #2:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu skener chyby není k dispozici. To může ovlivnit sestavy o stavu a metrik.

  Obě výstrahy můžete bezpečně ignorovat a bude automaticky zavřete v čase.  

- <!-- 2392907 – ASDK -->   You might see a *critical* alert for **Low memory capacity**. This alert has the following description: *The region has consumed more than 95.00 % of available memory. Creating virtual machines with large amounts of memory may fail.*

  Tuto výstrahu můžete generovat, je-li Azure zásobníku se nesprávně účty pro využití paměti v sadě pro vývoj Azure zásobníku.  

  Tuto výstrahu můžete ignorovat a tento problém nemá vliv na umístění virtuálních počítačů. 

- <!-- 2368581 - IS. ASDK --> An Azure Stack operator, if you receive a low memory alert and tenant virtual machines fail to deploy with a *Fabric VM creation error*, it is possible that the Azure Stack stamp is out of available memory. Use the [Azure Stack Capacity Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) to best understand the capacity available for your workloads. 


#### <a name="compute"></a>Compute
- <!-- TBD - IS, ASDK --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Jako řešení použijte jednu z následujících metod nasazení virtuálního počítače. V každé metody je třeba zadat velikost virtuálního počítače, který chcete použít.

  - **Šablona Azure Resource Manageru:** při použití šablony, nastavte *vmSize* v šabloně na velikost virtuálního počítače, kterou chcete použít. Například následující položku slouží k nasazení virtuálního počítače, který používá *F32s_v2* velikost:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** můžete použít [vytvořit virtuální počítač az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) příkaz a zadejte velikost virtuálního počítače jako parametr, podobně jako `--size "Standard_F32s_v2"`.

  - **Prostředí PowerShell:** pomocí prostředí PowerShell můžete použít [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) s parametrem, který určuje velikost virtuálního počítače, podobně jako `-VMSize "Standard_F32s_v2"`.


- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach a D series VM. All supported D series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Sítě
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původní virtuální počítač a ne do nového.


- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Chcete-li tento problém obejít, použijte plán rozšíření zvýšit kvótu sítě, pokud je již přidružena k odběru plánu. Další informace najdete v tématu Jak [zpřístupnit plán rozšíření](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL a MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.  

- <!-- TBD - IS ASDK --> App Service can only be deployed into the *Default Provider subscription* at this time. In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type.

#### <a name="usage"></a>Využití  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->



## <a name="build-201805131"></a>Sestavení 20180513.1

### <a name="new-features"></a>Nové funkce 
Toto sestavení obsahuje následující vylepšení a opravy pro Azure zásobníku.  

- <!-- 1759172 - IS, ASDK --> **New administrative subscriptions**. With 1804 there are two new subscription types available in the portal. These new subscription types are in addition to the Default Provider subscription and visible with new Azure Stack installations beginning with version 1804. *Do not use these new subscription types with this version of Azure Stack*. We will announce the availability to use these subscription types in with a future update. 

  Tyto nové typy předplatného jsou viditelné, ale součástí větší změnu pro zabezpečení výchozí zprostředkovatel odběru a aby bylo snazší nasazování sdílené prostředky, jako je hostování SQL servery. 

  Nyní k dispozici tři předplatné typy jsou:  
  - Výchozí zprostředkovatel odběru: nadále používat tento typ předplatného. 
  - Měření předplatné: *nepoužívejte tento typ předplatného.*
  - Spotřeba předplatné: *nepoužívejte tento typ předplatného*

### <a name="fixed-issues"></a>Opravené problémy
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán Azure zásobníku

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Další verze vypršel časový limit s touto aktualizací  
Toto jsou nyní k dispozici, ale nevyžadují zásobník Azure aktualizace 1804.
- **Aktualizace do sady sledování nástroje Microsoft Azure zásobníku System Center Operations Manager**. Nová verze (1.0.3.0) Microsoft System Center Operations Manager monitorování Pack pro zásobník Azure je k dispozici pro [Stáhnout](https://www.microsoft.com/download/details.aspx?id=55184). S touto verzí můžete použít objekty služby při přidání připojené nasazení Azure zásobníku. Tato verze taky funkce prostředí správy aktualizací, které umožňuje provádět akci nápravy přímo z prostředí nástroje Operations Manager. Existují také nové řídicí panely, které zobrazují poskytovatelů prostředků, jednotek škálování a škálování jednotky uzly.

- **Nový Azure zásobníku verze prostředí PowerShell správce 1.3.0**.  Azure PowerShell zásobníku 1.3.0 je nyní k dispozici pro instalaci. Tato verze obsahuje příkazy pro všechny poskytovatele správce prostředků ke správě Azure zásobníku.  V této verzi se obsah přestanou z Githubu nástroje zásobník Azure [úložiště](https://github.com/Azure/AzureStack-Tools). 

   Podrobné informace o instalaci, postupujte podle [pokyny](.\.\azure-stack-powershell-install.md) nebo [pomoci](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) obsahu pro modul zásobník Azure 1.3.0. 

- **Počáteční verze referenční dokumentace Azure zásobníku rozhraní API Rest**. [Referenční dokumentace rozhraní API pro všechny poskytovatele prostředků Azure zásobníku správce](https://docs.microsoft.com/rest/api/azure-stack/) je nyní publikována. 

### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Pro Azure zásobníku Development Kit použít https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Popis cesty](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *Chyba: šablonu pro FaultType ResourceProviderTimeout nebyl nalezen.*

    Tuto výstrahu můžete ignorovat. 

#### <a name="health-and-monitoring"></a>Sledování stavu a
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Výstrahy #1:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu prezenčního signálu skener není k dispozici. To může ovlivnit sestavy o stavu a metrik.  

  Výstrahy #2:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu skener chyby není k dispozici. To může ovlivnit sestavy o stavu a metrik.

  Obě výstrahy můžete bezpečně ignorovat. Se automaticky zavře v čase.  

#### <a name="marketplace"></a>Marketplace
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.
 
#### <a name="compute"></a>Compute
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Sítě
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původní virtuální počítač a ne do nového.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Chcete-li tento problém obejít, použijte plán rozšíření zvýšit kvótu sítě, pokud je již přidružena k odběru plánu. Další informace najdete v tématu Jak [zpřístupnit plán rozšíření](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL a MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>Využití  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Stažení nástroje Azure zásobníku z Githubu
- Při použití *vyvolání webrequest* rutiny prostředí PowerShell ke stažení protokolů Azure nástroje z Githubu, obdržíte chybu:     
    -  *vyvolání webrequest: požadavek byl přerušen: Nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu poslední zastaralá podpora Githubu Tlsv1 a Tlsv1.1 kryptografických standardů (výchozí nastavení pro prostředí PowerShell). Další informace najdete v tématu [slabé kryptografické standardy odebrání oznámení](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Sestavení 20180302.1

### <a name="new-features-and-fixes"></a>Nové funkce a opravy
Nové funkce a opravy vydané pro Azure zásobníku integrované systémy verzi 1803 použít pro Azure zásobníku Development Kit. Najdete v článku [nové funkce](.\.\azure-stack-update-1803.md#new-features) a [pevné problémy](.\.\azure-stack-update-1803.md#fixed-issues) části 1803 zásobník Azure aktualizovat poznámky k verzi pro podrobnosti.  
> [!IMPORTANT]    
> Některé z uvedených v položek **nové funkce** a **pevné problémy** části se vztahují pouze k zásobník Azure integrované systémy.

### <a name="changes"></a>Změny
- Způsob, jak změnit stav na nově vytvořený nabídku z *privátní* k *veřejné* nebo *vyřazení* došlo ke změně. Další informace najdete v tématu [vytvořit nabídku](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Známé problémy
 
#### <a name="portal"></a>Portál
- Možnost [otevřete novou žádost o podporu z rozevíracího seznamu](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z v rámci správce portálu není k dispozici. Místo toho použijte následující odkaz:     
    - Pro Azure zásobníku Development Kit použít https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Zobrazí **je vyžadována aktivace** upozornění s výzvou k registraci vaší Azure zásobníku Development Kit. Toto chování je očekávané.

- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.

- Nelze zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení ověřte oprávnění pomocí prostředí PowerShell.

- V řídicím panelu portálu pro správu se nezdaří aktualizace dlaždice zobrazíte informace o aktualizacích. Chcete-li vyřešit tento problém, klikněte na dlaždici jej aktualizovat.

-   V portálu pro správu může se zobrazit Kritická výstraha pro součást Microsoft.Update.Admin. Název výstrahy, popis a nápravu všech zobrazit jako:  
    - *Chyba: šablonu pro FaultType ResourceProviderTimeout nebyl nalezen.*

    Tuto výstrahu můžete ignorovat. 

- V portálu pro správu i v portálu user portal, v okně Přehled nepodaří načíst, když vyberete v okně Přehled pro účty úložiště, které byly vytvořeny ve starší verzi rozhraní API (Příklad: 2015-06-15). 

  Jako alternativní řešení, pomocí prostředí PowerShell spustit **Start-ResourceSynchronization.ps1** skript k obnovení přístup podrobnosti o účtu úložiště. [Skript je k dispozici z Githubu]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)a je nutné spustit s přihlašovací údaje Správce služby na hostiteli development kit Pokud použijete ASDK.  

- **Stav služby** okno nepodaří načíst. Když otevřete okno Stav služby je správce nebo uživatel portálu Azure zásobníku zobrazí chybu a nenačte informace. Toto je očekávané chování. I když můžete vybrat a otevřete stav služby, tato funkce ještě není k dispozici, ale bude implementované v budoucí verzi systému Azure zásobníku.


#### <a name="health-and-monitoring"></a>Sledování stavu a
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Výstrahy #1:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu prezenčního signálu skener není k dispozici. To může ovlivnit sestavy o stavu a metrik.  

  Výstrahy #2:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu skener chyby není k dispozici. To může ovlivnit sestavy o stavu a metrik.

  Obě výstrahy můžete bezpečně ignorovat. Se automaticky zavře v čase.  

- Na portálu správy Azure zásobníku může zobrazit kritickou výstrahu s názvem **čekající na vyřízení vypršení platnosti certifikátu externí**.  Tuto výstrahu můžete ignorovat a ovlivnit operace Azure zásobníku Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.
 
#### <a name="compute"></a>Compute
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

- Když vytvoříte virtuální počítače na portálu pro uživatele Azure zásobníku, portál zobrazí nesprávný počet datových disků, které můžete připojit k řady DS virtuálních počítačů. Řady DS virtuální počítače zvládne tolik datových disků jako konfiguraci Azure.

- Když image virtuálního počítače se nepodaří vytvořit, položku se nezdařilo, nelze odstranit, mohou být přidány do okna výpočetní Image virtuálního počítače.

  Jako alternativní řešení, vytvořte novou bitovou kopii virtuálního počítače s fiktivní virtuální pevný disk vytvořený pomocí technologie Hyper-V (nový virtuální pevný disk-C:\dummy.vhd cesta-Fixed – SizeBytes 1 GB). Tento proces by měla potíže vyřešit, která zabraňuje odstranění položky se nezdařilo. Potom 15 minut po vytvoření fiktivní bitovou kopii, chcete-li úspěšně odstranit.

  Potom můžete zkusit redownload image virtuálního počítače, který předtím selhal.

-  Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé měli nechat zřizování časový limit namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Sítě
- V části **sítě**, pokud kliknete na tlačítko **připojení** nastavit připojení k síti VPN **VNet-to-VNet** je uveden jako typ možné připojení. Nevybírejte tuto možnost. V současné době pouze **Site-to-site (IPsec)** možnost je podporována.

- Po vytvoření virtuálního počítače a související s veřejnou IP adresu, nelze zrušit přidružení tohoto virtuálního počítače z této IP adresy. Zrušení pravděpodobně fungovat, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu.

  V současné době používejte pouze nové veřejné IP adresy pro nové virtuální počítače, které vytvoříte.

  K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původní virtuální počítač a ne do nového.



- Azure zásobníku podporuje jediný *brány místní sítě* podle IP adresy. To platí ve všech předplatných klienta. Po vytvoření první místní síťové brány připojení, další pokusy o vytvoření prostředku brány místní sítě se stejnou adresou IP jsou blokovány.

- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS *automatické*, změna na vlastní selhání serveru DNS. Aktualizované nastavení nejsou nabídnutých do virtuálních počítačů v této virtuální sítě.
 
- Azure zásobník nepodporuje přidávání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definován v době nasazení.



#### <a name="sql-and-mysql"></a>SQL a MySQL 
- Může trvat až jednu hodinu, než uživatelé mohou vytvářet databází v nové SQL nebo MySQL SKU.

- Databázi hostitelské servery musí být vyhrazená pro použití prostředků zprostředkovatele a uživatelského úlohami. Nelze použít instanci, která se používá jakékoli další příjemce, včetně aplikační služby.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

#### <a name="app-service"></a>App Service
- Uživatelé musí registrovat poskytovatele prostředků úložiště, dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

- Aby bylo možné škálovat infrastrukturu (pracovních procesů, správy, front-end rolí), musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočet.
 
#### <a name="usage"></a>Využití  
- Využití veřejné IP adresy využití měření data zobrazují stejné *EventDateTime* hodnotu pro každý záznam místo *TimeDate* razítka, který ukazuje vytvoření záznamu. Tato data v současné době nelze použít pro monitorování přesné veřejnou IP adresu využití.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Stažení nástroje Azure zásobníku z Githubu
- Při použití *vyvolání webrequest* rutiny prostředí PowerShell ke stažení protokolů Azure nástroje z Githubu, obdržíte chybu:     
    -  *vyvolání webrequest: požadavek byl přerušen: Nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu poslední zastaralá podpora Githubu Tlsv1 a Tlsv1.1 kryptografických standardů (výchozí nastavení pro prostředí PowerShell). Další informace najdete v tématu [slabé kryptografické standardy odebrání oznámení](https://githubengineering.com/crypto-removal-notice/).

  Chcete-li vyřešit tento problém, přidejte `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` do horní části skript, který chcete vynutit konzole PowerShell pro stahování z úložiště Githubu TLSv1.2.





