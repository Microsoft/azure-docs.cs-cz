---
title: Propojení dat Palo Alto Networks Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Informace o připojení k Azure Sentinelu Palo Alto Networks data.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 40ee73b8cc9b95a4e2030ac38a6c322918dc878e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389100"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Připojit zařízení Palo Alto Networks

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Je možné připojit Sentinelu Azure do všech zařízení Palo Alto Networks s ukládání souborů protokolu ve formátu Syslog CEF. Integrace s Azure Sentinelu umožňuje snadno spouštět analýzy a dotazy napříč data protokolů z Palo Alto Networks. Další informace o tom, jak Azure Sentinelu ingestuje CEF data, naleznete v tématu [zařízení připojit CEF](connect-common-event-format.md).

> [!NOTE]
> Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>Krok 1: Připojit zařízení Palo Alto Networks pomocí agenta

Pro vaše zařízení Palo Alto Networks připojení k Azure Sentinelu, budete muset nasadit agenta na vyhrazený počítač (VM v místním prostředí) aby mohly podporovat komunikaci mezi zařízením a Sentinelu Azure. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je pouze k dispozici, pokud je vyhrazený počítač v Azure vytváříte nový virtuální počítač. 

Alternativně můžete nasadit agenta ručně na existující virtuální počítač Azure, na virtuálním počítači v jiném cloudu nebo na místním počítači.

Síťový diagram z obou možností najdete v tématu [připojení zdroje dat](connect-data-sources.md#agent-options).


### <a name="deploy-the-agent-in-azure"></a>Nasazení agenta v Azure

1. Na portálu Azure Sentinelu, klikněte na tlačítko **datové konektory** a vyberte typ zařízení. 

1. V části **konfigurace agenta protokolu Syslog v Linuxu**:
   - Zvolte **automatického nasazení** Pokud chcete vytvořit nový počítač, který je předem nainstalovaný s agentem Azure Sentinelu a obsahuje všechny nezbytné konfigurace, jak je popsáno výše. Vyberte **automatického nasazení** a klikněte na tlačítko **nasazení agentů pro automatickou diagnostiku**. Tím přejdete na stránku nákupní pro vyhrazený virtuální počítač, který je automaticky připojena k pracovnímu prostoru, je. Je virtuální počítač **standardní virtuální počítač D2s v3 (2 virtuální procesory, 8 GB paměti)** a má veřejnou IP adresu.
      1. V **vlastní nasazení** stránce zadejte své údaje a zvolte uživatelské jméno a heslo a pokud souhlasíte s podmínkami a ujednáními, zakoupit virtuální počítač.
      1. Konfigurace vašeho zařízení k odeslání protokolů pomocí nastavení uvedených na stránce připojení. Konektor obecný Common Event Format použijte tato nastavení:
         - Protokol = UDP
         - Port = 514
         - Zařízení = Local-4
         - Format = CEF
   - Zvolte **ručního nasazení** Pokud chcete použít existující virtuální počítač jako vyhrazený počítač s Linuxem do kterého by měla být nainstalován agent Sentinelu Azure. 
      1. V části **stažení a instalace agenta Syslog**vyberte **virtuálního počítače Azure s Linuxem**. 
      1. V **virtuálních počítačů** obrazovky, které se otevře, vyberte počítač, kterou chcete použít a klikněte na tlačítko **připojit**.
      1. Na obrazovce konektor v části **konfigurace a dopředné Syslog**, můžete nastavit, jestli je vaše démon procesu Syslog **rsyslog.d** nebo **syslog-ng**. 
      1. Zkopírujte tyto příkazy a spusťte je na vaše zařízení:
          - Pokud jste vybrali rsyslog.d:
              
            1. Démon procesu Syslog pro naslouchání local_4 zařízení a k odeslání zprávy Syslog do Azure ověřovací agent používá port 25226 říct. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.
            
            1. Restartujte démona syslogu `sudo service rsyslog restart`
             
          - Pokud jste vybrali syslog-ng:

              1. Démon procesu Syslog pro naslouchání local_4 zařízení a k odeslání zprávy Syslog do Azure ověřovací agent používá port 25226 říct. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.

              3. Restartujte démona syslogu `sudo service syslog-ng restart`
      2. Restartujte agenta Syslog pomocí tohoto příkazu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Potvrďte, že nejsou žádné chyby v protokolu agenta spuštěním tohoto příkazu: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Nasazení agenta na serveru Linux on premises

Pokud Azure nepoužíváte, ručně nasaďte agenta Sentinelu Azure ke spuštění na vyhrazeném serveru Linux.


1. Na portálu Azure Sentinelu, klikněte na tlačítko **datové konektory** a vyberte typ zařízení.
1. Chcete-li vytvořit vyhrazený virtuální počítač s Linuxem, v části **konfigurace agenta protokolu Syslog v Linuxu** zvolte **ručního nasazení**.
   1. V části **stažení a instalace agenta Syslog**vyberte **počítače mimo Azure Linux**. 
   1. V **přímý agent** obrazovku, která se otevře, vyberte **agenta pro Linux** stáhněte agenta nebo spuštěním tohoto příkazu si ho stáhnout na počítač s Linuxem:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Na obrazovce konektor v části **konfigurace a dopředné Syslog**, můžete nastavit, jestli je vaše démon procesu Syslog **rsyslog.d** nebo **syslog-ng**. 
      1. Zkopírujte tyto příkazy a spusťte je na vaše zařízení:
         - Pokud jste vybrali rsyslog:
           1. Démon procesu Syslog pro naslouchání local_4 zařízení a k odeslání zprávy Syslog do Azure ověřovací agent používá port 25226 říct. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.
           3. Restartujte démona syslogu `sudo service rsyslog restart`
         - Pokud jste vybrali syslog-ng:
            1. Démon procesu Syslog pro naslouchání local_4 zařízení a k odeslání zprávy Syslog do Azure ověřovací agent používá port 25226 říct. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Stáhněte a nainstalujte [security_events konfiguračního souboru](https://aka.ms/asi-syslog-config-file-linux) , který nakonfiguruje agenta Syslog pro naslouchání na port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Kde {0} by měla být nahrazena identifikátor GUID pracovního prostoru.
            3. Restartujte démona syslogu `sudo service syslog-ng restart`
      1. Restartujte agenta Syslog pomocí tohoto příkazu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Potvrďte, že nejsou žádné chyby v protokolu agenta spuštěním tohoto příkazu: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Krok 2: Předávání protokolů Palo Alto Networks agentům Syslog

Nakonfigurujte Palo Alto Networks předávat zprávy Syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta Syslog:
1.  Přejděte na [Průvodci konfigurací formát cef (Common Event Format)](https://docs.paloaltonetworks.com/resources/cef) a stahovat pdf pro váš typ zařízení. Postupujte podle všech pokynů v Průvodci nastavit vaše zařízení Palo Alto Networks shromažďování událostí CEF. 

1.  Přejděte na [monitorování konfigurace Syslog](https://aka.ms/asi-syslog-paloalto-forwarding) a postupujte podle kroků 2 a 3 a konfigurace předávání událostí CEF vaše zařízení Palo Alto Networks Sentinelu Azure.

    1. Nezapomeňte nastavit **formát serveru Syslog** k **BSD**.
    1. Ujistěte se, že nastavíte **zařízení číslo** na stejnou hodnotu, která jste nastavili v agentovi Syslog.

> [!NOTE]
> Operace kopírování a vložení ze souboru PDF může změnit text a vložte náhodných znaků. Abyste tomu předešli, zkopírujte text editoru a odeberte všechny znaky, které může to způsobit narušení formát protokolu před vložením, jak je vidět v tomto příkladu.
 
  ![CEF problém kopírovat text](./media/connect-cef/paloalto-text-prob1.png)

6. Použít příslušné schéma v Log Analytics pro Palo Alto Networks události, vyhledejte **CommonSecurityLog**.

## <a name="step-3-validate-connectivity"></a>Krok 3: Ověření připojení

Může trvat upwards of 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 

1. Ujistěte se, že používáte správné zařízení. Zařízení musí být stejné v zařízení a ověřovací Azure. Můžete zkontrolovat zařízení soubor, který používáte v Azure Sentinelu a upravte v souboru `security-config-omsagent.conf`. 

2. Ujistěte se, že vaše protokoly se zobrazuje na správný port Syslog agenta. Tento příkaz spustíte na počítači agenta Syslog: `tcpdump -A -ni any  port 514 -vv` Tento příkaz zobrazí protokoly, které datové proudy ze zařízení do počítače Syslog. Ujistěte se, že se přijímají protokoly ze zdrojového zařízení na správný port a správné zařízení.

3. Ujistěte se, že protokoly můžete odeslat dodržovat [RFC 5424](https://tools.ietf.org/html/rfc542).

4. Na počítači se systémem agenta Syslog, ujistěte se, že tyto porty 514, 25226 jsou otevřené a naslouchá, pomocí příkazu `netstat -a -n:`. Další informace o použití tohoto příkazu naleznete v tématu [netstat(8) - Linux man stránky](https://linux.die.net/man/8/netstat). Pokud naslouchá správně, uvidíte následující:

   ![Azure porty Sentinel](./media/connect-cef/ports.png) 

5. Ujistěte se, že démon je nastaven tak, aby naslouchala na portu 514, na kterém chcete odeslat protokoly.
    - Pro rsyslog:<br>Ujistěte se, že soubor `/etc/rsyslog.conf` tato konfigurace zahrnuje:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Další informace najdete v tématu [imudp: UDP Syslog vstupu modulu](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) a [imtcp: TCP modulu Syslog vstup](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Pro syslog-ng:<br>Ujistěte se, že soubor `/etc/syslog-ng/syslog-ng.conf` tato konfigurace zahrnuje:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Další informace najdete v tématu [imudp: UDP Syslog vstupu modulu] (Další informace najdete v tématu [syslog-ng otevřít 3,16 edici zdroje – Příručka pro správu](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Zkontrolujte, zda je komunikace mezi démona Syslogu a agenta. Tento příkaz spustíte na počítači agenta Syslog: `tcpdump -A -ni any  port 25226 -vv` Tento příkaz zobrazí protokoly, které datové proudy ze zařízení do počítače Syslog. Ujistěte se, že protokoly se také přijímají na agentovi.

6. Pokud obě tyto příkazy úspěšné výsledky, zkontrolujte Log Analytics, pokud chcete zobrazit, pokud vaše protokoly přicházejí. Všechny události Streamovat z těchto zařízení se zobrazí v nezpracovaném tvaru v Log Analytics v části `CommonSecurityLog` typu.

7. Zkontrolujte, jestli jsou chyby, nebo pokud nejsou přicházející v protokolech, vyhledejte `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Když je ve stavu chyby neshoda formát protokolu, přejděte na `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` a vyhledejte v souboru `security_events.conf`a ujistěte se, že vaše protokoly odpovídat formátu regulárního výrazu se zobrazí v tomto souboru.

8. Ujistěte se, že velikost výchozí zprávy Syslog je omezená na 2 048 bajtů. (2KB). Pokud jsou příliš dlouhé protokoly, aktualizujte security_events.conf pomocí tohoto příkazu: `message_length_limit 4096`








## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu zařízení Palo Alto Networks. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

