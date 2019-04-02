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
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803285"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Data služby Health service není aktuální výstrahy

## <a name="overview"></a>Přehled
Agenty na místní počítače, které Azure AD Connect Health monitoruje pravidelně odesílá data do služby Azure AD Connect Health. Pokud služba nepřijímá data z agenta, bude zastaralé informace uvedené na portálu. Abyste měli na očích problém, se vyvolat službu **data služby Health service není aktuální** upozornění. To se vygeneruje, když služba neobdržel data dokončené v posledních dvou hodin.  

* **Upozornění** výstraha o stavu je vyvoláno, pokud služba Health Service obdržela pouze **částečné** datové typy odeslaných ze serveru za posledních dvou hodin. Stav upozornění neaktivuje e-mailová oznámení nakonfigurované příjemcům. 
* **Chyba** stav upozornění je vyvoláno, pokud služba Health Service nedostal žádné typy dat ze serveru v posledních dvou hodin. Chyba stavu aktivuje upozornění e-mailová oznámení nakonfigurované příjemcům.

Služba načte data z agentů, které běží na místní počítače. Následující tabulka uvádí v závislosti na typu služby agentů, které běží na počítači, co dělají a také datové typy, které jsou vygenerovány pomocí služby. V některých případech existuje několik služeb, které jsou součástí procesu, tak buď z nich může být nadměrné spotřeby. 

## <a name="understanding-the-alert"></a>Principy upozornění
V okně podrobností o výstraze označuje čas, když výstraha se vyvolá a naposledy zjištěno. Výstraha je vygenerována nebo jejich opakované-evaluated procesem na pozadí, který se spouští každé dvě hodiny. V následujícím příkladu se počáteční upozornění vygenerovalo v 03/10 v 9:59:00. Stále existují i na 03/12 10:00:00 po upozornění byla vyhodnocena jako znovu.
V okně také podrobnosti o čas, kdy byl naposled přijal určitého typu dat služba Health Service. 
 
 ![Podrobnosti výstrahy služby Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Níže je mapa typů služeb a odpovídající typ požadovaná data.

| Typ služby | Agent (název služby Windows) | Účel | Datový typ vygeneruje  |
| --- | --- | --- | --- |  
| Azure AD Connect (Sync) | Služba analýz synchronizace služby Azure AD Connect Health | Shromažďování konkrétních informací AAD Connect (konektory, synchronizační pravidla atd.) | AadSyncService-SynchronizationRules <br />  AadSyncService konektory <br /> AadSyncService-GlobalConfigurations  <br />  AadSyncService-RunProfileResults <br /> AadSyncService-ServiceConfigurations <br /> AadSyncService-ServiceStatus   |
|  | Služba monitorování synchronizace služby Azure AD Connect Health | Shromáždit soubory (službou AAD Connect konkrétní) čítače výkonu, trasování ETW | Čítač výkonu |
| AD DS | Služba analýz AD DS pro Azure AD Connect Health | Provádět syntetické testy, shromažďovat informace o topologii, replikační Metadata |  - Adds-TopologyInfo-Json <br /> Běžné-TestData-Json (vytvoří výsledky testů)   | 
|  | Služba monitorování AD DS pro Azure AD Connect Health | Shromáždit soubory čítače, trasování událostí pro Windows trasování výkonu (specifické pro AD DS) | – Čítač výkonu  <br /> Běžné-TestData-Json (odešle výsledky testů)  |
| AD FS | Diagnostické služby AD FS pro Azure AD Connect Health | Provádět syntetické testy | TestResult (vytvoří výsledky testů) | 
| | Služba analýz AD FS pro Azure AD Connect Health  | Shromažďovat metriky využití služby AD FS | Adfs-UsageMetrics |
| | Služba monitorování AD FS pro Azure AD Connect Health | Shromáždit soubory čítače, trasování událostí pro Windows trasování výkonu (specifické pro služby AD FS) | TestResult (odešle výsledky testů) |

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
