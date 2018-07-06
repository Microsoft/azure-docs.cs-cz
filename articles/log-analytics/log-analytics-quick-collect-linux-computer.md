---
title: Shromažďování dat z místních počítačů s Linuxem pomocí Azure Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak nasadit agenta Log Analytics pro Linux a povolit shromažďování dat z tohoto operačního systému pomocí Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: magoedte
ms.custom: mvc
ms.component: na
ms.openlocfilehash: aca8ee7305f919068733dfa3cb636a0f9bad7b05
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127165"
---
# <a name="collect-data-from-linux-computer-hosted-in-your-environment"></a>Shromažďování dat z počítače s Linuxem hostovaného ve vašem prostředí
[Azure Log Analytics](log-analytics-overview.md) může shromažďovat data přímo z fyzického nebo virtuálního počítače s Linuxem a dalších prostředků ve vašem prostředí do jednoho úložiště pro účely podrobných analýz a korelací.  Tento rychlý start ukazuje, jak v několik snadných krocích nakonfigurovat počítač s Linuxem a shromažďovat z něj data.  Informace týkající se virtuálních počítačů Azure s Linuxem najdete v tématu [Shromažďování dat o virtuálních počítačích Azure](log-analytics-quick-collect-azurevm.md).  

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Linux](log-analytics-concept-hybrid.md#supported-linux-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-concept-hybrid.md#network-firewall-requirements).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Klikněte na **Vytvořit** a podle potřeby změňte hodnoty následujících položek:

  * Zadejte název nového **pracovního prostoru OMS**, například *DefaultLAWorkspace*. 
  * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
  * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
  * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).  
  * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. Po zadání požadovaných informací v podokně **Pracovní prostor OMS** klikněte na **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Před instalací agenta OMS pro Linux potřebujete ID a klíč vašeho pracovního prostoru Log Analytics.  Tyto informace vyžaduje skript obálky agenta ke správné konfiguraci agenta a zajištění, aby agent mohl úspěšně komunikovat s Log Analytics.  

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte *DefaultLAWorkspace* vytvořený dříve.
3. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Vyberte **Připojené zdroje** a pak **Servery s Linuxem**.   
5. Napravo se zobrazí hodnoty **ID pracovního prostoru** a **Primární klíč**. Obě hodnoty zkopírujte a vložte do oblíbeného editoru.   

## <a name="install-the-agent-for-linux"></a>Instalace agenta pro Linux
V následujících krocích se nakonfiguruje instalace agenta pro Log Analytics v Azure a cloudu Azure Government.  

>[!NOTE]
>Agenta OMS pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru Log Analytics.  

Pokud počítač s Linuxem potřebuje komunikovat se službou Log Analytics prostřednictvím proxy serveru, můžete konfiguraci proxy zadat na příkazovém řádku zahrnutím příkazu `-p [protocol://][user:password@]proxyhost[:port]`.  Vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy serveru. 

Příklad: `https://user01:password@proxy01.contoso.com:30443`

1. Pokud chcete nakonfigurovat počítač s Linuxem pro připojení k Log Analytics, spusťte následující příkaz a zadejte do něj ID a primární klíč pracovního prostoru, které jste zkopírovali dříve.  Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Následující příkaz zahrnuje parametr proxy `-p` a ukázku syntaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Pokud chcete nakonfigurovat počítač s Linuxem pro připojení k Log Analytics v cloudu Azure Government, spusťte následující příkaz a zadejte do něj ID a primární klíč pracovního prostoru, které jste zkopírovali dříve.  Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Následující příkaz zahrnuje parametr proxy `-p` a ukázku syntaxe.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Restartujte agenta spuštěním následujícího příkazu: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu
Log Analytics může shromažďovat události z protokolu Syslog v Linuxu a z čítačů výkonu, které určíte pro dlouhodobější analýzu a generování sestav, a provést akci při zjištění konkrétní podmínky.  Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu Syslog v Linuxu a několika běžných čítačů výkonu.  

1. Vyberte **Syslog**.  
2. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **Syslog** a klikněte na symbol plus **+**.  
3. V tabulce zrušte zaškrtnutí závažností **Informace**, **Oznámení** a **Ladění**. 
4. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.
5. Výběrem **Data o výkonu systému Linux** povolte shromažďování čítačů výkonu na počítači s Linuxem. 
6. Při první konfiguraci linuxových čítačů výkonu pro nový pracovní prostor Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.<br><br> ![Vybrané výchozí čítače výkonu Windows](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)<br> Klikněte na **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.  
7. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat
Teď, když jste povolili shromažďování dat, můžete spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílového počítače.  

1. Na webu Azure Portal přejděte do Log Analytics a vyberte pracovní prostor vytvořený dříve.
2. Klikněte na dlaždici **Prohledávání protokolu**, v podokně Prohledávání protokolu zadejte do pole dotazu `Perf` a stiskněte Enter nebo klikněte na tlačítko Vyhledat napravo od pole dotazu.<br><br> ![Příklad dotazu prohledávání protokolu v Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Například dotaz na následujícím obrázku vrátil 735 záznamů o výkonu.<br><br> ![Výsledek prohledávání protokolu v Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud je už nepotřebujete, můžete z počítače s Linuxem odebrat agenta a odstranit pracovní prostor Log Analytics.  

Pokud chcete agenta odebrat, spusťte v počítači s Linuxem následující příkaz.  Argument *--purge* úplně odebere agenta a jeho konfiguraci.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Pokud chcete odstranit pracovní prostor, vyberte pracovní prostor Log Analytics, který jste vytvořili dříve, a na stránce prostředku klikněte na **Odstranit**.<br><br> ![Odstranění prostředku Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky
Teď, když shromažďujete data o provozu a výkonu z místního počítače s Linuxem, můžete jednoduše a *zdarma* začít zkoumat a analyzovat shromážděná data a provádět na jejich základě akce.  

Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.   

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](log-analytics-tutorial-viewdata.md)
