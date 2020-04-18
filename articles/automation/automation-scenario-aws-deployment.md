---
title: Automatizace nasazení virtuálního počítače ve službách Amazon Web Services
description: Tento článek ukazuje, jak pomocí Azure Automation automatizovat vytváření virtuálního počítače amazonské webové služby
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604831"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scénář Azure Automation – zřízení virtuálního počítače AWS
V tomto článku se dozvíte, jak můžete využít Azure Automation zřídit virtuální počítač v předplatném Amazon Web Service (AWS) a dát tomuto virtuálnímu počítači konkrétní název – který AWS označuje jako "označování" virtuálního počítače.

## <a name="prerequisites"></a>Požadavky
Musíte mít účet Azure Automation a předplatné Amazon Web Services (AWS). Další informace o nastavení účtu Azure Automation a jeho konfiguraci pomocí přihlašovacích údajů k předplatnému AWS [najděte na webu Konfigurace ověřování pomocí webových služeb Amazon](automation-config-aws-account.md). Tento účet by měl být vytvořen nebo aktualizován s přihlašovacími údaji předplatného AWS před pokračováním, jak odkazujete na tento účet v následujících částech.

## <a name="deploy-amazon-web-services-powershell-module"></a>Nasazení modulu PowerShell amazonských webových služeb
Runbook pro zřizování virtuálních počítačů používá k práci modul AWS PowerShell. Pomocí následujících kroků přidejte modul do účtu Automation, který je nakonfigurován s pověřeními předplatného AWS.  

1. Otevřete svůj webový prohlížeč, přejděte do [Galerie PowerShellu](https://www.powershellgallery.com/packages/AWSPowerShell/) a klikněte na **tlačítko Nasadit do Azure Automation**.<br><br> ![Import modulu AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Přejdete na přihlašovací stránku Azure a po ověření se dostanete na portál Azure a zobrazí se následující stránka:<br><br> ![Stránka Importovat modul](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Vyberte účet automatizace, který chcete použít, a klepnutím na **tlačítko OK** spusťte nasazení.

   > [!NOTE]
   > Když Azure Automation importuje modul PowerShell, extrahuje rutiny. Aktivity se nezobrazí, dokud automatizace zcela dokončila import modulu a extrahování rutin. Tento proces může trvat několik minut.  
   > <br>

1. Na webu Azure Portal otevřete účet Automation.
2. Klikněte na dlaždici **Datové zdroje** a v podokně Datové zdroje vyberte **Moduly**.
3. Na stránce Moduly se v seznamu zobrazí modul **AWSPowerShell.**

## <a name="create-aws-deploy-vm-runbook"></a>Vytvoření runbooku pro nasazení virtuálního provozu AWS
Po nasazení modulu AWS PowerShell můžete nyní vytvořit runbook pro automatizaci zřizování virtuálního počítače v AWS pomocí skriptu PowerShell. Následující kroky ukazují, jak používat nativní skript PowerShellu v Azure Automation.  

> [!NOTE]
> Další možnosti a informace týkající se tohoto skriptu naleznete v [Galerii prostředí PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Stáhněte si skript PowerShellU New-AwsVM z Galerie Prostředí PowerShell tak, že otevřete relaci PowerShellu a zadáte následující příkaz:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Na portálu Azure otevřete účet Automation a v části **Automatizace procesů**vyberte **Runbooky** .  
3. Na stránce Runbook vyberte **Přidat runbook**.
4. V podokně Přidat runbook vyberte **Rychlé vytvoření** a vytvořte novou runbook.
5. V podokně vlastností runbooku zadejte název runbooku.
6. V rozevíracím seznamu **Typ knihy Runbook** vyberte **PowerShell**a klikněte na **Vytvořit**.<br><br> ![Vytvořit podokno runbooku](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Když se zobrazí stránka Edit PowerShell Runbook, zkopírujte a vložte skript PowerShellu do vývojového plátna runbooku.<br><br> ![Skript prostředí PowerShell aplikace Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Při práci s ukázkovým skriptem prostředí PowerShell si všimněte následujícího:
    > 
    > * Soubor Runbook obsahuje řadu výchozích hodnot parametrů. Vyhodnoťte všechny výchozí hodnoty a v případě potřeby je aktualizujte.
    > * Pokud jste uložili pověření AWS jako prostředek pověření `AWScred`s názvem jinak než , je třeba aktualizovat skript na řádku 57 tak, aby odpovídalodpovídajícím způsobem.  
    > * Při práci s příkazy Příkazcli AWS v prostředí PowerShell, zejména s tímto příkladem runbook, musíte zadat oblast AWS. V opačném případě rutiny nezdaří. Zobrazit téma AWS [Zadejte oblast AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) v dokumentu AWS Tools for PowerShell pro další podrobnosti.  
    >

7. Chcete-li načíst seznam názvů bitových obrázků z vašeho předplatného AWS, spusťte PowerShell ISE a importujte modul AWS PowerShell. Ověření proti AWS `Get-AutomationPSCredential` nahrazením v prostředí `AWScred = Get-Credential`ISE s . Toto prohlášení vyzve k zadání pověření a můžete zadat ID přístupového klíče pro uživatelské jméno a tajný přístupový klíč pro heslo. 

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
        
    Je vrácen následující výstup:<br><br>
   ![Získejte obrázky AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Zkopírujte a vložte jeden z názvů obrázků do proměnné Automation, jak je uvedeno v aplikaci Runbook jako `$InstanceType`. Vzhledem k tomu, že v tomto příkladu používáte bezplatné předplacené vrstvené AWS, použijete **t2.micro** pro příklad runbooku.  
9. Uložte runbook, pak klikněte na **Publikovat,** abyste ji publikovali, a pak **Ano,** když se zobrazí výzva.

### <a name="testing-the-aws-vm-runbook"></a>Testování runbooku virtuálního virtuálního provozu AWS
Než budete pokračovat v testování runbooku, je třeba ověřit několik věcí:

* Byl vytvořen `AWScred` datový zdroj, který byl volán k ověřování podle AWS, nebo byl skript aktualizován tak, aby odkazoval na název datového zdroje pověření.    
* Modul AWS PowerShell byl importován v Azure Automation.  
* Byla vytvořena nová kniha Runbook a v případě potřeby byly ověřeny a aktualizovány hodnoty parametrů.  
* **Protokolovat podrobné záznamy** a volitelně **protokolovat záznamy průběhu** v rámci operace sady Runbook **Protokolování a trasování** byly nastaveny **na zapnuto**.<br><br> ![Protokolování a trasování](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)knih Runbook .  

1. Kliknutím na **Start** spusťte runbook a po otevření podokna Spustit runbook klikněte na **OK.**
2. V podokně Spustit runbook zadejte název virtuálního počítače. Přijměte výchozí hodnoty pro ostatní parametry, které jste předkonfigurovali ve skriptu. Klepnutím na **tlačítko OK** spusťte úlohu runbooku.<br><br> ![Spustit runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Pro úlohu runbooku, kterou jste vytvořili, se otevře podokno úlohy. Zavřete toto podokno.
4. Průběh úlohy a výstupní datové proudy můžete zobrazit výběrem **možnosti Všechny protokoly** z podokna úloh y runbook.<br><br> ![Výstup datového proudu](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Chcete-li ověřit, že se virtuální ms zřápěl, přihlaste se do Konzoly pro správu AWS, pokud nejste aktuálně přihlášeni.<br><br> ![Konzola AWS nasazená v virtuálním ms](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Další kroky
* Informace o tom, jak začít s grafickými runbooky, najdete [v tématu Moje první grafická kniha runbook](automation-first-runbook-graphical.md).
* Pokud chcete začít s runbooky pracovního postupu prostředí PowerShell, [přečtěte si první runbook pracovního postupu Prostředí PowerShell](automation-first-runbook-textual.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v [tématu Typy runbooků Azure Automation](automation-runbook-types.md).
* Další informace o funkci podpory skriptů prostředí PowerShell najdete [v tématu Podpora nativního skriptu PowerShellu v Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).