---
title: 'Úvodní příručka: Shromažďování dat z hybridního počítače s Linuxem pomocí Azure Monitoru'
description: V tomto rychlém startu se dozvíte, jak nasadit agenta Log Analytics pro počítače s Linuxem, které běží mimo Azure, a povolit shromažďování dat pomocí protokolů monitorování Azure.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 1da862a7994d7db5ee8c9f090119e14f19c6b13a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637896"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Úvodní příručka: Shromažďování dat z počítače s Linuxem v hybridním prostředí pomocí Azure Monitoru

[Azure Monitor](../overview.md) může shromažďovat data přímo z vašich fyzických nebo virtuálních počítačů s Linuxem ve vašem prostředí do pracovního prostoru Log Analytics pro podrobnou analýzu a korelaci. Instalace [agenta Log Analytics](../platform/log-analytics-agent.md) umožňuje Azure Monitoru shromažďovat data z datového centra nebo jiného cloudového prostředí. Tento rychlý start vám ukáže, jak nakonfigurovat a shromažďovat data ze serveru Linux pomocí několika jednoduchých kroků. Informace o virtuálních počítačích Azure Linux najdete v [tématu Shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Informace o podporované konfiguraci naleznete v [tématu Podporované operační systémy Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) a konfigurace síťové brány [firewall](../../azure-monitor/platform/log-analytics-agent.md#network-requirements).
 
Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese . 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

1. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovní prostory Analýzy protokolů**.

    ![Hledání pracovního prostoru Log Analytics na webu Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Vyberte **Vytvořit**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*.  
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
   * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Vytvoření pracovního prostoru Analýzy protokolů na webu Azure Portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po zadání požadovaných informací v podokně **pracovního prostoru Log Analytics** vyberte **možnost OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru

Před instalací agenta Log Analytics pro Linux potřebujete ID a klíč vašeho pracovního prostoru služby Log Analytics. Tyto informace jsou vyžadovány skriptem obálky agenta, aby správně nakonfiguroval agenta a zajistil, že může úspěšně komunikovat s Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. V levém horním rohu portálu Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam filtruje na základě vašeho vstupu. Vyberte **pracovní prostory Analýzy protokolů**.

2. V seznamu pracovních prostorů Analýzy protokolů vyberte pracovní prostor, který jste vytvořili dříve. (Je možné, že jste jej pojmenovali **DefaultLAWorkspace**.)

3. Vyberte **Upřesnit nastavení**:

    ![Nabídka Pokročilá nastavení pro analýzu protokolů na webu Azure Portal](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Vyberte **Připojené zdroje** a pak **Servery s Linuxem**.

5. Napravo se zobrazí hodnoty **ID pracovního prostoru** a **Primární klíč**. Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

## <a name="install-the-agent-for-linux"></a>Instalace agenta pro Linux

V následujících krocích se nakonfiguruje instalace agenta pro Log Analytics v Azure a cloudu Azure Government.  

>[!NOTE]
>Agenta Log Analytics pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru služby Log Analytics.  

Pokud počítač s Linuxem potřebuje komunikovat se službou Log Analytics prostřednictvím proxy serveru, můžete konfiguraci proxy zadat na příkazovém řádku zahrnutím příkazu `-p [protocol://][user:password@]proxyhost[:port]`.  Vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy serveru. 

Příklad: `https://user01:password@proxy01.contoso.com:30443`

1. Chcete-li nakonfigurovat počítač s Linuxem tak, aby se připojil k pracovnímu prostoru Log Analytics, spusťte následující příkaz poskytující dříve zkopírované ID pracovního prostoru a primární klíč. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, pokud je ověření vyžadováno serverem proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Chcete-li nakonfigurovat počítač s Linuxem pro připojení k pracovnímu prostoru Log Analytics v cloudu Azure Government, spusťte následující příkaz poskytující ID pracovního prostoru a primární klíč zkopírovaný dříve. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, pokud je ověření vyžadováno serverem proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Restartujte agenta spuštěním následujícího příkazu: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Azure Monitor může shromažďovat události z linuxových syslogů a čítačů výkonu, které zadáte pro dlouhodobější analýzu a vytváření sestav. Může také provést akci, když zjistí určitou podmínku. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu Syslog v Linuxu a několika běžných čítačů výkonu.  

1. V levém dolním rohu portálu Azure vyberte **Další služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam filtruje na základě vašeho vstupu. Vyberte **pracovní prostory Analýzy protokolů**.

2. Vyberte **Data**a pak vyberte **Syslog**.  

3. Syslog přidáte zadáním názvu protokolu. Zadejte **Syslog** a vyberte znaménko **+** plus .  

4. V tabulce zrušte zaškrtnutí závažností **Informace**, **Oznámení** a **Ladění**. 

5. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

6. Výběrem **Data o výkonu systému Linux** povolte shromažďování čítačů výkonu na počítači s Linuxem. 

7. Při první konfiguraci linuxových čítačů výkonu pro nový pracovní prostor služby Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Výchozí čítače výkonu Linuxu vybrané v Azure Monitoru](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Vyberte **Použít níže konfiguraci na mé počítače** a pak vyberte Přidat vybrané **čítače výkonu**. Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.  

8. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat

Teď, když jste povolili shromažďování dat, můžete spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílového počítače.  

1. Ve vybraném pracovním prostoru vyberte v levém podokně **položku Protokoly**.

2. Na stránce Protokoly dotazu zadejte `Perf` do editoru dotazů a vyberte **Spustit**.
 
    ![Hledání protokolu Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Například dotaz na následujícím obrázku vrátil 10 000 záznamů výkonu. Vašich výsledků bude výrazně méně.

    ![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, můžete z počítače s Linuxem odebrat agenta a odstranit pracovní prostor služby Log Analytics.  

Pokud chcete agenta odebrat, spusťte v počítači s Linuxem následující příkaz. Argument *--purge* úplně odebere agenta a jeho konfiguraci.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Chcete-li pracovní prostor odstranit, vyberte pracovní prostor Log Analytics, který jste vytvořili dříve, a na stránce prostředků vyberte **Odstranit**.

![Odstranění prostředku Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky

Teď, když shromažďujete data o provozu a výkonu z místního počítače s Linuxem, můžete jednoduše a *zdarma* začít zkoumat a analyzovat shromážděná data a provádět na jejich základě akce.  

Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
