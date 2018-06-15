---
title: Výstrahy správy v aplikaci Microsoft monitoring produkty | Microsoft Docs
description: Výstraha naznačuje některé problém, který vyžaduje pozornost správce.  Tento článek popisuje rozdíly v tom, jak jsou výstrahy vytvořit a spravovat v System Center Operations Manager (SCOM) a analýzy protokolů a obsahuje osvědčené postupy v využívat tyto dva produkty pro správu výstrah strategie hybridní.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865768"
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Správa výstrah s monitorování Microsoft
Výstraha naznačuje některé problém, který vyžaduje pozornost správce.  Existují významné rozdíly mezi System Center Operations Manager (SCOM) a analýzy protokolů v Operations Management Suite (OMS) z hlediska vytváření výstrah, jak jsou spravovaná a analyzovat a jak jsou oznámit, že kritický problém byl zjištěna.

## <a name="alerts-in-operations-manager"></a>Výstrahy v nástroji Operations Manager
Výstrahy v aplikaci SCOM jsou generovány podle jednotlivých pravidel nebo monitorování k označení určitého problému.  Monitorování mohou generovat výstrahu, když vstupuje do chybového stavu, zatímco pravidla mohou generovat výstrahu označíte některé důležité problém, který přímo nesouvisí se stav spravovaného objektu.  Sady Management Pack zahrnují různé pracovní úlohy, která vytváří výstrahy pro aplikaci nebo službu, která spravují.  Součástí procesu konfigurace nové sady management pack je ladění k zajištění, že jste neobdrželi nadměrného výstrahy pro problémy, které nejsou považujete za důležité.

![Zobrazení výstrah SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM poskytuje kompletní správu výstrah s výstrahami, které mají status, která lze změnit správci, jak fungují k vyřešení problému.  Když byl problém vyřešen, nastaví správce výstrahu uzavřen, po kterém se nebude zobrazovat v zobrazeních zobrazení aktivní výstrahy.  Výstrahy, které jsou generované z monitorování lze vyřešit automaticky, když se sledování vrátí do stavu v pořádku.

![Stav výstrahy](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Výstrahy v analýzy protokolů
Výstrahy v analýzy protokolů je vytvořen z protokolu dotazu, který se automaticky spouští v pravidelných intervalech.  Pravidlo výstrahy můžete vytvořit z jakékoli protokolu dotazu.  Pokud dotaz vrátí výsledky, které splňují kritéria, která jste zadali, se vytvoří výstraha.  To může být konkrétní dotaz, který vytvoří výstrahu, pokud je zjištěna určitá událost, nebo můžete použít obecnější dotaz, který hledá všechny chybové události související s konkrétní aplikace.

Výstrahy Analytics protokolu se zapisují do úložiště OMS jako událost a můžete načíst pomocí dotazu protokolu.  Nemají stav jako SCOM události tak, aby můžete určit, pokud byl problém vyřešen.

![Výstraha OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Při SCOM slouží jako zdroj dat pro analýzy protokolů, SCOM výstrahy se zapisují do úložiště OMS jako jejich jsou vytvářena a upravována.  

![Výstraha SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[Řešení pro správu výstrah](http://technet.microsoft.com/library/mt484092.aspx) poskytuje souhrn aktivní výstrahy a několik běžných dotazů k načtení různé sady výstrahy.  To poskytuje efektivnější analýzu upozornění než sestavu v aplikaci SCOM.  Můžete přejít na nižší úroveň z souhrny na podrobná data a vytvořit ad hoc dotazy k načtení různé sady výstrahy.

![Řešení pro správu výstrah](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Oznámení
Oznámení v aplikaci SCOM odesílat e-mailu nebo textu v reakci na výstrahy, které odpovídají konkrétním kritériím.  Můžete vytvořit odběrů různých oznámení, které mají jiné osoby upozornění v závislosti na kritérií, jako například objekt, který je monitorován, závažnost výstrahy, druh problému, který zjistit nebo čas.

Několik předplatných lze použít k implementaci strategie dokončení oznámení pro velký počet sad management Pack.

![Výstrahy SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Analýzy protokolů vás může upozornit, prostřednictvím e-mailu že výstraha byla vytvořena nastavením akce oznámení e-mailu na každém [pravidlo výstrahy](http://technet.microsoft.com/library/mt614775.aspx).  Nemá schopnost SCOM přihlásit k odběru více výstrah s jedním pravidlem.  Také musíte vytvořit vlastní pravidla výstrah, protože OMS neposkytuje žádné předem nakonfigurován.

![Výstrahy Log Analytics](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Nelze spravovat zcela SCOM výstrahy v analýzy protokolů ale vzhledem k tomu, můžete je upravit pouze v konzoli Operations Console.  Analýzy protokolů je užitečné jako součást výstrahy správu zpracovat, když pro poskytování nástrojů pro analýzu této SCOM samostatně nemá.

## <a name="alert-remediation"></a>Výstrahy nápravy
[Náprava](http://technet.microsoft.com/library/mt614775.aspx) odkazuje na pokus o automaticky opravit problém identifikovaný výstrahu.

SCOM umožňuje spouštět diagnostiku a obnovení v reakci na monitorování zadávání stav není v pořádku.  To se stane souběžných monitorování, vytváření výstrahy.  Diagnostiky a obnovení jsou obvykle implementovány jako skriptu, který běží na agentovi.  Diagnostika se pokusí získat další informace o zjištěném problému při obnovení se pokusí problém vyřešit.

Analýzy protokolů umožňuje spouštět [runbook automatizace Azure](https://azure.microsoft.com/documentation/services/automation/) nebo volat webhook, jehož v reakci na výstrahy analýzy protokolů.  Sady Runbook může obsahovat komplexní logiku implementována v prostředí PowerShell.  Skript přístup všechny prostředky Azure nebo externím prostředkům, které jsou k dispozici z cloudu a spuštění v Azure.  Automatizace Azure mají spouštění sad runbook na serveru ve vašem místním datacentru, ale tato funkce není aktuálně k dispozici, při spuštění sady runbook v reakci na výstrahy analýzy protokolů.

Obnovení v aplikaci SCOM i sady runbook v OMS mohou obsahovat skripty prostředí PowerShell, ale obnovení je složité vytvořit a spravovat, protože musí být obsažena v sadě management pack.  Sady Runbook jsou uložené ve službě Azure Automation, který poskytuje funkce pro vytváření, testování a správu sad runbook.

Pokud používáte SCOM jako zdroj dat pro analýzu protokolu, můžete vytvořit upozornění na analýzy protokolů pomocí protokolu dotazu na načtení výstrah SCOM uložené v úložišti OMS.  To by umožnilo spuštění runbooku automatizace Azure v reakci na výstrahy SCOM.  Samozřejmě vzhledem k tomu, že sada runbook poběží v Azure, to nebude přijatelná strategie pro obnovení místního problémy.

## <a name="next-steps"></a>Další kroky
* Další podrobnosti o [výstrahy v System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).

