---
title: Připojení zdrojů dat Sentinelu ve verzi Preview Azure? | Dokumentace Microsoftu
description: Informace o připojení zdroje dat do Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5d8d97aefa2efadae70c976b8a1876b2c7d8f1c1
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921938"
---
# <a name="connect-data-sources"></a>Připojení zdrojů dat

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Do vlaku Sentinelu Azure musíte nejprve připojit ke zdrojům dat. Azure Sentinel se dodává s celou řadou konektorů pro Microsoft solutions, k dispozici z pole a poskytuje v reálném čase, integrace, včetně řešení pro ochranu před internetovými útoky Microsoft a Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho jsou integrované konektory do širšího ekosystému zabezpečení pro řešení jiného subjektu než Microsoft. Můžete také použít běžný formát události Syslog nebo rozhraní REST API pro připojení zdroje dat pomocí Azure Sentinelu také.  

![Kolekce dat](./media/collect-data/collect-data-page.png)

## <a name="data-connection-methods"></a>Metody datového připojení

Podporuje následující datové připojení metody Sentinelu Azure:

- **Služby Microsoft**:<br> Služby jsou připojené nativní využití Azure základ pro pole out v integraci, následující řešení můžete připojit pomocí několika kliknutí:
    - [Office 365](connect-office-365.md)
    - [Azure AD audit, přihlášení a protokolování](connect-azure-active-directory.md)
    - [Azure Activity](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Události zabezpečení Windows](connect-windows-security-events.md) 
    - [Brána Windows firewall](connect-windows-firewall.md)

- **Externí řešení přes rozhraní API**: Některé zdroje dat jsou připojené připojených zdrojů dat pomocí rozhraní API, která jsou k dispozici. Většina zabezpečení technologie obvykle poskytují sadu rozhraní API, pomocí kterého se dá načíst protokoly událostí. Rozhraní API pro připojení k Azure Sentinelu a shromažďovat konkrétní datové typy a odeslat do služby Azure Log Analytics. Zařízení připojené přes rozhraní API patří:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Externí řešení prostřednictvím agenta**: Azure Sentinel může být připojen k všechny ostatní zdroje dat, které můžete provádět, streamování v reálném čase protokolů pomocí protokolu Syslog, prostřednictvím agenta. <br>Většina zařízení pomocí protokolu Syslog odesílat zprávy o událostech, které zahrnují protokolu samostatně a data o protokolu. Formát protokoly se liší, ale většina zařízení podporují standardní formát cef (Common Event Format). <br>Agent Azure Sentinelu, která je založena na agenta Microsoft Monitoring Agent, převede protokoly ve formátu CEF do formátu, který je možné ingestovat službou Log Analytics. V závislosti na typu zařízení je agent nainstalovaný přímo na zařízení nebo na vyhrazeném serveru Linux. Agenta pro Linux přes protokol UDP. přijímá události z procesu démona Syslogu, ale v případech, kde je očekávána počítač s Linuxem ke shromažďování k velkému počtu události procesu Syslog, odešlou přes TCP z procesu démona Syslogu na agenta a z něj ke službě Log Analytics.
    - Brány firewall, proxy servery a koncové body:
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Ostatní zařízení CEF](connect-common-event-format.md)
        - [Ostatní zařízení Syslog](connect-syslog.md)
    - Řešení ochrany před únikem informací
    - [Poskytovatelů analýzy hrozeb](connect-threat-intelligence.md)
    - [DNS počítače](connect-dns.md) -agent instaluje přímo do počítače DNS
    - Servery s Linuxem
    - Jiné cloudy
    
## Možnosti připojení agenta<a name="agent-options"></a>

Připojení externích zařízení k Azure Sentinelu, musí být agent nasazený na vyhrazený počítač (VM nebo v místním prostředí) aby mohly podporovat komunikaci mezi zařízením a Azure Sentinelu. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je pouze k dispozici, pokud je vyhrazený počítač v Azure vytváříte nový virtuální počítač. 


![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativně můžete nasadit agenta ručně na existující virtuální počítač Azure, na virtuálním počítači v jiném cloudu nebo na místním počítači.

![CEF v místním prostředí](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Další postup

- Abyste mohli začít s Azure Sentinelu, budete potřebovat předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Zjistěte, jak [připojit vaše data do Azure Sentinelu](quickstart-onboard.md), a [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
