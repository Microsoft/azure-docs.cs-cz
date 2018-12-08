---
title: Shromažďování dat o virtuálních počítačích Azure | Dokumentace Microsoftu
description: Zjistěte, jak povolit rozšíření agenta Log Analytics pro virtuální počítače a zapnout shromažďování dat z virtuálních počítačů Azure pomocí Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: magoedte
ms.custom: mvc
ms.component: ''
ms.openlocfilehash: 2774a2dcb79e38a3168cd389a73f0c3ca59d2a11
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109523"
---
# <a name="collect-data-about-azure-virtual-machines"></a>Shromažďování dat o virtuálních počítačích Azure
[Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md) může shromažďovat data přímo z virtuálních počítačů Azure a dalších prostředků ve vašem prostředí do jednoho úložiště pro účely podrobných analýz a korelace.  Tento rychlý start ukazuje, jak v několik snadných krocích nakonfigurovat virtuální počítače Azure s Linuxem nebo Windows a shromažďovat z nich data.  
 
Tento rychlý start předpokládá, že máte existující virtuální počítač Azure. Pokud ne, můžete [vytvořit virtuální počítač s Windows](../../virtual-machines/windows/quick-create-portal.md) nebo [vytvořit virtuální počítač s Linuxem](../../virtual-machines/linux/quick-create-cli.md) podle našich rychlých startů pro virtuální počítače.

## <a name="log-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.

    ![portál Azure](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. Klikněte na **Vytvořit** a podle potřeby změňte hodnoty následujících položek:

  * Zadejte název nového **pracovního prostoru Log Analytics**, například *DefaultLAWorkspace*. Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.  
  * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
  * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
  * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).
  * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Create Log Analytics resource blade](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. Po zadání požadovaných informací v podokně **Pracovní prostor Log Analytics** klikněte na **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="enable-the-log-analytics-vm-extension"></a>Povolení rozšíření Log Analytics pro virtuální počítače

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Pro virtuální počítače s Windows a Linuxem, které už jsou nasazené v Azure, můžete agenta Log Analytics nainstalovat pomocí rozšíření Log Analytics pro virtuální počítače. Použití rozšíření zjednodušuje proces instalace a automaticky agenta nakonfiguruje pro odesílání dat do pracovního prostoru Log Analytics, který zadáte. Agent se také automaticky upgraduje a tím zajišťuje, abyste měli nejnovější funkce a opravy. Než budete pokračovat, ověřte, že virtuální počítač běží v opačném případě se nezdaří se nepodaří.  

>[!NOTE]
>Agenta Log Analytics pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru Log Analytics. 

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte *DefaultLAWorkspace* vytvořený dříve.
3. V nabídce vlevo v části Zdroje dat pracovního prostoru klikněte na **Virtuální počítače**.  
4. V seznamu **virtuálních počítačů** vyberte virtuální počítač, na který chcete nainstalovat agenta. Všimněte si, že **Stav připojení Log Analytics** pro tento virtuální počítač uvádí **Nepřipojeno**.
5. V podrobnostech pro váš virtuální počítač vyberte **Připojit**. Automaticky se nainstaluje a nakonfiguruje agent pro váš pracovní prostor Log Analytics. Tento proces trvá několik minut, během kterých **Stav** hlásí **Připojování**.
6. Po instalaci a připojení agenta se **Stav připojení Log Analytics** aktualizuje na **Tento pracovní prostor**.

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu
Log Analytics může shromažďovat události z protokolů událostí Windows nebo protokolu Syslog v Linuxu a z čítačů výkonu, které určíte pro dlouhodobější analýzu a generování sestav, a provést akci při zjištění konkrétní podmínky.  Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí ze systémového protokolu Windows a protokolu Syslog v Linuxu a několika běžných čítačů výkonu.  

### <a name="data-collection-from-windows-vm"></a>Shromažďování dat z virtuálního počítače s Windows
1. Vyberte **Upřesňující nastavení**.

    ![Upřesňující nastavení Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png)

3. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.  
4. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **Systém** a klikněte na symbol plus **+**.  
5. V tabulce zaškrtněte závažnosti **Chyby** a **Upozornění**.   
6. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.
7. Výběrem **Data o výkonu systému Windows** povolte shromažďování čítačů výkonu na počítači s Windows. 
8. Při první konfiguraci čítačů výkonu Windows pro nový pracovní prostor Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Vybrané výchozí čítače výkonu Windows](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Klikněte na **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.
  
9. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.

### <a name="data-collection-from-linux-vm"></a>Shromažďování dat z virtuálního počítače s Linuxem

1. Vyberte **Syslog**.  
2. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **Syslog** a klikněte na symbol plus **+**.  
3. V tabulce zrušte zaškrtnutí závažností **Informace**, **Oznámení** a **Ladění**. 
4. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.
5. Výběrem **Data o výkonu systému Linux** povolte shromažďování čítačů výkonu na počítači s Linuxem. 
6. Při první konfiguraci linuxových čítačů výkonu pro nový pracovní prostor Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Vybrané výchozí čítače výkonu Windows](media/quick-collect-azurevm/linux-perfcounters-default.png)

    Klikněte na **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.  

7. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat
Teď, když jste povolili shromažďování dat, můžete spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílových virtuálních počítačů.  

1. Na webu Azure Portal přejděte do Log Analytics a vyberte pracovní prostor vytvořený dříve.
2. Klikněte na dlaždici **Prohledávání protokolu**, v podokně Prohledávání protokolu zadejte do pole dotazu `Perf` a stiskněte Enter nebo klikněte na tlačítko Vyhledat napravo od pole dotazu.

    ![Příklad dotazu prohledávání protokolu v Log Analytics](./media/quick-collect-azurevm/log-analytics-portal-perf-query.png) 

Například dotaz na následujícím obrázku vrátil 735 záznamů o výkonu.  Vašich výsledků bude výrazně méně. 

![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až už pracovní prostor Log Analytics nebudete potřebovat, odstraňte ho. Provedete to výběrem pracovního prostoru Log Analytics, který jste vytvořili dříve, a kliknutím na **Odstranit** na stránce prostředku.


![Odstranění prostředku Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další postup
Teď, když shromažďujete data o provozu a výkonu z virtuálních počítačů s Windows nebo Linuxem, můžete jednoduše a *zdarma* začít zkoumat a analyzovat shromážděná data a provádět na jejich základě akce.  

Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.   

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
