---
title: Připojení dat Fortinet ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Fortinet ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 7a44d63b834a7b6b580909005a440637bf730918
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475792"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Připojení Fortinet ke službě Azure Sentinel



Tento článek vysvětluje, jak připojit zařízení Fortinet ke službě Azure Sentinel. Fortinet data Connector vám umožní snadno připojit protokoly Fortinet ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití Fortinet v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 


## <a name="how-it-works"></a>Jak to funguje

Musíte nasadit agenta na vyhrazený počítač se systémem Linux (virtuální počítač nebo místní), aby podporoval komunikaci mezi Fortinet a službou Azure Sentinel. Následující diagram popisuje nastavení v případě virtuálního počítače se systémem Linux v Azure.

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
 
 
1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Fortinet** a pak **otevřete stránku konektor**. 

1. V části **instalace a konfigurace agenta SYSLOG**vyberte typ počítače, Azure, jiný Cloud nebo místní prostředí. 
   > [!NOTE]
   > Vzhledem k tomu, že skript v dalším kroku nainstaluje agenta Log Analytics a připojí počítač k pracovnímu prostoru Sentinel Azure, ujistěte se, že tento počítač není připojený k žádnému jinému pracovnímu prostoru.
1. Na počítači musíte mít zvýšená oprávnění (sudo). Ujistěte se, že máte v počítači Python, a to pomocí následujícího příkazu: `python –version`

1. Spusťte na proxy počítači následující skript.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Když je skript spuštěný, zkontrolujte, že nezískáváte žádné chybové zprávy ani upozornění.

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Krok 2: předejte protokoly Fortinet do agenta syslog

Nakonfigurujte Fortinet pro přeposílání zpráv syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Otevřete rozhraní příkazového řádku na zařízení Fortinet a spusťte následující příkazy:

        config log syslogd setting
        set format cef
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Nahraďte **IP adresu** serveru IP adresou agenta.
    - Nastavte **port SYSLOG** na **514** nebo na port nastavený na agentovi.
    - Pokud chcete povolit formát CEF ve verzích rané FortiOS, možná budete muset spustit sadu příkazů **CSV Disable**.
 
   > [!NOTE] 
   > Další informace najdete v [knihovně dokumentů Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Vyberte svou verzi a použijte **odkaz na zprávu** **příručky** a protokolu.

 Pokud chcete pro události Fortinet použít příslušné schéma v Log Analytics Azure Monitor, vyhledejte `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Krok 3: ověření připojení

1. Otevřete Log Analytics a ujistěte se, že jsou protokoly přijaté pomocí schématu CommonSecurityLog.<br> Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

1. Před spuštěním skriptu doporučujeme, abyste odeslali zprávy z řešení zabezpečení, abyste se ujistili, že jsou předávány na počítač proxy syslog, který jste nakonfigurovali. 
1. Na počítači musíte mít zvýšená oprávnění (sudo). Ujistěte se, že máte v počítači Python, a to pomocí následujícího příkazu: `python –version`
1. Spuštěním následujícího skriptu zkontrolujete konektivitu mezi agentem, službou Azure Sentinel a vaším řešením zabezpečení. Kontroluje, zda je přesměrování démona správně nakonfigurováno, naslouchá na správných portech a zda nic neblokuje komunikaci mezi démonem a agentem Log Analytics. Skript také pošle příznakové zprávy "TestCommonEventFormat", aby zkontrolovala koncové připojení. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`




## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak připojit zařízení Fortinet ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

