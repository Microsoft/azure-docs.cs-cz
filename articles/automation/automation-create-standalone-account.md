---
title: Vytvoření samostatného účtu Azure Automation
description: V tomto článku se dozvíte, jak vytvořit samostatný účet Azure Automation a účet Spustit jako pro Azure Classic.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: c78a65c3cc2d913ba0b836947d2184b3a061a648
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714651"
---
# <a name="create-a-standalone-azure-automation-account"></a>Vytvoření samostatného účtu Azure Automation

V tomto článku se dozvíte, jak vytvořit účet Azure Automation v Azure Portal. Účet automatizace portálu můžete použít k vyhodnocení a získání informací o automatizaci bez použití dalších funkcí správy nebo integraci s protokoly Azure Monitor. Můžete přidat funkce pro správu nebo je integrovat s protokoly Azure Monitor pro pokročilé monitorování úloh runbooků v jakémkoli okamžiku v budoucnu.

Pomocí účtu Automation můžete ověřovat Runbooky pomocí správy prostředků v Azure Resource Manager nebo modelu nasazení Classic. Jeden účet Automation může spravovat prostředky napříč všemi oblastmi a předplatnými daného tenanta.

Když v Azure Portal vytvoříte účet Automation, automaticky se vytvoří účet **Spustit jako** . Tento účet provádí následující úlohy:

* Vytvoří instanční objekt ve službě Azure Active Directory (Azure AD).
* Vytvoří certifikát.
* Přiřadí roli přispěvatele, která spravuje Azure Resource Manager prostředky pomocí runbooků.

S tímto účtem vytvořeným pro vás můžete rychle začít sestavovat a nasazovat Runbooky pro podporu vašich potřeb automatizace.

## <a name="permissions-required-to-create-an-automation-account"></a>Oprávnění požadovaná k vytvoření účtu Automation

Pokud chcete vytvořit nebo aktualizovat účet Automation a dokončit úkoly popsané v tomto článku, musíte mít následující oprávnění a oprávnění:

* Pokud chcete vytvořit účet Automation, musíte mít uživatelský účet Azure AD přidaný do role s oprávněním ekvivalentním roli vlastníka pro `Microsoft.Automation` prostředky. Další informace najdete v tématu [Access Control na základě rolí v Azure Automation](automation-role-based-access-control.md).
* V Azure Portal v části **Azure Active Directory**  >  **Správa**  >  **uživatelských nastavení**, pokud je **Registrace aplikací** nastavená na **Ano**, uživatelé bez oprávnění správce v tenantovi Azure AD můžou [Registrovat aplikace Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Pokud je **Registrace aplikací** nastavená na **ne**, musí mít uživatel, který tuto akci provede, aspoň roli vývojáře aplikace v Azure AD.

Pokud před přidáním do role globálního správce nebo správce předplatného nejste členem instance Active Directory předplatného, přidáte do služby Active Directory jako host. V tomto scénáři se tato zpráva zobrazí v podokně Přidat účet Automation: `You do not have permissions to create.`

Pokud se nejdřív do role globálního správce nebo spolusprávce přidá uživatel, můžete tohoto uživatele odebrat z instance Active Directory předplatného. Uživatele můžete číst do role uživatele ve službě Active Directory. Ověření rolí uživatele:

1. V Azure Portal přejdete do podokna Azure Active Directory.
1. Vyberte **Uživatelé a skupiny**.
1. Vyberte **Všichni uživatelé**.
1. Po výběru konkrétního uživatele vyberte **profil**. Hodnota atributu **Type uživatele** v profilu uživatele by neměla být **Host**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Vytvoření nového účtu Automation v Azure Portal

Chcete-li vytvořit účet Azure Automation v Azure Portal, proveďte následující kroky:

1. Přihlaste se k Azure Portal pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
1. Vyberte **+ vytvořit prostředek**.
1. Vyhledejte **Automation**. Ve výsledcích hledání vyberte možnost **Automatizace**.

   ![Hledání a výběr ovládacího prvku automatizace & v Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na další obrazovce vyberte **vytvořit novou**.

   ![Přidat účet Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Pokud se v podokně Přidat účet Automation zobrazí následující zpráva, váš účet není členem role správců předplatného a spolusprávcem předplatného.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-without-perms.png" alt-text="Snímek obrazovky s výzvou nemáte oprávnění k vytvoření účtu Spustit jako v Azure Active Directory.":::

1. V podokně Přidat účet Automation zadejte název nového účtu Automation do pole **název** . Po zvolení tohoto názvu už tento název nemůžete změnit. 

    > [!NOTE]
    > Názvy účtů Automation jsou jedinečné pro jednotlivé oblasti a skupiny prostředků. Názvy pro odstraněné účty Automation nemusí být okamžitě k dispozici.

1. Pokud máte více než jedno předplatné, zadejte předplatné, které chcete použít pro nový účet, pomocí pole **předplatné** .
1. V případě **skupiny prostředků**zadejte nebo vyberte novou nebo existující skupinu prostředků.
1. Jako **umístění**vyberte umístění datacentra Azure.
1. U možnosti **vytvořit účet Spustit v Azure jako** vyberte **Ano** a pak klikněte na **vytvořit**.

   > [!NOTE]
   > Pokud se rozhodnete nevytvořit účet Spustit jako, vyberte pro **Vytvoření účtu spustit v Azure jako**možnost **ne** a v podokně Přidat účet Automation se zobrazí zpráva. I když je účet vytvořen v Azure Portal, nemá účet odpovídající identitu ověřování v rámci předplatného modelu nasazení Classic nebo v adresářové službě Azure Resource Manager předplatného. Proto účet Automation nemá přístup k prostředkům ve vašem předplatném. To brání tomu, aby Runbooky, které odkazují na tento účet, mohly ověřovat a provádět úlohy s prostředky v těchto modelech nasazení.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-decline-create-runas-msg.png" alt-text="Snímek obrazovky s výzvou nemáte oprávnění k vytvoření účtu Spustit jako v Azure Active Directory.":::
   >
   > Pokud objekt služby není vytvořen, role přispěvatele není přiřazena.
   >

1. Pokud chcete sledovat průběh vytváření účtu Automation, vyberte v nabídce možnost **oznámení** .

Po úspěšném vytvoření účtu Automation se pro vaší potřebu automaticky vytvoří několik prostředků. Po vytvoření můžete tyto Runbooky bezpečně odstranit, pokud je nechcete zachovat. Účty Spustit jako lze použít k ověření vašeho účtu v sadě Runbook a měli byste ji ponechat, pokud nevytvoříte jinou nebo ji nepotřebujete. Následující tabulka shrnuje prostředky pro účet Spustit jako.

| Prostředek | Popis |
| --- | --- |
| Runbook AzureAutomationTutorial |Příklad grafického Runbooku, který ukazuje, jak ověřit pomocí účtu Spustit jako. Sada Runbook získá všechny prostředky Správce prostředků. |
| Runbook AzureAutomationTutorialScript |Ukázkový Runbook PowerShellu, který ukazuje, jak ověřit pomocí účtu Spustit jako. Sada Runbook získá všechny prostředky Správce prostředků. |
| Runbook AzureAutomationTutorialPython2 |Příklad Runbooku v Pythonu, který ukazuje, jak ověřit pomocí účtu Spustit jako. Sada Runbook obsahuje seznam všech skupin prostředků přítomných v rámci předplatného. |
| AzureRunAsCertificate |Asset certifikátu, který se automaticky vytvoří při vytvoření účtu Automation, nebo pomocí skriptu PowerShellu pro existující účet. Certifikát se ověřuje pomocí Azure, abyste mohli spravovat Azure Resource Manager prostředky z runbooků. Tento certifikát má životnost jeden rok. |
| AzureRunAsConnection |Asset připojení, který se automaticky vytvoří při vytvoření účtu Automation, nebo pomocí skriptu PowerShellu pro existující účet. |

## <a name="create-a-classic-run-as-account"></a>Vytvoření účtu Spustit jako pro Azure Classic

Účty Spustit jako pro Classic se už ve výchozím nastavení nevytváří, když vytváříte účet Azure Automation. Pokud stále potřebujete účet Spustit jako pro Classic:

1. Z účtu Automation vyberte **účty Spustit jako** v části **Nastavení účtu**.
2. Vyberte **účet Spustit jako pro Azure Classic**.
3. Kliknutím na **vytvořit** pokračujte v vytváření účtu Spustit jako pro Azure Classic.

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafického obsahu najdete [v tématu vytváření grafických runbooků v Azure Automation](automation-graphical-authoring-intro.md).
* Informace o tom, jak začít se sadami Runbook PowerShellu, najdete v tématu [kurz: vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
* Pokud chcete začít s Runbooky pracovních postupů PowerShellu, přečtěte si [kurz: vytvoření Runbooku pracovního postupu PowerShellu](learn/automation-tutorial-runbook-textual.md).
* Chcete-li začít s Runbooky Python 2, přečtěte si [kurz: vytvoření Runbooku Python 2](learn/automation-tutorial-runbook-textual-python2.md).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0&preserve-view=true#automation).
