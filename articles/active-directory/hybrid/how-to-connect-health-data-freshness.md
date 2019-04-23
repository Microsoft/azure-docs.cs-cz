---
title: Azure AD Connect Health – data služby Health service není datum upozornění | Dokumentace Microsoftu
description: Tento dokument popisuje příčinu výstrahy "data služby Health service není aktuální." a jak řešit potíže se.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ffd783ec41b1b0c4a11ee426648c1e36fbbbf75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60349890"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Data služby Health service není aktuální výstrahy

## <a name="overview"></a>Přehled

Agents na vhodnost místních počítačů, které Azure AD Connect Health monitoruje pravidelně nahrávat data do službu Azure AD Connect Health. Pokud služba nepřijímá data z agenta, bude zastaralé informace, které se zobrazí na portálu. Abyste měli na očích problém, se vyvolat službu **data služby Health service není aktuální** upozornění. Tato výstraha se vygeneruje, když služba neobdržel kompletní data za poslední dvě hodiny.  

- **Upozornění** výstraha o stavu je vyvoláno, pokud služba Health Service obdržela pouze **částečné** datové typy odeslaných ze serveru za posledních dvou hodin. Stav upozornění neaktivuje e-mailová oznámení nakonfigurované příjemcům. 
- **Chyba** stav upozornění je vyvoláno, pokud služba Health Service nedostal žádné typy dat ze serveru za poslední dvě hodiny. Chyba stavu aktivuje upozornění e-mailová oznámení nakonfigurované příjemcům.

Služba načte data z agentů, které běží na místních počítačích, v závislosti na typu služby. V následující tabulce jsou uvedeny agenty, které běží na počítači, co dělají a datové typy, které generuje služba. V některých případech se několik služeb, které využívá řada v procesu, tak některé z nich může být nadměrné spotřeby. 

## <a name="understanding-the-alert"></a>Principy upozornění

**Podrobnosti výstrahy** okno při výstrahy došlo k chybě a byl naposledy zjištěno. Proces na pozadí, který spouští každé dvě hodiny generuje a znovu vyhodnotí upozornění. V následujícím příkladu, počáteční došlo k výstraze na 03/10 v 9:59:00. Výstraha se pořád byly na 03/12 v 10:00 po upozornění byla vyhodnocena jako znovu. V okně také podrobnosti o čas, služba Health Service naposled přijal konkrétní datový typ. 
 
 ![Podrobnosti výstrahy služby Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Následující tabulka mapuje na odpovídající typy požadované datové typy služeb:

| Typ služby | Agent (název služby Windows) | Účel | Datový typ vygeneruje  |
| --- | --- | --- | --- |  
| Azure AD Connect (Sync) | Služba analýz synchronizace služby Azure AD Connect Health | Shromažďovat informace specifické pro službu AAD Connect (konektory, synchronizační pravidla, atd.) | AadSyncService-SynchronizationRules <br />  AadSyncService konektory <br /> AadSyncService-GlobalConfigurations  <br />  AadSyncService-RunProfileResults <br /> AadSyncService-ServiceConfigurations <br /> AadSyncService-ServiceStatus   |
|  | Služba monitorování synchronizace služby Azure AD Connect Health | Shromáždit čítače výkonu specifické pro službu AAD Connect, trasování ETW, soubory | Počítadlo výkonu |
| AD DS | Služba analýz AD DS pro Azure AD Connect Health | Provádět syntetické testy, shromažďovat informace o topologii, replikační metadata |  - Adds-TopologyInfo-Json <br /> Běžné-TestData-Json (vytvoří výsledky testů)   | 
|  | Služba monitorování AD DS pro Azure AD Connect Health | Shromáždit čítače výkonu specifické pro AD DS, trasování ETW, soubory | – Čítač výkonu  <br /> Běžné-TestData-Json (odešle výsledky testů)  |
| AD FS | Diagnostické služby AD FS pro Azure AD Connect Health | Provádět syntetické testy | TestResult (vytvoří výsledky testů) | 
| | Služba analýz AD FS pro Azure AD Connect Health  | Shromažďovat metriky využití služby AD FS | Adfs-UsageMetrics |
| | Služba monitorování AD FS pro Azure AD Connect Health | Shromáždit čítače výkonu specifické pro služby AD FS, trasování ETW, soubory | TestResult (odešle výsledky testů) |

## <a name="troubleshooting-steps"></a>Postup při řešení potíží 

Kroky potřebné k diagnostice problému je uveden níže. První je sada základní kontroly, které jsou společné pro všechny typy služeb. Následující tabulka, která obsahuje seznam konkrétní kroky pro každý typ služby a datovým typem. 

> [!IMPORTANT] 
> Toto oznámení následuje Connect Health [zásady uchovávání dat](reference-connect-health-user-privacy.md#data-retention-policy)

* Ujistěte se, že jsou nainstalovány nejnovější verze agentů. Zobrazení [historie vydaných verzí](reference-connect-health-version-history.md). 
* Ujistěte se, že jsou agenti Azure AD Connect Health služby **systémem** na počítači. Connect Health pro AD FS by měl mít například tři služby.
  ![Ověření Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Ujistěte se, že se přenášejí prostřednictvím a vyhovět [části věnované požadavkům](how-to-connect-health-agent-install.md#requirements).
* Použití [nástroj pro testování připojení](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) ke zjišťování problémů s připojením.
* Pokud máte proxy server HTTP, postupujte podle těchto [kroky konfigurace](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Další postup
Pokud některý z výše uvedených kroků identifikovat problém, opravte ji a počkejte výstrahu, kterou chcete vyřešit. Proces oznámení na pozadí běží každé dvě hodiny, bude trvat až 2 hodiny tuto výstrahu vyřešíte. 

* [Zásady uchovávání dat služby Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
