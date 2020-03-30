---
title: Shromažďování dat z virtuálního počítače Azure pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit rozšíření agenta Log Analytics pro virtuální počítače a zapnout shromažďování dat z virtuálních počítačů Azure pomocí Log Analytics.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2e63b2ca0ee7246009068e6a2875795a14aeddb3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240335"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Shromažďování dat z virtuálního počítače Azure pomocí Azure Monitoru

[Azure Monitor](../overview.md) může shromažďovat data přímo z vašich virtuálních počítačů Azure do pracovního prostoru Analýzy protokolů pro podrobnou analýzu a korelaci. Instalace rozšíření virtuálního počítače Log Analytics pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md) umožňuje Azure Monitoru shromažďovat data z vašich virtuálních aplikací Azure. Tento rychlý start ukazuje, jak nakonfigurovat a shromažďovat data z virtuálních zařízení Azure Linux nebo Windows pomocí rozšíření virtuálního počítače s několika jednoduchými kroky.  
 
Tento rychlý start předpokládá, že máte existující virtuální počítač Azure. Pokud ne, můžete [vytvořit virtuální počítač s Windows](../../virtual-machines/windows/quick-create-portal.md) nebo [vytvořit virtuální počítač s Linuxem](../../virtual-machines/linux/quick-create-cli.md) podle našich rychlých startů pro virtuální počítače.

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

## <a name="enable-the-log-analytics-vm-extension"></a>Povolení rozšíření Log Analytics pro virtuální počítače

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Pro virtuální počítače s Windows a Linuxem, které už jsou nasazené v Azure, můžete agenta Log Analytics nainstalovat pomocí rozšíření Log Analytics pro virtuální počítače. Použití rozšíření zjednodušuje proces instalace a automaticky agenta nakonfiguruje pro odesílání dat do pracovního prostoru služby Log Analytics, který zadáte. Agent je také upgradován automaticky při vydání novější verze, což zajišťuje, že máte nejnovější funkce a opravy. Než budete pokračovat, ověřte, zda je virtuální hotel spuštěn, jinak se proces nepodaří úspěšně dokončit.  

>[!NOTE]
>Agenta Log Analytics pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru služby Log Analytics. 

1. Na webu Azure Portal vyberte **Všechny služby, které** se nacházejí v levém horním rohu. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovní prostory Analýzy protokolů**.

2. V seznamu pracovních prostorů Log Analytics vyberte *DefaultLAWorkspace* vytvořený dříve.

3. V nabídce na levé straně vyberte v části Zdroje dat pracovního prostoru **položku Virtuální počítače**.  

4. V seznamu **virtuálních počítačů** vyberte virtuální počítač, na který chcete nainstalovat agenta. Všimněte si, že **Stav připojení Log Analytics** pro tento virtuální počítač uvádí **Nepřipojeno**.

5. V podrobnostech pro váš virtuální počítač vyberte **Připojit**. Automaticky se nainstaluje a nakonfiguruje agent pro váš pracovní prostor služby Log Analytics. Tento proces trvá několik minut, během kterého se zobrazí **stav** **Připojení**.

6. Po instalaci a připojení agenta se **Stav připojení Log Analytics** aktualizuje na **Tento pracovní prostor**.

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Azure Monitor můžete shromažďovat události z protokolů událostí systému Windows nebo Linux Syslog a čítače výkonu, které zadáte pro dlouhodobější analýzy a vykazování a provést akci, když je zjištěna určitá podmínka. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí ze systémového protokolu Windows a protokolu Syslog v Linuxu a několika běžných čítačů výkonu.  

### <a name="data-collection-from-windows-vm"></a>Shromažďování dat z virtuálního počítače s Windows

1. Vyberte **Upřesňující nastavení**.

    ![Upřesňující nastavení Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.

3. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **systém** a vyberte znaménko **+** plus .

4. V tabulce zaškrtněte závažnosti **Chyby** a **Upozornění**.

5. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

6. Výběrem **Data o výkonu systému Windows** povolte shromažďování čítačů výkonu na počítači s Windows.

7. Při první konfiguraci čítačů výkonu Windows pro nový pracovní prostor služby Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Vybrané výchozí čítače výkonu Windows](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Vyberte **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.
  
8. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

### <a name="data-collection-from-linux-vm"></a>Shromažďování dat z virtuálního počítače s Linuxem

1. Vyberte **Syslog**.  

2. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **Syslog** a vyberte znaménko **+** plus .  

3. V tabulce odznačte informace **o**závažnosti , **oznámení** a **ladění**. 

4. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

5. Výběrem **Data o výkonu systému Linux** povolte shromažďování čítačů výkonu na počítači s Linuxem. 

6. Při první konfiguraci linuxových čítačů výkonu pro nový pracovní prostor služby Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Vybrané výchozí čítače výkonu Windows](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Vyberte **Použít níže konfiguraci na mé počítače** a pak vyberte Přidat vybrané **čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.  

7. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat

Teď, když jste povolili shromažďování dat, můžete spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílových virtuálních počítačů.  

1. Ve vybraném pracovním prostoru vyberte v levém podokně **položku Protokoly**.

2. Na stránce Protokoly dotazu zadejte `Perf` do editoru dotazů a vyberte **Spustit**.

    ![Příklad dotazu prohledávání protokolu v Log Analytics](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    Například dotaz na následujícím obrázku vrátil 10 000 záznamů o výkonu. Vašich výsledků bude výrazně méně.

    ![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až už pracovní prostor služby Log Analytics nebudete potřebovat, odstraňte ho. Chcete-li tak učinit, vyberte pracovní prostor Analýzy protokolů, který jste vytvořili dříve, a na stránce prostředků vyberte **Odstranit**.


![Odstranění prostředku Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky

Teď, když shromažďujete data o provozu a výkonu z virtuálních počítačů s Windows nebo Linuxem, můžete jednoduše a *zdarma* začít zkoumat a analyzovat shromážděná data a provádět na jejich základě akce.  

Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
