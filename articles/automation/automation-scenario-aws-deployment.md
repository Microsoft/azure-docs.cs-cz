---
title: Automatizace nasazení virtuálního počítače v Amazon Web Services
description: Tento článek ukazuje, jak použít Azure Automation k automatizaci vytváření virtuálního počítače webové služby Amazon.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2a58d3e79301f277143d8c4b6e810a377a211b9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849628"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation scénář – zřízení virtuálního počítače s AWS
V tomto článku se dozvíte, jak můžete pomocí Azure Automation zřídit virtuální počítač v předplatném Amazon Web Service (AWS) a dát tomuto VIRTUÁLNÍmu počítači konkrétní název, který AWS odkazuje na označení virtuálního počítače jako "označování".

## <a name="prerequisites"></a>Předpoklady
Pro účely tohoto článku potřebujete účet Azure Automation a předplatné AWS. Další informace o nastavení účtu Azure Automation a jeho konfiguraci s přihlašovacími údaji pro předplatné AWS najdete v [konfiguraci ověřování pomocí Amazon Web Services](automation-config-aws-account.md). Tento účet by se měl před pokračováním vytvořit nebo aktualizovat pomocí přihlašovacích údajů předplatného AWS, jak odkazujete na tento účet v následujících krocích.

## <a name="deploy-amazon-web-services-powershell-module"></a>Nasazení modulu Amazon Web Services PowerShell
Sada Runbook zřizování virtuálních počítačů využívá modul AWS PowerShell ke své práci. Provedením následujících kroků přidáte modul do svého účtu Automation, který je nakonfigurovaný s přihlašovacími údaji předplatného AWS.  

1. Otevřete webový prohlížeč a přejděte na [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) a klikněte na **tlačítko nasadit do Azure Automation**.<br><br> Import modulu ![AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Přejdete na přihlašovací stránku Azure a po ověření budete přesměrováni na Azure Portal a zobrazí se tato stránka:<br><br> ![Stránka importovat modul](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Vyberte účet Automation, který se má použít, a kliknutím na **OK** spusťte nasazení.

   > [!NOTE]
   > Při importu modulu PowerShellu do Azure Automation se také extrahují rutiny a tyto aktivity se nezobrazí, dokud modul nedokončí import a extrakci rutin. Tento proces může trvat několik minut.  
   > <br>

1. V Azure Portal otevřete účet Automation, na který odkazuje krok 3.
2. Klikněte na dlaždici **aktiva** a v podokně **prostředky** vyberte dlaždici **moduly** .
3. Na stránce **moduly** se v seznamu zobrazí modul **AWSPowerShell** .

## <a name="create-aws-deploy-vm-runbook"></a>Vytvoření Runbooku AWS nasazení virtuálního počítače
Po nasazení modulu AWS PowerShellu teď můžete vytvořit Runbook pro automatizaci zřizování virtuálního počítače v AWS pomocí skriptu PowerShellu. Následující postup ukazuje, jak využít nativní skript PowerShellu v Azure Automation.  

> [!NOTE]
> Další možnosti a informace týkající se tohoto skriptu najdete na [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Stáhněte PowerShellový skript New-AwsVM z Galerie prostředí PowerShell tak, že otevřete relaci PowerShellu a zadáte následující:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Z Azure Portal otevřete svůj účet Automation a v části **Automatizace procesu** na levé straně vyberte **Runbooky** .  
3. Na stránce sady **Runbook** vyberte **Přidat sadu Runbook**.
4. V podokně **Přidat sadu Runbook** vyberte možnost **rychle vytvořit** (vytvořit novou sadu Runbook).
5. V podokně vlastnosti **Runbooku** zadejte název do pole název Runbooku a v rozevíracím seznamu **typ Runbooku** vyberte **PowerShell**a pak klikněte na **vytvořit**.<br><br> ![vytvořit podokno sady Runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Když se zobrazí stránka upravit PowerShellový Runbook, zkopírujte a vložte PowerShellový skript na plátno pro tvorbu runbooků.<br><br> ![Skript Runbooku PowerShellu](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Při práci s ukázkovým skriptem prostředí PowerShell Pamatujte na následující:
    > 
    > * Sada Runbook obsahuje řadu výchozích hodnot parametrů. Vyhodnoťte všechny výchozí hodnoty a v případě potřeby aktualizujte.
    > * Pokud jste uložili přihlašovací údaje AWS jako prostředek přihlašovacích údajů s názvem jinou než **AWScred**, budete muset skript aktualizovat na řádku 57, aby odpovídajícím způsobem odpovídal.  
    > * Při práci s příkazy rozhraní příkazového řádku AWS v prostředí PowerShell, zejména v tomto příkladu sady Runbook, je nutné zadat oblast AWS. V opačném případě rutiny selžou. Další podrobnosti najdete v tématu AWS tématu [určení AWS oblasti](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) v dokumentu nástroje AWS Tools for PowerShell.  
    >

7. Pokud chcete načíst seznam názvů imagí z předplatného AWS, spusťte PowerShell ISE a importujte modul AWS PowerShellu. Ověřování proti AWS nahrazením **Get-AutomationPSCredential** v prostředí ISE pomocí **AWScred = Get-Credential**. Zobrazí se vám výzva k zadání přihlašovacích údajů a můžete zadat **ID přístupového klíče** pro uživatelské jméno a **tajný přístupový klíč** pro heslo. Viz následující příklad:  

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    Vrátí se následující výstup:<br><br>
   ![získání imagí AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Zkopírujte a vložte jeden z názvů obrázků do proměnné automatizace, jak je odkazováno v Runbooku jako **$instanceType**. Vzhledem k tomu, že v tomto příkladu používáte bezplatné AWS vrstvené předplatné, použijete pro Runbook příklad **T2. Micro** .  
9. Uložte Runbook a potom kliknutím na **publikovat** publikujte Runbook a po zobrazení výzvy klikněte na **Ano** .

### <a name="testing-the-aws-vm-runbook"></a>Testování Runbooku virtuálního počítače s AWS
Než budete pokračovat v testování Runbooku, musíte ověřit pár věcí. Zejména:  

* Asset pro ověřování proti AWS se vytvořil s názvem **AWScred** , nebo se skript aktualizoval tak, aby odkazoval na název vašeho prostředku přihlašovacích údajů.    
* Modul AWS PowerShell byl importován v Azure Automation  
* Vytvořil se nový Runbook a v případě potřeby byly ověřeny a aktualizovány hodnoty parametrů.  
* **Protokolovat podrobné záznamy** a volitelně **Protokolovat záznamy o průběhu** v protokolování nastavení sady Runbook **a trasování** bylo nastaveno na **zapnuto**.<br><br> ![protokolování a trasování sady Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Chcete spustit sadu Runbook, klikněte na tlačítko **Start** a po otevření podokna spustit sadu Runbook klikněte na tlačítko **OK** .
2. V podokně spustit sadu Runbook zadejte **VMname**. Přijměte výchozí hodnoty pro ostatní parametry, které jste předtím předem nakonfigurovali ve skriptu. Kliknutím na tlačítko **OK** spusťte úlohu Runbooku.<br><br> ![Start New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. Zavřete toto podokno.
4. Průběh úlohy a zobrazení výstupních **datových proudů** můžete zobrazit tak, že na stránce úlohy Runbooku vyberete dlaždici **všechny protokoly** .<br><br> výstup datového proudu ![](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Pokud se chcete ujistit, že se virtuální počítač zřizuje, přihlaste se do konzoly pro správu AWS, pokud nejste momentálně přihlášení.<br><br> ![Nasazený virtuální počítač konzoly AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Další kroky
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


