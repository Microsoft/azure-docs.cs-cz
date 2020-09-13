---
title: Přehled ověřování účtu Azure Automation
description: Tento článek poskytuje přehled ověřování účtu Azure Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 8068d6ebe67dee1408420441aacd83726a1986df
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434261"
---
# <a name="automation-account-authentication-overview"></a>Přehled ověřování účtů služby Automation

Azure Automation umožňuje automatizovat úlohy s prostředky v Azure, místně a u jiných poskytovatelů cloudu, například Amazon Web Services (AWS). Sady Runbook můžete použít k automatizaci úkolů nebo Hybrid Runbook Worker, pokud máte úkoly mimo Azure ke správě. Každé prostředí vyžaduje oprávnění k zabezpečenému přístupu k prostředkům s minimálními nároky vyžadovanými v rámci předplatného Azure.

Tento článek popisuje scénáře ověřování podporované Azure Automation a vysvětluje, jak začít na základě prostředí nebo prostředí, které potřebujete spravovat.

## <a name="automation-account"></a>Účet Automation

Při prvním spuštění služby Azure Automation vytvořte alespoň jeden účet Automation. Účty Automation umožňují izolovat vaše prostředky, Runbooky, prostředky a konfigurace služby Automation z prostředků jiných účtů. Účty Automation můžete použít k oddělení prostředků do samostatných logických prostředí. Jeden účet můžete například použít pro vývoj, druhý k produkci a další pro svoje místní prostředí. Účet Azure Automation se liší od účtu Microsoft a účtů vytvořených v rámci vašeho předplatného Azure. Úvod k vytvoření účtu Automation najdete v tématu [Vytvoření účtu Automation](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Prostředky služby Automation

Prostředky automatizace pro každý účet Automation jsou přidružené k jedné oblasti Azure, ale účet může spravovat všechny prostředky ve vašem předplatném Azure. Hlavním důvodem pro vytváření účtů Automation v různých oblastech je, že máte zásady, které vyžadují izolaci dat a prostředků do konkrétní oblasti.

Všechny úlohy, které vytvoříte na prostředky pomocí Azure Resource Manager a rutiny prostředí PowerShell v Azure Automation, se musí ověřit v Azure pomocí ověřování na základě přihlašovacích údajů organizační identity v Azure Active Directory (Azure AD).

## <a name="run-as-account"></a>Účet Spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků Azure pomocí rutin PowerShellu. Když vytvoříte účet Spustit jako, vytvoří se ve službě Azure AD nový uživatel instančního objektu, který tomuto uživateli přiřadí roli přispěvatel na úrovni předplatného. Pro Runbooky, které používají hybridní pracovní procesy Runbooku na virtuálních počítačích Azure, můžete použít [ověřování Runbooku se spravovanými identitami](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) místo účtů spustit jako k ověření pro vaše prostředky Azure.

## <a name="service-principal-for-run-as-account"></a>Instanční objekt pro účet Spustit jako

Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure AD, musíte oprávnění k instančnímu objektu udělit v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovému rozhraní API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Řízení přístupu na základě rolí je k dispozici u Azure Resource Manager pro udělení povolených akcí uživatelskému účtu Azure AD a účtu Spustit jako a ověření instančního objektu. Přečtěte si článek [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md), kde najdete další informace, které vám pomůžou s vývojem vašeho modelu pro správu oprávnění ve službě Automation.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Ověřování Runbooku pomocí Hybrid Runbook Worker

Runbooky běžící na Hybrid Runbook Worker ve vašem datovém centru nebo na výpočetní služby v jiných cloudových prostředích, jako je AWS, nemůžou použít stejnou metodu, která se obvykle používá pro Runbooky, které se ověřují v prostředcích Azure. Důvodem je to, že tyto prostředky jsou spuštěné mimo Azure a proto vyžadují vlastní přihlašovací údaje zabezpečení definované ve službě Automation, aby se ověřovaly v prostředcích, ke přistupují místně. Další informace o ověřování Runbooku pomocí procesů Runbook Worker najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit účet Automation z Azure Portal, přečtěte si téma [vytvoření samostatného účtu Azure Automation](automation-create-standalone-account.md).
* Pokud dáváte přednost vytvoření účtu pomocí šablony, přečtěte si téma [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](quickstart-create-automation-account-template.md).
* Ověřování pomocí Amazon Web Services najdete v tématu [ověřování runbooků pomocí Amazon Web Services](automation-config-aws-account.md).