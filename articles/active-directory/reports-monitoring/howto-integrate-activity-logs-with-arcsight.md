---
title: Integrace protokolů s ArcSight pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Zjistěte, jak integrovat protokoly služby Azure Active Directory s ArcSight pomocí Služby Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05002c1b11ef31b61fb4036f09dc8edcdafca767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75608376"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrace protokolů služby Azure Active Directory s ArcSight pomocí Služby Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) je řešení pro správu informací o zabezpečení a událostí (SIEM), které vám pomůže detekovat bezpečnostní hrozby na vaší platformě a reagovat na ně. Teď můžete směrovat protokoly Azure Active Directory (Azure AD) do ArcSight pomocí Azure Monitor pomocí konektoru ArcSight pro Azure AD. Tato funkce umožňuje sledovat klienta pro ohrožení zabezpečení pomocí ArcSight.  

V tomto článku se dozvíte, jak směrovat protokoly Azure AD do ArcSight pomocí Azure Monitoru. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Centrum událostí Azure, které obsahuje protokoly aktivit Azure AD. Přečtěte si, jak [streamovat protokoly aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Nakonfigurovaná instance ArcSight Syslog NG Daemon SmartConnector (SmartConnector) nebo ArcSight Load Balancer. Pokud jsou události odeslány arcsight vyrovnávání zatížení, jsou následně odeslány do SmartConnector pomocí vyrovnávání zatížení.

Stáhněte a otevřete [průvodce konfigurací pro ArcSight SmartConnector pro Centrum událostí Azure Monitor](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Tato příručka obsahuje kroky, které potřebujete k instalaci a konfiguraci ArcSight SmartConnector pro Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrace protokolů Azure AD s ArcSight

1. Nejprve proveďte kroky v části **Požadavky** v průvodci konfigurací. Tato část obsahuje následující kroky:
    * Nastavte uživatelská oprávnění v Azure, abyste zajistili, že je uživatel s rolí **vlastníka** k nasazení a konfiguraci konektoru.
    * Otevřete porty na serveru pomocí Syslog NG Daemon SmartConnector, aby byl přístupný z Azure. 
    * Nasazení spouští skript prostředí Windows PowerShell, takže je nutné povolit PowerShell upouštět skripty v počítači, ve kterém chcete nasadit konektor.

2. Postupujte podle pokynů v části **Nasazení konektoru** v průvodci konfigurací k nasazení konektoru. Tato část vás provede stažením a extrahováním konektoru, konfigurací vlastností aplikace a spuštěním skriptu nasazení z extrahované složky. 

3. Pomocí kroků v **ověření nasazení v Azure** se ujistěte, že je konektor nastavený a funguje správně. Zkontrolujte:
    * Požadované funkce Azure se vytvoří ve vašem předplatném Azure.
    * Protokoly Azure AD jsou vyponožené do správného cíle. 
    * Nastavení aplikace z vašeho nasazení se trvalé v nastavení aplikací v aplikacích Azure Function Apps. 
    * Nová skupina prostředků pro ArcSight se vytvoří v Azure, s aplikací Azure AD pro konektor ArcSight a účty úložiště obsahující mapované soubory ve formátu CEF.

4. Nakonec dokončete kroky po nasazení v **konfiguraci po nasazení** v průvodci konfigurací konfigurace. Tato část vysvětluje, jak provést další konfiguraci, pokud jste na plán služby App Service zabránit nečinnosti aplikací funkce po uplynutí časového limitu, konfigurace streamování diagnostických protokolů z centra událostí a aktualizace SysLog NG Daemon SmartConnector keystore certifikát k přidružení k nově vytvořenému účtu úložiště.

5. Průvodce konfigurací také vysvětluje, jak přizpůsobit vlastnosti konektoru v Azure a jak upgradovat a odinstalovat konektor. K dispozici je také část o zlepšení výkonu, včetně upgradu na [plán spotřeby Azure](https://azure.microsoft.com/pricing/details/functions) a konfigurace ArcSight nástroj pro vyrovnávání zatížení, pokud je zatížení událostí větší než to, co zvládne jeden Syslog NG Daemon SmartConnector.

## <a name="next-steps"></a>Další kroky

[Průvodce konfigurací pro ArcSight SmartConnector pro Centrum událostí Azure Monitor](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
