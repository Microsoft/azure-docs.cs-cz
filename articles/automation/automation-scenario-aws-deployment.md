---
title: Automatizace nasazení virtuálního počítače ve službě Amazon Web Services
description: Tento článek ukazuje, jak pomocí Azure Automation k automatizaci vytváření virtuálních počítačů Amazon Web Service
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 348c28f6a2d72048e34f117e802abf243597b458
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425216"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scénář Azure Automation – zřízení virtuálního počítače s AWS
V tomto článku se dozvíte, jak se dají využít Azure Automation ke zřízení virtuálního počítače ve vašem předplatném služby AWS (Amazon Web) a poskytnout tohoto virtuálního počítače konkrétním názvem – AWS označuje jako "označení" virtuální počítač.

## <a name="prerequisites"></a>Požadavky
Pro účely tohoto článku musíte mít účet Azure Automation a předplatné AWS. Další informace o nastavení účtu Azure Automation a konfigurace pomocí svých přihlašovacích údajů AWS předplatného najdete v tématu [konfigurace ověřování pomocí Amazon Web Services](automation-config-aws-account.md). Tento účet by měl vytvořit ani aktualizovat pomocí přihlašovacích údajů AWS předplatné než budete pokračovat, jak odkazovat na tento účet v níže uvedeném postupu.

## <a name="deploy-amazon-web-services-powershell-module"></a>Nasazení modulu prostředí PowerShell služby Amazon Web
Váš virtuální počítač zřizování runbook využívá modul Powershellu AWS ke své práci. Proveďte následující kroky, chcete-li přidat modul do vašeho účtu Automation, který je nakonfigurovaný pomocí svých přihlašovacích údajů AWS předplatného.  

1. Otevřete webový prohlížeč a přejděte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) a klikněte na **nasadit do Azure Automation tlačítko**.<br><br> ![Import modulu PS AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Budete přesměrováni na stránku pro přihlášení k Azure a po ověření, budete směrovat na webu Azure portal a zobrazí se následující stránka:<br><br> ![Stránka pro import modulu](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Vyberte účet Automation a klikněte na tlačítko **OK** ke spuštění nasazení.

   > [!NOTE]
   > Při importování modulu prostředí PowerShell do Azure Automation, to je také extrahování rutiny a tyto aktivity se nezobrazí, dokud modul zcela dokončení importování a extrahování rutiny. Tento proces může trvat několik minut.  
   > <br>

1. Na webu Azure Portal otevřete svůj účet Automation, který je odkazováno v kroku 3.
2. Klikněte na **prostředky** dlaždici a na **prostředky** podokně, vyberte **moduly** dlaždici.
3. Na **moduly** stránce se zobrazí **AWSPowerShell** modulu v seznamu.

## <a name="create-aws-deploy-vm-runbook"></a>Vytvoření nasazení sady runbook virtuálního počítače AWS
Po nasazení modulu PowerShell AWS, nyní můžete vytvořit runbook můžete automatizovat zřizování virtuálního počítače v AWS pomocí Powershellového skriptu. Následující kroky ukazují, jak využívat nativní skript prostředí PowerShell ve službě Azure Automation.  

> [!NOTE]
> Další možnosti a informace o tomto skriptu najdete [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Stáhněte Poweshellový skript New-AwsVM z Galerie prostředí PowerShell tak, že otevřete relaci Powershellu a následujícího příkazu:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Z portálu Azure portal otevřete svůj účet Automation a vyberte **sady Runbook** části **automatizace procesů** na levé straně.  
3. Z **sady Runbook** stránce **přidat runbook**.
4. Na **přidat runbook** vyberte **rychlé vytvoření** (vytvořit nový runbook).
5. Na **Runbook** podokno vlastností, zadejte název do pole název vaší sady runbook a **typ Runbooku** rozevíracího seznamu vyberte **Powershellu**a potom klikněte na tlačítko **Vytvořit**.<br><br> ![Vytvoření sady runbook podokno](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Jakmile se zobrazí stránka úprava Powershellového Runbooku, zkopírujte a vložte skript prostředí PowerShell do plátno pro vytváření obsahu runbooku.<br><br> ![Skript prostředí PowerShell v Runbooku](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Při práci s ukázkou skript prostředí PowerShell, mějte na paměti následující:
    > 
    > * Sada runbook obsahuje řadu výchozí hodnoty parametrů. Vyhodnotit všechny výchozí hodnoty a aktualizovat v případě potřeby.
    > * Pokud jste uložili přihlašovací údaje AWS jako asset přihlašovacích údajů pojmenované jinak než **AWScred**, je potřeba aktualizovat skript na řádku 57 tak, aby odpovídaly odpovídajícím způsobem.  
    > * Při práci s příkazy rozhraní příkazového řádku AWS v prostředí PowerShell, zejména s Tento ukázkový runbook, je nutné zadat oblast AWS. V opačném případě rutiny nezdaří. Zobrazení tématu AWS [určete oblasti AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) v AWS nástroje pro dokument prostředí PowerShell pro další podrobnosti.  
    >

7. Pokud chcete načíst seznam názvů imagí z předplatného AWS, spusťte prostředí PowerShell ISE a naimportujte modul Powershellu AWS. Ověřování na základě AWS nahrazením **Get-AutomationPSCredential** v prostředí ISE na základě s **AWScred = Get-Credential**. To vás vyzve k zadání přihlašovacích údajů a může poskytnout vaše **Access Key ID** pro uživatelské jméno a **tajný přístupový klíč** zadání hesla. Viz následující příklad:  

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
        
    Se vrátí následující výstup:<br><br>
   ![Získat Image AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Zkopírujte a vložte je názvy těchto imagí v proměnné Automation, který jste použili v sadě runbook jako **$InstanceType**. Protože v tomto příkladu jste pomocí bezplatné AWS vrstvené předplatné, můžete použít **t2.micro** například sady runbook.  
9. Uložte runbook a potom klikněte na tlačítko **publikovat** Publikovat sadu runbook a potom **Ano** po zobrazení výzvy.

### <a name="testing-the-aws-vm-runbook"></a>Testování runbooku virtuálního počítače AWS
Než budete pokračovat v testování sady runbook, je třeba ověřit pár věcí. Zejména:  

* Prostředek pro ověřování AWS vytvořila volané **AWScred** nebo skript byl aktualizován tak, aby odkazovaly na název vaší asset přihlašovacích údajů.    
* Modul AWS PowerShell se importovala ve službě Azure Automation  
* Vytvoří novou sadu runbook a hodnoty parametrů byl ověřen a aktualizovat v případě potřeby  
* **Protokolování podrobných záznamů** a volitelně **protokolování záznamů o průběhu** v části nastavení sady runbook **protokolování a trasování** byly nastaveny **na**.<br><br> ![Trasování a protokolování sad Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Chcete spouštět sadu runbook, proto klikněte na **Start** a potom klikněte na tlačítko **OK** po otevření podokna spuštění Runbooku.
2. V podokně spuštění Runbooku zadat **VMname**. Přijměte výchozí hodnoty pro parametry, které dříve předkonfigurované ve skriptu. Klikněte na tlačítko **OK** spuštění úlohy runbooku.<br><br> ![Spuštění AwsVM nový runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Podokno úlohy je otevřen pro úlohy runbooku, který jste vytvořili. Toto podokno zavřete.
4. Můžete zobrazit průběh úlohy a zobrazení výstupu **datové proudy** tak, že vyberete **všechny protokoly** dlaždice na stránce úlohy runbooku.<br><br> ![Výstupní Stream](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Potvrďte, že virtuální počítač se zřizuje, přihlaste se ke konzole pro správu AWS Jestliže nejste přihlášeni aktuálně.<br><br> ![Nasazení virtuálního počítače AWS konzoly](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Další postup
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory powershellových skriptů najdete v článku [Nativní podpora powershellových skriptů ve službě Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


