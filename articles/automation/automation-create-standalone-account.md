---
title: Vytvoření samostatného účtu Azure Automation
description: Tento článek vás provede kroky vytváření, testování a použití ukázkové ověřování primární zabezpečení v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 72a40363edf0e83eea26ee697ce992226da0db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392288"
---
# <a name="create-a-standalone-azure-automation-account"></a>Vytvoření samostatného účtu Azure Automation

Tento článek ukazuje, jak vytvořit účet Azure Automation na webu Azure Portal. Účet automatizace portálu můžete použít k vyhodnocení a sešlení informací o automatizaci bez použití dalších řešení pro správu nebo integrace s protokoly Azure Monitoru. Tato řešení pro správu můžete přidat nebo integrovat s protokoly Azure Monitor pro pokročilé monitorování úloh sady Runbook kdykoli v budoucnu.

Pomocí účtu Automation můžete ověřovat sady Runbook správou prostředků ve Správci prostředků Azure nebo v klasickém modelu nasazení. Jeden účet Automation může spravovat prostředky napříč všemi oblastmi a předplatnými daného tenanta.

Když vytvoříte účet Automation na webu Azure Portal, automaticky se vytvoří účet **Spustit jako.** Tento účet provádí následující úkoly:

* Vytvoří instanční objekt ve službě Azure Active Directory (Azure AD).
* Vytvoří certifikát.
* Přiřadí řízení přístupu na základě rolí přispěvatele (RBAC), které spravuje prostředky Azure Resource Manager pomocí runbooků.

S tímto účtem vytvořeným pro vás můžete rychle začít vytvářet a nasazovat runbooky pro podporu vašich potřeb automatizace.

## <a name="permissions-required-to-create-an-automation-account"></a>Oprávnění potřebná k vytvoření účtu automatizace

Chcete-li vytvořit nebo aktualizovat účet automatizace a dokončit úkoly popsané v tomto článku, musíte mít následující oprávnění a oprávnění:

* Chcete-li vytvořit účet automatizace, musí být váš uživatelský účet Azure AD `Microsoft.Automation` přidán do role s oprávněními ekvivalentními roli vlastníka pro prostředky. Další informace najdete [v tématu Řízení přístupu na základě rolí v Azure Automation](automation-role-based-access-control.md).
* Na webu Azure Portal můžete v části **Azure Active Directory** > **MANAGE** > **User settings**, pokud je registrace aplikací nastavené na **Ano**, mohou uživatelé bez oprávnění správce ve vašem tenantovi Azure AD [zaregistrovat aplikace služby Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). **App registrations** Pokud **je registrace aplikací** nastavena na **ne**, uživatel, který provádí tuto akci, musí být globálním správcem ve službě Azure AD.

Pokud nejste členem instance služby Active Directory předplatného před přidáním do role globálního správce/spolusprávce předplatného, budete přidáni do služby Active Directory jako host. V tomto scénáři se zobrazí tato zpráva v podokně Přidat účet automatizace:`You do not have permissions to create.`

Pokud je uživatel přidán nejprve do role globální správce/správce, můžete jej odebrat z instance služby Active Directory předplatného. Uživatele můžete přečíst do role Uživatel ve službě Active Directory.

Ověření uživatelských rolí:

1. Na webu Azure Portal přejděte do podokna Azure Active Directory.
1. Vyberte **Možnost Uživatelé a skupiny**.
1. Vyberte **možnost Všichni uživatelé**.
1. Po výběru konkrétního uživatele vyberte **profil**. Hodnota atributu **User type** pod profilem uživatele by neměla být **Guest**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Vytvoření nového účtu Automation na webu Azure Portal

Pokud chcete na webu Azure Portal vytvořit účet Azure Automation, postupujte takto:

1. Přihlaste se k portálu Azure pomocí účtu, který je členem role Správci předplatného a spolusprávcem předplatného.
1. Vyberte **+ Vytvořit zdroj**.
1. Vyhledejte **automatizaci**. Ve výsledcích hledání vyberte **automatizace**.

   ![Hledání a výběr automatizace & řízení na Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na další obrazovce vyberte **Vytvořit nový**.

   ![Přidat účet automatizace](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Pokud se v podokně Přidat účet automatizace zobrazí následující zpráva, váš účet není členem role Správci předplatného a spolusprávcem předplatného.
   >
   > ![Upozornění na účet automatizace](media/automation-create-standalone-account/create-account-without-perms.png)

1. V podokně Přidat účet automatizace zadejte název nového účtu Automation do pole **Název.** Po výběru nemůžete tento název změnit. 

    > [!NOTE]
    > Názvy účtů automatizace jsou jedinečné pro oblast a skupinu prostředků. Názvy odstraněných účtů automatizace nemusí být okamžitě k dispozici.

1. Pokud máte více než jedno předplatné, použijte pole **Předplatné** k určení předplatného, které se má použít pro nový účet.
1. Do **skupiny prostředků**zadejte nebo vyberte novou nebo existující skupinu prostředků.
1. V **aplikaci Umístění**vyberte umístění datového centra Azure.
1. U možnosti **Vytvořit účet Azure Run As** zkontrolujte, že je vybraná možnost **Ano,** a klikněte na **vytvořit**.

   > [!NOTE]
   > Pokud se rozhodnete nevytvářet účet Spustit jako výběrem možnosti **Ne** pro **vytvoření účtu Azure Run As**, zobrazí se v podokně Přidat účet automatizace zpráva. I když se účet vytvoří na webu Azure Portal, účet nemá odpovídající ověřovací identitu v rámci předplatného klasického modelu nasazení nebo v adresářové službě předplatného Azure Resource Manager. Proto účet automatizace nemá přístup k prostředkům ve vašem předplatném. Tím zabráníte všem runbookům, které odkazují na tento účet, možnost ověření a provádění úloh proti prostředkům v těchto modelech nasazení.
   >
   > ![Upozornění na účet automatizace](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Pokud není vytvořen instanční objekt, role přispěvatele není přiřazena.
   >

1. Chcete-li sledovat průběh vytváření účtu automatizace, vyberte **oznámení** v nabídce.

### <a name="resources-included"></a>Zahrnuté prostředky

Po úspěšném vytvoření účtu Automation se pro vaší potřebu automaticky vytvoří několik prostředků. Po vytvoření lze tyto sady Runbook bezpečně odstranit, pokud si nepřejete, aby byly uloženy. Spustit jako účty, lze použít k ověření na váš účet v runbooku a by měla být ponechána, pokud nevytvoříte jiný nebo je nevyžadují. Následující tabulka shrnuje prostředky pro účet Spustit jako.

| Prostředek | Popis |
| --- | --- |
| Runbook AzureAutomationTutorial |Ukázkový grafický runbook, který ukazuje, jak ověřit pomocí účtu Spustit jako. Runbook získá všechny prostředky Správce prostředků. |
| Runbook AzureAutomationTutorialScript |Příklad runbook prostředí PowerShell, který ukazuje, jak ověřit pomocí spustit jako účet. Runbook získá všechny prostředky Správce prostředků. |
| Runbook AzureAutomationTutorialPython2 |Příklad runbook pythonu, který ukazuje, jak ověřit pomocí spustit jako účet. V souboru Runbook jsou uvedeny všechny skupiny prostředků, které jsou v předplatném přítomny. |
| AzureRunAsCertificate |Datový zdroj certifikátu, který se automaticky vytvoří při vytvoření účtu Automation nebo pomocí skriptu prostředí PowerShell pro existující účet. Certifikát se ověřuje pomocí Azure, takže můžete spravovat prostředky Azure Resource Manager u runbooků. Tento certifikát má životnost jeden rok. |
| AzureRunAsConnection |Prostředek připojení, který se automaticky vytvoří při vytvoření účtu Automation nebo pomocí skriptu prostředí PowerShell pro existující účet. |

## <a name="create-a-classic-run-as-account"></a>Vytvoření účtu Klasické spuštění jako

Klasické spustit jako účty se už nevytvářejí ve výchozím nastavení při vytváření účtu Azure Automation. Pokud stále potřebujete účet Classic Run As:

1. V části Nastavení **účtu**automation vyberte možnost **Spustit jako účty** .
2. Vyberte **Azure Classic Run as Account**.
3. Chcete-li pokračovat v vytváření účtu Classic Run As, klepněte na **tlačítko Vytvořit.**

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafických funkcí najdete [v tématu Vytváření grafikva v Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* Pokud chcete začít s runbooky pracovních postupů Prostředí PowerShell, [přečtěte si první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Informace o tom, jak začít s runbooky pythonu 2, najdete [v tématu Moje první runbook pythonu2](automation-first-runbook-textual-python2.md).
* Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
