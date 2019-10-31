---
title: Integrace protokolů s ArcSight pomocí Azure Monitor | Microsoft Docs
description: Informace o tom, jak integrovat protokoly Azure Active Directory s využitím ArcSight pomocí Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6a65eee10c7c5547cc433c663a4294ec842047
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199731"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrace protokolů Azure Active Directory s využitím ArcSight pomocí Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) je řešení zabezpečení a Správa událostí (Siem), které pomáhá detekovat bezpečnostní hrozby ve vaší platformě a reagovat na ně. Nyní můžete směrovat protokoly Azure Active Directory (Azure AD) do ArcSight pomocí Azure Monitor pomocí konektoru ArcSight pro Azure AD. Tato funkce umožňuje monitorovat svého tenanta kvůli ohrožení zabezpečení pomocí ArcSight.  

V tomto článku se dozvíte, jak směrovat protokoly Azure AD do ArcSight pomocí Azure Monitor. 

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Centrum událostí Azure, které obsahuje protokoly aktivit služby Azure AD. Naučte se, jak [streamovat protokoly aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Nakonfigurovaná instance ArcSight syslog NG démon SmartConnector (SmartConnector) nebo ArcSight Load Balancer. Pokud jsou události odesílány do ArcSight Load Balancer, jsou v důsledku Load Balancer odesílány do SmartConnector.

Stáhněte si a otevřete [Průvodce konfigurací pro ArcSight SmartConnector pro centrum událostí Azure monitor](https://community.microfocus.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/8/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Tato příručka obsahuje kroky potřebné k instalaci a konfiguraci ArcSight SmartConnector pro Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrace protokolů Azure AD s ArcSight

1. Nejdřív proveďte kroky v části **požadavky** Průvodce konfigurací. Tato část obsahuje následující kroky:
    * Nastavte oprávnění uživatele v Azure, aby bylo zajištěno, že bude mít uživatel s rolí **vlastníka** nasadit a nakonfigurovat konektor.
    * Otevřete porty na serveru pomocí protokolu syslog NG daemon SmartConnector, aby byl dostupný z Azure. 
    * Nasazení spustí skript Windows PowerShellu, takže musíte povolit PowerShell, aby se skripty spouštěly na počítači, na kterém chcete konektor nasadit.

2. K nasazení konektoru použijte postup v části **nasazení konektoru** v Průvodci konfigurací. V této části se dozvíte, jak stáhnout a extrahovat konektor, nakonfigurovat vlastnosti aplikace a spustit skript nasazení z extrahované složky. 

3. Použijte postup v části **Ověření nasazení v Azure** , abyste se ujistili, že je konektor nastavený a funguje správně. Ověřte následující:
    * Ve vašem předplatném Azure se vytvoří požadované funkce Azure.
    * Protokoly služby Azure AD se streamují do správného cíle. 
    * Nastavení aplikace z vašeho nasazení je trvalé v nastavení aplikace ve službě Azure Function Apps. 
    * V Azure se vytvoří nová skupina prostředků pro ArcSight s aplikací Azure AD pro konektor ArcSight a účty úložiště obsahující mapované soubory ve formátu CEF.

4. Nakonec proveďte kroky po nasazení v Průvodci konfigurací **po nasazení** . V této části se dozvíte, jak provést další konfiguraci, pokud jste v plánu App Service, abyste zabránili nečinnosti aplikací Function App po uplynutí časového limitu, nakonfigurujete streamování diagnostických protokolů z centra událostí a aktualizujete démona SysLog NG. SmartConnector certifikát úložiště klíčů pro přidružení k nově vytvořenému účtu úložiště.

5. Průvodce konfigurací také vysvětluje, jak přizpůsobit vlastnosti konektoru v Azure a jak upgradovat a odinstalovat konektor. K dispozici je také část týkající se vylepšení výkonu, včetně upgradu na [plán spotřeby Azure](https://azure.microsoft.com/pricing/details/functions) a konfigurace ArcSight Load Balancer, pokud je zatížení události větší než v případě, že je možné vyřadit z jednoho procesu syslog ng SmartConnector.

## <a name="next-steps"></a>Další kroky

[Průvodce konfigurací pro ArcSight SmartConnector pro centrum událostí Azure Monitor](https://community.microfocus.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/8/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
