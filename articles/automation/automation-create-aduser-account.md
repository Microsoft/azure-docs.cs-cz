---
title: Vytvoření účtu uživatele Azure AD
description: Tento článek popisuje postup vytvoření pověření účtu uživatele Azure AD pro runbooky ve službě Azure Automation k ověřování v Azure.
keywords: uživatel azure active directory, správa služby azure, uživatelský účet azure ad
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 43490b8ec2139b5e9f62def614dc67e4274304c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Ověření runbooků pomocí nasazení Azure Classic a Resource Manager
Tento článek popisuje kroky, které musíte provést při konfiguraci uživatelského účtu Azure AD pro runbooky Azure Automation, které běží s modelem nasazení Azure Classic nebo prostředky Azure Resource Manageru. Když toto zůstává podporovanou identitou ověřování pro runbooky na základě Azure Resource Manager, je použít účet spustit v Azure jako doporučenou metodu.       

## <a name="create-a-new-azure-active-directory-user"></a>Vytvoření nového uživatele Azure Active Directory
1. Přihlaste se k portálu Azure jako správce služby pro předplatné Azure, které chcete spravovat.
2. Vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé** > **nového uživatele**.
3. Zadejte podrobnosti pro uživatele, jako je třeba **název** a **uživatelské jméno**.  
4. Poznamenejte si celé jméno uživatele a dočasné heslo.
5. Vyberte **Directory role**.
6. Přiřazení role globální nebo správce s omezeními.
7. Odhlaste se z Azure a pak se přihlaste zpět pomocí účtu, který jste právě vytvořili. Zobrazí se výzva ke změně hesla uživatele.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Vytvoření účtu Automation na webu Azure Portal
V této části provedete následující kroky, abyste na webu Azure Portal vytvořili účet Azure Automation, který budete spolu s runbooky používat ke správě prostředků v režimu Azure Resource Manager.  

1. Přihlaste se k portálu Azure jako správce služby pro předplatné Azure, které chcete spravovat.
2. Vyberte **Účty Automation**.
3. Vyberte **Přidat**.<br><br>![Přidání účtu Automation](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. V okně **Přidat účet Automation** do pole **Název** zadejte název nového účtu Automation.
5. Pokud máte více než jedno předplatné, zadejte předplatné pro nový účet a také novou nebo existující **skupinu prostředků** a **umístění** datového centra Azure.
6. U možnosti **Vytvořit účet Spustit v Azure jako** vyberte **Ano** a klikněte na tlačítko **Vytvořit**.  
   
    > [!NOTE]
    > Pokud se rozhodnete vytvořit účet Spustit jako, a to vybráním možnosti **ne**, zobrazí se upozornění v **přidat účet Automation** okno. Když se účet vytvoří a přiřazené **Přispěvatel** role v rámci předplatného, nemá odpovídající identitu ověřování v rámci adresářové služby a proto žádný přístup k prostředkům ve vaší předplatné. Tím se zabrání všechny runbooky odkazující na tento účet nebudou moct ověřit a provádět úlohy s prostředky Azure Resource Manager.
    > 
    >

    <br>![Přidání upozornění k účtu Automation](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Zatímco Azure vytváří účet Automation, můžete průběh sledovat v nabídce v části **Oznámení**.

Po vytvoření přihlašovacích údajů bude nutné, abyste vytvořili asset přihlašovacích údajů, který přidružíte k účtu Automation pomocí dříve vytvořeného účtu uživatele služby AD. Pamatujte si, že vytvoříte účet Automation a není přidružená k identitě ověřování. Proveďte kroky popsané v [článku Assety přihlašovacích údajů v Azure Automation](automation-credentials.md#creating-a-new-credential-asset) a zadejte hodnotu **uživatelského jména** ve formátu **doména\uživatel**.

## <a name="use-the-credential-in-a-runbook"></a>Použití přihlašovacích údajů v runbooku
Přihlašovací údaje v runbooku můžete získat pomocí aktivity [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) a použít je s [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) k připojení k vašemu předplatnému Azure. Pokud přihlašovací údaje patří správci několika předplatných Azure, potom byste měli použít také [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) a určit to správné. To je znázorněno v následující ukázce prostředí PowerShell, který obvykle se zobrazí v horní části většiny runbooků služeb automatizace Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Opakujte tyto řádky po všech [kontrolní body](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) v sadě runbook. Pokud runbook pozastavený a potom obnoví u dalšího pracovního procesu, je potřeba znovu provést ověření.

## <a name="next-steps"></a>Další kroky
* O různých typech runbooků a krocích k vytvoření vlastních runbooků si můžete přečíst v článku [Typy runbooků v Azure Automation](automation-runbook-types.md)

