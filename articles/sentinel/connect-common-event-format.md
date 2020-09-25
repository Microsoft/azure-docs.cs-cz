---
title: Připojení dat CEF k Azure Sentinel Preview | Microsoft Docs
description: Připojte externí řešení, které pošle zprávy CEF (Common Event Format) do Azure Sentinel pomocí počítače se systémem Linux jako proxy serveru.
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
ms.openlocfilehash: cd84a4b50ba32ee3f562ace9b2583cf5e561be84
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320383"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Připojení externího řešení pomocí běžných formátů událostí


Když připojujete externí řešení, které odesílá zprávy CEF, je třeba se připojit ke službě Azure Sentinel pomocí tří kroků:

Krok 1: [připojení CEF nasazením protokolu syslog/CEF](connect-cef-agent.md) – Krok 2: [provedení kroků specifických pro řešení](connect-cef-solution-config.md) krok 3: [ověření připojení](connect-cef-verify.md)

Tento článek popisuje, jak připojení funguje, nabízí požadavky a poskytuje postup nasazení agenta v řešeních zabezpečení, která odesílají zprávy CEF (Common Event Format) do protokolu syslog. 

> [!NOTE] 
> Data se ukládají do geografického umístění pracovního prostoru, na kterém běží Azure Sentinel.

Aby bylo možné vytvořit toto připojení, je nutné nasadit server předávání syslog pro podporu komunikace mezi zařízením a službou Azure Sentinel.  Server se skládá z vyhrazeného počítače se systémem Linux (virtuální počítač nebo místní) s nainstalovaným agentem Log Analytics pro Linux. 

Následující diagram popisuje nastavení v případě virtuálního počítače se systémem Linux v Azure:

 ![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně bude tato instalace existovat, pokud používáte virtuální počítač v jiném cloudu nebo v místním počítači: 

 ![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Aspekty zabezpečení

Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. Pomocí následujících pokynů můžete zlepšit konfiguraci zabezpečení počítače:  [zabezpečený virtuální počítač v Azure](../virtual-machines/security-policy.md), [osvědčené postupy pro zabezpečení sítě](../security/fundamentals/network-best-practices.md).

Pokud chcete používat komunikaci TLS mezi zdrojem syslog a službou pro předávání syslog, budete muset nakonfigurovat démona syslog (rsyslog nebo syslog-ng) pro komunikaci v TLS: [šifrování provozu syslog pomocí TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [šifrování zpráv protokolu pomocí TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Požadavky
Ujistěte se, že počítač se systémem Linux, který používáte jako proxy, používá jeden z následujících operačních systémů:

- 64bitová
  - CentOS 6 a 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 a 7
  - Red Hat Enterprise Linux Server 6 a 7
  - Debian GNU/Linux 8 a 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS a 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32bitová
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 a 9
   - Ubuntu Linux 14,04 LTS a 16,04 LTS
 
 - Verze démona
   - Syslog-ng: 2,1-3.22.1
   - Rsyslog: V8
  
 - Podporované specifikace RFC syslog
   - Protokol syslog RFC 3164
   - Protokol syslog RFC 5424
 
Ujistěte se, že váš počítač splňuje i následující požadavky: 
- Oprávnění
    - Na počítači musíte mít zvýšená oprávnění (sudo). 
- Požadavky na software
    - Ujistěte se, že je v počítači spuštěný Python.



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).

