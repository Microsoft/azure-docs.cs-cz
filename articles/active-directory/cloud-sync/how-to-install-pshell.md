---
title: Instalace agenta zřizování cloudu Azure AD Connect pomocí prostředí PowerShell
description: Naučte se, jak pomocí rutin PowerShellu nainstalovat agenta pro zřizování cloudu Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613328"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Instalace agenta Azure AD Connectho zřizování pomocí rutin prostředí PowerShell 
Následující dokument vám ukáže, jak nainstalovat agenta Azure AD Connect zřízení pomocí rutin prostředí PowerShell.
 

## <a name="prerequisite"></a>Požadavek: 


>[!IMPORTANT]
>Následující pokyny k instalaci předpokládají, že byly splněny všechny [požadavky](how-to-prerequisites.md) .
>
> Aby bylo možné nainstalovat agenta Azure AD Connectho zřizování pomocí rutin prostředí PowerShell, musí mít Windows Server povolený protokol TLS 1,2. Pokud chcete povolit TLS 1,2, můžete použít postup, který najdete [tady](how-to-prerequisites.md#tls-requirements).

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Instalace agenta Azure AD Connectho zřizování pomocí rutin prostředí PowerShell 


 1. Přihlaste se k Azure Portal a pak přejít na **Azure Active Directory**.
 2. V nabídce vlevo vyberte **Azure AD Connect**.
 3. Vyberte **Spravovat zřizování (Preview)**  >  **Zkontrolujte všechny agenty**.
 4. Stáhněte si Azure AD Connect agenta zřizování z Azure Portal místně.  

   ![Stáhnout místního agenta](media/how-to-install/install-9.png)</br>
 5. Pro účely těchto pokynů se agent stáhl do následující složky: "C:\ProvisioningSetup". 
 6. Instalace ProvisioningAgent v tichém režimu

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Importovat modul zřizovacího agenta PS 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Připojte se k AzureAD pomocí přihlašovacích údajů globálního správce, můžete přizpůsobit tento oddíl, aby se načetlo heslo z zabezpečeného úložiště. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD – pověření $globalAdminCreds 

 9. Přidejte účet gMSA, zadejte přihlašovací údaje správce domény, aby se vytvořil výchozí účet gMSA. 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Nebo použijte výše uvedenou rutinu k poskytnutí předem vytvořeného účtu gMSA. 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Přidat doménu 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Nebo pomocí výše uvedené rutiny nakonfigurujte upřednostňované řadiče domény. 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Zopakováním předchozího kroku přidejte další domény, zadejte názvy účtů a názvy domén příslušných domén. 
 14. Restartujte službu. 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Pokud chcete vytvořit konfiguraci synchronizace cloudu, přejdete na portál Azure Portal.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Rutiny prostředí PowerShell pro zřizování agenta gMSA
Teď, když jste nainstalovali agenta, můžete pro gMSA použít podrobnější oprávnění.  Informace a podrobné pokyny ke konfiguraci oprávnění najdete v tématu [Azure AD Connect rutiny prostředí PowerShell pro zřizování cloudu gMSA](how-to-gmsa-cmdlets.md) .

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Rutiny prostředí PowerShell pro gMSA Azure AD Connectho zřizování cloudu](how-to-gmsa-cmdlets.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)