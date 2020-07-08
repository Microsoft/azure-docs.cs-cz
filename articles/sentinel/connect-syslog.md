---
title: Připojit data syslogu ke službě Azure Sentinel | Microsoft Docs
description: Připojte jakékoli místní zařízení, které podporuje syslog, do Azure Sentinel pomocí agenta v počítači se systémem Linux mezi zařízením a Sentinel. 
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
ms.openlocfilehash: 65c4e5d9e0752379541063c8a80a4316196ad7c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565377"
---
# <a name="connect-your-external-solution-using-syslog"></a>Připojení externího řešení pomocí protokolu syslog

Můžete připojit libovolné místní zařízení, které podporuje syslog, do Azure Sentinel. K tomu je potřeba použít agenta založeného na počítači se systémem Linux mezi zařízením a službou Sentinel Azure. Pokud je váš počítač se systémem Linux v Azure, můžete streamovat protokoly z vašeho zařízení nebo aplikace do vyhrazeného pracovního prostoru vytvořeného v Azure a připojit ho. Pokud Váš počítač se systémem Linux není v Azure, můžete streamovat protokoly ze zařízení na vyhrazený místní virtuální počítač nebo počítač, na který instalujete agenta pro Linux. 

> [!NOTE]
> Pokud vaše zařízení podporuje syslog CEF, připojení je více dokončeno a měli byste zvolit tuto možnost a postupovat podle pokynů v tématu [připojení dat z CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Jak to funguje

Syslog je protokol protokolování událostí, který je společný pro Linux. Aplikace budou odesílat zprávy, které mohou být uloženy v místním počítači nebo doručeny do kolekce syslog. Pokud je nainstalován agent Log Analytics pro Linux, nakonfiguruje místní démon syslog, aby předal zprávy agentovi. Agent potom zprávu pošle Azure Monitor, kde se vytvoří odpovídající záznam.

Další informace najdete v tématu [zdroje dat syslog v Azure monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - Agent může shromažďovat protokoly z více zdrojů, ale musí být nainstalovaný na vyhrazeném proxy počítači.
> - Pokud chcete podporovat konektory pro CEF i syslog na jednom virtuálním počítači, postupujte podle následujících kroků, abyste se vyhnuli duplicitování dat:
>    1. Postupujte podle pokynů pro [připojení CEF](connect-common-event-format.md).
>    2. Pokud chcete data protokolu syslog připojit, použijte **Nastavení**  >  **pracovní prostor**nastavení  >  **Upřesnit nastavení**  >  **data**  >  **syslog** a nastavte zařízení a jejich priority tak, aby nepoužívaly stejné možnosti a vlastnosti, které jste použili v konfiguraci CEF. <br></br>Pokud **na moje počítače vyberete použít**, použije se tato upravena vlastním nastavením na všechny virtuální počítače připojené k tomuto pracovnímu prostoru.


## <a name="connect-your-syslog-appliance"></a>Připojení zařízení syslog

1. V Azure Sentinel vyberte **datové konektory** a pak vyberte konektor **syslog** .

2. V okně **syslog** vyberte **otevřít stránku konektoru**.

3. Nainstalujte agenta pro Linux:
    
    - Pokud je váš virtuální počítač se systémem Linux v Azure, vyberte **Stáhnout a nainstalovat agenta na virtuálním počítači Azure Linux**. V okně **virtuální počítače** vyberte virtuální počítače, pro které chcete nainstalovat agenta, a pak klikněte na **připojit**.
    - Pokud počítač se systémem Linux není v Azure, vyberte **Stáhnout a nainstalovat agenta na počítači se systémem Linux mimo Azure**. V okně **přímý agent** zkopírujte příkaz pro **Stažení a zprovoznění agenta pro Linux** a spusťte ho na svém počítači. 
    
   > [!NOTE]
   > Ujistěte se, že pro tyto počítače nakonfigurujete nastavení zabezpečení podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat nastavení sítě tak, aby odpovídalo zásadám zabezpečení sítě ve vaší organizaci, a změnit porty a protokoly v procesu démona tak, aby odpovídaly požadavkům na zabezpečení.

4. Vyberte **otevřít konfiguraci rozšířeného nastavení v pracovním prostoru**.

5. V okně **Upřesnit nastavení** vyberte **data**  >  **syslog**. Pak přidejte zařízení, aby se konektor mohl shromažďovat.
    
    Přidejte do svých hlaviček protokolů zařízení, která vaše zařízení syslog zahrnuje. Tuto konfiguraci můžete zobrazit v zařízení syslog ve složce **syslog-d** `/etc/rsyslog.d/security-config-omsagent.conf` a v **r-syslog** z `/etc/syslog-ng/security-config-omsagent.conf` .
    
    Pokud chcete použít zjišťování přihlášení neobvyklé SSH s daty, která shromáždíte, přidejte **auth** a **authpriv**. Další podrobnosti najdete v [následující části](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

6. Po přidání všech zařízení, která chcete monitorovat, a upravení všech možností závažnosti pro každé z nich zaškrtněte políčko **použít pro tyto počítače níže uvedenou konfiguraci**.

7. Vyberte **Uložit**. 

8. V zařízení syslog se ujistěte, že posíláte zařízení, která jste zadali.

9. Chcete-li použít příslušné schéma v Azure Monitor pro protokoly syslog, vyhledejte protokol **SYSLOG**.

10. Pomocí funkce Kusto popsané v tématu [použití funkcí v Azure Monitorch dotazech protokolu](../azure-monitor/log-query/functions.md) můžete analyzovat zprávy syslog. Pak je můžete uložit jako novou funkci Log Analytics k použití jako nového datového typu.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurace konektoru syslog pro detekci přihlášení neobvyklé SSH

> [!IMPORTANT]
> Zjišťování přihlášení SSH neobvyklé je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel může použít Machine Learning (ML) na data syslog k identifikaci přihlašovací aktivity neobvyklé Secure Shell (SSH). Mezi scénáře patří:

- Nemožná cesta – když dojde k dvěma událostem úspěšného přihlášení ze dvou umístění, která nejsou v rámci časového rámce dvou přihlašovacích událostí možná dosažitelná.
- Neočekávané umístění – došlo k podezření na místo, kde došlo k úspěšné události přihlášení. Například umístění se nezobrazilo v poslední době.
 
Tato detekce vyžaduje specifickou konfiguraci konektoru dat syslog: 

1. V kroku 5 v předchozím postupu se ujistěte, že jsou jako zařízení, která chcete monitorovat, vybraná možnost **auth** i **authpriv** . U možností závažnosti nechte výchozí nastavení tak, aby byly všechny vybrané. Příklad:
    
    > [!div class="mx-imgBorder"]
    > ![Zařízení požadovaná pro detekci přihlášení neobvyklé SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Umožněte shromažďování informací syslogu dostatek času. Pak přejděte do části **Azure Sentinel-logs**a zkopírujte a vložte následující dotaz:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    V případě potřeby změňte **časový rozsah** a vyberte **Spustit**.
    
    Pokud je výsledný počet nula, potvrďte konfiguraci konektoru a monitorované počítače mají po dobu, kterou jste zadali pro dotaz, aktivitu úspěšného přihlášení.
    
    Pokud je výsledný počet větší než nula, data syslogu jsou vhodná pro detekci přihlášení neobvyklé SSH. Toto zjišťování povolíte pomocí **Analytics**  >   **šablon pravidel**analýz  >  **(Preview) neobvyklé zjišťování přihlášení SSH**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit místní zařízení syslog ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

