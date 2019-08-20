---
title: Připojení dat CEF k Azure Sentinel Preview | Microsoft Docs
description: Naučte se připojit data CEF ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2019
ms.author: rkarlin
ms.openlocfilehash: 28def73926294a025d70844e535a0856153ae30a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611932"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Připojení externího řešení pomocí běžných formátů událostí

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete připojit Azure Sentinel k externímu řešení, které umožňuje ukládat soubory protokolu v protokolu syslog. Pokud vaše zařízení umožňuje ukládat protokoly jako CEF (Common Event Format) syslog, Integration with Azure Sentinel vám umožní snadno spouštět analýzy a dotazy napříč daty.

> [!NOTE] 
> Data se ukládají do geografického umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="how-it-works"></a>Jak to funguje

Připojení mezi službou Azure Sentinel a zařízením CEF se provádí ve třech krocích:

1. Na zařízení je potřeba nastavit tyto hodnoty tak, aby zařízení odesílalo potřebné protokoly v potřebném formátu do agenta Azure Sentinel syslog, a to na základě Microsoft Monitoring Agent. Tyto parametry můžete upravit v zařízení, pokud je také upravíte v procesu démona syslog na agentu služby Azure Sentinel.
    - Protokol = UDP
    - Port = 514
    - Facilita = Local4
    - Format = CEF
2. Agent syslog data shromáždí a bezpečně ho pošle Log Analytics, kde je analyzovaný a obohacený.
3. Agent ukládá data do pracovního prostoru Log Analytics, takže se na ně můžete dotazovat podle potřeby, pomocí pravidel analýzy, korelace a řídicích panelů.

> [!NOTE]
> Agent může shromažďovat protokoly z více zdrojů, ale musí být nainstalovaný na vyhrazeném počítači.


 ![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně můžete agenta nasadit ručně na existující virtuální počítač Azure, na virtuální počítač v jiném cloudu nebo na místní počítač. 

 ![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Aspekty zabezpečení

Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. Pomocí následujících pokynů můžete zlepšit konfiguraci zabezpečení počítače:  [zabezpečený virtuální počítač v Azure](../virtual-machines/linux/security-policy.md), osvědčené [postupy pro zabezpečení sítě](../security/fundamentals/network-best-practices.md).

Pokud chcete používat komunikaci TLS mezi řešením zabezpečení a počítačem syslog, budete muset nakonfigurovat démona syslog (rsyslog nebo syslog-ng) pro komunikaci v TLS: [Šifrování provozu syslog pomocí TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [šifrování zpráv protokolu pomocí TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).


## <a name="step-1-configure-your-syslog-vm"></a>Krok 1: Konfigurace virtuálního počítače syslog

Musíte nasadit agenta na vyhrazený počítač se systémem Linux (virtuální počítač nebo místní), aby podporoval komunikaci mezi zařízením a službou Azure Sentinel. 

> [!NOTE]
> Nezapomeňte nakonfigurovat zabezpečení počítače podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat síť tak, aby byla v souladu se zásadami zabezpečení podnikové sítě, a změnit porty a protokoly v procesu démona tak, aby odpovídaly vašim požadavkům. 


1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Common Event Format (CEF)** a pak **otevřete stránku konektor**. 

1. V části **Stáhnout a nainstalovat agenta SYSLOG**vyberte typ počítače, buď Azure, nebo místní. 
1. Na obrazovce **virtuální počítače** , která se otevře, vyberte počítač, který chcete použít, a klikněte na **připojit**.
1. Pokud zvolíte **Stáhnout a nainstalovat agenta pro virtuální počítače se systémem Azure Linux**, vyberte počítač a klikněte na **připojit**. Pokud jste zvolili **Stáhnout a nainstalovat agenta pro virtuální počítače, které nejsou na platformě Azure Linux**, na obrazovce **Direct agent** spusťte skript v části stáhnout a začlenit **agenta pro Linux**.
1. Na obrazovce konektoru CEF v části **Konfigurovat a přeposlání SYSLOG**nastavte, zda je démon procesu Syslog **rsyslog. d** nebo **syslog-ng**. 
1. Zkopírujte tyto příkazy a spusťte je na svém zařízení:
    - Pokud jste vybrali rsyslog. d:
              
       1. Informujte démona syslog, aby naslouchal na zařízení local_4 a odesílal zprávy syslog do agenta Azure Sentinel pomocí portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Stáhněte a nainstalujte [konfigurační soubor security_events](https://aka.ms/asi-syslog-config-file-linux) , který konfiguruje agenta syslog k naslouchání na portu 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Kde {0} by měl být nahrazen identifikátorem GUID pracovního prostoru.
            
       1. Restartujte proces démona syslogu.`sudo service rsyslog restart`<br> Další informace najdete v [dokumentaci k rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) .
           
    - Pokud jste vybrali syslog-ng:
       1. Informujte démona syslog, aby naslouchal na zařízení local_4 a odesílal zprávy syslog do agenta Azure Sentinel pomocí portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Stáhněte a nainstalujte [konfigurační soubor security_events](https://aka.ms/asi-syslog-config-file-linux) , který konfiguruje agenta syslog k naslouchání na portu 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Kde {0} by měl být nahrazen identifikátorem GUID pracovního prostoru.

        3. Restartujte proces démona syslogu.`sudo service syslog-ng restart` <br>Další informace najdete v [dokumentaci k syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2) .
1. Restartujte agenta syslog pomocí tohoto příkazu:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Spuštěním tohoto příkazu ověřte, že protokol agenta neobsahuje chyby:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

Pokud chcete použít příslušné schéma v Log Analytics pro události CEF, vyhledejte `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Krok 2: Protokol CEF (Common Event Format) do agenta syslog

Nastavte řešení zabezpečení tak, aby odesílalo zprávy syslog ve formátu CEF do vašeho agenta syslog. Ujistěte se, že používáte stejné parametry, které se zobrazí v konfiguraci agenta. Obvykle jsou to:

- Port 514
- LOCAL4 zařízení

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


## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).

