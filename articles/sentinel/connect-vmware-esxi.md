---
title: Připojit VMware ESXi dat ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor VMware ESXi k načtení protokolů ESXi do Azure Sentinel. Zobrazení dat ESXi v sešitech, vytváření výstrah a vylepšení šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700818"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Připojení VMware ESXi k Azure Sentinel

> [!IMPORTANT]
> Konektor VMware ESXi je v tuto chvíli ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení VMware ESXi ke službě Azure Sentinel. Konektor VMware ESXi data umožňuje snadno ingestovat protokoly VMware ESXi do služby Azure Sentinel. poskytuje lepší přehled o aktivitách ESXi vaší organizace a pomáhá vylepšit možnosti vašich operací zabezpečení. Integrace mezi VMware ESXi a službou Azure Sentinel používá server syslog s nainstalovaným agentem Log Analytics. Používá také uživatelsky sestavený analyzátor protokolů založený na funkci Kusto.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Vaše řešení VMware ESXi musí být nakonfigurováno pro export protokolů prostřednictvím protokolu syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Odeslání protokolů VMware ESXi agenta syslog  

Nakonfigurujte VMware ESXi pro přeposílání zpráv syslog do pracovního prostoru Azure Sentinel prostřednictvím agenta syslog.
3. Postupujte podle pokynů na stránce **VMware ESXi** .


1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte konektor **VMware ESXi (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na stránce **VMware ESXi** konektoru:

    1. Instalace a zprovoznění agenta pro Linux

        - Vyberte virtuální počítač Azure Linux nebo počítač se systémem Linux bez platformy Azure (fyzický nebo virtuální).

    1. Konfigurovat protokoly, které se mají shromažďovat

        - V **konfiguraci agentů pracovního prostoru** vyberte zařízení a závažnost.

    1. Konfigurace a připojení VMware ESXi

        - Postupujte podle těchto pokynů a nakonfigurujte VMware ESXi pro předávací protokol syslog:
            - [VMware ESXi 3,5 a 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 a vyšší](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Pro vzdálený server použijte IP adresu počítače se systémem Linux, na který jste nainstalovali agenta pro Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Ověření připojení a hledání dat

Může trvat až 20 minut, než se protokoly začnou zobrazovat v Azure Sentinel. 

Po navázání úspěšného připojení se data objeví v **protokolech** v části *Správa protokolu* v tabulce *syslog* .

Tento datový konektor závisí na analyzátoru založeném na funkci Kusto, která bude fungovat podle očekávání. [Pomocí těchto kroků](https://aka.ms/sentinel-vmwareesxi-parser) vytvořte alias funkce **VMwareESXi** Kusto. Pak můžete zadat `VMwareESXi` libovolné okno dotazu v Azure Sentinel a dotazovat se na data.

Několik užitečných ukázek dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit VMware ESXi ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
