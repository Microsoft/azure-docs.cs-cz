---
title: Shromažďování dat z hybridního počítače s Windows pomocí Azure Monitoru
description: V tomto rychlém startu se dozvíte, jak nasadit agenta Log Analytics pro počítače s Windows spuštěné mimo Azure a povolit shromažďování dat pomocí protokolů monitorování Azure.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 6435535ee14450c590a79ea68c8855c29df8bd9a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637871"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Shromažďování dat z počítače s Windows v hybridním prostředí pomocí Azure Monitoru

[Azure Monitor](../overview.md) může shromažďovat data přímo z vašich fyzických nebo virtuálních počítačů se systémem Windows ve vašem prostředí do pracovního prostoru Log Analytics pro podrobnou analýzu a korelaci. Instalace [agenta Log Analytics](../platform/log-analytics-agent.md) umožňuje Azure Monitoru shromažďovat data z datového centra nebo jiného cloudového prostředí. Tento rychlý start ukazuje, jak v několik snadných krocích nakonfigurovat počítač s Windows a shromažďovat z něj data. Informace o virtuálních počítačích Azure pro Windows najdete v [tématu Shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Informace o podporované konfiguraci naleznete v [tématu Podporované operační systémy Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) a konfigurace síťové brány [firewall](../../azure-monitor/platform/log-analytics-agent.md#network-requirements).
 
Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese . 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

1. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovní prostory Analýzy protokolů**.

    ![portál Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Vyberte **Vytvořit**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*.  
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
   * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Vytvořit okno prostředků Analýzy protokolů](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po zadání požadovaných informací v podokně **pracovního prostoru Log Analytics** vyberte **možnost OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 


## <a name="get-the-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru

Před instalací agenta Analýzy protokolů pro Systém Windows (označované také jako Microsoft Monitoring Agent (MMA)) potřebujete ID pracovního prostoru a klíč pro pracovní prostor Log Analytics. Průvodce instalací potřebuje tyto informace správně nakonfigurovat agenta a zajistit, že může komunikovat s Azure Monitor.  

1. V levém horním rohu portálu Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam filtruje na základě vašeho vstupu. Vyberte **pracovní prostory Analýzy protokolů**.

2. V seznamu pracovních prostorů Analýzy protokolů vyberte pracovní prostor, který jste vytvořili dříve. (Je možné, že jste jej pojmenovali **DefaultLAWorkspace**.)

3. Vyberte **Upřesnit nastavení**:

    ![Log Analytics upřesňující nastavení](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.

5. Zkopírujte hodnoty vpravo od **ID pracovního prostoru** a **primárního klíče**. Vložte je do svého oblíbeného editoru.

## <a name="install-the-agent-for-windows"></a>Instalace agenta pro Windows

Následující kroky nainstalují a nakonfigurují agenta pro analýzu protokolů v Azure a Azure Government. K instalaci agenta do počítače použijete instalační program Microsoft Monitoring Agent.

1. Pokračujte od předchozí sady kroků a na stránce **Servery systému Windows** vyberte verzi **stáhnout agenta Windows,** kterou chcete stáhnout. Vyberte příslušnou verzi pro architekturu procesoru operačního systému Windows.

2. Spusťte instalační program a nainstalujte agenta na svém počítači.

3. Na **úvodní** stránce vyberte **Další**.

4. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak vyberte **Souhlasím**.

5. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak vyberte **Další**.

6. Na stránce **Možnosti nastavení agenta** připojte agenta k Azure Log Analytics a pak vyberte **Další**.

7. Na stránce **Azure Log Analytics** proveďte tyto kroky:

   1. Vložte do **ID pracovního prostoru** a **klíče pracovního prostoru (primární klíč),** které jste zkopírovali dříve. Pokud by měl počítač hlásit do pracovního prostoru Log Analytics v Azure Government, vyberte **Azure US Government** v seznamu Azure **Cloud.**  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, vyberte **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru. Pokud proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověřování pomocí proxy serveru a pak vyberte **Další**.  

8. Po přidání nastavení konfigurace vyberte **Další:**

    ![Instalace agenta monitorování společnosti Microsoft](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak vyberte **Nainstalovat**.

10. Na stránce **Konfigurace byla úspěšně dokončena** vyberte **možnost Dokončit**.

Po dokončení instalace a instalace se v Ovládacích panelech zobrazí agent Microsoft Monitoring Agent. Můžete zkontrolovat konfiguraci a ověřit, zda je agent připojen k pracovnímu prostoru Log Analytics. Po připojení se na kartě **Azure Log Analytics** agent zobrazí tato zpráva: **Agent monitorování Microsoftu se úspěšně připojil ke službě Microsoft Log Analytics.**<br><br> ![Stav připojení MMA](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Azure Monitor může shromažďovat události, které zadáte z protokolu událostí systému Windows a čítačů výkonu pro dlouhodobější analýzu a vytváření sestav. Může také provést akci, když zjistí určitou podmínku. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu událostí Windows a několika běžných čítačů výkonu.  

1. V levém dolním rohu portálu Azure vyberte **Další služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam filtruje na základě vašeho vstupu. Vyberte **pracovní prostory Analýzy protokolů**.

2. Vyberte **Upřesnit nastavení**:

    ![Log Analytics upřesňující nastavení](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.  

4. Protokol událostí přidáte zadáním názvu protokolu. Zadejte **systém**a vyberte**+** znaménko plus ( ).  

5. V tabulce vyberte **závažnost chyby** a **upozornění.**

6. V horní části stránky vyberte **Uložit.**

7. Výběrem **možnosti Čítače výkonu systému Windows** povolíte shromažďování čítačů výkonu v počítači se systémem Windows.

8. Při první konfiguraci čítačů výkonu systému Windows pro nový pracovní prostor Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Každá možnost je uvedena se zaškrtávacím políčkem vedle ní:

    ![Čítače výkonu Windows](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Vyberte **Přidat vybrané čítače výkonu**. Čítače jsou přidány a přednastaveny s desetisekundovým intervalem vzorkování.

9. V horní části stránky vyberte **Uložit.**

## <a name="view-collected-data"></a>Zobrazit shromážděná data

Teď, když jste povolili shromažďování dat, spustíme jednoduché hledání protokolu, abychom viděli některá data z cílového počítače.  

1. Ve vybraném pracovním prostoru vyberte v levém podokně **položku Protokoly**.

2. Na stránce Protokoly dotazu zadejte `Perf` do editoru dotazů a vyberte **Spustit**.
 
    ![Hledání protokolu Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Například dotaz v tomto obrázku vrátil 10 000 záznamů výkonu. Vašich výsledků bude výrazně méně.

    ![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Agenta můžete odebrat z počítače a odstranit pracovní prostor Log Analytics, pokud ho již nepotřebujete.  

Chcete-li agenta odebrat, proveďte následující kroky:

1. Otevřete okno Ovládací panely.

2. Otevřete **Programy a funkce**.

3. V **části Programy a funkce**vyberte položku Microsoft Monitoring **Agent** a potom vyberte **odinstalovat**.

Chcete-li odstranit pracovní prostor Log Analytics, který jste vytvořili dříve, vyberte jej a na stránce zdroje vyberte **Odstranit**:

![Odstranit pracovní prostor Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky

Nyní, když shromažďujete provozní a výkonová data z počítače se systémem Windows, můžete snadno začít zkoumat, analyzovat a jednat na základě shromažďovacích dat *zdarma*.  

Chcete-li se dozvědět, jak zobrazit a analyzovat data, pokračujte v kurzu:

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](tutorial-viewdata.md)
