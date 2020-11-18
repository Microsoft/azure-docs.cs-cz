---
title: Připojení dat CEF k Azure Sentinel Preview | Microsoft Docs
description: Připojte externí řešení, které pošle zprávy CEF (Common Event Format) do Azure Sentinel, pomocí počítače se systémem Linux jako služby pro odesílání protokolů.
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 4b34477a40530a0e6f26b59dd9707c019418b2a4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655846"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Připojení externího řešení pomocí běžných formátů událostí

Když připojujete externí řešení, které odesílá zprávy CEF, je třeba se připojit ke službě Azure Sentinel pomocí tří kroků:

Krok 1: [připojení CEF nasazením protokolu syslog/CEF](connect-cef-agent.md) – Krok 2: [provedení kroků specifických pro řešení](connect-cef-solution-config.md) krok 3: [ověření připojení](connect-cef-verify.md)

Tento článek popisuje, jak připojení funguje, uvádí seznam požadavků a ukazuje postup nasazení mechanismu pro řešení zabezpečení pro posílání zpráv Common Event Format (CEF) nad protokolem syslog. 

> [!NOTE] 
> Data se ukládají do geografického umístění pracovního prostoru, na kterém běží Azure Sentinel.

Aby bylo možné vytvořit toto připojení, je nutné nasadit server předávání syslog pro podporu komunikace mezi zařízením a službou Azure Sentinel.  Server se skládá z vyhrazeného počítače se systémem Linux (virtuální počítač nebo místní) s nainstalovaným agentem Log Analytics pro Linux. 

Následující diagram popisuje nastavení v případě virtuálního počítače se systémem Linux v Azure:

 ![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně bude tato instalace existovat, pokud používáte virtuální počítač v jiném cloudu nebo v místním počítači: 

 ![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. Pomocí následujících pokynů můžete zlepšit konfiguraci zabezpečení počítače:  [zabezpečený virtuální počítač v Azure](../virtual-machines/security-policy.md), [osvědčené postupy pro zabezpečení sítě](../security/fundamentals/network-best-practices.md).

Pokud chcete používat komunikaci TLS mezi zdrojem syslog a službou pro předávání syslog, budete muset nakonfigurovat démona syslog (rsyslog nebo syslog-ng) pro komunikaci v TLS: [šifrování provozu syslog pomocí TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [šifrování zpráv protokolu pomocí TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Požadavky

Ujistěte se, že počítač se systémem Linux, který používáte jako službu pro překládání protokolů, používá jeden z následujících operačních systémů:

- 64bitová
  - CentOS 7 a 8, včetně dílčích verzí (ne 6)
  - Amazon Linux 2017,09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 a 8, včetně dílčích verzí (ne 6)
  - Debian GNU/Linux 8 a 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS a 18,04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32bitová
  - CentOS 7 a 8, včetně dílčích verzí (ne 6)
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 a 8, včetně dílčích verzí (ne 6)
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
  - Ujistěte se, že na vašem počítači běží Python 2,7 nebo 3.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak Azure Sentinel shromažďuje protokoly CEF z řešení zabezpečení a zařízení. Informace o tom, jak připojit vaše řešení ke službě Azure Sentinel, najdete v následujících článcích:

- Krok 1: [připojení CEF nasazením protokolu syslog/CEF pro přeposílání](connect-cef-agent.md)
- Krok 2: [provedení kroků specifických pro řešení](connect-cef-solution-config.md)
- Krok 3: [ověření připojení](connect-cef-verify.md)

Další informace o tom, co dělat s daty, která jste shromáždili v Azure Sentinel, najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).