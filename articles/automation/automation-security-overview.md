---
title: Přehled ověřování účtu Azure Automation
description: Tento článek poskytuje přehled Azure Automation metod zabezpečení a ověřování pro účty Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: c9e3c2b15b45eb15b40782a5d0ecfe1e736fc013
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683517"
---
# <a name="azure-automation-account-authentication-overview"></a>Přehled ověřování účtu Azure Automation

Azure Automation umožňuje automatizovat úlohy s prostředky v Azure, místně a u jiných poskytovatelů cloudu, například Amazon Web Services (AWS). Aby sada Runbook mohla provádět požadované akce, musí mít oprávnění k zabezpečenému přístupu k prostředkům s minimálními nároky vyžadovanými v rámci předplatného.

Při prvním spuštění služby Azure Automation vytvořte alespoň jeden účet Automation. Účty Automation umožňují izolovat vaše prostředky Automation (runbooky, assety, konfigurace) od prostředků, které jsou obsažené v jiných účtech Automation. Účty Automation můžete použít k oddělení prostředků do samostatných logických prostředí. Jeden účet můžete například použít pro vývoj, druhý k produkci a další pro svoje místní prostředí. Účet Azure Automation se liší od účtu Microsoft a účtů vytvořených v rámci vašeho předplatného Azure.

Prostředky Automation jednotlivých účtů Automation jsou přidružené k jedné oblasti Azure, ale účty Automation mohou spravovat veškeré prostředky v rámci předplatného. Hlavním důvodem k vytvoření účtů Automation v různých oblastech by byla situace, kdy máte zásady, které vyžadují izolaci dat a prostředků v určité oblasti.

Všechny úlohy, které provedete na prostředky pomocí Azure Resource Manager a rutiny Azure v Azure Automation, se musí ověřovat v Azure pomocí ověřování na základě přihlašovacích údajů organizace v Azure Active Directory (Azure AD). Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří se ve službě Azure AD nový uživatel instančního objektu, který tomuto uživateli přiřadí roli přispěvatel na úrovni předplatného. Pro Runbooky, které používají hybridní pracovní procesy Runbooku na virtuálních počítačích Azure, můžete místo účtů spustit jako použít pro ověřování prostředků Azure [ověřování pomocí sady Runbook se spravovanými identitami](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) .

Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure AD, musíte oprávnění k instančnímu objektu udělit v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

Řízení přístupu na základě role je dostupné v aplikaci Azure Resource Manager pro udělování povolených akcí na uživatelském účtu služby Azure AD a účtu Spustit jako a ověřování takového objektu služby. Přečtěte si článek [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md), kde najdete další informace, které vám pomůžou s vývojem vašeho modelu pro správu oprávnění ve službě Automation.  

Runbooky běžící na Hybrid Runbook Worker ve vašem datovém centru nebo na výpočetní služby v jiných cloudových prostředích, jako je AWS, nemůžou použít stejnou metodu, která se obvykle používá pro Runbooky, které se ověřují v prostředcích Azure. Důvodem je to, že tyto prostředky jsou spuštěné mimo Azure a proto vyžadují vlastní přihlašovací údaje zabezpečení definované ve službě Automation, aby se ověřovaly v prostředcích, ke přistupují místně. Další informace o ověřování Runbooku pomocí procesů Runbook Worker najdete v tématu [ověření runbooků pro procesy Hybrid Runbook Worker](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Další kroky

* [Vytvořte účet Automation z Azure Portal](automation-create-standalone-account.md).
* [Vytvořte účet Automation pomocí šablony Azure Resource Manager](automation-create-account-template.md).
* [Ověřování pomocí Amazon Web Services (AWS)](automation-config-aws-account.md).
