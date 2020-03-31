---
title: Azure AD Connect Health – data zdravotní služby nejsou aktuální výstraha | Dokumenty společnosti Microsoft
description: Tento dokument popisuje příčinu výstrahy "Data zdravotní služby nejsou aktuální" a jak je řešit.
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
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897180"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Data zdravotní služby nejsou aktuální.

## <a name="overview"></a>Přehled

Agenti v místních počítačích, které Azure AD Connect Health monitoruje pravidelně nahrazovat data do služby Azure AD Connect Health. Pokud služba nepřijímá data od agenta, informace, které portál zobrazí, budou zastaralé. Chcete-li tento problém zvýraznit, služba zvýší **zdravotní služby data není aktuální** výstraha. Tato výstraha je generována, pokud služba neobdržela úplná data v posledních dvou hodinách.  

- **Upozornění upozornění** upozornění se aktivuje, pokud služba Health Service obdržela pouze **částečné** datové typy odeslané ze serveru v posledních dvou hodinách. Upozornění na stav upozornění neaktivuje e-mailová oznámení nakonfigurovaným příjemcům. 
- Výstraha **stavu chyby** se aktivuje, pokud služba Health Service neobdržela žádné datové typy ze serveru v posledních dvou hodinách. Výstraha stavu chyby aktivuje e-mailová oznámení nakonfigurovaným příjemcům.

Služba získá data od agentů, které jsou spuštěny na místních počítačích, v závislosti na typu služby. V následující tabulce jsou uvedeny agenty, kteří běží v počítači, co dělají, a datové typy, které služba generuje. V některých případech existuje více služeb zapojených do procesu, takže některý z nich by mohl být viníkem. 

## <a name="understanding-the-alert"></a>Principy výstrahy

Okno **Podrobnosti výstrahy** zobrazuje, kdy došlo k výstraze a kdy byla naposledy zjištěna. Proces na pozadí, který běží každé dvě hodiny generuje a přehodnocuje výstrahu. V následujícím příkladu došlo k počáteční výstrahy na 03/10 v 9:59. Výstraha stále existovala dne 03/12 v 10:00, kdy byla výstraha znovu vyhodnocena. Okno také podrobně popisuje čas, kdy služba Health Service naposledy obdržela určitý datový typ. 
 
 ![Podrobnosti výstrahy azure a připojení připojit stav](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Následující tabulka mapuje typy služeb na odpovídající požadované datové typy:

| Typ služby | Agent (název služby systému Windows) | Účel | Generovaný datový typ  |
| --- | --- | --- | --- |  
| Azure AD Connect (synchronizace) | Služba analýz synchronizace služby Azure AD Connect Health | Shromažďovat informace specifické pro připojení AAD (konektory, pravidla synchronizace atd.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Konektory <br /> - AadSyncService-GlobalKonfigurace  <br />  - AadSyncService-RunProfileResults <br /> - Konfigurace služby AadSyncService <br /> - AadSyncService-ServiceStatus   |
|  | Služba monitorování synchronizace služby Azure AD Connect Health | Sbírat AAD Connect-specifické perf čítače, ETW stopy, soubory | Čítač výkonu |
| AD DS | Služba analýz AD DS pro Azure AD Connect Health | Provádění syntetických testů, shromažďování informací o topologii, metadata replikace |  - Přidává-TopologyInfo-Json <br /> - Common-TestData-Json (vytvoří výsledky testů)   | 
|  | Služba monitorování AD DS pro Azure AD Connect Health | Sbírat čítače perf specifické pro přidání, etw stopy, soubory | - Čítač výkonu  <br /> - Common-TestData-Json (nahraje výsledky testů)  |
| AD FS | Diagnostické služby AD FS pro Azure AD Connect Health | Provádění syntetických testů | TestResult (vytvoří výsledky testu) | 
| | Služba analýz AD FS pro Azure AD Connect Health  | Shromažďujte metriky využití systému ADFS | Adfs-UsageMetrics |
| | Služba monitorování AD FS pro Azure AD Connect Health | Shromažďujte čítače perf specifické pro ADFS, stopy ETW, soubory | TestResult (nahraje výsledky testu) |

## <a name="troubleshooting-steps"></a>Postup při řešení potíží 

Kroky potřebné k diagnostice problému jsou uvedeny níže. První je sada základních kontrol, které jsou společné pro všechny typy služeb. 

> [!IMPORTANT] 
> Tato výstraha se řídí zásadami uchovávání dat připojení [stavu.](reference-connect-health-user-privacy.md#data-retention-policy)

* Ujistěte se, že jsou nainstalovány nejnovější verze agentů. Zobrazit [historii verzí](reference-connect-health-version-history.md). 
* Ujistěte se, že služby Azure AD Connect Health Agents **běží** na počítači. Například Connect Health pro službu AD FS by měl mít tři služby.
  ![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Ujistěte se, že jít přes a splňují [požadavky části](how-to-connect-health-agent-install.md#requirements).
* Pomocí [nástroje test připojení](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) zjišťujte problémy s připojením.
* Pokud máte proxy server HTTP, postupujte podle těchto [kroků konfigurace](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Další kroky
Pokud některý z výše uvedených kroků identifikoval problém, opravte jej a počkejte, až výstraha vyřeší. Proces na pozadí výstrahy se spouští každé 2 hodiny, takže vyřešení výstrahy bude trvat až 2 hodiny. 

* [Zásady uchovávání dat stavu Azure AD Connect](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
