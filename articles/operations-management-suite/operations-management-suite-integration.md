---
title: Integrace s Operations Management Suite (OMS) | Microsoft Docs
description: Kromě použití standardní funkce OMS, můžete službu integrovat s dalšími aplikací pro správu a službami k poskytování správy hybridní prostředí, zadejte vlastní správu scénáře, které jsou jedinečné pro vaše prostředí nebo zadejte vlastní správy prostředí pro vaše zákazníky.  Tento článek obsahuje přehled různé možnosti pro integraci s odkazy na články, které poskytuje podrobné technické informace a OMS.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865859"
---
# <a name="integrating-with-operations-management-suite-oms"></a>Integrace s Operations Management Suite (OMS)
Služby Operations Management Suite je společnosti Microsoft založená na cloudu IT řešení správy, které pomáhá spravovat a chránit místní a cloudové infrastruktury.  Kromě použití standardní funkce OMS, můžete službu integrovat s dalšími aplikací pro správu a službami k poskytování správy hybridní prostředí, zadejte vlastní správu scénáře, které jsou jedinečné pro vaše prostředí nebo zadejte vlastní správy prostředí pro vaše zákazníky.  Tento článek obsahuje přehled různé možnosti pro integraci se službami OMS a odkazy na články, které poskytuje podrobné technické informace. 

## <a name="log-analytics"></a>Log Analytics
Správa data shromažďovaná společností analýzy protokolů je uložen v úložišti, který je hostován v Azure.  Všechna data uložená v úložišti je k dispozici ve vyhledávání protokolu, které poskytují rychlé analýzy napříč velmi velké objemy dat.  Požadavky na integraci může být k naplnění úložiště s nová data zpřístupnění pro analýzu nebo pro extrahování dat v úložišti zadejte novou vizualizaci a integrovat jiný nástroj pro správu.

Každá položka dat v úložišti je uloženo jako záznam.  Při naplňování úložiště by měl poskytovat uživatelům s typ záznamu, který používá vaše řešení a popis jeho vlastnosti.  Při načítání dat, musíte tyto informace o datech, kterou právě pracujete s.

![Plnění úložiště OMS](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Naplnění úložiště analýzy protokolů
Existuje více metod pro sestavování úložiště OMS.  Metodu, kterou zvolíte, bude záviset na faktorech, například kde se nachází zdroj dat, formát data a které budete potřebovat k podpoře klientů.  Jakmile data uložena v úložišti, nezáleží na tom, jak byl shromážděný.

Následující části popisují různé možnosti pro sestavování úložiště OMS.

#### <a name="connected-sources-and-data-sources"></a>Připojené zdroje a zdroje dat.
Umístění, kde můžete načíst data pro úložiště OMS jsou připojené zdroje.  Zdroje dat a řešení pro spuštění na připojené zdroje a definovat konkrétní data, která se shromažďují.  Pokud vaše aplikace zapisuje data do jednoho z těchto zdrojů dat, můžete ho shromažďování podle konfigurace zdroje dat.  Například pokud vaše aplikace vytvoří události procesu Syslog, pak se můžou shromažďovat zdroj dat Syslog na agenta systému Linux.

* [Zdroje dat v analýzy protokolů](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Řešení
Řešení rozšiřují možnosti OMS.  Řešení může shromažďovat data z připojeného zdroje nebo jej může provádět analýzy na záznamy již shromážděné v úložišti.  Každé řešení od společnosti Microsoft má jednotlivých článek, který obsahuje podrobné informace na data, která shromažďuje.

* [Řešení v analýzy protokolů](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>Sběrač dat protokolu HTTP rozhraní API
Log Analytics HTTP dat kolekce API je rozhraní REST API, která umožňuje přidat JSON data do úložiště analýzy protokolů.  Toto rozhraní API můžete využít, když máte aplikaci, která neposkytuje data prostřednictvím jednoho z jiných zdrojů dat nebo řešení.  Slouží k naplnění úložiště z libovolného klienta, který můžete volat rozhraní API a nespoléhá se na plán kolekce všechny zdroje dat nebo řešení.

* [Kolektor protokolů analýzy dat protokolu HTTP rozhraní API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Načtení dat z úložiště analýzy protokolů
Existuje několik metod načítání dat z úložiště OMS.  Můžete mají uživatelé k načtení dat pomocí konzole OMS a poskytují různé druhy vizualizaci a analýzu.  Můžete také načíst data z externího procesu například jiné řešení pro správu.

#### <a name="log-searches"></a>Protokol hledání
Všechna data uložená v úložišti OMS je k dispozici prostřednictvím protokolu hledání.  Uživatelé mohou provádět vlastní analýz ad hoc v konzole OMS nebo vytvořit řídicí panel s vizualizací pro konkrétní protokolu vyhledávání.  Řešení může obsahovat vlastní zobrazení s vizualizacemi podle předdefinovaných hledání.  Rozhraní API pro vyhledávání protokolu můžete použít pro přístup k datům v úložišti OMS z externí aplikace nebo správu nástroj.  

* [Protokol hledání v analýzy protokolů](../log-analytics/log-analytics-log-searches.md)
* [Hledání protokolu analýzy protokolů REST API](../log-analytics/log-analytics-log-search-api.md)
* [Rutiny analýzy protokolů](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Vlastní zobrazení
Návrhář zobrazení umožňuje vytvářet vlastní zobrazení v konzole OMS, která uživatelům poskytnout vizualizaci a analýzu dat ve vašem řešení.  Každé zobrazení zahrnuje dlaždice, který se zobrazí na hlavní stránce konzole a libovolný počet částí vizualizace, které jsou založené na protokolu hledání, které definujete.

* [Návrhář zobrazení analýzy protokolů](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Analýzy protokolů můžete automaticky exportovat data z úložiště OMS do Power BI, můžete využít jeho vizualizace a nástrojů pro analýzu.  Provede export podle plánu, tak data je pořád aktuální. 

* [Exportovat data analýzy protokolů do Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automation
OMS můžete automatizovat procesy reagování na shromážděná data nebo provádět další funkce správy.  Může shromažďovat data z vaší aplikace a vložte ji do OMS úložiště, nebo může automatizovat oprava známý problém na základě dat v úložišti nalezen. 

![Automation](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooky
Runbooky ve službě Azure Automation spustit skripty prostředí PowerShell a pracovních postupů v cloudu Azure.  Můžete je používat ke správě prostředků v Azure nebo jiným prostředkům, které jsou přístupné z cloudu.  Sady Runbook lze také spustit v místním datacentru pomocí hybridní pracovní proces Runbooku.  Sady runbook můžete spustit z portálu Azure nebo z externí procesů pomocí metody, jako je například PowerShell nebo rozhraní API automatizace.

* [Spuštění sady runbook ve službě Azure Automation](../automation/automation-starting-a-runbook.md)
* [Rutiny Azure Automation](https://msdn.microsoft.com/library/dn690262.aspx)
* [Rozhraní API REST automatizace](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automatizace rozhraní .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Výstrahy
Pravidla výstrah automaticky spouštět protokolu hledání podle plánu.  Pokud výsledky odpovídají konkrétním kritériím výsledné výstrahy můžete spuštění sady runbook ve službě Azure Automation nebo volat webhook, který můžete spustit externího procesu.  Obě tyto odpovědí může obsahovat podrobnosti o výstraze, včetně s daty vrácenými do protokolu vyhledávání.

* [Výstrahy v analýzy protokolů](../log-analytics/log-analytics-alerts.md)
* [Log Analytics výstrahy API](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Zálohování a obnovení lokality
Azure Site Recovery a Backup poskytují služby pro ochranu podnikových dat a zajištění dostupnosti serverů a aplikací.  Můžete využít tyto služby k provedení takových scénářů, jako poskytují zálohování služby pro vaši aplikaci nebo zahájení převzetí služeb při selhání virtuálního počítače.

* [Rutiny Azure Backup](https://msdn.microsoft.com/library/mt619253.aspx)
* [Azure Site Recovery rozhraní REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Rutiny Azure Site Recovery](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Vlastní řešení
Integrace logiku může zapouzdřit do vlastní řešení pro spuštění v pracovním prostoru nebo v pracovním prostoru zákazníka.  Řešení může zahrnovat kteroukoliv metodu integrace v tomto článku kromě jiných prostředky k zajištění scénář dokončení správy.  Prostředky v řešení jsou zabalené tak, že pokud je odebrán řešení, všechny prostředky, které vytvořil se odeberou z pracovní prostor OMS a předplatné Azure.

Řešení může obsahovat třeba runbook služby automatizace shromažďovat a zpracovávat data a pak naplnit úložiště analýzy protokolů pomocí rozhraní API sady kolekcí dat protokolu HTTP.  Může také obsahovat vlastní zobrazení, které představuje a analyzuje shromážděná data.  

* Vytváření vlastních řešení (už brzy)    

## <a name="next-steps"></a>Další kroky
* Odkaz [OMS SDK](operations-management-suite-sdk.md) technické informace o automatizaci služby OMS.  

