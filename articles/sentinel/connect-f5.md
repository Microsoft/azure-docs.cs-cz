---
title: Připojit data F5 ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit data F5 ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 64ea16b6e5a2821db4f053928e4b95ba80d177dd
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240014"
---
# <a name="connect-your-f5-appliance"></a>Připojení zařízení F5



Můžete připojit Azure Sentinel k jakémukoli zařízení F5 tím, že soubory protokolu uložíte jako CEF syslog. Integrace s Azure Sentinel umožňuje snadno spouštět analýzy a dotazy napříč daty souboru protokolu z F5. Další informace o tom, jak Azure Sentinel ingestuje CEF data, najdete v tématu [připojení zařízení CEF](connect-common-event-format.md).

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="step-1-connect-your-f5-appliance-using-an-agent"></a>Krok 1: Připojení zařízení F5 pomocí agenta

Pokud chcete připojit zařízení F5 ke službě Azure Sentinel, musíte agenta nasadit na vyhrazený počítač (virtuální počítač nebo místně), aby podporoval komunikaci mezi zařízením a službou Azure Sentinel.

Případně můžete agenta nasadit ručně na existující virtuální počítač Azure, na virtuální počítač v jiném cloudu nebo na místní počítač.

> [!NOTE]
> Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. 

Postup zobrazení síťového diagramu obou možností najdete v tématu [připojení zdrojů dat](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Nasazení agenta 

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **F5** a pak **otevřete stránku konektor**. 

1. V části **Stáhnout a nainstalovat agenta SYSLOG**vyberte typ počítače, buď Azure, nebo místní. 
1. Na obrazovce **virtuální počítače** , která se otevře, vyberte počítač, který chcete použít, a klikněte na **připojit**.
1. Pokud zvolíte **Stáhnout a nainstalovat agenta pro virtuální počítače se systémem Azure Linux**, vyberte počítač a klikněte na **připojit**. Pokud jste zvolili **Stáhnout a nainstalovat agenta pro virtuální počítače, které nejsou na platformě Azure Linux**, na obrazovce **Direct agent** spusťte skript v části stáhnout a začlenit **agenta pro Linux**.
1. Na obrazovce konektoru v části **Konfigurovat a přeposlání SYSLOG**nastavte, jestli se má démon syslog **rsyslog. d** nebo **syslog-ng**. 
1. Zkopírujte tyto příkazy a spusťte je na svém zařízení:
    - Pokud jste vybrali rsyslog. d:
              
      1. Informujte démona syslog, aby naslouchal na zařízení local_4 a odesílal zprávy syslog do agenta Azure Sentinel pomocí portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
      2. Stáhněte a nainstalujte [konfigurační soubor security_events](https://aka.ms/asi-syslog-config-file-linux) , který konfiguruje agenta syslog k naslouchání na portu 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Kde {0} by měl být nahrazen identifikátorem GUID pracovního prostoru.
            
      1. Restartujte proces démona syslogu.`sudo service rsyslog restart`
             
    - Pokud jste vybrali syslog-ng:

      1. Informujte démona syslog, aby naslouchal na zařízení local_4 a odesílal zprávy syslog do agenta Azure Sentinel pomocí portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      2. Stáhněte a nainstalujte [konfigurační soubor security_events](https://aka.ms/asi-syslog-config-file-linux) , který konfiguruje agenta syslog k naslouchání na portu 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Kde {0} by měl být nahrazen identifikátorem GUID pracovního prostoru.

      3. Restartujte proces démona syslogu.`sudo service syslog-ng restart`
 1. Restartujte agenta syslog pomocí tohoto příkazu:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
 1. Spuštěním tohoto příkazu ověřte, že protokol agenta neobsahuje chyby:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


## <a name="step-2-forward-f5-logs-to-the-syslog-agent"></a>Krok 2: Přeposílání protokolů F5 do agenta syslog

Nakonfigurujte F5 pro přeposílání zpráv syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog:

Přejděte na F5 [Konfigurace protokolování událostí zabezpečení aplikace](https://aka.ms/asi-syslog-f5-forwarding)a podle pokynů nastavte vzdálené protokolování, a to podle následujících pokynů:
  - Nastavte **typ vzdáleného úložiště** na **CEF**.
  - Nastavte **protokol** na **UDP**.
  - Nastavte **IP adresu** na IP adresu serveru syslog.
  - Nastavte **číslo portu** na **514**nebo port, který nastavíte pro použití v agentovi.
  - Nastavte **zařízení** na tu, kterou jste nastavili v agentu syslogu (ve výchozím nastavení agent tuto hodnotu nastaví na **LOCAL4**).
  - Můžete nastavit **maximální velikost řetězce dotazu** na velikost, kterou jste nastavili v agentovi.

## <a name="step-3-validate-connectivity"></a>Krok 3: Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

1. Ujistěte se, že používáte správné zařízení. Zařízení musí být ve vašem zařízení stejné a v Azure Sentinel. Můžete zjistit, který soubor zařízení používáte v rámci Azure Sentinel, a upravit ho v souboru `security-config-omsagent.conf`. 

2. Zajistěte, aby se protokoly načítají do správného portu agenta syslog. Spusťte tento příkaz na počítači agenta syslog: `tcpdump -A -ni any  port 514 -vv`Tento příkaz zobrazí protokoly, které ze zařízení streamují do počítače syslog. Zajistěte, aby byly protokoly přijímány ze zdrojového zařízení na správném portu a v pravém zařízení.

3. Ujistěte se, že protokoly, které odesíláte, vyhovují [specifikaci RFC 3164](https://tools.ietf.org/html/rfc3164).

4. V počítači, na kterém je spuštěn Agent syslog, se ujistěte, že jsou porty 514, 25226 otevřené a naslouchající, `netstat -a -n:`pomocí příkazu. Další informace o použití tohoto příkazu naleznete v tématu [netstat (8) – Linux Man](https://linux.die.net/man/8/netstat). Pokud je naslouchá správně, uvidíte toto:

   ![Porty Sentinel Azure](./media/connect-cef/ports.png) 

5. Ujistěte se, že démon je nastaven na naslouchání na portu 514, na který odesíláte protokoly.
    - Pro rsyslog:<br>Ujistěte se, že tento `/etc/rsyslog.conf` soubor obsahuje tuto konfiguraci:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Další informace najdete v tématu [imudp: Vstupní modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) a [imtcp UDP protokolu UDP: Vstupní modul protokolu TCP syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Pro syslog-ng:<br>Ujistěte se, že tento `/etc/syslog-ng/syslog-ng.conf` soubor obsahuje tuto konfiguraci:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Další informace naleznete v tématu [syslog-ng Open Source Edition 3,16-příručka pro správu](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Ověřte, zda mezi démonem syslog a agentem probíhá komunikace. Spusťte tento příkaz na počítači agenta syslog: `tcpdump -A -ni any  port 25226 -vv`Tento příkaz zobrazí protokoly, které ze zařízení streamují do počítače syslog. Ujistěte se, že jsou protokoly také přijímány v agentovi.

6. Pokud se oba tyto příkazy dodávají úspěšně, zkontrolujte Log Analytics a zjistěte, jestli jsou protokoly přicházející. Všechny události streamované z těchto zařízení se zobrazí v nezpracované podobě v `CommonSecurityLog` Log Analytics pod položkou Typ.

7. Pokud chcete zjistit, jestli se vyskytly chyby, nebo jestli se protokoly nepřicházejí `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`, podívejte se na. Pokud se vyskytnou chyby neshody formátu protokolu, přejděte na `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` soubor `security_events.conf`a podívejte se na něj a ujistěte se, že protokoly odpovídají formátu regulárního výrazu, který vidíte v tomto souboru.

8. Ujistěte se, že výchozí velikost zprávy syslog je omezená na 2048 bajtů (2 KB). Pokud jsou protokoly moc dlouhé, aktualizujte security_events. conf pomocí tohoto příkazu:`message_length_limit 4096`



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení F5 ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

