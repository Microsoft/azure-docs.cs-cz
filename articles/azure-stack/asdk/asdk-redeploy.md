---
title: Opětovné nasazení Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak ASDK přeinstalovat.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d166916ca54f3b8c26a418ff83093e53dcdbe515
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413731"
---
# <a name="redeploy-the-asdk"></a>Opětovné nasazení ASDK
V tomto článku se dozvíte, jak znovu nasadit Azure Stack Development Kit (ASDK) v neprodukčním prostředí. Protože upgrade ASDK není podporován, budete muset zcela jej přesunout na novější verzi znovu nasadit. Můžete také znovu nasadit ASDK kdykoli, který chcete začít znovu od začátku.

> [!IMPORTANT]
> Upgrade ASDK na novou verzi se nepodporuje. Je nutné znovu nasadit ASDK na hostitelském počítači development kit pokaždé, když chcete vyhodnotit novější verze služby Azure Stack.

## <a name="remove-azure-registration"></a>Odebrat registraci Azure 
Pokud jste dříve zaregistrovali ASDK instalace s Azure, měli byste odebrat registrace prostředků před znovu se nasazuje ASDK. Opětovná registrace ASDK při opětovném nasazování ASDK povolit dostupnost položky na webu Marketplace. Pokud jste se ještě nezaregistrovali dříve ASDK ve vašem předplatném Azure, můžete tuto část přeskočit.

K odebrání prostředků registrace, použijte **odebrat AzsRegistration** rutiny ke zrušení registrace Azure Stack. Potom použijte **odebrat AzureRMRsourceGroup** rutiny z vašeho předplatného Azure odstranit skupinu prostředků Azure Stack:

1. Otevřete konzolu Powershellu s oprávněními správce na počítači, který má přístup k privilegovaným koncový bod. Pro ASDK, který je hostitelský počítač development kit.

2. Spusťte následující příkazy prostředí PowerShell a zrušit registraci instalace ASDK odstranit **azurestack** skupinu prostředků z vašeho předplatného Azure:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Add-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Zobrazí se výzva k přihlášení k vašemu předplatnému Azure a místní instalace ASDK po spuštění skriptu.
4. Po dokončení skriptu, měli byste vidět zprávy, jako v následujících příkladech:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Azure Stack by měl nyní úspěšně se zrušit registraci z vašeho předplatného Azure. Navíc by měl také odstranit skupinu prostředků azurestack, který je vytvořen po registraci ASDK s Azure.

## <a name="deploy-the-asdk"></a>Nasazení ASDK
Opětovné nasazení Azure Stack, je nutné spustit přes úplně od začátku jak je popsáno níže. Postup se liší v závislosti na tom, jestli jste k instalaci ASDK použili skript instalačního programu (asdk installer.ps1) Azure Stack.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Znovu nasadit ASDK pomocí skriptů Instalační program
1. Na počítači ASDK, otevřete konzolu Powershellu se zvýšenými oprávněními a přejděte do skriptu asdk installer.ps1 **AzureStack_Installer** directory umístěný na nesystémové jednotce. Spusťte skript a klikněte na tlačítko **restartování**.

   ![Spusťte skript asdk installer.ps1](media/asdk-redeploy/1.png)

2. Vyberte základní operační systém (ne **Azure Stack**) a klikněte na tlačítko **Další**.

   ![Restartování do hostitelského operačního systému](media/asdk-redeploy/2.png)

3. Po restartování hostitele development kit do základní operační systém se přihlaste jako místní správce. Vyhledejte a odstraňte **C:\CloudBuilder.vhdx** soubor, který byl použit jako součást předchozí nasazení. 

4. Opakujte stejný postup, které jste provedli na první [nasadit ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Opětovné nasazení ASDK bez použití instalačního programu
Pokud jste nepoužili skript asdk installer.ps1 k instalaci ASDK, nutné ručně překonfigurovat development kit hostitelský počítač před ASDK opětovného nasazení.

1. Spusťte nástroj Konfigurace systému spuštěním **msconfig.exe** ASDK počítače. Na **spouštěcí** kartu, vyberte operační systém počítače hostitelů (ne Azure Stack), klikněte na tlačítko **nastavit jako výchozí**a potom klikněte na tlačítko **OK**. Klikněte na tlačítko **restartovat** po zobrazení výzvy.

      ![Nastavit spouštěcí konfigurace](media/asdk-redeploy/4.png)

2. Po restartování hostitele development kit do základní operační systém se přihlaste jako místní správce pro hostitelský počítač development kit. Vyhledejte a odstraňte **C:\CloudBuilder.vhdx** soubor, který byl použit jako součást předchozí nasazení. 

3. Opakujte stejný postup, které jste provedli na první [ASDK pomocí Powershellu nasadit](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Další postup
[Po dokončení instalace ASDK úlohy konfigurace](asdk-post-deploy.md)




