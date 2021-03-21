---
title: Vytvoření účtu Azure Automation spustit jako
description: V tomto článku se dozvíte, jak vytvořit účet Spustit jako pomocí PowerShellu nebo z Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: ef6afff30da48b79b42e5fb4b3c72c3500f22dd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172299"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Vytvoření účtu Azure Automation spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků v modelu nasazení Azure Resource Manager nebo Azure Classic pomocí runbooků Automation a dalších funkcí automatizace. Tento článek popisuje, jak vytvořit účet Spustit jako nebo účet Spustit jako pro Classic z Azure Portal nebo Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Vytvořit účet v Azure Portal

Chcete-li aktualizovat účet Azure Automation v Azure Portal, proveďte následující kroky. Účty Spustit jako a účet Spustit jako pro Classic se vytvářejí samostatně. Pokud nepotřebujete spravovat klasické prostředky, můžete vytvořit jenom účet Spustit jako pro Azure.

1. Přihlaste se k webu Azure Portal pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

2. Vyhledejte a vyberte **účty Automation**.

3. Na stránce **účty Automation** v seznamu vyberte svůj účet Automation.

4. V levém podokně vyberte **účty Spustit jako** v části **Nastavení účtu** .

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Vyberte možnost účet Spustit jako.":::

5. V závislosti na účtu, který požadujete, použijte podokno **+ účet Spustit jako pro Azure** nebo účet **Spustit jako pro Azure Classic** . Po kontrole informací v přehledu klikněte na **vytvořit**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Vyberte možnost vytvoření účtu Spustit jako.":::

6. Zatímco Azure vytváří účet Spustit jako, můžete průběh sledovat v nabídce v části **Oznámení**. Zobrazí se také zpráva s informacemi o tom, že účet se vytváří. Dokončení procesu může trvat několik minut.

## <a name="create-account-using-powershell"></a>Vytvoření účtu pomocí PowerShellu

Následující seznam uvádí požadavky na vytvoření účtu Spustit jako v prostředí PowerShell pomocí poskytnutého skriptu. Tyto požadavky platí pro oba typy účtů spustit jako.

* Windows 10 nebo Windows Server 2016 s Azure Resource Manager moduly 3.4.1 a novější. PowerShellový skript nepodporuje starší verze Windows.
* Azure PowerShell PowerShell 6.2.4 nebo novější. Informace najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps).
* Účet Automation, na který se odkazuje jako na hodnotu pro `AutomationAccountName` parametry a `ApplicationDisplayName` .
* Oprávnění, která jsou shodná s těmi, která jsou uvedena v [požadovaných oprávněních ke konfiguraci účtů spustit jako](automation-security-overview.md#permissions).

Chcete-li získat hodnoty pro `AutomationAccountName` , `SubscriptionId` a `ResourceGroupName` , které jsou pro skript prostředí PowerShell požadovány, proveďte následující kroky.

1. Přihlaste se k webu Azure Portal.

1. Vyhledejte a vyberte **účty Automation**.

1. Na stránce účty Automation v seznamu vyberte svůj účet Automation.

1. V levém podokně vyberte možnost **vlastnosti**.

1. Poznamenejte si hodnoty pro **název**, **ID předplatného** a **skupinu prostředků** na stránce **vlastnosti** .

   ![Stránka vlastností účtu služby Automation](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Skript PowerShellu pro vytvoření účtu Spustit jako

PowerShellový skript zahrnuje podporu pro několik konfigurací.

* Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem
* Vytvořte účet Spustit jako nebo účet Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem.
* Vytvořte účet Spustit jako nebo účet Spustit jako pro Classic pomocí certifikátu vydaného certifikační autoritou (CA) vaší organizace.
* Vytvořte účet Spustit jako nebo účet Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government.

1. Stáhněte a uložte skript do místní složky pomocí následujícího příkazu.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Spusťte PowerShell se zvýšenými uživatelskými právy a přejděte do složky, která obsahuje skript.

3. Spusťte jeden z následujících příkazů pro vytvoření účtu Spustit jako nebo pro klasický účet Spustit jako v závislosti na vašich požadavcích.

    * Vytvořte účet Spustit jako pomocí certifikátu podepsaného svým držitelem.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí podnikového certifikátu

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Pokud jste vytvořili účet Spustit jako pro Classic s podnikovým veřejným certifikátem (soubor. cer), použijte tento certifikát. Skript vytvoří a uloží ho do složky dočasných souborů v počítači v rámci profilu uživatele, `%USERPROFILE%\AppData\Local\Temp` který jste použili ke spuštění relace PowerShellu. Viz [nahrání certifikátu rozhraní API pro správu do Azure Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Po spuštění skriptu budete vyzváni k ověření pomocí Azure. Přihlaste se pomocí účtu, který je členem role správců předplatného. Pokud vytváříte účet Spustit jako pro Azure Classic, váš účet musí být spolusprávcem předplatného.

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafického obsahu najdete [v tématu vytváření grafických runbooků v Azure Automation](automation-graphical-authoring-intro.md).
* Informace o tom, jak začít se sadami Runbook PowerShellu, najdete v tématu [kurz: vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
* Informace o tom, jak začít s Runbooku Python 3, najdete v tématu [kurz: vytvoření sady Runbook Python 3](learn/automation-tutorial-runbook-textual-python-3.md).
