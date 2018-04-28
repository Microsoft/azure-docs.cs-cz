---
title: Znovu nasaďte Azure zásobníku Development Kit (ASDK) | Microsoft Docs
description: V tomto kurzu zjistěte, jak znovu nainstalovat ASDK.
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
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 33879187a912394b5cec6e9f9a8898f431134f5c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-redeploy-the-asdk"></a>Kurz: znovu nasaďte ASDK
V tomto kurzu zjistěte, jak se znovu nasadit Azure zásobníku Development Kit (ASDK) v testovacím prostředí. Protože upgrade ASDK není podporován, budete muset zcela znovu nasaďte ho přesunout na novější verzi. Můžete také znovu nasadit ASDK kdykoli, který chcete začít znovu od začátku.

> [!IMPORTANT]
> Upgrade ASDK na novou verzi není podporován. Budete muset znovu nasaďte ASDK na hostitelském počítači development kit pokaždé, když chcete vyhodnotit na novější verzi Azure zásobníku.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odebrání registrace Azure 
> * Znovu nasaďte ASDK

## <a name="remove-azure-registration"></a>Odebrání registrace Azure 
Pokud jste již dříve zaregistrovali ASDK instalace s Azure, byste měli odebrat před opětovného nasazení ASDK registrace prostředků. Opakujte registraci ASDK při opětovném ASDK povolit syndikace marketplace. Pokud ve vašem předplatném Azure nejsou dříve registrován ASDK, můžete tuto část přeskočit.

Pokud chcete odstranit prostředek registrace, použijte **odebrat AzsRegistration** rutiny se zrušit registraci Azure zásobníku. Potom použít **odebrat AzureRMRsourceGroup** rutiny Odstraňte skupinu prostředků Azure zásobníku ze svého předplatného Azure:

1. Otevřete konzolu prostředí PowerShell jako správce na počítači, který má přístup k privilegované koncový bod. Pro ASDK, která se development kit hostitelský počítač.

2. Spusťte následující příkazy prostředí PowerShell odstranit a zrušit registraci instalace ASDK **azurestack** skupině prostředků z vašeho předplatného Azure:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Connect-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -CloudAdminCredential $YourCloudAdminCredential `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Zobrazí se výzva k přihlášení do vašeho předplatného Azure a místní instalaci ASDK při spuštění skriptu.
4. Po dokončení skriptu, měli byste vidět zprávy podobná následující příklady:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Azure zásobníku by měl nyní úspěšně se zrušit registraci ze svého předplatného Azure. Kromě toho azurestack skupinu prostředků, vytvořen po registraci ASDK s Azure, má být rovněž odstraněn.

## <a name="redeploy-the-asdk"></a>Znovu nasaďte ASDK
K opětovnému nasazení zásobník Azure, musíte spustit přes od začátku níže popsané. Postup se liší v závislosti na tom, jestli použít skript instalačního programu (asdk-installer.ps1) zásobníku Azure k instalaci ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Znovu nasaďte ASDK pomocí skriptu Instalační program
1. V počítači, ASDK, otevřete konzolu prostředí PowerShell se zvýšenými oprávněními a přejděte do skriptu asdk installer.ps1 **AzureStack_Installer** directory uložené na nesystémové jednotce. Spusťte skript a klikněte na tlačítko **restartovat**.

   ![Spusťte skript asdk installer.ps1](media/asdk-redeploy/1.png)

2. Vyberte základní operační systém (ne **zásobník Azure**) a klikněte na tlačítko **Další**.

   ![Restartujte do hostitelského operačního systému](media/asdk-redeploy/2.png)

3. Po hostitele development kit restartuje do základního operačního systému, přihlaste se jako místní správce. Vyhledejte a odstraňte **C:\CloudBuilder.vhdx** soubor, který byl použit v rámci předchozí nasazení. 

4. Opakujte stejný postup, které jste si pro první [nasadit ASDK](asdk-deploy.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Znovu nasaďte ASDK bez použití Instalační program
Pokud jste nepoužili asdk installer.ps1 skript k instalaci ASDK, je nutné ručně překonfigurovat development kit hostitelský počítač před opětovného nasazení ASDK.

1. Spusťte nástroj Konfigurace systému spuštěním **msconfig.exe** v počítači ASDK. Na **spouštěcí** , vyberte operační systém počítače hostitelů (ne Azure Stack), klikněte na **nastavit jako výchozí**a potom klikněte na **OK**. Klikněte na tlačítko **restartujte** po zobrazení výzvy.

      ![Nastavení konfigurace spouštění](media/asdk-redeploy/4.png)

2. Po hostitele development kit restartuje do základního operačního systému, přihlaste se jako místní správce pro hostitelský počítač development kit. Vyhledejte a odstraňte **C:\CloudBuilder.vhdx** soubor, který byl použit v rámci předchozí nasazení. 

3. Opakujte stejný postup, které jste si pro první [nasazení ASDK pomocí prostředí PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Další postup
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Odebrání registrace Azure 
> * Znovu nasaďte ASDK

Přechodu na v dalším kurzu se dozvíte, jak přidat položku zásobník Azure marketplace.

> [!div class="nextstepaction"]
> [Přidání položky zásobníku Azure marketplace.](asdk-marketplace-item.md)




