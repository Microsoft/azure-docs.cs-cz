---
title: Vytvoření samostatného účtu Azure Automation
description: Tento článek vás provede kroky pro vytváření, testování a používání ověřování instančních objektů příklad zabezpečení ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 02ca9065107b999fa0182c89ae2b9d8ca0c8607a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362870"
---
# <a name="create-a-standalone-azure-automation-account"></a>Vytvoření samostatného účtu Azure Automation

V tomto článku se dozvíte, jak vytvořit účet Azure Automation na webu Azure Portal. Na portálu účtu Automation můžete použít k vyhodnocení a další informace o automatizaci bez použití dalších řešení pro správu nebo integrace s Azure Log Analytics. Můžete přidat tato řešení pro správu nebo integraci s Log Analytics pro pokročilé monitorování úloh runbooků v libovolném okamžiku v budoucnu.

Pomocí účtu Automation můžete ověření runbooků pomocí správy prostředků v Azure Resource Manageru nebo v modelu nasazení classic.

Při vytváření účtu Automation na webu Azure Portal, se automaticky vytvoří tyto účty:

* **Účet Spustit jako**. Tento účet neobsahuje následující úkoly:
  * Vytvoří instanční objekt v Azure Active Directory (Azure AD).
  * Vytvoří certifikát.
  * Přiřadí Contributor Role-Based řízení přístupu (RBAC), která spravuje prostředky Azure Resource Manageru pomocí runbooků.
* **Účet Spustit jako pro Classic**. Tento účet nahraje certifikát pro správu. Certifikát spravuje klasických prostředků pomocí runbooků.

Tyto účty vytvořené pro vás rychle začít vytvářet a nasazovat runbooky na podporu vašich automatizačních potřeb.

## <a name="permissions-required-to-create-an-automation-account"></a>Oprávnění požadovaná k vytvoření účtu Automation

Vytvořit nebo aktualizovat účet Automation a splnit úkoly popsané v tomto článku, musí mít následující oprávnění a oprávnění:

* Chcete-li vytvořit účet Automation, musí váš uživatelský účet Azure AD přidají do role s oprávněním role vlastníka pro **Microsoft. Automatizace** prostředky. Další informace najdete v tématu [řízení přístupu na základě rolí ve službě Azure Automation](automation-role-based-access-control.md).
* Na webu Azure Portal v části **Azure Active Directory** > **SPRAVOVAT** > **registrace aplikací**, pokud **registrace aplikací**  je nastavena na **Ano**, můžou uživatelé bez oprávnění správce ve vašem tenantovi Azure AD [registrovat aplikace služby Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Pokud **registrace aplikací** je nastavena na **ne**, uživatel, který provádí tuto akci musíte být globálním správcem ve službě Azure AD.

Pokud nejste členem instance Active Directory předplatného před přidáním k roli globálního správce nebo spolusprávce předplatného, jsou přidány do služby Active Directory jako Host. V tomto scénáři se zobrazí tato zpráva na **přidat účet Automation** stránky: "Nemáte oprávnění k vytvoření."

Pokud uživatel je přidán do role globálního správce nebo spolusprávce poprvé, můžete ho odebrat z instance Active Directory předplatného a potom je znovu přidat do úplné role uživatele ve službě Active Directory.

Ověření role uživatele:

1. Na webu Azure Portal, přejděte **Azure Active Directory** podokně.
1. Vyberte **Uživatelé a skupiny**.
1. Vyberte **všichni uživatelé**.
1. Po výběru konkrétního uživatele vyberte **profilu**. Hodnota **typ uživatele** atribut v rámci profilu uživatele by neměla být **hosta**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Vytvořit nový účet Automation na webu Azure Portal

Chcete-li vytvořit účet Azure Automation na webu Azure Portal, proveďte následující kroky:

1. Přihlaste se k webu Azure portal pomocí účtu, který je členem role správců předplatného a spolusprávce předplatného.
1. Vyberte **+ vytvořit prostředek**.
1. Vyhledejte **automatizace**. Ve výsledcích hledání vyberte **automatizace**.

   ![Vyhledání a výběr Automation and Control na webu Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na další obrazovce vyberte **vytvořit**.

  ![Přidat účet služby Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Pokud se zobrazí následující zpráva **přidat účet Automation** podokno, váš účet není členem role správců předplatného a spolusprávce předplatného.
  >
  > ![Přidání upozornění pro účet služby Automation](media/automation-create-standalone-account/create-account-without-perms.png)

1. V **přidat účet Automation** podokno v **název** pole, zadejte název nového účtu Automation. Tento název nedá změnit, jakmile je vybrán. *Názvy účtů Automation jsou na oblast a skupinu prostředků jedinečné.*
1. Pokud máte více než jedno předplatné, v **předplatné** , určete předplatné, které chcete použít pro nový účet.
1. Pro **skupiny prostředků**, zadejte nebo vyberte skupinu pro nový nebo existující prostředek.
1. Pro **umístění**, vyberte umístění datového centra Azure.
1. Pro **vytvoření účtu Azure spustit jako** možnost, ujistěte se, že **Ano** je vybrané a pak vyberte **vytvořit**.

  > [!NOTE]
  > Pokud se rozhodnete vytvořit účet Spustit jako výběrem **č** pro **vytvoření účtu Azure spustit jako**, zobrazí se zpráva v **přidat účet Automation** podokně. I když je účet vytvořený na webu Azure Portal, nemá účet odpovídající identitu ověřování ve vašem předplatném modelu nasazení classic nebo v adresářové službě předplatného Azure Resource Manageru. Účet Automation proto nemá přístup k prostředkům ve vašem předplatném. To zabraňuje všechny runbooky odkazující na tento účet tomu nebudou moct ověřit a provádět úlohy s prostředky v těchto modelech nasazení.
  >
  > ![Přidání upozornění pro účet služby Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Pokud nevytvoříte objekt služby, nebude přiřazená role přispěvatele.
  >

1. Chcete-li sledovat průběh vytváření účtu Automation, v nabídce vyberte **oznámení**.

### <a name="resources-included"></a>Zahrnuté prostředky

Po úspěšném vytvoření účtu Automation se pro vaší potřebu automaticky vytvoří několik prostředků. Po vytvoření tyto sady runbook lze bezpečně odstranit, pokud nechcete, aby se. Účty spustit jako, můžete použít k ověření ke svému účtu v sadě runbook a by měla zůstat, není-li vytvořit jinou nebo je nevyžadují. Následující tabulka shrnuje prostředky pro účet Spustit jako.

| Prostředek | Popis |
| --- | --- |
| Runbook AzureAutomationTutorial |Ukázkový grafický runbook, který předvádí ověření pomocí účtu spustit jako. Sada runbook získává všechny prostředku Resource Manageru. |
| Runbook AzureAutomationTutorialScript |Ukázkový runbook Powershellu, který předvádí ověření pomocí účtu spustit jako. Sada runbook získává všechny prostředku Resource Manageru. |
| Runbook AzureAutomationTutorialPython2 |Ukázkový runbook Python, který předvádí ověření pomocí účtu spustit jako. Sada runbook obsahuje seznam všech skupin prostředků v předplatném. |
| AzureRunAsCertificate |Asset certifikátu vytvořený automaticky při vytvoření účtu Automation, nebo pomocí skriptu prostředí PowerShell pro existující účet. Certifikát se ověřuje pomocí Azure tak můžete spravovat prostředky Azure Resource Manageru pomocí runbooků. Tento certifikát má životnost jeden rok. |
| AzureRunAsConnection |Asset připojení vytvořený automaticky při vytvoření účtu Automation, nebo pomocí skriptu prostředí PowerShell pro existující účet. |

Následující tabulka shrnuje prostředky pro účet Spustit jako pro Azure Classic.

| Prostředek | Popis |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Ukázkový grafický runbook. Sada runbook získá všechny klasické virtuální počítače v rámci předplatného pomocí spustit jako účtu pro Azure Classic (certifikát). Potom zobrazí názvy virtuálních počítačů a stav. |
| Runbook se skriptem AzureClassicAutomationTutorial |Ukázkový runbook Powershellu. Sada runbook získá všechny klasické virtuální počítače v rámci předplatného pomocí spustit jako účtu pro Azure Classic (certifikát). Potom zobrazí názvy virtuálních počítačů a stav. |
| AzureClassicRunAsCertificate |Asset certifikátu vytvořený automaticky. Certifikát ověřuje s Azure, abyste mohli spravovat klasické prostředky Azure pomocí runbooků. Tento certifikát má životnost jeden rok. |
| AzureClassicRunAsConnection |Asset připojení vytvořený automaticky. Asset ověřuje s Azure, abyste mohli spravovat klasické prostředky Azure pomocí runbooků. |

## <a name="next-steps"></a>Další postup

* Další informace o vytváření grafického obsahu najdete v tématu [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* První kroky s runbooky Python2 najdete v článku [Můj první runbook Python2](automation-first-runbook-textual-python2.md).
