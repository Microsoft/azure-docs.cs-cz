---
title: Azure AD Connect Health – data služby Health Service nejsou aktuální upozornění | Microsoft Docs
description: Tento dokument popisuje příčinu upozornění "data služby Health Service nejsou aktuální" a jejich řešení.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d1d87d9b576a8e181b5b339052a6b6512f18a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85359224"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Data služby Health Service nejsou aktuální. upozornění

## <a name="overview"></a>Přehled

Agenti na místních počítačích, které Azure AD Connect Health monitoruje pravidelně nahrávají data do služby Azure AD Connect Health. Pokud služba neobdrží data od agenta, informace, které portál prezentuje, budou zastaralé. Chcete-li tento problém zvýraznit, služba vyvolá **data služby Health Service neaktuální** výstrahy. Tato výstraha se vygeneruje, když služba nedostala dokončená data za poslední dvě hodiny.  

- Výstraha stav **Upozornění** se aktivuje, pokud Health Service v posledních dvou hodinách přijaly jenom **částečné** datové typy odeslané ze serveru. Upozornění na stav upozornění neaktivuje e-mailová oznámení pro konfigurované příjemce. 
- Výstraha o stavu **chyby** se aktivuje, pokud Health Service nedostal žádné datové typy ze serveru za poslední dvě hodiny. Výstraha o stavu chyby aktivuje e-mailová oznámení pro konfigurované příjemce.

Služba získá data z agentů, kteří běží na místních počítačích v závislosti na typu služby. Následující tabulka uvádí agenty, které jsou spuštěny v počítači, co dělají a typy dat, které služba generuje. V některých případech se v procesu účastní více služeb, takže některý z nich může být příčinou. 

## <a name="understanding-the-alert"></a>Princip upozornění

V okně **Podrobnosti výstrahy** se dozvíte, kdy došlo k upozornění a byla naposledy zjištěna. Proces na pozadí, který se spouští každé dvě hodiny, vygeneruje a znovu vyhodnotí výstrahu. V následujícím příkladu se počáteční výstraha vyskytla v 03/10.9:59. Výstraha ještě existovala v 03/12 v 10:00 až do vyhodnocení výstrahy. V okně se zobrazí také informace o čase, kdy Health Service naposledy přijal konkrétní datový typ. 
 
 ![Podrobnosti výstrahy Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Následující tabulka mapuje typy služby na odpovídající požadované datové typy:

| Typ služby | Agent (název služby systému Windows) | Účel | Generovaný datový typ  |
| --- | --- | --- | --- |  
| Azure AD Connect (synchronizace) | Služba analýz synchronizace služby Azure AD Connect Health | Shromažďování informací specifických pro AAD Connect (konektory, synchronizační pravidla atd.) | -AadSyncService-SynchronizationRules <br />  -AadSyncService-Connectors <br /> -AadSyncService-GlobalConfigurations  <br />  -AadSyncService-RunProfileResults <br /> -AadSyncService-ServiceConfigurations <br /> -AadSyncService-ServiceStatus   |
|  | Služba monitorování synchronizace služby Azure AD Connect Health | Shromažďování čítačů výkonu pro AAD Connect, trasování ETW, soubory | Čítač výkonu |
| AD DS | Služba analýz AD DS pro Azure AD Connect Health | Provádět syntetické testy, shromažďovat informace o topologii, metadata replikace |  -Přidat-TopologyInfo-JSON <br /> -Common-TestData-JSON (vytvoří výsledky testu)   | 
|  | Služba monitorování AD DS pro Azure AD Connect Health | Shromažďování čítačů výkonu specifických pro přidání, trasování ETW, soubory | – Čítač výkonu  <br /> -Common-TestData-JSON (odesílá výsledky testu)  |
| AD FS | Diagnostické služby AD FS pro Azure AD Connect Health | Provádět syntetické testy | TestResult (vytvoří výsledky testu) | 
| | Služba analýz AD FS pro Azure AD Connect Health  | Shromažďovat metriky využití služby AD FS | Adfs-UsageMetrics |
| | Služba monitorování AD FS pro Azure AD Connect Health | Shromažďování čítačů výkonu specifických pro AD FS, trasování ETW, soubory | TestResult (nahrává výsledky testu) |

## <a name="troubleshooting-steps"></a>Postup při řešení potíží 

Postup potřebný k diagnostice tohoto problému je uveden níže. První je sada základních kontrol, které jsou společné pro všechny typy služeb. 

> [!IMPORTANT] 
> Tato výstraha následuje po připojení [zásady uchovávání dat](reference-connect-health-user-privacy.md#data-retention-policy) stavu.

* Ujistěte se, že jsou nainstalované nejnovější verze agentů. Projděte si [historii vydaných verzí](reference-connect-health-version-history.md). 
* Ujistěte se, že jsou na počítači **spuštěné** služby agentů Azure AD Connect Health. Například služba Connect Health pro AD FS by měla mít tři služby.
  ![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Ujistěte se, že překročíte a splňujete [část požadavky](how-to-connect-health-agent-install.md#requirements).
* Pomocí [nástroje test Connectivity](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) můžete zjistit problémy s připojením.
* Pokud máte proxy server HTTP, postupujte podle těchto [kroků konfigurace](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Další kroky
Pokud některý z výše uvedených kroků identifikoval problém, vyřešte ho a počkejte na vyřešení výstrahy. Proces na pozadí výstrahy se spouští každé 2 hodiny, takže bude trvat až 2 hodiny, než se výstraha vyřeší. 

* [Azure AD Connect Health zásady uchovávání dat](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
