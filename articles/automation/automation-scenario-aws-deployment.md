---
title: Nasazení virtuálního počítače s Amazon Web Services pomocí sady Azure Automation Runbook
description: V tomto článku se dozvíte, jak automatizovat vytváření Amazon Web Servicesch virtuálních počítačů.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 456a7e4f07b2416e1d2037205574f2e7149e70e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185938"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Nasazení virtuálního počítače s Amazon Web Services pomocí Runbooku

V tomto článku se dozvíte, jak můžete pomocí Azure Automation zřídit virtuální počítač v předplatném Amazon Web Service (AWS) a dát tomuto VIRTUÁLNÍmu počítači konkrétní název, který AWS odkazuje na označení virtuálního počítače jako "označování".

## <a name="prerequisites"></a>Požadavky

Musíte mít účet Azure Automation a předplatné Amazon Web Services (AWS). Další informace o nastavení účtu Azure Automation a jeho konfiguraci s přihlašovacími údaji pro předplatné AWS najdete v [konfiguraci ověřování pomocí Amazon Web Services](automation-config-aws-account.md). Tento účet by se měl před pokračováním vytvořit nebo aktualizovat pomocí přihlašovacích údajů předplatného AWS, jak odkazujete na tento účet v níže uvedených částech.

## <a name="deploy-amazon-web-services-powershell-module"></a>Nasazení modulu Amazon Web Services PowerShell

Sada Runbook zřizování virtuálních počítačů používá ke své práci modul AWS PowerShell. Pomocí následujících kroků přidejte modul do účtu Automation, který je nakonfigurovaný s přihlašovacími údaji předplatného AWS.  

1. Otevřete webový prohlížeč a přejděte na [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) a klikněte na **tlačítko nasadit do Azure Automation**.<br><br> ![Import modulu AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Přejdete na přihlašovací stránku Azure a po ověření budete přesměrováni na Azure Portal a zobrazí se tato stránka:<br><br> ![Stránka importovat modul](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Vyberte účet Automation, který se má použít, a kliknutím na **OK** spusťte nasazení.

   > [!NOTE]
   > Když Azure Automation importuje modul prostředí PowerShell, extrahuje rutiny. Aktivity se nezobrazí, dokud Automation nedokončí kompletní import modulu a extrakci rutin. Tento proces může trvat několik minut.  
   > <br>

1. Na webu Azure Portal otevřete účet Automation.
2. Klikněte na dlaždici **aktiva** a v podokně prostředky vyberte **moduly**.
3. Na stránce moduly se v seznamu zobrazí modul **AWSPowerShell** .

## <a name="create-aws-deploy-vm-runbook"></a>Vytvoření Runbooku AWS nasazení virtuálního počítače

Po nasazení modulu AWS PowerShellu teď můžete vytvořit Runbook pro automatizaci zřizování virtuálního počítače v AWS pomocí skriptu PowerShellu. Následující postup ukazuje použití nativního skriptu PowerShellu v Azure Automation.  

> [!NOTE]
> Další možnosti a informace týkající se tohoto skriptu najdete na [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Stáhněte PowerShellový skript New-AwsVM z Galerie prostředí PowerShell tak, že otevřete relaci PowerShellu a zadáte následující příkaz:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Z Azure Portal otevřete svůj účet Automation a v části **Automatizace procesu**vyberte **Runbooky** .  
3. Na stránce sady Runbook vyberte **Přidat sadu Runbook**.
4. V podokně Přidat Runbook vyberte **rychlé vytvoření** a vytvořte novou sadu Runbook.
5. V podokně vlastnosti Runbooku zadejte název Runbooku.
6. V rozevíracím seznamu **typ Runbooku** vyberte **PowerShell**a pak klikněte na **vytvořit**.<br><br> ![Vytvořit podokno Runbooku](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. Když se zobrazí stránka upravit PowerShellový Runbook, zkopírujte a vložte PowerShellový skript na plátno pro tvorbu runbooků.<br><br> ![Skript Runbooku PowerShellu](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Při práci s ukázkovým skriptem prostředí PowerShell Pamatujte na následující:

    * Sada Runbook obsahuje řadu výchozích hodnot parametrů. Vyhodnoťte všechny výchozí hodnoty a v případě potřeby aktualizujte.
    * Pokud jste uložili přihlašovací údaje AWS jako prostředek přihlašovacích údajů s názvem odlišným od `AWScred` , budete muset skript aktualizovat na řádku 57, aby odpovídajícím způsobem odpovídal.  
    * Při práci s příkazy rozhraní příkazového řádku AWS v prostředí PowerShell, zejména v tomto příkladu sady Runbook, je nutné zadat oblast AWS. V opačném případě rutiny selžou. Další podrobnosti najdete v tématu AWS tématu [určení AWS oblasti](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) v dokumentu nástroje AWS Tools for PowerShell.  

8. Pokud chcete načíst seznam názvů imagí z předplatného AWS, spusťte PowerShell ISE a importujte modul AWS PowerShellu. Ověřování proti AWS nahrazením `Get-AutomationPSCredential` v prostředí ISE pomocí `AWScred = Get-Credential` . Tento příkaz zobrazí výzvu k zadání vašich přihlašovacích údajů a můžete zadat ID přístupového klíče pro uživatelské jméno a tajný přístupový klíč pro heslo. 

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
   ![Získání imagí AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Zkopírujte a vložte jeden z názvů obrázků do proměnné automatizace, jak je odkazováno v Runbooku jako `$InstanceType` . Vzhledem k tomu, že v tomto příkladu používáte bezplatné AWS vrstvené předplatné, použijete pro Runbook příklad **T2. Micro** .  
10. Uložte Runbook a potom kliknutím na **publikovat** publikujte Runbook a po zobrazení výzvy klikněte na **Ano** .

### <a name="test-the-aws-vm-runbook"></a>Test Runbooku virtuálního počítače s AWS

1. Ověřte, že sada Runbook vytvoří Asset s názvem `AWScred` pro ověřování proti AWS, nebo aktualizujte skript tak, aby odkazoval na název assetu přihlašovacích údajů.    
2. Ověřte novou sadu Runbook a ujistěte se, že jsou všechny hodnoty parametrů aktualizované.
Ujistěte se, že se modul AWS PowerShell importoval do Azure Automation.  
3. V Azure Automation nastavte **protokol podrobné záznamy** a volitelně **protokolujte záznamy o průběhu** v protokolování operací Runbooku **a trasování** do **zapnuto**.<br><br> ![Protokolování ](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png) a trasování Runbooku  
4. Kliknutím na **Start** spustíte Runbook a potom po otevření podokna spustit Runbook klikněte na **OK** .
5. V podokně spustit Runbook zadejte název virtuálního počítače. Přijměte výchozí hodnoty pro ostatní parametry, které jste nakonfigurovali ve skriptu. Kliknutím na tlačítko **OK** spusťte úlohu Runbooku.<br><br> ![Spustit Runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. Zavřete toto podokno.
7. Můžete zobrazit průběh úlohy a zobrazit výstupní datové proudy výběrem možnosti **všechny protokoly** v podokně úlohy Runbooku.<br><br> ![Výstup streamu](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Pokud se chcete ujistit, že se virtuální počítač zřizuje, přihlaste se do konzoly pro správu AWS, pokud se momentálně nejste přihlášeni.<br><br> ![Nasazený virtuální počítač konzoly AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Další kroky
 
* Chcete-li zjistit, jaké sady Runbook jsou podporovány, přečtěte si téma[Azure Automation typy runbooků](automation-runbook-types.md).
* Informace o práci se sadami Runbook najdete [v tématu Správa runbooků v Azure Automation](manage-runbooks.md).
* Podrobnosti o PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
* Podporu skriptů najdete v tématu [Podpora nativních skriptů PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
