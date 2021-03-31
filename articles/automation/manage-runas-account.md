---
title: Správa účtu Azure Automation spustit jako
description: V tomto článku se dozvíte, jak spravovat účet Spustit jako pomocí PowerShellu nebo Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f170fc948f136f4f46634e7ae2645ed2eb357afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096457"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Správa účtu Azure Automation spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků v modelu nasazení Azure Resource Manager nebo Azure Classic pomocí runbooků Automation a dalších funkcí automatizace. Tento článek poskytuje pokyny k tomu, jak spravovat účet Spustit jako nebo účet Spustit jako pro Classic.

Další informace o ověřování účtu Azure Automation a pokyny týkající se scénářů automatizace procesů najdete v tématu [Přehled ověřování účtů služby Automation](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Obnovení certifikátu podepsaného svým držitelem

Certifikát podepsaný svým držitelem, který jste vytvořili pro účet Spustit jako, vyprší jeden rok od data vytvoření. V určitém okamžiku před vypršením platnosti účtu Spustit jako je nutné certifikát obnovit. Můžete ho prodloužit kdykoli předtím, než vyprší jeho platnost.

Při obnovení certifikátu podepsaného svým držitelem se zachová aktuální platný certifikát, aby se zajistilo, že všechny Runbooky, které jsou ve frontě nebo aktivně spuštěné a které se ověřují pomocí účtu Spustit jako, nejsou negativně ovlivněny. Certifikát zůstane platný až do data vypršení jeho platnosti.

>[!NOTE]
>Pokud si myslíte, že došlo k ohrožení zabezpečení účtu Spustit jako, můžete certifikát podepsaný svým držitelem odstranit a znovu vytvořit.

>[!NOTE]
>Pokud jste nakonfigurovali účet Spustit jako, aby používal certifikát vydaný certifikační autoritou rozlehlé sítě, a použijete možnost k obnovení certifikátu podepsaného svým držitelem, bude podnikový certifikát nahrazen certifikátem podepsaným svým držitelem.

K obnovení certifikátu podepsaného svým držitelem použijte následující postup.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V části nastavení účtu vyberte účet Automation a vyberte **účty Spustit jako** .

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Podokno vlastností účtu Automation.":::

1. Na stránce vlastností **účty Spustit jako** vyberte účet **Spustit jako** nebo **účet Spustit jako pro Classic** v závislosti na účtu, pro který potřebujete certifikát obnovit.

1. Na stránce **vlastnosti** vybraného účtu vyberte **obnovit certifikát**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Prodloužit platnost certifikátu pro účet Spustit jako.":::

1. Zatímco se certifikát obnovuje, můžete průběh sledovat v nabídce v části **Oznámení**.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Udělení oprávnění účtu Spustit jako v jiných předplatných

Azure Automation podporuje použití jednoho účtu Automation z jednoho předplatného a spouštění Runbooků pro Azure Resource Manager prostředků napříč několika předplatnými. Tato konfigurace nepodporuje model nasazení Azure Classic.

Instančnímu objektu účtu Spustit jako přiřadíte roli [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) v jiném předplatném nebo více omezujících oprávnění. Další informace najdete v tématu [řízení přístupu na základě role](automation-role-based-access-control.md) v Azure Automation. Pokud chcete účet Spustit jako přiřadit k roli v jiném předplatném, musí být uživatelský účet, který provádí tuto úlohu, členem role **vlastníka** v tomto předplatném.

> [!NOTE]
> Tato konfigurace podporuje jenom několik předplatných organizace, které používají společného tenanta Azure AD.

Před udělením oprávnění účtu Spustit jako musíte nejdřív poznamenat zobrazovaný název instančního objektu, který se má přiřadit.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Z účtu Automation vyberte **účty Spustit jako** v části **Nastavení účtu**.
1. Vyberte **účet Spustit jako pro Azure**.
1. Zkopírujte nebo Poznamenejte si hodnotu pro **zobrazované jméno** na stránce **účtu Spustit jako pro Azure** .

Podrobné pokyny, jak přidat přiřazení rolí, najdete v následujících článcích v závislosti na metodě, kterou chcete použít.

* [Přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Přiřazení rolí Azure pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Přiřazení rolí Azure pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Přiřazení rolí Azure pomocí REST API](..//role-based-access-control/role-assignments-rest.md)

Po přiřazení účtu Spustit jako k roli v sadě Runbook určete, že má být `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` použit kontext předplatného. Další informace najdete v tématu [set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Omezení oprávnění účtu Spustit jako

Pokud chcete řídit cílení na automatizaci u prostředků v Azure, můžete spustit skript [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) . Tento skript změní existující instanční objekt účtu Spustit jako tak, aby vytvořil a používal vlastní definici role. Role má oprávnění pro všechny prostředky kromě [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Po spuštění skriptu **Update-AutomationRunAsAccountRoleAssignments.ps1** sady Runbook, které mají přístup Key Vault prostřednictvím používání účtů spustit jako, již nebudou fungovat. Před spuštěním skriptu byste měli zkontrolovat Runbooky ve vašem účtu pro volání Azure Key Vault. Pokud chcete povolit přístup k Key Vault z Azure Automation sad Runbook, musíte [Přidat účet Spustit jako pro Key Vault oprávnění](#add-permissions-to-key-vault).

Pokud potřebujete ještě více omezit, co může instanční objekt spustit jako, můžete přidat další typy prostředků do `NotActions` prvku definice vlastní role. Následující příklad omezuje přístup na `Microsoft.Compute/*` . Pokud přidáte tento typ prostředku do `NotActions` definice role, role nebude mít přístup k žádnému výpočetnímu prostředku. Další informace o definicích rolí najdete v tématu [vysvětlení definic rolí pro prostředky Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Můžete určit, jestli instanční objekt používaný účtem spustit jako přiřadil roli **přispěvatele** nebo vlastní.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V části nastavení účtu vyberte účet Automation a vyberte **účty Spustit jako** .
1. Vyberte **účet Spustit jako pro Azure**.
1. Vyberte **role** pro vyhledání používané definice role.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Ověřte roli účtu Spustit jako." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Můžete také určit definici role používané účty Spustit jako pro více předplatných nebo účtů Automation. Použijte k tomu skript [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) v Galerie prostředí PowerShell.

### <a name="add-permissions-to-key-vault"></a>Přidat oprávnění pro Key Vault

Můžete Azure Automation, abyste ověřili, jestli Key Vault a objekt služby účet Spustit jako používá vlastní definici role. Musíte:

* Udělte oprávnění Key Vault.
* Nastavte zásady přístupu.

Pomocí skriptu [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) v Galerie prostředí PowerShell můžete udělit oprávnění ke svému účtu spustit jako Key Vault. Další podrobnosti o nastavení oprávnění pro Key Vault najdete v tématu [přiřazení zásad přístupu k Key Vault](../key-vault/general/assign-access-policy-powershell.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Řešení potíží s chybou konfigurace pro účty Spustit jako

Některé položky konfigurace potřebné pro účet Spustit jako nebo pro klasický účet Spustit jako byly pravděpodobně při počátečním nastavení odstraněny nebo vytvořeny nesprávně. Mezi možné instance nestejné konfigurace patří:

* Asset certifikátu
* Asset připojení
* Účet Spustit jako odebraný z role Přispěvatel
* Instanční objekt nebo aplikace v Azure AD

U takových instancí s *neúplnými* konfigurace účet Automation zjistí změny a v podokně vlastností účty Spustit jako pro tento účet zobrazí stav nedokončeno.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Neúplná konfigurace účtu Spustit jako.":::

Když vyberete účet Spustit jako, v podokně vlastností účtu se zobrazí následující chybová zpráva:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Tyto problémy s účtem spustit jako můžete rychle vyřešit tak, že [odstraníte](delete-run-as-account.md) a [znovu vytvoříte](create-run-as-account.md) účet Spustit jako.

## <a name="next-steps"></a>Další kroky

* [Aplikační objekty a instanční objekty služby](../active-directory/develop/app-objects-and-service-principals.md).
* [Přehled certifikátů pro Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
* Chcete-li vytvořit nebo znovu vytvořit účet Spustit jako, přečtěte si téma [Vytvoření účtu Spustit jako](create-run-as-account.md).
* Pokud již nepotřebujete používat účet Spustit jako, přečtěte si téma [odstranění účtu Spustit jako](delete-run-as-account.md).