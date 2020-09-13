---
title: Správa účtu Azure Automation spustit jako
description: V tomto článku se dozvíte, jak spravovat účet Spustit jako pomocí PowerShellu nebo Azure Portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: cb804b21d6f5312c13bfdbf7b0fc0404961ba1e3
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005730"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Správa účtu Azure Automation spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří se nový uživatel instančního objektu ve službě Azure Active Directory (AD) a přiřadí roli přispěvatele tomuto uživateli na úrovni předplatného.

## <a name="types-of-run-as-accounts"></a>Typy účtů spustit jako

Azure Automation používá dva typy účtů spustit jako:

* Účet Spustit jako pro Azure
* Účet Spustit jako pro Azure Classic

>[!NOTE]
>Předplatná Azure Cloud Solution Provider (CSP) podporují pouze model Azure Resource Manager. V programu nejsou k dispozici služby, které nejsou Azure Resource Manager. Pokud používáte předplatné CSP, účet Spustit jako pro Azure Classic se nevytvoří, ale vytvoří se účet Spustit jako pro Azure. Další informace o předplatných CSP najdete [v tématu dostupné služby v předplatných CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure AD, musíte oprávnění k instančnímu objektu udělit v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovému rozhraní API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

### <a name="run-as-account"></a>Účet Spustit jako

Účet Spustit jako spravuje prostředky [modelu nasazení Správce prostředků](../azure-resource-manager/management/deployment-models.md) . Provádí následující úlohy.

* Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro tuto aplikaci v Azure AD a přiřadí roli přispěvatele pro tento účet v aktuálním předplatném. Nastavení certifikátu můžete změnit na vlastník nebo na jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).

* Vytvoří prostředek certifikátu Automation s názvem `AzureRunAsCertificate` v zadaném účtu Automation. Asset certifikátu obsahuje privátní klíč certifikátu, který používá aplikace Azure AD.

* Vytvoří prostředek připojení Automation s názvem `AzureRunAsConnection` v zadaném účtu Automation. Asset připojení obsahuje ID aplikace, ID tenanta, ID předplatného a kryptografický otisk certifikátu.

### <a name="azure-classic-run-as-account"></a>Azure Classic spuštěné jako účet

Účet Spustit jako pro Azure Classic spravuje prostředky [modelu nasazení Classic](../azure-resource-manager/management/deployment-models.md) . Aby bylo možné vytvořit nebo obnovit tento typ účtu, musíte být spolusprávcem předplatného.

Účet Spustit jako pro Azure Classic provádí následující úlohy.

  * Vytvoří v předplatném certifikát pro správu.

  * Vytvoří prostředek certifikátu Automation s názvem `AzureClassicRunAsCertificate` v zadaném účtu Automation. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.

  * Vytvoří prostředek připojení Automation s názvem `AzureClassicRunAsConnection` v zadaném účtu Automation. Prostředek připojení obsahuje název předplatného, ID předplatného a název assetu certifikátu.

>[!NOTE]
>Účet Spustit jako pro Azure Classic se ve výchozím nastavení ve stejnou chvíli při vytváření účtu Automation nevytvoří. Tento účet se vytváří jednotlivě podle kroků popsaných dále v tomto článku.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Získání oprávnění účtu Spustit jako

Tato část definuje oprávnění pro účty běžných účtů spustit jako a účty Spustit jako pro Azure Classic.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Získat oprávnění ke konfiguraci účtů spustit jako

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, je nutné mít specifická oprávnění a oprávnění. Správce aplikace v Azure Active Directory a vlastník v rámci předplatného může dokončit všechny úlohy. V situaci, kdy máte oddělení povinností, je v následující tabulce uveden seznam úkolů, ekvivalentní rutina a potřebná oprávnění:

|Úloha|Rutina  |Minimální oprávnění  |Místo nastavení oprávnění|
|---|---------|---------|---|
|Vytvoření aplikace Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Role vývojáře aplikace<sup>1</sup>        |[Služba Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Registrace aplikací > domovského > služby Azure AD |
|Přidejte do aplikace přihlašovací údaje.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Správce aplikace nebo globální správce<sup>1</sup>         |[Služba Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Registrace aplikací > domovského > služby Azure AD|
|Vytvoření a získání instančního objektu služby Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Správce aplikace nebo globální správce<sup>1</sup>        |[Služba Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Registrace aplikací > domovského > služby Azure AD|
|Přiřazení nebo získání role Azure pro zadaný objekt zabezpečení|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Správce nebo vlastník přístupu uživatele nebo musí mít následující oprávnění:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Předplatné](../role-based-access-control/role-assignments-portal.md)</br>Předplatné Home > > \<subscription name\> -Access Control (IAM)|
|Vytvoření nebo odebrání certifikátu Automation|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Přispěvatel ve skupině prostředků         |Skupina prostředků účtu služby Automation|
|Vytvoření nebo odebrání připojení služby Automation|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Přispěvatel ve skupině prostředků |Skupina prostředků účtu služby Automation|

<sup>1</sup> uživatelé bez oprávnění správce v TENANTOVI Azure AD můžou [Registrovat aplikace služby AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) , pokud je možnost Uživatelé klienta Azure AD **můžou registrovat aplikace** na stránce nastavení uživatele je nastavená na **Ano**. Pokud je nastavení registrace aplikace **ne**, uživatel provádějící tuto akci musí být definovaný v této tabulce.

Pokud nejste členem instance Active Directory předplatného, než se přiřadíte do role globálního správce předplatného, jste přidaní jako host. V takovém případě se `You do not have permissions to create…` na stránce **Přidat účet Automation** zobrazí upozornění.

Pokud jste členem instance Active Directory předplatného, když je přiřazená role globálního správce, můžete také zobrazit `You do not have permissions to create…` Upozornění na stránce **Přidat účet Automation** . V takovém případě si můžete vyžádat odebrání z instance Active Directory předplatného a potom požádat o opětovné přidání, takže se ve službě Active Directory stanete úplným uživatelem.

Chcete-li ověřit, zda byla odstraněna situace, kdy došlo k chybě této chybové zprávy:

1. V podokně Azure Active Directory v Azure Portal vyberte **Uživatelé a skupiny**.
2. Vyberte **Všichni uživatelé**.
3. Zvolte své jméno a pak vyberte **profil**.
4. Ujistěte se, že hodnota atributu **Type uživatele** v profilu uživatele není nastavená na **Host**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Získat oprávnění ke konfiguraci účtů spustit jako pro Classic

Pokud chcete nakonfigurovat nebo prodloužit účty Spustit jako pro Azure Classic, musíte mít roli spolusprávce na úrovni předplatného. Další informace o oprávněních klasického předplatného najdete v tématu [Správci předplatného Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Vytvoření účtu Spustit jako v Azure Portal

Chcete-li aktualizovat účet Azure Automation v Azure Portal, proveďte následující kroky. Vytvořte účty Spustit jako a účet Spustit jako pro Azure Classic samostatně. Pokud nepotřebujete spravovat klasické prostředky, můžete vytvořit jenom účet Spustit jako pro Azure.

1. Přihlaste se k portálu Azure pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

2. Vyhledejte a vyberte **účty Automation**.

3. Na stránce účty Automation v seznamu vyberte svůj účet Automation.

4. V levém podokně vyberte **účty Spustit jako** v části nastavení účtu.

5. Podle toho, který účet požadujete, vyberte buď **Účet Spustit jako pro Azure**, nebo **Účet Spustit jako pro Azure Classic**.

6. V závislosti na účtu zájmu použijte podokno přidat účet Spustit v **Azure jako** nebo **Přidat účet Spustit jako** pro Azure Classic. Po kontrole informací v přehledu klikněte na **vytvořit**.

7. Zatímco Azure vytváří účet Spustit jako, můžete průběh sledovat v nabídce v části **Oznámení**. Zobrazí se také zpráva s informacemi o tom, že účet se vytváří. Dokončení procesu může trvat několik minut.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Odstranění účet Spustit jako nebo Spustit jako pro Classic

Tato část popisuje, jak odstranit účet Spustit jako nebo účet Spustit jako pro Azure Classic. Při provedení této akce se účet Automation uchová. Po odstranění účtu ho můžete znovu vytvořit v Azure Portal.

1. Na webu Azure Portal otevřete účet Automation.

2. V levém podokně vyberte **účty Spustit jako** v části nastavení účtu.

3. Na stránce vlastností Účty Spustit jako vyberte účet Spustit jako nebo účet Spustit jako pro Classic, který chcete odstranit.

4. V podokně vlastnosti vybraného účtu klikněte na **Odstranit**.

   ![Odstranění účtu Spustit jako](media/manage-runas-account/automation-account-delete-runas.png)

5. Zatímco se účet odstraňuje, můžete průběh sledovat v nabídce v části **Oznámení**.

6. Účet po odstranění můžete znovu vytvořit na stránce vlastností Účty Spustit jako výběrem možnosti Vytvořit v části **Účet Spustit jako pro Azure**.

   ![Znovuvytvoření účtu Automation Spustit jako](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Obnovení certifikátu podepsaného svým držitelem

Certifikát podepsaný svým držitelem, který jste vytvořili pro účet Spustit jako, vyprší jeden rok od data vytvoření. V určitém okamžiku před vypršením platnosti účtu Spustit jako je nutné certifikát obnovit. Můžete ho prodloužit kdykoli předtím, než vyprší jeho platnost.

Při obnovení certifikátu podepsaného svým držitelem se zachová aktuální platný certifikát, aby se zajistilo, že všechny Runbooky, které jsou ve frontě nebo aktivně spuštěné a které se ověřují pomocí účtu Spustit jako, nejsou negativně ovlivněny. Certifikát zůstane platný až do data vypršení jeho platnosti.

>[!NOTE]
>Pokud si myslíte, že došlo k ohrožení zabezpečení účtu Spustit jako, můžete certifikát podepsaný svým držitelem odstranit a znovu vytvořit.

>[!NOTE]
>Pokud jste nakonfigurovali účet Spustit jako, aby používal certifikát vydaný certifikační autoritou rozlehlé sítě, a použijete možnost k obnovení certifikátu podepsaného svým držitelem, bude podnikový certifikát nahrazen certifikátem podepsaným svým držitelem.

K obnovení certifikátu podepsaného svým držitelem použijte následující postup.

1. Na webu Azure Portal otevřete účet Automation.

1. V části nastavení účtu vyberte **účty Spustit jako** .

    ![Podokno vlastností účtu Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stránce vlastností účty Spustit jako vyberte účet Spustit jako nebo účet Spustit jako pro Classic, pro který chcete obnovit certifikát.

1. V podokně vlastnosti vybraného účtu klikněte na **obnovit certifikát**.

    ![Obnovení certifikátu pro účet Spustit jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Zatímco se certifikát obnovuje, můžete průběh sledovat v nabídce v části **Oznámení**.

## <a name="limit-run-as-account-permissions"></a>Omezení oprávnění účtu Spustit jako

Pokud chcete řídit cílení na automatizaci u prostředků v Azure, můžete spustit skript [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) . Tento skript změní existující instanční objekt účtu Spustit jako tak, aby vytvořil a používal vlastní definici role. Role má oprávnění pro všechny prostředky kromě [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Po spuštění skriptu **Update-AutomationRunAsAccountRoleAssignments.ps1** sady Runbook, které mají přístup Key Vault prostřednictvím používání účtů spustit jako, již nebudou fungovat. Před spuštěním skriptu byste měli zkontrolovat Runbooky ve vašem účtu pro volání Azure Key Vault. Pokud chcete povolit přístup k Key Vault z Azure Automation sad Runbook, musíte [Přidat účet Spustit jako pro Key Vault oprávnění](#add-permissions-to-key-vault).

Pokud potřebujete omezit, co může instanční objekt spustit jako, můžete přidat další typy prostředků do `NotActions` prvku definice vlastní role. Následující příklad omezuje přístup na `Microsoft.Compute/*` . Pokud přidáte tento typ prostředku do `NotActions` definice role, role nebude mít přístup k žádnému výpočetnímu prostředku. Další informace o definicích rolí najdete v tématu [vysvětlení definic rolí pro prostředky Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Můžete určit, jestli instanční objekt používaný účtem spustit jako je v definici role přispěvatele, nebo na vlastní.

1. V části nastavení účtu vyberte účet Automation a vyberte **účty Spustit jako** .
2. Vyberte **účet Spustit jako pro Azure**.
3. Vyberte **role** pro vyhledání používané definice role.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Ověřte roli účtu Spustit jako." lightbox="media/manage-runas-account/verify-role-expanded.png":::


Můžete také určit definici role používané účty Spustit jako pro více předplatných nebo účtů Automation. Použijte k tomu skript [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) v Galerie prostředí PowerShell.

### <a name="add-permissions-to-key-vault"></a>Přidat oprávnění pro Key Vault

Můžete Azure Automation, abyste ověřili, jestli Key Vault a objekt služby účet Spustit jako používá vlastní definici role. Musíte:

* Udělte oprávnění Key Vault.
* Nastavte zásady přístupu.

Pomocí skriptu [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) v Galerie prostředí PowerShell můžete dát účtu Spustit jako oprávnění k Key Vault. Další podrobnosti o nastavení oprávnění pro Key Vault najdete v tématu [přiřazení zásad přístupu k Key Vault](/azure/key-vault/general/assign-access-policy-powershell) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Řešení potíží s chybou konfigurace pro účty Spustit jako

Některé položky konfigurace potřebné pro účet Spustit jako nebo pro klasický účet Spustit jako byly pravděpodobně při počátečním nastavení odstraněny nebo vytvořeny nesprávně. Mezi možné instance nestejné konfigurace patří:

* Asset certifikátu
* Asset připojení
* Účet Spustit jako odebraný z role Přispěvatel
* Instanční objekt nebo aplikace v Azure AD

U takových instancí s *neúplnými* konfigurace účet Automation zjistí změny a v podokně vlastností účty Spustit jako pro tento účet zobrazí stav nedokončeno.

![Nedokončená konfigurace účtu Spustit jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Když vyberete účet Spustit jako, v podokně vlastností účtu se zobrazí následující chybová zpráva:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Tyto problémy s účtem spustit jako můžete rychle vyřešit tak, že odstraníte a znovu vytvoříte účet Spustit jako.

## <a name="next-steps"></a>Další kroky

* [Aplikační objekty a instanční objekty služby](../active-directory/develop/app-objects-and-service-principals.md).
* [Přehled certifikátů pro Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
