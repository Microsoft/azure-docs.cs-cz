---
title: Připojení dat Fortinet k Azure Sentinel Preview | Microsoft Docs
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
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 339b8c1b59720989016f68fdb94fae30c26b42f0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679286"
---
# <a name="connect-your-fortinet-appliance"></a>Připojení zařízení Fortinet

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Nedoporučujeme ho pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Službu Azure Sentinel můžete připojit k jakémukoli Fortinet zařízení tím, že soubory protokolu uložíte jako formát CEF (Common Event Format) syslog. Díky integraci se službou Azure Sentinel můžete snadno spouštět analýzy a dotazy napříč daty souboru protokolu z Fortinet. Další informace o tom, jak Azure Sentinel ingestuje CEF data, najdete v tématu [připojení zařízení CEF](connect-common-event-format.md).

> [!NOTE]
> Data se ukládají do geografického umístění pracovního prostoru, na kterém spouštíte Azure Sentinel.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Krok 1: Připojení zařízení Fortinet pomocí agenta

Pokud chcete připojit zařízení Fortinet ke službě Azure Sentinel, nasaďte agenta na vyhrazený virtuální počítač nebo místní počítač pro podporu komunikace mezi zařízením a službou Azure Sentinel. 

Agenta můžete také nasadit ručně na stávající virtuální počítač Azure, na VIRTUÁLNÍm počítači v jiném cloudu nebo na místním počítači.

> [!NOTE]
> Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. 

Postup zobrazení síťového diagramu obou možností najdete v tématu [připojení zdrojů dat](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Nasazení agenta

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Fortinet** a pak **otevřete stránku konektor**. 

1. V části **Stáhnout a nainstalovat agenta SYSLOG**vyberte typ počítače, buď Azure, nebo místní. 
1. Na obrazovce **virtuální počítače** , která se otevře, vyberte počítač, který chcete použít, a klikněte na **připojit**.
1. Pokud zvolíte **Stáhnout a nainstalovat agenta pro virtuální počítače se systémem Azure Linux**, vyberte počítač a klikněte na **připojit**. Pokud jste zvolili **Stáhnout a nainstalovat agenta pro virtuální počítače, které nejsou na platformě Azure Linux**, na obrazovce **Direct agent** spusťte skript v části stáhnout a začlenit **agenta pro Linux**.
1. Na obrazovce konektoru v části **Konfigurovat a přeposlání SYSLOG**nastavte, jestli se má démon syslog **rsyslog. d** nebo **syslog-ng**. 
1. Zkopírujte tyto příkazy a spusťte je na svém zařízení:
   - Pokud jste vybrali rsyslog. d:
            
     1. Informujte démona syslog, aby naslouchal na zařízení local_4 a odesílal zprávy syslog do agenta Azure Sentinel pomocí portu 25226. Použijte tento příkaz:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. Stáhněte a nainstalujte [konfigurační soubor security_events](https://aka.ms/asi-syslog-config-file-linux) , který konfiguruje agenta syslog k naslouchání na portu 25226. Použijte tento příkaz: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` kde {0} by se měl nahradit identifikátorem GUID pracovního prostoru.
     1. Restartujte proces démona syslog pomocí tohoto příkazu:`sudo service rsyslog restart`
            
   - Pokud jste vybrali syslog-ng:

      1. Informujte démona syslog, aby naslouchal na zařízení local_4 a odesílal zprávy syslog do agenta Azure Sentinel pomocí portu 25226. Použijte tento příkaz:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. Stáhněte a nainstalujte [konfigurační soubor security_events](https://aka.ms/asi-syslog-config-file-linux) , který konfiguruje agenta syslog k naslouchání na portu 25226. Použijte tento příkaz: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` kde {0} by se měl nahradit identifikátorem GUID pracovního prostoru.
      1. Restartujte proces démona syslog pomocí tohoto příkazu:`sudo service syslog-ng restart`
1. Restartujte agenta syslog pomocí tohoto příkazu:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Spuštěním tohoto příkazu ověřte, že protokol agenta neobsahuje chyby:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Krok 2: Přeposílání protokolů Fortinet do agenta syslog

Nakonfigurujte Fortinet pro přeposílání zpráv syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Otevřete rozhraní příkazového řádku na zařízení Fortinet a spusťte následující příkazy:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Nahraďte **IP adresu** serveru IP adresou agenta.
    - Nastavte **facility_name** na použití zařízení, které jste nakonfigurovali v agentovi. Ve výchozím nastavení je agent nastaven na LOCAL4.
    - Nastavte **port SYSLOG** na **514** nebo na port nastavený na agentovi.
    - Pokud chcete povolit formát CEF ve verzích rané FortiOS, možná budete muset spustit sadu příkazů **CSV Disable**.
 
   > [!NOTE] 
   > Další informace najdete v [knihovně dokumentů Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Vyberte svou verzi a použijte **odkaz na zprávu** **příručky** a protokolu.

 Pokud chcete použít příslušné schéma v Azure Monitor Log Analytics pro události Fortinet, vyhledejte `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Krok 3: Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

1. Ujistěte se, že používáte správné zařízení. Zařízení musí být ve vašem zařízení stejné a v Azure Sentinel. Můžete zjistit, který soubor zařízení používáte v rámci Azure Sentinel, a upravit ho v souboru `security-config-omsagent.conf`. 

2. Zajistěte, aby se protokoly načítají do správného portu agenta syslog. Spusťte tento příkaz na počítači agenta syslog: `tcpdump -A -ni any  port 514 -vv`. Tento příkaz zobrazí protokoly, které streamuje ze zařízení do počítače syslog. Zajistěte, aby byly protokoly přijímány ze zdrojového zařízení na správném portu a na správném zařízení.

3. Ujistěte se, že protokoly, které odesíláte, vyhovují [specifikaci RFC 3164](https://tools.ietf.org/html/rfc3164).

4. V počítači, na kterém je spuštěn Agent syslog, se ujistěte, že jsou porty 514 a 25226 otevřené a naslouchající pomocí `netstat -a -n:`příkazu. Další informace o použití tohoto příkazu naleznete v tématu [netstat (8) – Linux Man](https://linux.die.net/man/8/netstat). Pokud je naslouchá správně, zobrazí se:

   ![Porty Sentinel Azure](./media/connect-cef/ports.png) 

5. Ujistěte se, že démon je nastaven na naslouchání na portu 514, na který odesíláte protokoly.
    - Pro rsyslog:<br>Ujistěte se, že tento `/etc/rsyslog.conf` soubor obsahuje tuto konfiguraci:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Další informace najdete v tématu [imudp: Vstupní modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) a [imtcp UDP protokolu UDP: Vstupní modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)protokolu TCP syslog.

   - Pro syslog-ng:<br>Ujistěte se, že tento `/etc/syslog-ng/syslog-ng.conf` soubor obsahuje tuto konfiguraci:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Další informace najdete v tématu [imudp: Vstupní modul](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) protokolu UDP syslog a [syslog-ng Open Source Edition 3,16 – příručka pro správu](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)

1. Ověřte, zda se jedná o komunikaci mezi démonem syslog a agentem. Spusťte tento příkaz na počítači agenta syslog: `tcpdump -A -ni any  port 25226 -vv`. Tento příkaz zobrazí protokoly, které streamuje ze zařízení do počítače syslog. Ujistěte se, že jsou protokoly také přijímány v agentovi.

6. Pokud se oba tyto příkazy dodávají úspěšně, zkontrolujte Log Analytics a zjistěte, jestli jsou protokoly přicházející. Všechny události streamované z těchto zařízení se zobrazí v nezpracované podobě v `CommonSecurityLog` Log Analytics pod položkou Typ.

7. Pokud chcete zjistit, jestli se vyskytly chyby, nebo jestli se protokoly nepřicházejí `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`, podívejte se na. Pokud se zobrazí chybové zprávy o neshodě formátu protokolu, otevřete `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` soubor `security_events.conf`a podívejte se na něj. Ujistěte se, že protokoly odpovídají formátu regulárního výrazu, který vidíte v tomto souboru.

8. Ujistěte se, že výchozí velikost zprávy syslog je omezená na 2048 bajtů (2 KB). Pokud jsou protokoly moc dlouhé, aktualizujte security_events. conf pomocí tohoto příkazu:`message_length_limit 4096`

10. Pokud agent Fortinet protokoly nepřijímá, spusťte tento příkaz v závislosti na typu démona syslog, který používáte, k nastavení zařízení a nastavení protokolů pro hledání slova Fortinet v protokolech:
       - rsyslog. d:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Restartujte proces démona syslog pomocí tohoto příkazu:`sudo service rsyslog restart`
       - syslog-ng:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Restartujte proces démona syslog pomocí tohoto příkazu:`sudo service syslog-ng restart`

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak připojit zařízení Fortinet ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).

