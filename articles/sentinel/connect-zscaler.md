---
title: Připojení dat Zscaler ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Zscaler ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: fe7ba0f6daec0b85ec73611ba4e48d72f16146e3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515132"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Připojení Zscaler k Internetu do Azure Sentinel

> [!IMPORTANT]
> Zscaler data Connector ve službě Azure Sentinel je momentálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak připojit zařízení s internetovým přístupem Zscaler ke službě Azure Sentinel. Zscaler data Connector umožňuje snadno připojit protokoly Zscaler Internet Access (ZIA) k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití Zscaler v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 


## <a name="how-it-works"></a>Jak to funguje

Musíte nasadit agenta na vyhrazený počítač se systémem Linux (virtuální počítač nebo místní) pro podporu komunikace mezi Zscaler Internet přístupem a Azure Sentinel. Následující diagram popisuje nastavení v případě virtuálního počítače se systémem Linux v Azure.

 ![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně bude tato instalace existovat, pokud použijete virtuální počítač v jiném cloudu nebo v místním počítači. 

 ![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Informace o zabezpečení

Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. Pomocí následujících pokynů můžete zlepšit konfiguraci zabezpečení počítače:  [zabezpečený virtuální počítač v Azure](../virtual-machines/linux/security-policy.md), [osvědčené postupy pro zabezpečení sítě](../security/fundamentals/network-best-practices.md).

Pokud chcete používat komunikaci TLS mezi řešením zabezpečení a počítačem syslog, budete muset nakonfigurovat démona syslog (rsyslog nebo syslog-ng) pro komunikaci v TLS: [šifrování provozu syslog pomocí TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [šifrování zpráv protokolu pomocí TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Předpoklady
Ujistěte se, že počítač se systémem Linux, který používáte jako proxy, používá jeden z následujících operačních systémů:

- 64 – bit
  - CentOS 6 a 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 a 7
  - Red Hat Enterprise Linux Server 6 a 7
  - Debian GNU/Linux 8 a 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS a 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32 – bit
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
## <a name="step-1-deploy-the-agent"></a>Krok 1: nasazení agenta

V tomto kroku musíte vybrat počítač se systémem Linux, který bude fungovat jako proxy server mezi službou Azure Sentinel a řešením zabezpečení. Na proxy počítači budete muset spustit skript, který:
- Nainstaluje agenta Log Analytics a nakonfiguruje ho podle potřeby, aby naslouchal zprávy syslog na portu 514 přes TCP a odesílal zprávy CEF do pracovního prostoru Azure Sentinel.
- Nakonfiguruje démon procesu Syslog pro přeposílání zpráv CEF na agenta Log Analytics pomocí portu 25226.
- Nastaví agenta syslog ke shromáždění dat a jeho bezpečnému odeslání do Log Analytics, kde se analyzuje a obohacuje.
 
 
1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Zscaler** a pak **otevřete stránku konektor**. 

1. V části **instalace a konfigurace agenta SYSLOG**vyberte typ počítače, Azure, jiný Cloud nebo místní prostředí. 
   > [!NOTE]
   > Vzhledem k tomu, že skript v dalším kroku nainstaluje agenta Log Analytics a připojí počítač k pracovnímu prostoru Sentinel Azure, ujistěte se, že tento počítač není připojený k žádnému jinému pracovnímu prostoru.
1. Na počítači musíte mít zvýšená oprávnění (sudo). Ujistěte se, že máte v počítači Python, a to pomocí následujícího příkazu: `python –version`

1. Spusťte na proxy počítači následující skript.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Když je skript spuštěný, zkontrolujte, že nezískáváte žádné chybové zprávy ani upozornění.


## <a name="step-2-configure-your-zscaler-to-send-cef-messages"></a>Krok 2: Konfigurace Zscaler pro posílání zpráv CEF

1. Na zařízení Zscaler musíte nastavit tyto hodnoty tak, aby zařízení odesílalo potřebné protokoly v potřebném formátu do agenta Azure Sentinel syslog na základě agenta Log Analytics. Tyto parametry můžete upravit v zařízení, pokud je také upravíte v procesu démona syslog na agentu služby Azure Sentinel.
    - Protokol = TCP
    - Port = 514
    - Format = CEF
    - IP adresa – zajistěte odeslání zpráv CEF na IP adresu virtuálního počítače, který jste si pro tento účel vyhradi.
 Další informace najdete v tématu [Průvodce integrací Azure Sentinel v Zscaler](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Toto řešení podporuje syslog RFC 3164 nebo RFC 5424.


1. Pokud chcete použít příslušné schéma v Log Analytics pro události CEF, vyhledejte `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>Krok 3: ověření připojení

1. Otevřete Log Analytics a ujistěte se, že jsou protokoly přijaté pomocí schématu CommonSecurityLog.<br> Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

1. Před spuštěním skriptu doporučujeme, abyste odeslali zprávy z řešení zabezpečení, abyste se ujistili, že jsou předávány na počítač proxy syslog, který jste nakonfigurovali. 
1. Na počítači musíte mít zvýšená oprávnění (sudo). Ujistěte se, že máte v počítači Python, a to pomocí následujícího příkazu: `python –version`
1. Spuštěním následujícího skriptu zkontrolujete konektivitu mezi agentem, službou Azure Sentinel a vaším řešením zabezpečení. Kontroluje, zda je přesměrování démona správně nakonfigurováno, naslouchá na správných portech a zda nic neblokuje komunikaci mezi démonem a agentem Log Analytics. Skript také pošle příznakové zprávy "TestCommonEventFormat", aby zkontrolovala koncové připojení. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit přístup k Internetu Zscaler k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).

