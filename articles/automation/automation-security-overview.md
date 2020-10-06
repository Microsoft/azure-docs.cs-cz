---
title: Přehled ověřování účtu Azure Automation
description: Tento článek poskytuje přehled ověřování účtu Azure Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766084"
---
# <a name="automation-account-authentication-overview"></a>Přehled ověřování účtů služby Automation

Azure Automation umožňuje automatizovat úlohy s prostředky v Azure, místně a u jiných poskytovatelů cloudu, například Amazon Web Services (AWS). Sady Runbook můžete použít k automatizaci úkolů nebo Hybrid Runbook Worker, pokud máte obchodní nebo provozní procesy pro správu mimo Azure. Práce v jednom z těchto prostředí vyžaduje oprávnění k zabezpečenému přístupu k prostředkům s minimálními nároky na požadavky.

Tento článek popisuje scénáře ověřování podporované Azure Automation a vysvětluje, jak začít na základě prostředí nebo prostředí, které potřebujete spravovat.

## <a name="automation-account"></a>Účet Automation

Při prvním spuštění služby Azure Automation vytvořte alespoň jeden účet Automation. Účty Automation umožňují izolovat vaše prostředky, Runbooky, prostředky a konfigurace služby Automation z prostředků jiných účtů. Účty Automation můžete použít k oddělení prostředků do samostatných logických prostředí. Jeden účet můžete například použít pro vývoj, druhý k produkci a další pro svoje místní prostředí. Účet Azure Automation se liší od účtu Microsoft a účtů vytvořených v rámci vašeho předplatného Azure. Úvod k vytvoření účtu Automation najdete v tématu [Vytvoření účtu Automation](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Prostředky služby Automation

Prostředky automatizace pro každý účet Automation jsou přidružené k jedné oblasti Azure, ale účet může spravovat všechny prostředky ve vašem předplatném Azure. Hlavním důvodem pro vytváření účtů Automation v různých oblastech je, že máte zásady, které vyžadují izolaci dat a prostředků do konkrétní oblasti.

Všechny úlohy, které vytvoříte na prostředky pomocí Azure Resource Manager a rutiny prostředí PowerShell v Azure Automation, se musí ověřit v Azure pomocí ověřování na základě přihlašovacích údajů organizační identity v Azure Active Directory (Azure AD).

## <a name="run-as-accounts"></a>Účty Spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků Azure Resource Manager nebo prostředků nasazených v modelu nasazení Classic. V Azure Automation existují dva typy účtů spustit jako:

* Účet Spustit jako pro Azure
* Účet Spustit jako pro Azure Classic

Další informace o těchto dvou modelech nasazení najdete v tématu [Správce prostředků a klasické nasazení](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Předplatná Azure Cloud Solution Provider (CSP) podporují pouze model Azure Resource Manager. V programu nejsou k dispozici služby, které nejsou Azure Resource Manager. Pokud používáte předplatné CSP, účet Spustit jako pro Azure Classic se nevytvoří, ale vytvoří se účet Spustit jako pro Azure. Další informace o předplatných CSP najdete [v tématu dostupné služby v předplatných CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>Účet Spustit jako

Účet Spustit jako pro Azure spravuje prostředky Azure na základě služby Azure Resource Manager Deployment and Management Service for Azure.

Když vytvoříte účet Spustit jako, provede následující úlohy:

* Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro aplikaci ve službě Azure AD a přiřadí roli [přispěvatele](../role-based-access-control/built-in-roles.md#contributor) pro tento účet v aktuálním předplatném. Nastavení certifikátu můžete změnit na vlastník nebo na jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).

* Vytvoří prostředek certifikátu Automation s názvem `AzureRunAsCertificate` v zadaném účtu Automation. Asset certifikátu obsahuje privátní klíč certifikátu, který používá aplikace Azure AD.

* Vytvoří prostředek připojení Automation s názvem `AzureRunAsConnection` v zadaném účtu Automation. Asset připojení obsahuje ID aplikace, ID tenanta, ID předplatného a kryptografický otisk certifikátu.

### <a name="azure-classic-run-as-account"></a>Azure Classic spuštěné jako účet

Účet Spustit jako pro Azure Classic spravuje prostředky Azure Classic na základě modelu nasazení Classic. Abyste mohli vytvořit nebo obnovit tento typ účtu Spustit jako, musíte být spolusprávcem předplatného.

Když vytvoříte účet Spustit jako pro Azure Classic, provede následující úkoly.

* Vytvoří v předplatném certifikát pro správu.

* Vytvoří prostředek certifikátu Automation s názvem `AzureClassicRunAsCertificate` v zadaném účtu Automation. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.

* Vytvoří prostředek připojení Automation s názvem `AzureClassicRunAsConnection` v zadaném účtu Automation. Prostředek připojení obsahuje název předplatného, ID předplatného a název assetu certifikátu.

>[!NOTE]
>Účet Spustit jako pro Azure Classic se ve výchozím nastavení ve stejnou chvíli při vytváření účtu Automation nevytvoří. Tento účet se vytváří jednotlivě podle postupu popsaného v článku [Správa účtu Spustit jako](manage-runas-account.md#create-a-run-as-account-in-azure-portal) .

## <a name="service-principal-for-run-as-account"></a>Instanční objekt pro účet Spustit jako

Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure AD, musíte oprávnění k instančnímu objektu udělit v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovému rozhraní API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Řízení přístupu na základě rolí je k dispozici u Azure Resource Manager pro udělení povolených akcí uživatelskému účtu Azure AD a účtu Spustit jako a ověření instančního objektu. Přečtěte si článek [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md), kde najdete další informace, které vám pomůžou s vývojem vašeho modelu pro správu oprávnění ve službě Automation.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Ověřování Runbooku pomocí Hybrid Runbook Worker

Runbooky běžící na Hybrid Runbook Worker ve vašem datovém centru nebo na výpočetní služby v jiných cloudových prostředích, jako je AWS, nemůžou použít stejnou metodu, která se obvykle používá pro Runbooky, které se ověřují v prostředcích Azure. Důvodem je to, že tyto prostředky jsou spuštěné mimo Azure a proto vyžadují vlastní přihlašovací údaje zabezpečení definované ve službě Automation, aby se ověřovaly v prostředcích, ke přistupují místně. Další informace o ověřování Runbooku pomocí procesů Runbook Worker najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

Pro Runbooky, které používají hybridní pracovní procesy Runbooku na virtuálních počítačích Azure, můžete použít [ověřování Runbooku se spravovanými identitami](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) místo účtů spustit jako k ověření pro vaše prostředky Azure.

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit účet Automation z Azure Portal, přečtěte si téma [vytvoření samostatného účtu Azure Automation](automation-create-standalone-account.md).
* Pokud dáváte přednost vytvoření účtu pomocí šablony, přečtěte si téma [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](quickstart-create-automation-account-template.md).
* Ověřování pomocí Amazon Web Services najdete v tématu [ověřování runbooků pomocí Amazon Web Services](automation-config-aws-account.md).