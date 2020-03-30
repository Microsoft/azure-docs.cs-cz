---
title: Připojení dat Syslogu k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit data Syslogu s Azure Sentinelem.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588072"
---
# <a name="connect-your-external-solution-using-syslog"></a>Připojte externí řešení pomocí syslogu

K Azure Sentinelu můžete připojit libovolné místní zařízení, které podporuje Syslog. To se provádí pomocí agenta založeného na počítači s Linuxem mezi zařízením a Azure Sentinelem. Pokud je váš počítač s Linuxem v Azure, můžete streamovat protokoly z vašeho zařízení nebo aplikace do vyhrazeného pracovního prostoru, který vytvoříte v Azure a připojíte. Pokud váš počítač s Linuxem není v Azure, můžete streamovat protokoly z vašeho zařízení do vyhrazeného virtuálního počítače nebo počítače, do kterého nainstalujete agenta pro Linux. 

> [!NOTE]
> Pokud váš spotřebič podporuje Syslog CEF, připojení je úplnější a měli byste zvolit tuto možnost a postupovat podle pokynů v [části Připojení dat z CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Jak to funguje

Syslog je protokol protokolování událostí, který je společný pro Linux. Aplikace budou odesílat zprávy, které mohou být uloženy v místním počítači nebo doručeny do kolektoru Syslog. Když je nainstalován agent Log Analytics pro Linux, nakonfiguruje místního daemona Syslogu tak, aby přesměrovává zprávy agentovi. Agent pak odešle zprávu azure monitoru, kde je vytvořen odpovídající záznam.

Další informace najdete v tématu [Zdroje dat Syslog v Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - Agent může shromažďovat protokoly z více zdrojů, ale musí být nainstalován na vyhrazeném počítači proxy.
> - Pokud chcete podporovat konektory pro CEF a Syslog na stejném virtuálním počítači, proveďte následující kroky, abyste zabránili duplikování dat:
>    1. Postupujte podle pokynů pro [připojení cef](connect-common-event-format.md).
>    2. Chcete-li propojit data syslogu, přejděte na **nastavení** > **nastavení** > pracovního prostoru**Upřesnit nastavení** > **Data** > **Syslog** a nastavte zařízení a jejich priority tak, aby se nejedná o stejné zařízení a vlastnosti, které jste použili v konfiguraci nástroje Sdílení prostor. <br></br>Pokud vyberete **použít níže konfiguraci na mé počítače**, použije tyto setings pro všechny virtuální počítače připojené k tomuto pracovnímu prostoru.


## <a name="connect-your-syslog-appliance"></a>Připojte zařízení Syslog

1. V Azure Sentinelu vyberte **datové konektory** a pak vyberte konektor **Syslog.**

2. V okně **Syslog** vyberte **Otevřít stránku konektoru**.

3. Nainstalujte agenta Linuxu:
    
    - Pokud je váš virtuální počítač s Linuxem v Azure, vyberte **Stáhnout a nainstalovat agenta na virtuálním počítači Azure Linux**. V okně **Virtuální počítače** vyberte virtuální počítače, na které chcete agenta nainstalovat, a klikněte na **připojit**.
    - Pokud váš počítač s Linuxem není v Azure, vyberte **Stáhnout a nainstalovat agenta na počítači s Linuxem, než je Azure**. V okně **Direct agent** zkopírujte příkaz ke stažení a palubní agent **pro LINUX** a spusťte jej v počítači. 
    
   > [!NOTE]
   > Ujistěte se, že jste pro tyto počítače nakonfigurovali nastavení zabezpečení podle zásad zabezpečení vaší organizace. Můžete například nakonfigurovat nastavení sítě tak, aby byla zarovnána se zásadami zabezpečení sítě vaší organizace, a změnit porty a protokoly v daemonu tak, aby odpovídaly požadavkům na zabezpečení.

4. Vyberte **Otevřít konfiguraci upřesňujících nastavení pracovního prostoru**.

5. V okně **Upřesnit nastavení** vyberte **položku Data** > **Syslog**. Pak přidejte zařízení pro konektor sbírat.
    
    Přidejte zařízení, které váš syslog zařízení obsahuje v záhlaví protokolu. Tuto konfiguraci můžete vidět ve vašem zařízení Syslog v `/etc/rsyslog.d/security-config-omsagent.conf` **Syslog-d** ve složce a v **r-Syslog** od `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Pokud chcete použít neobvyklé zjišťování přihlášení SSH s daty, které shromažďujete, přidejte **auth** a **authpriv**. Další podrobnosti naleznete v [následující části.](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)

6. Pokud jste přidali všechna zařízení, která chcete sledovat, a upravíte všechny možnosti závažnosti pro každou z nich, zaškrtněte políčko **Použít pod konfigurací na mé počítače**.

7. Vyberte **Uložit**. 

8. Na zařízení syslogu se ujistěte, že odesíláte zařízení, která jste zadali.

9. Chcete-li použít příslušné schéma v Azure Monitor u protokolů syslog, vyhledejte **Syslog**.

10. Funkci Kusto popsanou v [části Použití funkcí v dotazech protokolu Azure Monitor](../azure-monitor/log-query/functions.md) můžete použít k analýzu zpráv Syslog. Potom je můžete uložit jako novou funkci Log Analytics, která se použije jako nový datový typ.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurace konektoru Syslog pro neobvyklé zjišťování přihlášení SSH

> [!IMPORTANT]
> Neobvyklé zjišťování přihlášení SSH je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel můžete použít strojové učení (ML) na data syslog u identifikace neobvyklé aktivity přihlášení Zabezpečené prostředí (SSH). Scénáře zahrnují:

- Nemožné cestování – když dojde ke dvěma úspěšným událostem přihlášení ze dvou míst, které nelze dosáhnout v časovém rámci dvou událostí přihlášení.
- Neočekávané umístění – umístění, odkud došlo k úspěšnému přihlášení, je podezřelé. Například umístění nebylo naposledy zobrazeno.
 
Toto zjištění vyžaduje specifickou konfiguraci datového konektoru Syslog: 

1. Pro krok 5 v předchozím postupu, ujistěte se, že **auth** a **authpriv** jsou vybrány jako zařízení pro sledování. Ponechte výchozí nastavení pro možnosti závažnosti, aby byly všechny vybrány. Například:
    
    > [!div class="mx-imgBorder"]
    > ![Zařízení potřebná pro neobvyklé zjišťování přihlášení SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Vyčkejte dostatek času pro shromažďování informací o syslogu. Potom přejděte na **Azure Sentinel – protokoly**a zkopírujte a vložte následující dotaz:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    V případě potřeby změňte **časový rozsah** a vyberte **spustit**.
    
    Pokud je výsledný počet nula, potvrďte konfiguraci konektoru a že monitorované počítače mají úspěšnou aktivitu přihlášení za zadané časové období pro dotaz.
    
    Pokud výsledný počet je větší než nula, vaše data syslog je vhodný pro detekci neobvyklé přihlášení SSH. Toto zjišťování povolíte ze**šablon** > pravidel **Analytics** >  **(náhled) neobvyklé zjišťování přihlášení SSH**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit místní zařízení Syslog k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

