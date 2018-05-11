---
title: Úvod k ověřování v Azure Automation.
description: Tento článek nabízí přehled zabezpečení služby Automation a různé metody ověřování, které jsou dostupné pro účty Automation ve službě Azure Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: e8ea60899f907793b804d86cf0750a73b23d46fd
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Úvod do ověřování ve službě Azure Automation  
Azure Automation umožňuje automatizovat úlohy s prostředky v Azure, místně a u jiných poskytovatelů cloudu, například Amazon Web Services (AWS).  Aby mohl runbook provádět požadované akce, musí mít oprávnění pro bezpečný přístup k prostředkům s minimálními požadovanými právy v rámci předplatného.

Tento článek se bude zabývat různými scénáři ověřování, které Azure Automation podporuje, a ukáže vám, jak začít pracovat s ohledem na prostředí, které nebo která potřebujete spravovat.  

## <a name="automation-account-overview"></a>Přehled účtu Automation
Při prvním spuštění služby Azure Automation vytvořte alespoň jeden účet Automation. Účty Automation umožňují izolovat vaše prostředky Automation (runbooky, assety, konfigurace) od prostředků, které jsou obsažené v jiných účtech Automation. Účty Automation můžete použít k oddělení prostředků do samostatných logických prostředí. Jeden účet můžete například použít pro vývoj, druhý k produkci a další pro svoje místní prostředí.  Účet Azure Automation se liší od účtu Microsoft a účtů vytvořených v rámci vašeho předplatného Azure.

Prostředky Automation jednotlivých účtů Automation jsou přidružené k jedné oblasti Azure, ale účty Automation mohou spravovat veškeré prostředky v rámci předplatného. Hlavním důvodem k vytvoření účtů Automation v různých oblastech by byla situace, kdy máte zásady, které vyžadují izolaci dat a prostředků v určité oblasti.

Všechny úlohy, které s prostředky provádíte pomocí správce Azure Resource Manager a rutin Azure ve službě Azure Automation, se musí ověřovat pro službu Azure pomocí ověření na základě přihlašovacích údajů organizační identity v Azure Active Directory.  Ověřování pomocí certifikátů bylo původní metodou ověřování pro Azure Classic, ale jeho nastavení bylo složité.  Ověřování v Azure pomocí uživatele Azure AD bylo znovu zavedeno v roce 2014, nejen kvůli zjednodušení procesu konfigurace ověřovacího účtu, ale také kvůli podpoře možnosti neinteraktivního ověření pro Azure pomocí jediného uživatelského účtu, který pracoval s nástrojem Azure Resource Manager a klasickými prostředky.   

Momentálně, když na portálu Azure vytvoříte nový účet Automation, účet automaticky vytvoří následující:

* Účet Spustit jako, který vytvoří nový instanční objekt v Azure Active Directory (certifikát) a přiřadí řízení přístupu na základě role (RBAC) Přispěvatel, které se používá ke správě prostředků Resource Manageru pomocí runbooků.
* Účet Spustit jako pro Classic tím, že nahraje certifikát pro správu, který se používá ke správě prostředků Azure Classic pomocí runbooků.  

Řízení přístupu na základě role je dostupné v aplikaci Azure Resource Manager pro udělování povolených akcí na uživatelském účtu služby Azure AD a účtu Spustit jako a ověřování takového objektu služby.  Přečtěte si článek [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md), kde najdete další informace, které vám pomůžou s vývojem vašeho modelu pro správu oprávnění ve službě Automation.  

Runbooky, které běží v procesu Hybrid Runbook Worker ve vašem datovém centru nebo s výpočetními službami v AWS, nemůžou používat stejné metody, které se obvykle používají pro runbooky ověřované pro prostředky Azure.  Důvodem je to, že tyto prostředky jsou spuštěné mimo Azure a proto vyžadují vlastní přihlašovací údaje zabezpečení definované ve službě Automation, aby se ověřovaly v prostředcích, ke přistupují místně.  

## <a name="authentication-methods"></a>Metody ověřování
Následující tabulka shrnuje různé metody ověřování pro jednotlivá prostředí podporovaná službou Azure Automation a články popisující, jak nastavit ověřování runbooků.

| Metoda | Prostředí | Článek |
| --- | --- | --- |
| Uživatelský účet Azure AD |Azure Resource Manager a Azure Classic |[Ověření runbooků pomocí uživatelského účtu Azure AD](automation-create-aduser-account.md) |
| Azure spuštěné jako účet |Azure Resource Manager |[Ověření runbooků pomocí účtu Spustit v Azure jako](automation-sec-configure-azure-runas-account.md) |
| Azure Classic spuštěné jako účet |Azure Classic |[Ověření runbooků pomocí účtu Spustit v Azure jako](automation-sec-configure-azure-runas-account.md) |
| Ověřování systému Windows |Místní datové centrum |[Ověření runbooků pro proces Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) |
| Přihlašovací údaje služby Amazon Web Services |Amazon Web Services |[Ověření runbooků pomocí Amazon Web Services (AWS)](automation-config-aws-account.md) |
