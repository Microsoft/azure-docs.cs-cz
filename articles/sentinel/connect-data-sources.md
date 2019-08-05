---
title: Připojit zdroje dat ke službě Azure Sentinel Preview? | Microsoft Docs
description: Naučte se připojit zdroje dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 4928657aa9052b50faf1f326cc09797c5aaf69bb
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780505"
---
# <a name="connect-data-sources"></a>Připojení zdrojů dat

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Aby bylo možné připojit se ke službě Azure Sentinel, musíte se nejdřív připojit ke zdrojům dat. Služba Azure Sentinel se dodává s řadou konektorů pro řešení Microsoftu, které jsou dostupné předem a poskytuje integraci v reálném čase, včetně řešení ochrany před internetovými útoky Microsoftu a Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. Pro připojení zdrojů dat k Azure Sentinel taky můžete použít také běžné formáty událostí, syslog nebo REST API.  

1. V nabídce vyberte **datové konektory**. Tato stránka vám umožní zobrazit úplný seznam konektorů, které poskytuje Azure Sentinel, a jejich stav. Vyberte konektor, který chcete připojit, a vyberte **stránku otevřít konektor**. 

   ![Sběrače dat](./media/collect-data/collect-data-page.png)

1. Na stránce konkrétní konektor se ujistěte, že jste splnili všechny předpoklady, a postupujte podle pokynů pro připojení dat ke službě Azure Sentinel. Může chvíli trvat, než se protokoly spustí synchronizace s Sentinel Azure. Po připojení se zobrazí souhrn dat v grafu **přijatá data** a stav připojení datových typů.

   ![Kolektory připojení](./media/collect-data/opened-connector-page.png)
  
1. Kliknutím na kartu **Další kroky** zobrazíte seznam předem připraveného obsahu Azure Sentinel pro konkrétní datový typ.

   ![Sběrače dat](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody datového připojení

Služba Azure Sentinel podporuje následující metody datového připojení:

- **Služby Microsoftu**:<br> Služby společnosti Microsoft jsou nativně propojeny, což využívá Azure Foundation k předem připravené integraci, lze pomocí několika kliknutí připojit následující řešení:
    - [Office 365](connect-office-365.md)
    - [Protokoly a přihlášení k auditu Azure AD](connect-azure-active-directory.md)
    - [Aktivita Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Rozšířená ochrana před internetovými útoky Azure](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Události zabezpečení systému Windows](connect-windows-security-events.md) 
    - [Brána Windows Firewall](connect-windows-firewall.md)

- **Externí řešení prostřednictvím rozhraní API**: Některé zdroje dat jsou propojeny pomocí rozhraní API, která jsou poskytována připojeným zdrojem dat. Většina technologií zabezpečení obvykle poskytuje sadu rozhraní API, pomocí kterých lze načíst protokoly událostí. Rozhraní API se připojují k Azure Sentinel a shromažďují konkrétní datové typy a odesílají je do Azure Log Analytics. Mezi zařízení připojená přes rozhraní API patří:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Externí řešení prostřednictvím agenta**: Pomocí protokolu syslog prostřednictvím agenta je možné připojit Azure Sentinel ke všem dalším zdrojům dat, které mohou provádět streamování protokolů v reálném čase pomocí protokolu syslog. <br>Většina zařízení používá protokol syslog k posílání zpráv o událostech, které obsahují samotný protokol a data o protokolu. Formát protokolů se liší, ale většina zařízení podporuje standard CEF (Common Event Format). <br>Agent Azure Sentinel, který je založený na Microsoft Monitoring Agent, převede protokoly naformátované na CEF do formátu, který je možné pomocí Log Analytics ingestovat. V závislosti na typu zařízení se agent nainstaluje buď přímo na zařízení, nebo na vyhrazený server Linux. Agent pro Linux přijímá události z procesu démona syslog přes protokol UDP, ale pokud se očekává, že počítač se systémem Linux shromáždí velký objem událostí syslog, pošle se přes protokol TCP z procesu démona syslog do agenta a tam, kde Log Analytics.
    - Brány firewall, proxy servery a koncové body:
        - [F5](connect-f5.md)
        - [Kontrolní bod](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Jiná zařízení CEF](connect-common-event-format.md)
        - [Jiná zařízení syslog](connect-syslog.md)
    - Řešení ochrany před únikem informací
    - [Poskytovatelé analýzy hrozeb](connect-threat-intelligence.md)
    - [Počítače DNS](connect-dns.md) – agent nainstalovaný přímo na počítači DNS
    - Servery Linux
    - Další cloudy
    
## Možnosti připojení agenta<a name="agent-options"></a>

Aby bylo možné připojit externí zařízení ke službě Azure Sentinel, musí být agent nasazen na vyhrazeném počítači (VM nebo místně), aby podporoval komunikaci mezi zařízením a službou Azure Sentinel. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je dostupné jenom v případě, že váš vyhrazený počítač je nový virtuální počítač, který vytváříte v Azure. 


![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně můžete agenta nasadit ručně na existující virtuální počítač Azure, na virtuální počítač v jiném cloudu nebo na místní počítač.

![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Další postup

- Abyste mohli začít používat službu Azure Sentinel, potřebujete Microsoft Azure předplatné. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Naučte se, jak začlenit [data do Azure Sentinel](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
