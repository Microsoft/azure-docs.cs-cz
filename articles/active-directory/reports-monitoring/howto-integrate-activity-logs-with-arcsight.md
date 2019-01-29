---
title: Postup pro integraci protokolů Azure Active Directory ArcSight používat Azure Monitor (preview) | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory s ArcSight používat Azure Monitor (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/03/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e9f9fe5c04e5293c91765795dcbfb9b0800b809
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168596"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor-preview"></a>Integrace protokolů Azure Active Directory s ArcSight používat Azure Monitor (preview)

[Micro ArcSight fokus](https://software.microfocus.com/products/siem-security-information-event-management/overview) je událostí (SIEM) řešení pro správu zabezpečení, která umožňuje detekovat a reagovat na hrozby zabezpečení ve vaší platformě. Teď můžete protokoly služby Azure Active Directory (Azure AD) směrovat ArcSight používat Azure Monitor pomocí ArcSight konektoru pro Azure AD. Tato funkce umožňuje monitorovat pro případ ohrožení zabezpečení pomocí ArcSight vašeho tenanta.  

V tomto článku se dozvíte, jak chcete směrovat ArcSight používat Azure Monitor protokoly služby Azure AD. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Protokoly centra událostí Azure, který obsahuje aktivitu Azure AD. Zjistěte, jak [streamování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Nakonfigurovanou instanci ArcSight Syslog NG démon SmartConnector (SmartConnector) nebo nástroje pro vyrovnávání zatížení ArcSight. Pokud nástroj pro vyrovnávání zatížení ArcSight jsou odesílány události, jsou v důsledku odeslány do SmartConnector nástrojem pro vyrovnávání zatížení.

Stáhnout a otevřít [Průvodce konfigurací pro ArcSight SmartConnector pro Centrum událostí Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Tato příručka obsahuje kroky potřebné k instalaci a konfiguraci ArcSight SmartConnector pro monitorování Azure. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrace protokolů Azure AD s ArcSight

1. Nejprve proveďte kroky v **požadavky** část Průvodce konfigurací. Tato část zahrnuje následující kroky:
    * Nastavit oprávnění pro uživatele v Azure, aby se zajistilo uživatele s **vlastníka** role k nasazení a konfiguraci konektoru.
    * Otevřete porty na serveru s SmartConnector NG démona Syslog, tak, aby byl přístupný z Azure. 
    * Nasazení spustí skript prostředí Windows PowerShell, takže je potřeba povolit PowerShell ke spouštění skriptů v počítači, ve které chcete nasadit konektor.

2. Postupujte podle pokynů **nasazení konektoru** konfigurace příručce k nasazení konektoru. Tato část vás provede ke stažení a extrakci konektoru, konfiguraci vlastností aplikace a spusťte skript nasazení z extrahovanou složku. 

3. Postupujte podle kroků v **ověření nasazení v Azure** k Ujistěte se, že konektor je nastavený a funguje správně. Ověřte následující:
    * Požadované funkce Azure se vytvoří ve vašem předplatném Azure.
    * Protokoly služby Azure AD se streamují do správné cíl. 
    * Nastavení aplikace z nasazení služby se uchovávají v nastavení aplikace v Azure aplikace Function App. 
    * Novou skupinu prostředků pro ArcSight se vytvoří v Azure, pomocí aplikace Azure AD pro konektor ArcSight a účty úložiště, který obsahuje mapované soubory ve formátu CEF.

4. Nakonec dokončete po nasazení kroků v **konfigurace po nasazení** Průvodce konfigurací. Tato část vysvětluje, jak provést další konfiguraci, pokud jste na plán služby App Service zabrání přepnutí do režimu nečinnosti po časový limit aplikace function App, nakonfigurujte streamování diagnostických protokolů z centra událostí a aktualizaci NG démon procesu SysLog Certifikát SmartConnector úložiště klíčů pro přidružení k na nově vytvořený účet úložiště.

5. Průvodce konfigurací také vysvětluje, jak přizpůsobit vlastnosti konektoru v Azure a tom, jak upgradovat a odinstalovat konektor. Vylepšení výkonu, včetně upgradu na je také část [plán Azure Consumption](https://azure.microsoft.com/pricing/details/functions) a konfiguraci Load Balancer ArcSight v případě zatížení událostmi je větší než co lze jeden SmartConnector NG démon procesu Syslog Obslužná rutina.

## <a name="next-steps"></a>Další postup

* [Průvodce konfigurací pro ArcSight SmartConnector pro Centrum událostí Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
