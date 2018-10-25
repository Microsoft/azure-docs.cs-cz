---
title: Azure AD Connect Health – data služby Health service není datum upozornění | Dokumentace Microsoftu
description: Tento dokument popisuje příčinu výstrahy "data služby Health service není aktuální." a jak řešit potíže se.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: e470a44732b881311eacecfdf2bd2211598d880a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984856"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Data služby Health service není aktuální výstrahy

## <a name="overview"></a>Přehled
<li>Azure AD Connect Health vyvolá data novou výstrahu, jestliže neobdrží všechny datové body ze serveru pro dvě hodiny. Název upozornění je **data služby Health service není aktuální**. </li>
<li>**Upozornění** výstraha o stavu je vyvoláno, pokud Connect Health přijme prvky částečná data ze serveru odesílají dvě hodiny. Stav upozornění neaktivuje e-mailová oznámení pro správce tenanta. </li>
<li>**Chyba** stav upozornění je vyvoláno, pokud Connect Health přijme všechny datové prvky odeslaných ze serveru pro dvě hodiny. Chyba stavu aktivuje upozornění e-mailová oznámení správce tenanta. </li>

>[!IMPORTANT] 
> Toto oznámení následuje Connect Health [zásady uchovávání dat](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Postup při řešení potíží 
* Ujistěte se, že se přenášejí prostřednictvím a vyhovět [části věnované požadavkům](how-to-connect-health-agent-install.md#requirements).
* Použití [nástroj pro testování připojení](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) ke zjišťování problémů s připojením.
* Pokud máte proxy server HTTP, postupujte prosím podle [kroky konfigurace zde](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

### <a name="connect-health-for-adfs"></a>Connect Health pro AD FS
Další kroky k ověření pro službu AD FS a pracovní postup v [nápovědy k AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

### <a name="data-collection-map-required-steps"></a>Shromažďování dat namapovat požadované kroky
| Název služby | Datových prvků | Postup při řešení potíží |
| --- | --- | --- | 
| Connect Health pro AD FS | PerfCounter TestResult | - [Odchozí připojení ke koncovému bodu služby Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Kontrola protokolu SSL pro odchozí provoz je filtrovaná nebo zakázaná](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porty brány firewall na serveru se spuštěným agentem](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Povolit určené websites, pokud je povoleno rozšířené zabezpečení Internet Exploreru](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | UsageMetrics služby AD FS | Odchozí připojení na základě IP adres najdete [rozsahy IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653) | 
| Connect Health pro synchronizaci | PerfCounter | - [Odchozí připojení ke koncovému bodu služby Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Kontrola protokolu SSL pro odchozí provoz je filtrovaná nebo zakázaná](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Porty brány firewall na serveru se spuštěným agentem](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Povolit určené websites, pokud je povoleno rozšířené zabezpečení Internet Exploreru](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | AadSyncService SynchronizationRules, <br /> AadSyncService konektorů <br /> AadSyncService GlobalConfigurations, <br /> AadSyncService RunProfileResults, <br /> AadSyncService ServiceConfigurations, <br /> AadSyncService ServiceStatus | -Odchozí připojení na základě IP adres najdete [rozsahy IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Odchozí připojení ke koncovému bodu služby Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Porty brány firewall na serveru se spuštěným agentem](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 
| Connect Health pro AD DS  | PerfCounter, přidá TopologyInfo-Json a běžné. TestData Json | - [Odchozí připojení ke koncovému bodu služby Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [Kontrola protokolu SSL pro odchozí provoz je filtrovaná nebo zakázaná](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porty brány firewall na serveru se spuštěným agentem](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Povolit určené websites, pokud je povoleno rozšířené zabezpečení Internet Exploreru](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) <br />  -Odchozí připojení na základě IP adres najdete [rozsahy IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653)  |




## <a name="next-steps"></a>Další postup
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
