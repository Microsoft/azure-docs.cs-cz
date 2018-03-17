---
title: "Automatizace nasazení virtuálního počítače ve službě Amazon Web Services"
description: "Tento článek ukazuje, jak používat Azure Automation k automatizovanému vytvoření virtuálního počítače Amazon webové služby"
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 371510bf335bda2e2475e6ba3f5f74ee2117d9a5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation scénář – zřizování se AWS virtuální počítač
V tomto článku se dozvíte, jak můžete využít Azure Automation pro zřízení virtuálního počítače ve vašem předplatném Amazon Web Service (AWS) a pojmenujte tohoto virtuálního počítače konkrétní název – AWS označuje jako "označování" virtuální počítač.

## <a name="prerequisites"></a>Požadavky
Pro účely tohoto článku musíte mít účet Azure Automation a předplatné AWS. Další informace o vytvoření účtu Azure Automation a nastavit ho pomocí svých přihlašovacích údajů AWS předplatné, zkontrolujte [konfigurace ověřování pomocí Amazon Web Services](automation-config-aws-account.md). Tento účet by měl vytvořit nebo aktualizovat pomocí svých přihlašovacích údajů AWS předplatné než budete pokračovat, protože odkazujete tento účet v následujících krocích.

## <a name="deploy-amazon-web-services-powershell-module"></a>Nasazení modulu Amazon Web Services prostředí PowerShell
Virtuální počítač zřizování runbook využívá modul AWS PowerShell ke své práci. Proveďte následující kroky pro přidání modulu ke svému účtu Automation, který je nakonfigurovaný pomocí svých přihlašovacích údajů AWS předplatného.  

1. Otevřete webový prohlížeč a přejděte do [Galerie prostředí PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) a klikněte na **nasadit do Azure Automation tlačítko**.<br><br> ![Import modulu AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Budete přesměrováni na stránku přihlášení k Azure a po ověření, můžete se směruje na portálu Azure a zobrazí se následující stránka:<br><br> ![Import modulu stránky](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Vyberte účet Automation, který chcete použít a klikněte na **OK** ke spuštění nasazení.

   > [!NOTE]
   > Při importu do Azure Automation modul prostředí PowerShell, ho je také extrahování rutiny a tyto aktivity se nezobrazí, dokud modul úplně nedokončí import a extrahování rutiny. Tento proces může trvat několik minut.  
   > <br>

1. Na portálu Azure otevřete účet Automation z kroku 3.
2. Klikněte na **prostředky** dlaždici a na **prostředky** podokně, vyberte **moduly** dlaždici.
3. Na **moduly** stránky, uvidíte **AWSPowerShell** modulu v seznamu.

## <a name="create-aws-deploy-vm-runbook"></a>Vytvoření AWS nasazení virtuálních počítačů sady runbook
Po nasazený modul PowerShell AWS, teď můžete vytvářet sady runbook k automatizaci zřízení virtuálního počítače v AWS pomocí skriptu prostředí PowerShell. Následující kroky ukazují, jak využívat nativní skript prostředí PowerShell ve službě Azure Automation.  

> [!NOTE]
> Pro další možnosti a informace týkající se tohoto skriptu, naleznete [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Stáhněte skript prostředí PowerShell New-AwsVM z Galerie prostředí PowerShell pomocí otevřete relaci prostředí PowerShell a zadáte následující:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Z portálu Azure otevřete účet Automation a vyberte **Runbooky** části **automatizace procesu** na levé straně.  
3. Z **Runbooky** vyberte **přidat runbook**.
4. Na **přidat runbook** podokně, vyberte **rychle vytvořit** (vytvořit novou sadu runbook).
5. Na **Runbook** podokno vlastností, zadejte název do pole název vaší sady runbook a z **typ Runbooku** rozevíracího seznamu vyberte **prostředí PowerShell**a potom klikněte na **Vytvořit**.<br><br> ![Vytvoření sady runbook podokně](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Jakmile se zobrazí stránka úprava Powershellového Runbooku, zkopírujte a vložte skript prostředí PowerShell do runbooku vytváření plátno.<br><br> ![Skript prostředí PowerShell sady Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Při práci s příklad skriptu prostředí PowerShell, pamatujte na tyhle:
    > 
    > * Sada runbook obsahuje počet výchozí hodnoty parametrů. Posuďte všechny výchozí hodnoty a aktualizovat v případě potřeby.
    > * Pokud máte uložené přihlašovací údaje AWS jako asset přihlašovacích údajů jiný název než **AWScred**, budete muset aktualizovat skript na řádku 57 tak, aby odpovídaly odpovídajícím způsobem.  
    > * Při práci s příkazy rozhraní příkazového řádku AWS v prostředí PowerShell, zejména s Tento ukázkový runbook, je nutné zadat oblasti AWS. Rutiny, jinak hodnota nezdaří. Zobrazení AWS tématu [zadejte oblast AWS](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) v AWS nástroje pro dokument prostředí PowerShell pro další podrobnosti.  
    >

7. Pokud chcete načíst seznam názvů bitové kopie ze svého předplatného AWS, spusťte prostředí PowerShell ISE a naimportujte modul Powershellu AWS. Ověřování na základě AWS nahrazením **Get-AutomationPSCredential** ve vašem prostředí ISE s **AWScred = Get-Credential**. To vás vyzve k zadání přihlašovacích údajů a může poskytnout vaše **Access Key ID** pro uživatelské jméno a **tajný přístupový klíč** hesla. Viz následující příklad:  

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

    Se vrátí následující výstup:<br><br>
   ![Získat AWS obrázků](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Zkopírujte a vložte jeden z názvy obrázků v proměnné Automation jako odkazovaná v sadě runbook jako **$InstanceType**. Vzhledem k tomu, že v tomto příkladu jsou pomocí volné AWS vrstvené předplatné, je použít **t2.micro** například vaše sady runbook.  
9. Uložte runbook a potom klikněte na **publikovat** Publikovat sadu runbook a potom **Ano** po zobrazení výzvy.

### <a name="testing-the-aws-vm-runbook"></a>Otestování sady runbook AWS virtuálních počítačů
Než přikročíte k testování sady runbook, je třeba ověřit pár věcí. Zejména:  

* Prostředek pro ověřování proti AWS vytvořila volané **AWScred** nebo skript se aktualizovalo a odkazovat na název vaší asset přihlašovacích údajů.    
* Modul prostředí PowerShell AWS importu v Azure Automation.  
* Byla vytvořena nová sada runbook a byl ověřen a podle potřeby je aktualizován hodnoty parametru  
* **Protokolování podrobných záznamů** a volitelně **protokolování záznamů o průběhu** v části nastavení sady runbook **protokolování a trasování** byla nastavena na **na**.<br><br> ![Protokolování sad Runbook a trasování](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Chcete spustit runbook, proto klikněte na **spustit** a pak klikněte na **OK** při spuštění Runbooku podokně otevře.
2. V podokně spuštění Runbooku zadat **VMname**. Přijměte výchozí hodnoty pro parametry, které dříve předkonfigurované ve skriptu. Klikněte na tlačítko **OK** při spuštění úlohy sady runbook.<br><br> ![Spuštění AwsVM nový runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Podokno úlohy se spustí úloha sady runbook, kterou jste vytvořili. Zavřete toto podokno.
4. Můžete sledovat průběh úlohy a zobrazení výstupu **datové proudy** výběrem **všechny protokoly** dlaždice ze stránky úlohy sady runbook.<br><br> ![Výstup datového proudu](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Potvrďte, že je zřizovaný virtuální počítač, přihlaste se k konzole pro správu AWS Pokud nejste aktuálně přihlášeni.<br><br> ![Konzoly AWS nasazení virtuálních počítačů](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Další postup
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

