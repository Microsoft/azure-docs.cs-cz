---
title: 'Rychlý Start: shromáždění dat z hybridního počítače se systémem Linux pomocí Azure Monitor'
description: V tomto rychlém startu se dozvíte, jak nasadit agenta Log Analytics pro počítače se systémem Linux běžící mimo Azure a povolit shromažďování dat pomocí protokolů Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: magoedte
ms.custom: mvc, seo-javascript-september2019
ms.openlocfilehash: 72f50754a28f0bbf5648ae64299d28ff13e2ec31
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703056"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Rychlý Start: shromáždění dat z počítače se systémem Linux v hybridním prostředí pomocí Azure Monitor

[Azure monitor](../overview.md) může shromažďovat data přímo z fyzických nebo virtuálních počítačů se systémem Linux ve vašem prostředí do pracovního prostoru Log Analytics pro účely podrobných analýz a korelace. Instalace [agenta Log Analytics](../platform/log-analytics-agent.md) umožňuje Azure monitor shromažďovat data z datového centra nebo jiného cloudového prostředí. V tomto rychlém startu se dozvíte, jak pomocí několika jednoduchých kroků nakonfigurovat a shromažďovat data ze serveru Linux. Informace o virtuálních počítačích Azure Linux najdete v tématu [shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Další informace o podporované konfiguraci najdete v tématu [podporované operační systémy Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) a [Konfigurace brány firewall sítě](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvořit pracovní prostor

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Po zahájení psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. Vyberte **vytvořit**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru Log Analytics**, například *DefaultLAWorkspace*.  
   * Vyberte **předplatné** , které chcete propojit, výběrem z rozevíracího seznamu, pokud výchozí hodnota není vhodná.
   * V případě **skupiny prostředků**vyberte existující skupinu prostředků, která obsahuje jeden nebo víc virtuálních počítačů Azure.  
   * Vyberte **umístění** , do kterého jsou nasazené vaše virtuální počítače.  Další informace najdete v tématu které [oblasti Log Analytics jsou dostupné v](https://azure.microsoft.com/regions/services/).
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *za GB* a možnost výběru cenové úrovně nebude k dispozici.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubna nebo do předplatného, které bylo vázáno na existující registraci EA, vyberte upřednostňovanou cenovou úroveň.  Další informace o jednotlivých vrstvách najdete v tématu [informace o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Okno pro vytvoření Log Analytics prostředku](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. Po zadání požadovaných informací v podokně **log Analyticsho pracovního prostoru** vyberte **OK**.  

I když se informace ověřují a vytvoří se pracovní prostor, můžete sledovat jeho průběh v části **oznámení** z nabídky. 

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru

Před instalací agenta Log Analytics pro Linux budete potřebovat ID a klíč pracovního prostoru pro pracovní prostor Log Analytics.  Tyto informace vyžaduje skript obálky agenta ke správné konfiguraci agenta a zajištění, aby mohl úspěšně komunikovat s Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. V levém horním rohu Azure Portal vyberte **všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory**.

2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste vytvořili dříve. (Je možné, že jste nastavili název **DefaultLAWorkspace**.)

3. Vybrat **upřesňující nastavení**:

    ![Log Analytics nastavení zálohy](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png) 
 
4. Vyberte **připojené zdroje**a pak vyberte **servery Linux**.

5. Hodnota napravo od **ID pracovního prostoru** a **primárního klíče**. Zkopírujte a vložte do svého oblíbeného editoru.

## <a name="install-the-agent-for-linux"></a>Instalace agenta pro Linux

Následující kroky nakonfigurují nastavení agenta pro Log Analytics v Azure a cloudu Azure Government.  

>[!NOTE]
>Agenta Log Analytics pro Linux nelze nakonfigurovat tak, aby hlásil do více než jednoho pracovního prostoru Log Analytics.  

Pokud počítač se systémem Linux potřebuje komunikovat prostřednictvím proxy server Log Analytics, může být konfigurace proxy serveru zadána na příkazovém řádku, a to vložením `-p [protocol://][user:password@]proxyhost[:port]`.  Vlastnost *ProxyHost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy server. 

Například: `https://user01:password@proxy01.contoso.com:30443`

1. Chcete-li počítač se systémem Linux nakonfigurovat pro připojení k Log Analyticsmu pracovnímu prostoru, spusťte následující příkaz, který zadává dříve zkopírovaný identifikátor pracovního prostoru a primární klíč. Následující příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Následující příkaz obsahuje parametr proxy `-p` a ukázkovou syntaxi.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Pokud chcete počítač se systémem Linux nakonfigurovat tak, aby se připojil k Log Analytics pracovnímu prostoru v Azure Government cloudu, spusťte následující příkaz, který zadává dříve zkopírovaný ID a primární klíč pracovního prostoru. Následující příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Následující příkaz obsahuje parametr proxy `-p` a ukázkovou syntaxi.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Restartujte agenta spuštěním následujícího příkazu: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Azure Monitor může shromažďovat události ze systému Linux syslog a čítačů výkonu, které určíte pro dlouhodobé analýzy a vytváření sestav. Může také provést akci při zjištění konkrétní podmínky. Pomocí těchto kroků můžete nakonfigurovat shromažďování událostí z protokolu syslog pro Linux a několik běžných čítačů výkonu, které začínají na.  

1. V levém dolním rohu Azure Portal vyberte **Další služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory**.

2. Vyberte **data**a pak vyberte **syslog**.  

3. Syslog přidáte zadáním názvu protokolu. Zadejte **SYSLOG** a potom vyberte znaménko plus **+** .  

4. V tabulce zrušte kontrolu **informací o**závažnosti, **Upozornění** a **ladění**. 

5. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

6. Pokud chcete povolit shromažďování čítačů výkonu na počítači se systémem Linux, vyberte **data o výkonu systému Linux** . 

7. Když nakonfigurujete čítače výkonu pro systém Linux pro nový pracovní prostor Log Analytics, budete mít možnost rychle vytvořit několik běžných čítačů. Jsou uvedeny u nich zaškrtávací políčko vedle každého.

    ![Vybrané výchozí čítače výkonu Windows](media/quick-collect-azurevm/linux-perfcounters-default.png)

    Vyberte **použít níže konfiguraci na moje počítače** a pak vyberte **Přidat vybrané čítače výkonu**. Přidávají se a se do deseti sekund vzorkovacího intervalu kolekce.  

8. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

## <a name="view-data-collected"></a>Zobrazit shromážděná data

Teď, když jste povolili shromažďování dat, umožňuje spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílového počítače.  

1. Ve vybraném pracovním prostoru v levém podokně vyberte **protokoly**.

2. Na stránce dotaz na protokoly zadejte do editoru dotazů `Perf` a vyberte **Spustit**.
 
    ![Log Analytics prohledávání protokolu](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Například dotaz na následujícím obrázku vrátil 10 000 záznamů o výkonu. Vaše výsledky budou podstatně menší.

    ![Výsledek hledání Log Analytics protokolu](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete agenta z počítače se systémem Linux odebrat a odstranit Log Analytics pracovní prostor.  

Chcete-li odebrat agenta, spusťte následující příkaz v počítači se systémem Linux. Argument *--vyprázdnění* zcela odebere agenta a jeho konfiguraci.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Pokud chcete pracovní prostor odstranit, vyberte pracovní prostor Log Analytics, který jste vytvořili dříve, a na stránce prostředku vyberte **Odstranit**.

![Odstranit prostředek Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky

Teď, když shromažďujete data o provozu a výkonu z místního počítače se systémem Linux, můžete snadno začít zkoumat a analyzovat data, která shromáždíte *zdarma*, a provádět s nimi akce.  

Pokud se chcete dozvědět, jak zobrazit a analyzovat data, přejděte k kurzu.

> [!div class="nextstepaction"]
> [Umožňuje zobrazit nebo analyzovat data v Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
