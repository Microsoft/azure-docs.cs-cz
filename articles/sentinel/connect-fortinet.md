---
title: Propojení dat Fortinet Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Informace o připojení k Azure Sentinelu Fortinet data.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 6f061a99a526b3865081a7d19b1eecbdc158d6a1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442659"
---
# <a name="connect-your-fortinet-appliance"></a>Připojit zařízení Fortinet

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze preview neposkytujeme bez smlouvy o úrovni služeb. To nedoporučujeme pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v tématu [dodatečnými podmínkami použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete připojit Sentinelu Azure do všech zařízení Fortinet uložením souboru protokolu jako formát cef (Syslog Common Event Format). Díky integraci s Azure Sentinelu můžete snadno spouštět analýzy a dotazy napříč data protokolů z Fortinet. Další informace o tom, jak Azure Sentinelu ingestuje CEF data, naleznete v tématu [zařízení připojit CEF](connect-common-event-format.md).

> [!NOTE]
> Data se ukládají v zeměpisné oblasti pracovního prostoru, na kterém jste spustili Sentinelu Azure.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Krok 1: Připojení vašich zařízení Fortinet pomocí agenta

Pro vaše zařízení Fortinet připojení k Azure Sentinelu, nasazení agenta ve vyhrazený virtuální počítač nebo místní počítač, aby mohly podporovat komunikaci mezi zařízením a Sentinelu Azure. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je pouze k dispozici, pokud je vyhrazený počítač nový virtuální počítač, který vytvoříte v Azure.

Můžete také nasadit agenta ručně na existující virtuální počítač Azure, na virtuálním počítači v jiném cloudu nebo na místním počítači.

Síťový diagram z obou možností najdete v tématu [připojení zdroje dat](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Nasazení agenta v Azure

1. Na portálu Azure Sentinelu vyberte **datové konektory** a vyberte typ zařízení.

1. V části **konfigurace agenta protokolu Syslog v Linuxu**:
   - Zvolte **automatického nasazení** Pokud chcete vytvořit nový počítač, který je předinstalován pomocí agenta Azure Sentinelu a zahrnuje veškeré konfigurace nezbytné, jak je uvedeno výše. Vyberte **automatického nasazení** > **nasazení agentů pro automatickou diagnostiku**. Zobrazí se stránka nákupní vyhrazený virtuální počítač, který je automaticky připojený k pracovnímu prostoru. Je virtuální počítač **standardní virtuální počítač D2s v3 (2 virtuální procesory, 8 GB paměti)** a má veřejnou IP adresu.
      1. Na **vlastní nasazení** stránce, zadejte své údaje, zadejte uživatelské jméno a heslo a pokud souhlasíte s podmínkami a ujednáními, zakoupit virtuální počítač.
      1. Konfigurace vašeho zařízení k odeslání protokolů s použitím nastavení uvedených na stránce připojení. Konektor obecný Common Event Format použijte tato nastavení:
         - Protokol = UDP
         - Port = 514
         - Zařízení = Local-4
         - Format = CEF
   - Zvolte **ručního nasazení** Pokud chcete použít existující virtuální počítač jako vyhrazený počítač Linux, na kterém je nainstalovaný agent Sentinelu Azure. 
      1. V části **stažení a instalace agenta Syslog**vyberte **virtuálního počítače Azure s Linuxem**. 
      1. Na **virtuálních počítačů** obrazovky, vyberte počítač, který chcete použít a vyberte **připojit**.
      1. Na obrazovce konektor v části **konfigurace a dopředné Syslog**, můžete nastavit, jestli je vaše démon procesu Syslog **rsyslog.d** nebo **syslog-ng**. 
      1. Zkopírujte tyto příkazy a spusťte je na vaše zařízení:
          - Pokud jste vybrali rsyslog.d:
              
            1. Dejte vědět, démon procesu Syslog tak, aby naslouchala na local_4 zařízení a k odeslání zprávy Syslog do agenta Azure Sentinelu pomocí port 25226. Použijte tento příkaz: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. Použijte tento příkaz: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.
            1. Restartujte démona syslogu pomocí tohoto příkazu: `sudo service rsyslog restart`
             
          - Pokud jste vybrali syslog-ng:

              1. Dejte vědět, démon procesu Syslog tak, aby naslouchala na local_4 zařízení a k odeslání zprávy Syslog do agenta Azure Sentinelu pomocí port 25226. Použijte tento příkaz: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. Použijte tento příkaz: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.
              1. Restartujte démona syslogu pomocí tohoto příkazu: `sudo service syslog-ng restart`
      1. Restartujte agenta Syslog pomocí tohoto příkazu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Potvrďte, že nejsou žádné chyby v protokolu agenta spuštěním tohoto příkazu: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Nasazení agenta na serveru v místním systému Linux

Pokud Azure nepoužíváte, ručně nasaďte agenta Sentinelu Azure ke spuštění na vyhrazeném serveru Linux.

1. Na portálu Azure Sentinelu vyberte **datové konektory** a vyberte typ zařízení.
1. Chcete-li vytvořit vyhrazený virtuální počítač s Linuxem, v části **konfigurace agenta protokolu Syslog v Linuxu** vyberte **ručního nasazení**.

    1. V části **stažení a instalace agenta Syslog**vyberte **počítače mimo Azure Linux**.
    1. V **přímý agent** obrazovku, která se otevře, vyberte **agenta pro Linux** stáhněte agenta nebo spuštěním tohoto příkazu si ho stáhnout na počítač s Linuxem: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. Na obrazovce konektor v části **konfigurace a dopředné Syslog**, můžete nastavit, jestli je vaše démon procesu Syslog **rsyslog.d** nebo **syslog-ng**.
       1. Zkopírujte tyto příkazy a spusťte je na vaše zařízení:

          - Pokud jste vybrali rsyslog:

            1. Démon procesu Syslog pro naslouchání local_4 zařízení a k odeslání zprávy Syslog do Azure ověřovací agent používá port 25226 říct. Použijte tento příkaz: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. Použijte tento příkaz: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.
            1. Restartujte démona syslogu pomocí tohoto příkazu: `sudo service rsyslog restart`

          - Pokud jste vybrali syslog-ng:

            1. Dejte vědět, démon procesu Syslog tak, aby naslouchala na local_4 zařízení a k odeslání zprávy Syslog do agenta Azure Sentinelu pomocí port 25226. Použijte tento příkaz: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. Použijte tento příkaz: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.
            1. Restartujte démona syslogu pomocí tohoto příkazu: `sudo service syslog-ng restart`

      1. Restartujte agenta Syslog pomocí tohoto příkazu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Potvrďte, že nejsou žádné chyby v protokolu agenta spuštěním tohoto příkazu: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Krok 2: Předávání protokolů Fortinet agentům Syslog

Nakonfigurujte Fortinet předávat zprávy Syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta Syslog.

1. Otevřete rozhraní příkazového řádku na vaše zařízení Fortinet a spusťte následující příkazy:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Nahraďte server **ip adresu** s IP adresou agenta.
    - Nastavte **facility_name** používat zařízení, které jste nakonfigurovali v agentovi. Ve výchozím nastavení agent nastaví to local4.
    - Nastavte **syslog port** k **514** nebo port nastaven na agentovi.
    - Povolit formátu CEF v dřívějších verzích FortiOS, budete pravděpodobně potřebovat ke spuštění sady příkazů **sdíleného svazku clusteru zakázat**.
 
   > [!NOTE] 
   > Další informace najdete v části [knihovny dokumentů Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Vyberte svou verzi a použít **Příručka** a **odkaz na zprávu protokolu**.

 Použít příslušné schéma ve službě Azure Monitor Log Analytics pro události Fortinet, vyhledejte `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Krok 3: Ověření připojení

Může trvat až 20 minut, dokud vaše protokoly spuštění se zobrazí v Log Analytics. 

1. Ujistěte se, že používáte správné zařízení. Zařízení musí být stejné v zařízení a ověřovací Azure. Můžete zkontrolovat zařízení soubor, který používáte v Azure Sentinelu a upravte v souboru `security-config-omsagent.conf`. 

2. Ujistěte se, že vaše protokoly se zobrazuje na správný port Syslog agenta. Tento příkaz spustit na počítači agenta Syslog: `tcpdump -A -ni any  port 514 -vv`. Tento příkaz zobrazí protokoly, které streamování ze zařízení do počítače Syslog. Ujistěte se, že se přijímají protokoly ze zdrojového zařízení na správný port a správné zařízení.

3. Ujistěte se, že protokoly můžete odeslat dodržovat [RFC 5424](https://tools.ietf.org/html/rfc542).

4. V počítači běží Syslog agent, ujistěte se, že jsou porty 514 a 25226 pomocí příkazu Otevřít a naslouchá `netstat -a -n:`. Další informace o použití tohoto příkazu najdete v tématu [netstat(8) - Linux man stránky](https://linux.die.net/man/8/netstat). Pokud naslouchá správně, zobrazí:

   ![Azure porty Sentinel](./media/connect-cef/ports.png) 

5. Ujistěte se, že démon je nastaven tak, aby naslouchala na portu 514, na kterém chcete odeslat protokoly.
    - Pro rsyslog:<br>Ujistěte se, že soubor `/etc/rsyslog.conf` tato konfigurace zahrnuje:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Další informace najdete v tématu [imudp: Vstupní modulu UDP Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) a [imtcp: Vstupní modulu TCP Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Pro syslog-ng:<br>Ujistěte se, že soubor `/etc/syslog-ng/syslog-ng.conf` tato konfigurace zahrnuje:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Další informace najdete v tématu [imudp: Vstupní modulu UDP Syslog](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) a [syslog-ng Open source edice 3,16 – Příručka pro správu](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Zkontrolujte, zda je komunikace mezi démona Syslogu a agenta. Tento příkaz spustit na počítači agenta Syslog: `tcpdump -A -ni any  port 25226 -vv`. Tento příkaz zobrazí protokoly, které streamování ze zařízení do počítače Syslog. Ujistěte se, že protokoly se také přijímají na agentovi.

6. Pokud obě tyto příkazy úspěšné výsledky, zkontrolujte Log Analytics, pokud chcete zobrazit, pokud vaše protokoly přicházejí. Všechny události Streamovat z těchto zařízení se zobrazí v nezpracovaném tvaru v Log Analytics v části `CommonSecurityLog` typu.

7. Zkontrolujte, jestli jsou chyby, nebo pokud nejsou přicházející v protokolech, vyhledejte `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Když je ve stavu chyby neshoda formát protokolu, přejděte na `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` a vyhledejte v souboru `security_events.conf`. Ujistěte se, že vaše protokoly odpovídat formátu regulárního výrazu, které se zobrazí v tomto souboru.

8. Ujistěte se, že velikost výchozí zprávy Syslog je omezená na 2 048 bajtů. (2 KB). Pokud jsou příliš dlouhé protokoly, aktualizujte security_events.conf pomocí tohoto příkazu: `message_length_limit 4096`

10. Pokud vaše protokoly Fortinet nejsou přijímá agenta, spusťte tento příkaz, v závislosti na tom, jaký typ démon procesu Syslog, které používáte, k nastavení zařízení a protokoly pro hledání v protokolech slovo Fortinet nastavení:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Restartujte démona Syslogu pomocí tohoto příkazu: `sudo service rsyslog restart`
       - Syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Restartujte démona Syslogu pomocí tohoto příkazu: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak se připojit k Azure Sentinelu Fortinet zařízení. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

