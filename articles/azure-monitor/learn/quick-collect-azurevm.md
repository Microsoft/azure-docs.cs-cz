---
title: Shromažďování dat z virtuálního počítače Azure pomocí Azure Monitor | Microsoft Docs
description: Zjistěte, jak povolit rozšíření agenta Log Analytics pro virtuální počítače a zapnout shromažďování dat z virtuálních počítačů Azure pomocí Log Analytics.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: fb7a32199117be76e411155910d558ef187398ab
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448249"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Shromažďování dat z virtuálního počítače Azure pomocí Azure Monitor

[Azure monitor](../overview.md) může shromažďovat data přímo z virtuálních počítačů Azure do pracovního prostoru Log Analytics a analyzovat podrobnosti a korelace. Když nainstalujete rozšíření Log Analytics VM pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md) , umožníte Azure monitor shromažďovat data z vašich virtuálních počítačů Azure. V tomto rychlém startu se dozvíte, jak nakonfigurovat a shromažďovat data z virtuálních počítačů se systémem Azure Linux nebo Windows pomocí rozšíření virtuálního počítače v několika snadných krocích.  
 
Tento rychlý start předpokládá, že máte existující virtuální počítač Azure. Pokud ne, můžete [vytvořit virtuální počítač s Windows](../../virtual-machines/windows/quick-create-portal.md) nebo [vytvořit virtuální počítač s Linuxem](../../virtual-machines/linux/quick-create-cli.md) podle našich rychlých startů pro virtuální počítače.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

    ![portál Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Vyberte **vytvořit**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*.  
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
   * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Okno pro vytvoření Log Analytics prostředku](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po zadání požadovaných informací v podokně **log Analyticsho pracovního prostoru** vyberte **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="enable-the-log-analytics-vm-extension"></a>Povolení rozšíření Log Analytics pro virtuální počítače

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Pro virtuální počítače s Windows a Linuxem, které už jsou nasazené v Azure, můžete agenta Log Analytics nainstalovat pomocí rozšíření Log Analytics pro virtuální počítače. Použití rozšíření zjednodušuje proces instalace a automaticky agenta nakonfiguruje pro odesílání dat do pracovního prostoru služby Log Analytics, který zadáte. Agent je také automaticky upgradován při vydání novější verze, čímž se zajistí, že budete mít nejnovější funkce a opravy. Než budete pokračovat, ověřte, že je virtuální počítač spuštěný, jinak se proces nepodaří úspěšně dokončit.  

>[!NOTE]
>Agenta Log Analytics pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru služby Log Analytics. 

1. V Azure Portal vyberte **všechny služby** , které najdete v levém horním rohu. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

2. V seznamu pracovních prostorů Log Analytics vyberte *DefaultLAWorkspace* vytvořený dříve.

3. V nabídce na levé straně v části zdroje dat pracovního prostoru vyberte **virtuální počítače**.  

4. V seznamu **virtuálních počítačů** vyberte virtuální počítač, na který chcete nainstalovat agenta. Všimněte si, že **Stav připojení Log Analytics** pro tento virtuální počítač uvádí **Nepřipojeno**.

5. V podrobnostech pro váš virtuální počítač vyberte **Připojit**. Automaticky se nainstaluje a nakonfiguruje agent pro váš pracovní prostor služby Log Analytics. Tento proces trvá několik minut, během kterých se zobrazuje **stav** **připojení**.

6. Po instalaci a připojení agenta se **Stav připojení Log Analytics** aktualizuje na **Tento pracovní prostor**.

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Azure Monitor může shromažďovat události z protokolů událostí systému Windows nebo systému Linux syslog a čítače výkonu, které zadáte pro dlouhodobé analýzy a vytváření sestav, a provést akci při zjištění konkrétní podmínky. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí ze systémového protokolu Windows a protokolu Syslog v Linuxu a několika běžných čítačů výkonu.  

### <a name="data-collection-from-windows-vm"></a>Shromažďování dat z virtuálního počítače s Windows

1. Vyberte **Upřesňující nastavení**.

    ![Upřesňující nastavení Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.

3. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **systém** a potom vyberte znaménko plus **+** .

4. V tabulce zaškrtněte závažnosti **Chyby** a **Upozornění**.

5. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

6. Výběrem **Data o výkonu systému Windows** povolte shromažďování čítačů výkonu na počítači s Windows.

7. Při první konfiguraci čítačů výkonu Windows pro nový pracovní prostor služby Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Snímek obrazovky s podoknem čítače výkonu systému Windows se seznamem vybraných čítačů a vybraným tlačítkem Přidat vybrané čítače výkonu](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Vyberte **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.
  
8. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

### <a name="data-collection-from-linux-vm"></a>Shromažďování dat z virtuálního počítače s Linuxem

1. Vyberte **Syslog**.  

2. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **SYSLOG** a potom vyberte znaménko plus **+** .  

3. V tabulce zrušte výběr **informací o**závažnosti, **Upozornění** a **ladění**. 

4. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

5. Výběrem **Data o výkonu systému Linux** povolte shromažďování čítačů výkonu na počítači s Linuxem. 

6. Při první konfiguraci linuxových čítačů výkonu pro nový pracovní prostor služby Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.

    ![Snímek obrazovky s podoknem čítače výkonu pro Linux se seznamem vybraných čítačů a vybraným tlačítkem Přidat vybrané čítače výkonu](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Vyberte **použít níže konfiguraci na moje počítače** a pak vyberte **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.  

7. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat

Teď, když jste povolili shromažďování dat, můžete spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílových virtuálních počítačů.  

1. Ve vybraném pracovním prostoru v levém podokně vyberte **protokoly**.

2. Na stránce dotaz na protokoly zadejte `Perf` do editoru dotazů a vyberte **Spustit**.

    ![Příklad dotazu prohledávání protokolu v Log Analytics](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    Například dotaz na následujícím obrázku vrátil 10 000 záznamů o výkonu. Vašich výsledků bude výrazně méně.

    ![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až už pracovní prostor služby Log Analytics nebudete potřebovat, odstraňte ho. Provedete to tak, že vyberete pracovní prostor Log Analytics, který jste vytvořili dříve, a na stránce prostředek vyberete **Odstranit**.


![Odstranění prostředku Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky

Teď, když shromažďujete data o provozu a výkonu z virtuálních počítačů s Windows nebo Linuxem, můžete jednoduše a *zdarma* začít zkoumat a analyzovat shromážděná data a provádět na jejich základě akce.  

Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../log-query/get-started-portal.md)
