---
title: Vytvoření samostatného účtu Azure Automation
description: Tento článek vás provede kroky vytváření, testování a používání ověření objektu příklad zabezpečení ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: acd1ea95f4ef2717e315d9a247cd2fed54a02bf3
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Vytvoření samostatného účtu Azure Automation

Tento článek ukazuje, jak vytvořit účet Azure Automation na portálu Azure. Portál účtu Automation můžete použít k vyhodnocení a další informace o automatizaci bez integrace nebo další správu řešení pomocí Azure Log Analytics. Můžete přidat těchto řešení pro správu nebo integrovat analýzy protokolů pro pokročilé monitorování úlohy sady runbook v libovolném bodě v budoucnu.

Pomocí účtu Automation můžete ověřovat sady runbook ve správě prostředků v Azure Resource Manager nebo modelu nasazení classic.

Když vytvoříte účet Automation na portálu Azure, se automaticky vytvoří tyto účty:

* **Účet Spustit jako**. Tento účet provede následující úlohy:
  * Vytvoří objekt služby v Azure Active Directory (Azure AD).
  * Vytvoří certifikát.
  * Přiřadí Contributor Role-Based řízení přístupu (RBAC), která spravuje prostředky Azure Resource Manageru pomocí sad runbook.
* **Classic účet Spustit jako**. Tento účet odešle certifikát pro správu. Certifikát spravuje klasické prostředky pomocí sady runbook.

S těmito účty vytvořené pro vás můžete rychle začít vytvářet a nasazovat runbooky na podporu vašich automatizačních potřeb.

## <a name="permissions-required-to-create-an-automation-account"></a>Oprávnění potřebná k vytvoření účtu Automation

Vytvořit nebo aktualizovat účet automatizace a splnit úkoly popsané v tomto článku, musíte mít následující oprávnění a oprávnění:

* Pokud chcete vytvořit účet Automation, musí váš uživatelský účet služby Azure AD přidat do role s oprávnění na roli vlastníka pro **společnosti Microsoft. Automatizace** prostředky. Další informace najdete v tématu [řízení přístupu na základě rolí ve službě Azure Automation](automation-role-based-access-control.md).
* Na portálu Azure v části **Azure Active Directory** > **SPRAVOVAT** > **registrace aplikace**, pokud **registrace aplikace**  je nastaven na **Ano**, mohou uživatelé bez oprávnění správce v klientovi služby Azure AD [registraci aplikace služby Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Pokud **registrace aplikace** je nastaven na **ne**, uživatele, který provádí tato akce musí být globálním správcem ve službě Azure AD.

Pokud nejste členem odběru instanci Active Directory předtím, než jsou přidány do role Globální správce nebo spolusprávce odběru, jsou přidány do služby Active Directory jako Host. V tomto scénáři se zobrazí tato zpráva na **přidat účet Automation** stránky: "Nemáte oprávnění k vytvoření."

Pokud je uživatel přidaný k roli globálního správce nebo spolusprávce nejprve můžete můžete je odebrat z instance odběru služby Active Directory a potom je znovu přidat na úplné role uživatele ve službě Active Directory.

Ověření role uživatele:

1. V portálu Azure přejděte do **Azure Active Directory** podokně.
1. Vyberte **uživatelů a skupin**.
1. Vyberte **všichni uživatelé**.
1. Po výběru konkrétního uživatele, vyberte **profil**. Hodnota **typ uživatele** atribut v profilu uživatele by neměl být **hosta**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Vytvořit nový účet Automation na portálu Azure

Pokud chcete vytvořit účet Azure Automation na portálu Azure, proveďte následující kroky:

1. Přihlaste se k portálu Azure pomocí účtu, který je členem role Správci předplatného a spolusprávce předplatného.
1. Vyberte **+ vytvořit prostředek**.
1. Vyhledejte **automatizace**. Ve výsledcích hledání vyberte **automatizace**.

   ![Vyhledejte a vyberte ovládací prvek a Automation v Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na další obrazovce vyberte **vytvořit**.
  ![Přidání účtu Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Pokud se zobrazí následující zprávu ve **přidat účet Automation** podokno, váš účet není členem role Správci předplatného a spolusprávce předplatného.
  >
  > ![Přidání upozornění účtu Automation.](media/automation-create-standalone-account/create-account-without-perms.png)
  >
1. V **přidat účet Automation** podokně, v **název** pole, zadejte název nového účtu Automation.
1. Pokud máte ve více než jedno předplatné, **předplatné** zadejte předplatné, které chcete použít pro nový účet.
1. Pro **skupiny prostředků**, zadejte nebo vyberte skupinu nový nebo existující prostředek.
1. Pro **umístění**, vyberte umístění datového centra Azure.
1. Pro **vytvořit Azure účet Spustit jako** možnost, ujistěte se, že **Ano** vybrané, a pak vyberte možnost **vytvořit**.

  > [!NOTE]
  > Pokud se rozhodnete vytvořit účet Spustit jako výběrem **ne** pro **vytvořit Azure účet Spustit jako**, zobrazí se zpráva v **přidat účet Automation** podokně. I když je účet vytvořený na portálu Azure, účet nemá odpovídající identitu ověřování v rámci vašeho předplatného modelu nasazení classic nebo v adresářové službě předplatné Azure Resource Manager. Účet Automation proto nemá přístup k prostředkům ve vašem předplatném. Tím se zabrání jakékoli sady runbook, které odkazují na tento účet nebudou moct ověřit a provádět úlohy s prostředky v těchto modelech nasazení.
  >
  > ![Přidání upozornění účtu Automation.](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Po vytvoření objektu služby není není přiřazena role Přispěvatel.
  >

1. Chcete-li sledovat průběh vytváření účtu Automation, v nabídce vyberte **oznámení**.

### <a name="resources-included"></a>Zahrnuté prostředky

Po úspěšném vytvoření účtu Automation se pro vaší potřebu automaticky vytvoří několik prostředků. Po vytvoření tyto sady runbook lze bezpečně odstranit, pokud nechcete, aby se uložily. Účty spustit jako, můžete použít k ověřování ke svému účtu v sadě runbook a by mělo být ponecháno, není-li vytvořit jinou nebo je nevyžadují. Následující tabulka shrnuje prostředky pro účet Spustit jako.

| Prostředek | Popis |
| --- | --- |
| Runbook AzureAutomationTutorial |Ukázkový grafický runbook, který ukazuje, jak ověřit pomocí účtu spustit jako. Sada runbook získá všechny prostředky Resource Manager. |
| Runbook AzureAutomationTutorialScript |Ukázkový runbook prostředí PowerShell, který ukazuje, jak ověřit pomocí účtu spustit jako. Sada runbook získá všechny prostředky Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Ukázkový runbook Python, který ukazuje, jak ověřit pomocí účtu spustit jako. Sada runbook obsahuje seznam všechny skupiny prostředků v předplatném existuje. |
| AzureRunAsCertificate |Asset certifikátu, který se automaticky vytvoří při vytvoření účtu Automation, nebo pomocí skriptu prostředí PowerShell pro existující účet. Tento certifikát ověřuje s Azure lze tedy spravovat prostředky Azure Resource Manager ze sady runbook. Tento certifikát má životnost jeden rok. |
| AzureRunAsConnection |Asset připojení, který se automaticky vytvoří při vytvoření účtu Automation, nebo pomocí skriptu prostředí PowerShell pro existující účet. |

Následující tabulka shrnuje prostředky pro účet Spustit jako pro Azure Classic.

| Prostředek | Popis |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Příklad grafický runbook. Sada runbook získá všechny klasické virtuální počítače v předplatném pomocí klasického účet Spustit jako (certifikátu). Potom zobrazí názvy virtuálních počítačů a stav. |
| Runbook se skriptem AzureClassicAutomationTutorial |Ukázkový runbook prostředí PowerShell. Sada runbook získá všechny klasické virtuální počítače v předplatném pomocí klasického účet Spustit jako (certifikátu). Potom zobrazí názvy virtuálních počítačů a stav. |
| AzureClassicRunAsCertificate |Asset certifikátu, který se automaticky vytvoří. Tento certifikát ověřuje s Azure lze tedy spravovat prostředky Azure classic ze sady runbook. Tento certifikát má životnost jeden rok. |
| AzureClassicRunAsConnection |Asset připojení, který se automaticky vytvoří. Asset ověřuje s Azure, lze tedy spravovat prostředky Azure classic ze sady runbook. |

## <a name="next-steps"></a>Další postup

* Další informace o vytváření grafického obsahu najdete v tématu [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* První kroky s runbooky Python2 najdete v článku [Můj první runbook Python2](automation-first-runbook-textual-python2.md).
