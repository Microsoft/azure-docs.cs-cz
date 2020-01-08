---
title: Vytvoření samostatného účtu Azure Automation
description: Tento článek vás provede jednotlivými kroky při vytváření, testování a používání ukázkového ověřování objektu zabezpečení v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 22efb5e94049b975780c6f6ea69aa94a71cc9992
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366647"
---
# <a name="create-a-standalone-azure-automation-account"></a>Vytvoření samostatného účtu Azure Automation

V tomto článku se dozvíte, jak vytvořit účet Azure Automation v Azure Portal. Účet automatizace portálu můžete použít k vyhodnocení a získání informací o automatizaci bez použití dalších řešení pro správu nebo integrace s protokoly Azure Monitor. Tato řešení pro správu můžete přidat nebo integrovat s protokoly Azure Monitor pro pokročilé monitorování úloh runbooků v jakémkoli okamžiku v budoucnu.

Pomocí účtu Automation můžete ověřovat Runbooky pomocí správy prostředků v Azure Resource Manager nebo modelu nasazení Classic. Jeden účet Automation může spravovat prostředky napříč všemi oblastmi a předplatnými daného tenanta.

Když v Azure Portal vytvoříte účet Automation, automaticky se vytvoří tyto účty:

* **Účet Spustit jako**. Tento účet provádí následující úlohy:
  * Vytvoří instanční objekt ve službě Azure Active Directory (Azure AD).
  * Vytvoří certifikát.
  * Přiřadí Access Control na základě rolí přispěvatele (RBAC), která spravuje prostředky Azure Resource Manager pomocí runbooků.

S těmito účty vytvořenými pro vás můžete rychle začít sestavovat a nasazovat Runbooky pro podporu vašich potřeb automatizace.

## <a name="permissions-required-to-create-an-automation-account"></a>Oprávnění požadovaná k vytvoření účtu Automation

Pokud chcete vytvořit nebo aktualizovat účet Automation a dokončit úkoly popsané v tomto článku, musíte mít následující oprávnění a oprávnění:

* Pokud chcete vytvořit účet Automation, musíte mít uživatelský účet Azure AD přidaný do role s oprávněním ekvivalentním roli vlastníka pro **Microsoft.** Prostředky služby Automation. Další informace najdete v tématu [Access Control na základě rolí v Azure Automation](automation-role-based-access-control.md).
* V Azure Portal v části **Azure Active Directory** > **Spravovat** > **uživatelská nastavení**, pokud je **Registrace aplikací** nastavená na **Ano**, uživatelé, kteří nejsou správci v tenantovi Azure AD, můžou [Registrovat aplikace Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Pokud je **Registrace aplikací** nastaveno na **ne**, uživatel, který tuto akci provede, musí být globální správce v Azure AD.

Pokud před přidáním do role globálního správce nebo správce předplatného nejste členem instance Active Directory předplatného, přidáte do služby Active Directory jako host. V tomto scénáři se na stránce **Přidat účet Automation** zobrazí tato zpráva: nemáte oprávnění k vytvoření. "

Pokud je nejprve uživatel přidán do role Globální správce/spolusprávce, můžete ho odebrat z instance služby Active Directory předplatného a pak je číst do role úplného uživatele ve službě Active Directory.

Ověření rolí uživatele:

1. V Azure Portal přejdete do podokna **Azure Active Directory** .
1. Vyberte **Uživatelé a skupiny**.
1. Vyberte **Všichni uživatelé**.
1. Po výběru konkrétního uživatele vyberte **profil**. Hodnota atributu **Type uživatele** v profilu uživatele by neměla být **Host**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Vytvoření nového účtu Automation v Azure Portal

Chcete-li vytvořit účet Azure Automation v Azure Portal, proveďte následující kroky:

1. Přihlaste se k Azure Portal pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
1. Vyberte **+ vytvořit prostředek**.
1. Vyhledejte **Automation**. Ve výsledcích hledání vyberte možnost **Automatizace**.

   ![Vyhledat a vybrat Automation & Control v Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na další obrazovce vyberte **vytvořit**.

   ![Přidat účet Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Pokud se v podokně **Přidat účet Automation** zobrazí následující zpráva, váš účet není členem role správců předplatného a spolusprávcem předplatného.
   >
   > ![Přidat upozornění účtu Automation](media/automation-create-standalone-account/create-account-without-perms.png)

1. V podokně **Přidat účet Automation** do pole **název** zadejte název nového účtu Automation. Po zvolení tohoto názvu nelze tento název změnit. *Názvy účtů Automation jsou jedinečné pro jednotlivé oblasti a skupiny prostředků. Názvy účtů Automation, které byly odstraněny, nemusí být okamžitě k dispozici.*
1. Pokud máte více než jedno předplatné, zadejte do pole **předplatné** předplatné, které chcete pro nový účet použít.
1. V případě **skupiny prostředků**zadejte nebo vyberte novou nebo existující skupinu prostředků.
1. Jako **umístění**vyberte umístění datacentra Azure.
1. V možnosti **vytvořit účet Spustit v Azure jako** zvolte možnost **Ano** a pak vyberte **vytvořit**.

   > [!NOTE]
   > Pokud se rozhodnete nevytvořit účet Spustit jako, vyberte pro **Vytvoření účtu spustit v Azure jako**možnost **ne** a v podokně **Přidat účet Automation** se zobrazí zpráva. I když je účet vytvořen v Azure Portal, nemá účet odpovídající identitu ověřování v rámci předplatného modelu nasazení Classic nebo v adresářové službě Azure Resource Manager předplatného. Proto účet Automation nemá přístup k prostředkům ve vašem předplatném. To brání tomu, aby Runbooky, které odkazují na tento účet, mohly ověřovat a provádět úlohy s prostředky v těchto modelech nasazení.
   >
   > ![Přidat upozornění účtu Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Pokud objekt služby není vytvořen, role přispěvatele není přiřazena.
   >

1. Chcete-li sledovat průběh vytváření účtu Automation, v nabídce vyberte možnost **oznámení**.

### <a name="resources-included"></a>Zahrnuté prostředky

Po úspěšném vytvoření účtu Automation se pro vaší potřebu automaticky vytvoří několik prostředků. Po vytvoření můžete tyto Runbooky bezpečně odstranit, pokud je nechcete zachovat. Účty Spustit jako lze použít k ověření vašeho účtu v sadě Runbook a měli byste ji ponechat, pokud nevytvoříte jinou nebo ji nepotřebujete. Následující tabulka shrnuje prostředky pro účet Spustit jako.

| Prostředek | Popis |
| --- | --- |
| Runbook AzureAutomationTutorial |Příklad grafického Runbooku, který ukazuje, jak ověřit pomocí účtu Spustit jako. Sada Runbook získá všechny prostředky Správce prostředků. |
| Runbook AzureAutomationTutorialScript |Ukázkový Runbook PowerShellu, který ukazuje, jak ověřit pomocí účtu Spustit jako. Sada Runbook získá všechny prostředky Správce prostředků. |
| Runbook AzureAutomationTutorialPython2 |Příklad Runbooku v Pythonu, který ukazuje, jak ověřit pomocí účtu Spustit jako. Sada Runbook obsahuje seznam všech skupin prostředků přítomných v rámci předplatného. |
| AzureRunAsCertificate |Asset certifikátu, který se automaticky vytvoří při vytvoření účtu Automation, nebo pomocí skriptu PowerShellu pro existující účet. Certifikát se ověřuje pomocí Azure, abyste mohli spravovat Azure Resource Manager prostředky z runbooků. Tento certifikát má životnost jeden rok. |
| AzureRunAsConnection |Asset připojení, který se automaticky vytvoří při vytvoření účtu Automation, nebo pomocí skriptu PowerShellu pro existující účet. |

## <a name="classic-run-as-accounts"></a>Účty Spustit jako pro Azure Classic

V případě, že vytvoříte účet Azure Automation, ve výchozím nastavení se už nevytváří účty Spustit jako pro Classic. Pokud stále potřebujete účet Spustit jako pro Classic, proveďte následující kroky.

1. Na stránce **účet Automation** vyberte **účty Spustit jako** v části **Nastavení účtu**.
2. Vyberte **účet Spustit jako pro Azure Classic**.
3. Kliknutím na **vytvořit** pokračujte v vytváření účtu Spustit jako pro Azure Classic.

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafického obsahu najdete v tématu věnovaném [vytváření grafik v Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* První kroky s runbooky Python2 najdete v článku [Můj první runbook Python2](automation-first-runbook-textual-python2.md).

