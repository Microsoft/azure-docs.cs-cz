---
title: Protokolu analýzy funkce pro poskytovatele služeb | Microsoft Docs
description: Analýzy protokolů může pomoci spravovat poskytovatelé služeb (MSPs), velké podniky nezávislí výrobci softwaru (ISV) a poskytovatele hostitelských služeb, spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 6934e92df562099122eaede39fd26cf51cf1ee44
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593045"
---
# <a name="log-analytics-features-for-service-providers"></a>Funkce analýzy protokolů pro poskytovatele služeb
Analýzy protokolů může pomoct zprostředkovatelé spravované služby (MSPs), velké podniky, nezávislí dodavatelé softwaru (ISV) a poskytovatele hostitelských služeb spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu. 

Velké podniky sdílet mnoho společného s poskytovatelé služeb, zejména v případě, že je centralizované IT tým, který je zodpovědný za správu IT pro mnoho různých podnikových jednotek. Pro jednoduchost, tento dokument používá termín *poskytovatele služeb* ale stejné funkce je také k dispozici pro podniky a dalších zákazníků.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider
Pro partnery a poskytovatelé, kteří jsou součástí služby [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) programu, analýzy protokolů je jednou ze služeb Azure k dispozici v [předplatného poskytovatele CSP Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

Pro analýzy protokolů jsou povoleny následující možnosti v *poskytovatele Cloud Solution Provider* odběry.

Jako *poskytovatele Cloud Solution Provider* můžete:

* Vytváření pracovních prostorů analýzy protokolů v rámci předplatného klienta (zákazníka).
* Pracovní prostory přístup vytvořené klienty. 
* Přidání a odebrání uživatelům přístup k pracovním prostoru pomocí správy Azure uživatelů. Když v pracovním prostoru klienta na portálu OMS Správa uživatelů stránky v části nastavení není k dispozici
  * Analýzy protokolů nepodporuje přístupu podle rolí ještě - poskytnutí uživatele `reader` oprávnění na portálu Azure vám umožní provádět změny konfigurace na portálu OMS

Přihlásit se k předplatnému klienta, budete muset zadat identifikátor klienta. Identifikátor klienta je často poslední část e-mailovou adresu použili k přihlášení.

* Na portálu OMS přidat `?tenant=contoso.com` v adrese URL pro portál. Například `mms.microsoft.com/?tenant=contoso.com`.
* V prostředí PowerShell, použijte `-Tenant contoso.com` parametr při použití `Connect-AzureRmAccount` rutiny
* Identifikátor klienta je automaticky přidáno, jakmile použijete `OMS portal` odkaz z portálu Azure otevřete a přihlaste se k portálu OMS pro vybraný pracovní prostor

Jako *zákazníka* z poskytovatele cloudových řešení, můžete:

* Vytvořit protokol analýzy pracovní prostory v předplatném zprostředkovatele kryptografických služeb
* Pracovní prostory přístup vytvořené CSP
  * Použití `OMS portal` odkaz z portálu Azure otevřete a přihlaste se k portálu OMS pro vybraný pracovní prostor
* Zobrazení a používání stránku správy uživatelů v části nastavení na portálu OMS

> [!NOTE]
> Součástí řešení zálohování a obnovení lokality pro analýzy protokolů nejsou možné se připojit k trezoru služeb zotavení a nejde nakonfigurovat v předplatného poskytovatele CSP. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Správa více zákazníků pomocí analýzy protokolů
Doporučuje se vytvořit pracovní prostor analýzy protokolů pro každého zákazníka, které spravujete. Pracovní prostor analýzy protokolů poskytuje:

* Geografické umístění pro data k uložení. 
* Členitost fakturace 
* Izolaci dat 
* Jedinečné konfigurace

Vytvořením pracovního prostoru pro zákazníka, budete moci oddělit data každého zákazníka a také sledovat využití každého zákazníka.

Další informace o kdy a proč vytvořit několik pracovních prostorů je popsaná v [spravovat přístup k protokolu analýzy](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Vytvoření a konfigurace pracovních prostorů zákazníka je možné automatizovat pomocí [prostředí PowerShell](log-analytics-powershell-workspace-configuration.md), [šablony Resource Manageru](log-analytics-template-workspace-configuration.md), nebo pomocí [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

Použití šablon Resource Manageru pro konfiguraci prostoru umožňuje hlavní konfigurace, které je možné vytvořit a nakonfigurovat pracovní prostory. Můžete si být jisti, že pracovní prostory, které jsou vytvořeny pro zákazníky, jsou automaticky nakonfigurované vašim požadavkům. Při aktualizaci požadavků na šabloně se aktualizuje a pak ho znovu použít stávající pracovní prostory. Tento proces zajišťuje, že i existující pracovní prostory splňují vaše standardy nové.    

Při správě více analýzy protokolů pracovních prostorů, doporučujeme integraci do stávajícího systému systém lístků podpory každém pracovním prostoru / pomocí konzole operations console [výstrahy](log-analytics-alerts.md) funkce. Díky integraci do stávajících systémů, můžete pokračovat podle jejich známých procesy pracovníky technické podpory. Analýzy protokolů pravidelně kontroluje každý pracovní prostor proti kritéria výstrahy, které určíte a vygeneruje výstrahu, když akce je vyžadována.

Přizpůsobené zobrazení dat, použijte [řídicí panel](../azure-portal/azure-portal-dashboards.md) funkce na portálu Azure.  

Pro executive úrovni sestavy, který shrnout data napříč pracovních prostorů můžete integrace mezi analýzy protokolů a [PowerBI](log-analytics-powerbi.md). Pokud potřebujete integrovat jinému systému pro vytváření sestav, můžete použít rozhraní API služby Search (pomocí prostředí PowerShell nebo [REST](log-analytics-log-search-api.md)) ke spouštění dotazů a exportovat výsledky hledání.

## <a name="next-steps"></a>Další kroky
* Automatizovat vytvoření a konfigurace pomocí pracovních prostorů [šablony Resource Manageru](log-analytics-template-workspace-configuration.md)
* Automatizovat vytváření pracovních prostorů pomocí [prostředí PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Použití [výstrahy](log-analytics-alerts.md) pro integraci s existujícími systémy
* Souhrnné sestavy pomocí [PowerBI](log-analytics-powerbi.md)

