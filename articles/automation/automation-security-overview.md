---
title: Úvod do ověřování v Azure Automation
description: Tento článek nabízí přehled zabezpečení služby Automation a různé metody ověřování, které jsou dostupné pro účty Automation ve službě Azure Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114526"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Úvod do ověřování ve službě Azure Automation

Azure Automation umožňuje automatizovat úlohy s prostředky v Azure, místně a u jiných poskytovatelů cloudu, například Amazon Web Services (AWS). Aby mohl runbook provádět požadované akce, musí mít oprávnění pro bezpečný přístup k prostředkům s minimálními požadovanými právy v rámci předplatného.

Tento článek se zabývá různými scénáři ověřování podporovanými nástrojem Azure Automation a postupem, jak začít na základě prostředí nebo prostředí, které potřebujete spravovat.  

## <a name="automation-account-overview"></a>Přehled účtu Automation

Při prvním spuštění služby Azure Automation vytvořte alespoň jeden účet Automation. Účty Automation umožňují izolovat vaše prostředky Automation (runbooky, assety, konfigurace) od prostředků, které jsou obsažené v jiných účtech Automation. Účty Automation můžete použít k oddělení prostředků do samostatných logických prostředí. Jeden účet můžete například použít pro vývoj, druhý k produkci a další pro svoje místní prostředí. Účet Azure Automation se liší od účtu Microsoft a účtů vytvořených v rámci vašeho předplatného Azure.

Prostředky Automation jednotlivých účtů Automation jsou přidružené k jedné oblasti Azure, ale účty Automation mohou spravovat veškeré prostředky v rámci předplatného. Hlavním důvodem k vytvoření účtů Automation v různých oblastech by byla situace, kdy máte zásady, které vyžadují izolaci dat a prostředků v určité oblasti.

Všechny úlohy, které s prostředky provádíte pomocí správce Azure Resource Manager a rutin Azure ve službě Azure Automation, se musí ověřovat pro službu Azure pomocí ověření na základě přihlašovacích údajů organizační identity v Azure Active Directory. Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří se nový uživatel instančního objektu ve službě Azure Active Directory (AD) a přiřadí roli přispěvatele tomuto uživateli na úrovni předplatného. Pro Runbooky, které používají hybridní pracovní procesy Runbooku na virtuálních počítačích Azure, můžete k ověřování prostředků Azure použít [spravované identity pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) místo účtů spustit jako.

Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure AD, musíte oprávnění k instančnímu objektu udělit v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

Řízení přístupu na základě role je dostupné v aplikaci Azure Resource Manager pro udělování povolených akcí na uživatelském účtu služby Azure AD a účtu Spustit jako a ověřování takového objektu služby. Přečtěte si článek [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md), kde najdete další informace, které vám pomůžou s vývojem vašeho modelu pro správu oprávnění ve službě Automation.  

Runbooky běžící na Hybrid Runbook Worker ve vašem datovém centru nebo na výpočetní služby v jiných cloudových prostředích, jako je AWS, nemůžou použít stejnou metodu, která se obvykle používá pro Runbooky, které se ověřují v prostředcích Azure. Důvodem je to, že tyto prostředky jsou spuštěné mimo Azure a proto vyžadují vlastní přihlašovací údaje zabezpečení definované ve službě Automation, aby se ověřovaly v prostředcích, ke přistupují místně. Další informace o ověřování Runbooku pomocí procesů Runbook Worker najdete v tématu [ověření runbooků pro procesy Hybrid Runbook Worker](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Další kroky

* [Vytvořte účet Automation z Azure Portal](automation-create-standalone-account.md).

* [Vytvořte účet Automation pomocí šablony Azure Resource Manager](automation-create-account-template.md).

* [Ověřování pomocí Amazon Web Services (AWS)](automation-config-aws-account.md).
