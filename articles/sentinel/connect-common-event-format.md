---
title: Připojení dat CeF k Azure Sentinel Preview| Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit data cef s Azure Sentinelem.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588344"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Připojení externího řešení pomocí společného formátu událostí


Když připojíte externí řešení, které odesílá zprávy cef, existují tři kroky pro připojení k Azure Sentinelu:

KROK 1: [Připojení cef nasazením agenta](connect-cef-agent.md) KROK 2: [Provedení kroků specifických pro řešení](connect-cef-solution-config.md) KROK 3: Ověření [připojení](connect-cef-verify.md)

Tento článek popisuje, jak připojení funguje, poskytuje požadavky a poskytuje kroky pro nasazení agenta na řešení zabezpečení, které odesílají zprávy společného formátu událostí (CEF) nad Syslog. 

> [!NOTE] 
> Data se ukládají v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.

Chcete-li vytvořit toto připojení, je třeba nasadit agenta na vyhrazeném počítači s Linuxem (VM nebo místně) pro podporu komunikace mezi zařízením a Azure Sentinel. Následující diagram popisuje nastavení v případě virtuálního počítače s Linuxem v Azure.

 ![CeF v Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativně toto nastavení bude existovat, pokud používáte virtuální počítač v jiném cloudu nebo místní počítač. 

 ![CeF v prostorách](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Důležité informace o zabezpečení

Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete například nakonfigurovat síť tak, aby byla zarovnána se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v daemonu tak, aby odpovídaly vašim požadavkům. Ke zlepšení konfigurace zabezpečení počítače můžete použít následující pokyny:  [Zabezpečení zabezpečeného virtuálního počítače v Azure](../virtual-machines/linux/security-policy.md), Doporučené postupy pro zabezpečení [sítě](../security/fundamentals/network-best-practices.md).

Chcete-li používat komunikaci TLS mezi řešením zabezpečení a počítačem Syslog, budete muset nakonfigurovat daemon Syslog (rsyslog nebo syslog-ng) pro komunikaci v TLS: [Šifrování syslogprovozu pomocí TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Šifrování zpráv protokolu pomocí TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Požadavky
Ujistěte se, že počítač s Linuxem, který používáte jako proxy server, používá jeden z následujících operačních systémů:

- 64bitová
  - CentOS 6 a 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 a 7
  - Red Hat Enterprise Linux Server 6 a 7
  - Debian GNU/Linux 8 a 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS a 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32bitová
   - Centos 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 a 9
   - Ubuntu Linux 14.04 LTS a 16.04 LTS
 
 - Daemon verze
   - Syslog-ng: 2,1 - 3,22,1
   - Rsyslog: v8
  
 - Podporované dokumenty RFC Syslog
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Ujistěte se, že váš stroj splňuje také následující požadavky: 
- Oprávnění
    - Musíte mít zvýšená oprávnění (sudo) na vašem počítači. 
- Požadavky na software
    - Ujistěte se, že máte na počítači spuštěný Python



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení CEF k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).

