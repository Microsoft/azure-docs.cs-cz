---
title: 'Rychlý Start: shromáždění dat z hybridního počítače se systémem Linux pomocí Azure Monitor'
description: V tomto rychlém startu se dozvíte, jak nasadit agenta Log Analytics pro počítače se systémem Linux běžící mimo Azure a povolit shromažďování dat pomocí protokolů Azure Monitor.
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
ms.openlocfilehash: 04d53dc213d5e992dc79e322b071025bdb447b1b
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057497"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Rychlý Start: shromáždění dat z počítače se systémem Linux v hybridním prostředí pomocí Azure Monitor

[Azure monitor](../overview.md) může shromažďovat data přímo z fyzických nebo virtuálních počítačů se systémem Linux ve vašem prostředí do pracovního prostoru Log Analytics pro účely podrobných analýz a korelace. Instalace [agenta Log Analytics](../platform/log-analytics-agent.md) umožňuje Azure monitor shromažďovat data z datového centra nebo jiného cloudového prostředí. V tomto rychlém startu se dozvíte, jak pomocí několika jednoduchých kroků nakonfigurovat a shromažďovat data ze serveru Linux. Informace o virtuálních počítačích Azure Linux najdete v tématu [shromažďování dat o virtuálních počítačích Azure](./quick-collect-azurevm.md).  

Další informace o podporované konfiguraci najdete v tématu [podporované operační systémy](../platform/agents-overview.md#supported-operating-systems) a [Konfigurace brány firewall sítě](../platform/log-analytics-agent.md#network-requirements).
 
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

    ![Hledání Log Analyticsho pracovního prostoru v Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Vyberte **vytvořit**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*.  
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
   * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Vytvoření pracovního prostoru Log Analytics v Azure Portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po zadání požadovaných informací v podokně **log Analyticsho pracovního prostoru** vyberte **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru

Před instalací agenta Log Analytics pro Linux potřebujete ID a klíč vašeho pracovního prostoru služby Log Analytics. Tyto informace vyžaduje skript obálky agenta ke správné konfiguraci agenta a zajištění, aby mohl úspěšně komunikovat s Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. V levém horním rohu Azure Portal vyberte **všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory**.

2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste vytvořili dříve. (Je možné, že jste nastavili název **DefaultLAWorkspace**.)

3. Vyberte **správu agentů**:
 
4. Pak vyberte **servery Linux**.

5. Hodnota napravo od **ID pracovního prostoru** a **primárního klíče**. Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

## <a name="install-the-agent-for-linux"></a>Instalace agenta pro Linux

V následujících krocích se nakonfiguruje instalace agenta pro Log Analytics v Azure a cloudu Azure Government.  

>[!NOTE]
>Agenta Log Analytics pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru služby Log Analytics.  

Pokud počítač s Linuxem potřebuje komunikovat se službou Log Analytics prostřednictvím proxy serveru, můžete konfiguraci proxy zadat na příkazovém řádku zahrnutím příkazu `-p [protocol://][user:password@]proxyhost[:port]`.  Vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy serveru. 

Příklad: `https://user01:password@proxy01.contoso.com:30443`

1. Chcete-li počítač se systémem Linux nakonfigurovat pro připojení k Log Analyticsmu pracovnímu prostoru, spusťte následující příkaz, který zadává dříve zkopírovaný identifikátor pracovního prostoru a primární klíč. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, když proxy server vyžaduje ověření:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Pokud chcete počítač se systémem Linux nakonfigurovat tak, aby se připojil k Log Analytics pracovnímu prostoru v Azure Government cloudu, spusťte následující příkaz, který zadává dříve zkopírovaný ID a primární klíč pracovního prostoru. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, když proxy server vyžaduje ověření:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Restartujte agenta spuštěním následujícího příkazu: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Azure Monitor může shromažďovat události ze systému Linux syslog a čítačů výkonu, které určíte pro dlouhodobé analýzy a vytváření sestav. Může také provést akci při zjištění konkrétní podmínky. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu Syslog v Linuxu a několika běžných čítačů výkonu.  

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte Log Analytics. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory** a v seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který hledáte, a vyberte **Upřesnit nastavení** **log Analyticsho** pracovního prostoru.

2. Vyberte **data**a pak vyberte **syslog**.  

3. Syslog přidáte zadáním názvu protokolu. Zadejte **SYSLOG** a potom vyberte znaménko plus **+** .  

4. V tabulce zrušte zaškrtnutí závažností **Informace**, **Oznámení** a **Ladění**. 

5. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

6. Výběrem **Data o výkonu systému Linux** povolte shromažďování čítačů výkonu na počítači s Linuxem. 

7. Při první konfiguraci linuxových čítačů výkonu pro nový pracovní prostor služby Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Výchozí čítače výkonu pro Linux vybrané v Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Vyberte **použít níže konfiguraci na moje počítače** a pak vyberte **Přidat vybrané čítače výkonu**. Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.  

8. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat

Teď, když jste povolili shromažďování dat, můžete spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílového počítače.  

1. Ve vybraném pracovním prostoru v levém podokně vyberte **protokoly**.

2. Na stránce dotaz na protokoly zadejte `Perf` do editoru dotazů a vyberte **Spustit**.
 
    ![Log Analytics prohledávání protokolu](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Například dotaz na následujícím obrázku vrátil 10 000 záznamů o výkonu. Vašich výsledků bude výrazně méně.

    ![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, můžete z počítače s Linuxem odebrat agenta a odstranit pracovní prostor služby Log Analytics.  

Pokud chcete agenta odebrat, spusťte v počítači s Linuxem následující příkaz. Argument *--purge* úplně odebere agenta a jeho konfiguraci.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Pokud chcete pracovní prostor odstranit, vyberte pracovní prostor Log Analytics, který jste vytvořili dříve, a na stránce prostředku vyberte **Odstranit**.

![Odstranění prostředku Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky

Teď, když shromažďujete data o provozu a výkonu z místního počítače s Linuxem, můžete jednoduše a *zdarma* začít zkoumat a analyzovat shromážděná data a provádět na jejich základě akce.  

Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../log-query/get-started-portal.md)

