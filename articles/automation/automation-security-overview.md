---
title: Přehled ověřování účtu Azure Automation
description: Tento článek poskytuje přehled ověřování účtu Azure Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 546407ce7286cebc04d3c86422f6242051d1dbf3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830425"
---
# <a name="azure-automation-account-authentication-overview"></a>Přehled ověřování účtu Azure Automation

Azure Automation umožňuje automatizovat úlohy s prostředky v Azure, místně a u jiných poskytovatelů cloudu, například Amazon Web Services (AWS). Sady Runbook můžete použít k automatizaci úkolů nebo Hybrid Runbook Worker, pokud máte obchodní nebo provozní procesy pro správu mimo Azure. Práce v jednom z těchto prostředí vyžaduje oprávnění k zabezpečenému přístupu k prostředkům s minimálními nároky na požadavky.

Tento článek popisuje scénáře ověřování podporované Azure Automation a vysvětluje, jak začít na základě prostředí nebo prostředí, které potřebujete spravovat.

## <a name="automation-account"></a>Účet Automation

Při prvním spuštění služby Azure Automation vytvořte alespoň jeden účet Automation. Účty Automation umožňují izolovat vaše prostředky, Runbooky, prostředky a konfigurace služby Automation z prostředků jiných účtů. Účty Automation můžete použít k oddělení prostředků do samostatných logických prostředí nebo delegovaných odpovědností. Jeden účet můžete například použít pro vývoj, druhý k produkci a další pro svoje místní prostředí. Případně můžete vyhradit účet Automation pro správu aktualizací operačního systému ve všech vašich počítačích pomocí [Update Management](update-management/overview.md). 

Účet Azure Automation se liší od účtu Microsoft a účtů vytvořených v rámci vašeho předplatného Azure. Úvod k vytvoření účtu Automation najdete v tématu [Vytvoření účtu Automation](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Prostředky služby Automation

Prostředky automatizace pro každý účet Automation jsou přidružené k jedné oblasti Azure, ale účet může spravovat všechny prostředky ve vašem předplatném Azure. Hlavním důvodem pro vytváření účtů Automation v různých oblastech je, že máte zásady, které vyžadují izolaci dat a prostředků do konkrétní oblasti.

Všechny úlohy, které vytvoříte na prostředky pomocí Azure Resource Manager a rutiny prostředí PowerShell v Azure Automation, se musí ověřit v Azure pomocí ověřování na základě přihlašovacích údajů organizační identity v Azure Active Directory (Azure AD).

## <a name="managed-identities-preview"></a>Spravované identity (Preview)

Spravovaná identita z Azure Active Directory (Azure AD) umožňuje sadě Runbook snadný přístup k dalším prostředkům chráněným službou Azure AD. Identita je spravovaná platformou Azure a nevyžaduje zřízení ani otočení jakýchkoli tajných klíčů. Další informace o spravovaných identitách v Azure AD najdete v tématu [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview).

Zde jsou některé výhody použití spravovaných identit:

- Spravované identity můžete použít k ověření pro libovolnou službu Azure, která podporuje ověřování Azure AD. Dají se použít pro Cloud i pro hybridní úlohy. Hybridní úlohy můžou používat spravované identity při spuštění na Hybrid Runbook Worker, která běží na virtuálním počítači Azure nebo mimo Azure.

- Spravované identity lze použít bez jakýchkoli dalších nákladů.

- Nemusíte obnovovat certifikát používaný účtem Automation spustit jako.

- V kódu Runbooku nemusíte určovat objekt připojení spustit jako. K prostředkům můžete přistupovat pomocí spravované identity účtu Automation z Runbooku bez vytváření certifikátů, připojení, účtů spustit jako atd.

Účtu Automation se dají udělit dva typy identit:

- Identita přiřazená systémem je svázána s vaší aplikací a je odstraněna, pokud je vaše aplikace odstraněna. Aplikace může mít jenom jednu identitu přiřazenou systémem.

- Uživatelsky přiřazená identita je samostatný prostředek Azure, který je možné přiřadit k vaší aplikaci. Aplikace může mít více uživatelsky přiřazených identit.

>[!NOTE]
> Identity přiřazené uživateli ještě nejsou podporované.

Podrobnosti o používání spravovaných identit najdete v tématu [Povolení spravované identity pro Azure Automation (Preview)](enable-managed-identity-for-automation.md).

## <a name="run-as-accounts"></a>Účty Spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků Azure Resource Manager nebo prostředků nasazených v modelu nasazení Classic. V Azure Automation existují dva typy účtů spustit jako:

* Účet Spustit jako pro Azure: umožňuje spravovat prostředky Azure na základě služby Azure Resource Manager Deployment and Management Service for Azure.
* Účet Spustit jako pro Azure Classic: umožňuje spravovat klasické prostředky Azure na základě modelu nasazení Classic.

Další informace o modelech nasazení Azure Resource Manager a Classic najdete v tématu [Správce prostředků a klasické nasazení](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Předplatná Azure Cloud Solution Provider (CSP) podporují pouze model Azure Resource Manager. V programu nejsou k dispozici služby, které nejsou Azure Resource Manager. Pokud používáte předplatné CSP, účet Spustit jako pro Azure Classic se nevytvoří, ale vytvoří se účet Spustit jako pro Azure. Další informace o předplatných CSP najdete [v tématu dostupné služby v předplatných CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Když vytvoříte účet Automation, vytvoří se ve výchozím nastavení účet Spustit jako. Pokud se rozhodnete, že ho nechcete vytvořit spolu s účtem Automation, můžete ho vytvořit jednotlivě později. Účet Spustit jako pro Azure Classic je volitelný a vytvoří se samostatně, pokud potřebujete spravovat klasické prostředky.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Účet Spustit jako

Když vytvoříte účet Spustit jako, provede následující úlohy:

* Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro aplikaci ve službě Azure AD a přiřadí roli [přispěvatele](../role-based-access-control/built-in-roles.md#contributor) pro tento účet v aktuálním předplatném. Nastavení certifikátu můžete změnit na [Čtenář](../role-based-access-control/built-in-roles.md#reader) nebo jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).

* Vytvoří prostředek certifikátu Automation s názvem `AzureRunAsCertificate` v zadaném účtu Automation. Asset certifikátu obsahuje privátní klíč certifikátu, který používá aplikace Azure AD.

* Vytvoří prostředek připojení Automation s názvem `AzureRunAsConnection` v zadaném účtu Automation. Asset připojení obsahuje ID aplikace, ID tenanta, ID předplatného a kryptografický otisk certifikátu.

### <a name="azure-classic-run-as-account"></a>Účet Spustit jako pro Azure Classic

Když vytvoříte účet Spustit jako pro Azure Classic, provede následující úlohy:

> [!NOTE]
> Abyste mohli vytvořit nebo obnovit tento typ účtu Spustit jako, musíte být spolusprávcem předplatného.

* Vytvoří v předplatném certifikát pro správu.

* Vytvoří prostředek certifikátu Automation s názvem `AzureClassicRunAsCertificate` v zadaném účtu Automation. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.

* Vytvoří prostředek připojení Automation s názvem `AzureClassicRunAsConnection` v zadaném účtu Automation. Prostředek připojení obsahuje název předplatného, ID předplatného a název assetu certifikátu.

## <a name="service-principal-for-run-as-account"></a>Instanční objekt pro účet Spustit jako

Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure AD, musíte oprávnění k instančnímu objektu udělit v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovému rozhraní API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Oprávnění účtu Spustit jako

Tato část definuje oprávnění pro účty běžných účtů spustit jako a účty Spustit jako pro Azure Classic.

* Pokud chcete vytvořit nebo aktualizovat účet Spustit jako, může správce aplikace Azure Active Directory a vlastník v rámci předplatného dokončit všechny úlohy.
* Pokud chcete nakonfigurovat nebo prodloužit účty Spustit jako pro Azure Classic, musíte mít roli spolusprávce na úrovni předplatného. Další informace o oprávněních klasického předplatného najdete v tématu [Správci předplatného Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

V situaci, kdy máte oddělení povinností, je v následující tabulce uveden seznam úkolů, ekvivalentní rutina a potřebná oprávnění:

|Úkol|Rutina  |Minimální oprávnění  |Místo nastavení oprávnění|
|---|---------|---------|---|
|Vytvoření aplikace Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Role vývojáře aplikace<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Registrace aplikací > domovského > služby Azure AD |
|Přidejte do aplikace přihlašovací údaje.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Správce aplikace nebo globální správce<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Registrace aplikací > domovského > služby Azure AD|
|Vytvoření a získání instančního objektu služby Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Správce aplikace nebo globální správce<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Registrace aplikací > domovského > služby Azure AD|
|Přiřazení nebo získání role Azure pro zadaný objekt zabezpečení|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Správce nebo vlastník přístupu uživatele nebo musí mít následující oprávnění:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Předplatné](../role-based-access-control/role-assignments-portal.md)</br>Předplatné Home > > \<subscription name\> -Access Control (IAM)|
|Vytvoření nebo odebrání certifikátu Automation|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Přispěvatel ve skupině prostředků         |Skupina prostředků účtu služby Automation|
|Vytvoření nebo odebrání připojení služby Automation|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Přispěvatel ve skupině prostředků |Skupina prostředků účtu služby Automation|

<sup>1</sup> uživatelé bez oprávnění správce v TENANTOVI Azure AD můžou [Registrovat aplikace služby AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) , pokud je možnost Uživatelé klienta Azure AD **můžou registrovat aplikace** na stránce **nastavení uživatele** je nastavená na **Ano**. Pokud je nastavení registrace aplikace **ne**, uživatel provádějící tuto akci musí být definovaný v této tabulce.

Pokud nejste členem instance Active Directory předplatného, než se přiřadíte do role globálního správce předplatného, jste přidaní jako host. V takovém případě se `You do not have permissions to create…` na stránce **Přidat účet Automation** zobrazí upozornění.

Chcete-li ověřit, zda byla odstraněna situace, kdy došlo k chybě této chybové zprávy:

1. V podokně Azure Active Directory v Azure Portal vyberte **Uživatelé a skupiny**.
2. Vyberte **Všichni uživatelé**.
3. Zvolte své jméno a pak vyberte **profil**.
4. Ujistěte se, že hodnota atributu **Type uživatele** v profilu uživatele není nastavená na **Host**.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Řízení přístupu na základě rolí je k dispozici u Azure Resource Manager pro udělení povolených akcí uživatelskému účtu Azure AD a účtu Spustit jako a ověření instančního objektu. Přečtěte si článek [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md), kde najdete další informace, které vám pomůžou s vývojem vašeho modelu pro správu oprávnění ve službě Automation.

Pokud máte přísné bezpečnostní prvky zabezpečení pro přiřazení oprávnění ve skupinách prostředků, musíte přiřadit členství účtu Spustit jako k roli **Přispěvatel** ve skupině prostředků.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Ověřování Runbooku pomocí Hybrid Runbook Worker

Runbooky běžící na Hybrid Runbook Worker ve vašem datovém centru nebo na výpočetní služby v jiných cloudových prostředích, jako je AWS, nemůžou použít stejnou metodu, která se obvykle používá pro Runbooky, které se ověřují v prostředcích Azure. Důvodem je to, že tyto prostředky jsou spuštěné mimo Azure a proto vyžadují vlastní přihlašovací údaje zabezpečení definované ve službě Automation, aby se ověřovaly v prostředcích, ke přistupují místně. Další informace o ověřování Runbooku pomocí procesů Runbook Worker najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

Pro Runbooky, které používají hybridní pracovní procesy Runbooku na virtuálních počítačích Azure, můžete použít [ověřování Runbooku se spravovanými identitami](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) místo účtů spustit jako k ověření pro vaše prostředky Azure.

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit účet Automation z Azure Portal, přečtěte si téma [vytvoření samostatného účtu Azure Automation](automation-create-standalone-account.md).
* Pokud dáváte přednost vytvoření účtu pomocí šablony, přečtěte si téma [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](quickstart-create-automation-account-template.md).
* Ověřování pomocí Amazon Web Services najdete v tématu [ověřování runbooků pomocí Amazon Web Services](automation-config-aws-account.md).
* Seznam služeb Azure, které podporují funkci spravovaných identit prostředků Azure, najdete v článku o [službách podporujících spravované identity prostředků Azure](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).
