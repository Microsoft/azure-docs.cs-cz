---
title: Konfigurace agenta Azure Log Analytics pro hybridní počítač s Windows | Microsoft Docs
description: Zjistěte, jak nasadit agenta Log Analytics pro Windows na počítačích mimo Azure a povolit shromažďování dat pomocí Log Analytics.
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
ms.date: 11/13/2018
ms.author: magoedte
ms.custom: mvc
ms.component: ''
ms.openlocfilehash: 5c064c7b369b7f695d9166cb93f2a018b5329bef
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625602"
---
# <a name="configure-log-analytics-agent-for-windows-computers-in-a-hybrid-environment"></a>Konfigurace agenta Log Analytics pro počítače s Windows v hybridním prostředí
[Azure Log Analytics](log-analytics-agent-windows.md) může shromažďovat data přímo z fyzického nebo virtuálního počítače s Windows ve vašem datacentru nebo jiném cloudovém prostředí do jednoho úložiště pro účely podrobných analýz a korelací.  Tento rychlý start ukazuje, jak v několik snadných krocích nakonfigurovat počítač s Windows a shromažďovat z něj data.  Informace týkající se virtuálních počítačů Azure s Windows najdete v tématu [Shromažďování dat o virtuálních počítačích Azure](log-analytics-quick-collect-azurevm.md).  

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Windows](log-analytics-agent-overview.md#supported-windows-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-agent-overview.md#network-firewall-requirements).
 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-windows-computer/azure-portal-01.png)<br><br>  
2. Klikněte na **Vytvořit** a podle potřeby změňte hodnoty následujících položek:

  * Zadejte název nového **pracovního prostoru Log Analytics**, například *DefaultLAWorkspace*. 
  * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
  * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
  * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).  
  * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-windows-computer/create-loganalytics-workspace-02.png)<br>  

3. Po zadání požadovaných informací v podokně **Pracovní prostor Log Analytics** klikněte na **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Před instalací agenta Microsoft Monitoring Agent pro Windows potřebujete ID a klíč vašeho pracovního prostoru Log Analytics.  Tyto informace vyžaduje průvodce instalací ke správné konfiguraci agenta a zajištění, aby agent mohl úspěšně komunikovat s Log Analytics.  

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte *DefaultLAWorkspace* vytvořený dříve.
3. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-advanced-settings-01.png)<br><br>  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.   
5. Napravo se zobrazí hodnoty **ID pracovního prostoru** a **Primární klíč**. Obě hodnoty zkopírujte a vložte do oblíbeného editoru.   

## <a name="install-the-agent-for-windows"></a>Instalace agenta pro Windows
Následujícím postupem se nainstaluje a nakonfiguruje agent pro Log Analytics v Azure a cloudu Azure Government s použitím nastavení agenta Microsoft Monitoring Agent na vašem počítači.  

1. Na stránce **Servery Windows** vyberte odpovídající verzi **agenta pro Windows ke stažení** v závislosti na architektuře procesorů operačního systému Windows.
2. Spusťte instalační program a nainstalujte agenta na svém počítači.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak klikněte na **Souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak klikněte na **Další**.
5. Na stránce **Možnosti instalace agenta** zvolte připojení agenta k Azure Log Analytics a pak klikněte na **Další**.   
6. Na stránce **Azure Log Analytics** postupujte následovně:
   1. Vložte **ID pracovního prostoru** a **Klíč pracovního prostoru (primární klíč)**, které jste si zkopírovali dříve.  Pokud se má počítač hlásit do pracovního prostoru Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu **Cloud Azure** možnost **Azure US Government**.  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, klikněte na **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru.  Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření proxy serveru a pak klikněte na **Další**.  
7. Jakmile dokončíte zadávání nezbytných nastavení konfigurace, klikněte na **Další**.<br><br> ![vložení ID pracovního prostoru a primárního klíče](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak klikněte na **Nainstalovat**.
9. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Můžete zkontrolovat svou konfiguraci a ověřit připojení agenta k Log Analytics. Pokud je agent připojený, na kartě **Azure Log Analytics** zobrazí zprávu: **The Microsoft Monitoring Agent has successfully connected to the Microsoft Log Analytics service** (Microsoft Monitoring Agent se úspěšně připojil ke službě Microsoft Log Analytics).<br><br> ![Stav připojení MMA k Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu
Log Analytics může shromažďovat události z protokolu událostí Windows a z čítačů výkonu, které určíte pro dlouhodobější analýzu a generování sestav, a provést akci při zjištění konkrétní podmínky.  Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu událostí Windows a několika běžných čítačů výkonu.  

1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-advanced-settings-01.png)<br><br> 
3. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.  
4. Protokol událostí přidáte zadáním názvu protokolu.  Zadejte **Systém** a klikněte na symbol plus **+**.  
5. V tabulce zaškrtněte závažnosti **Chyby** a **Upozornění**.   
6. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.
7. Výběrem **Data o výkonu systému Windows** povolte shromažďování čítačů výkonu na počítači s Windows. 
8. Při první konfiguraci čítačů výkonu Windows pro nový pracovní prostor Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.<br> ![Vybrané výchozí čítače výkonu Windows](media/log-analytics-quick-collect-windows-computer/windows-perfcounters-default.png)<br> Klikněte na **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.  
9. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat
Teď, když jste povolili shromažďování dat, můžete spustit příklad jednoduchého prohledávání protokolu a zobrazit nějaká data z cílového počítače.  

1. Na webu Azure Portal ve vybraném pracovním prostoru klikněte na dlaždici **Prohledávání protokolu**.  
2. V podokně Prohledávání protokolu zadejte do pole dotazu `Perf` a stiskněte Enter nebo klikněte na tlačítko Vyhledat napravo od pole dotazu.<br><br> ![Příklad dotazu prohledávání protokolu v Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-portal-queryexample.png)<br><br> Například dotaz na následujícím obrázku vrátil 735 záznamů o výkonu.<br><br> ![Výsledek prohledávání protokolu v Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud je už nepotřebujete, můžete z počítače s Windows odebrat agenta a odstranit pracovní prostor Log Analytics.  

Pokud chcete odebrat agenta, proveďte následující kroky.

1. Otevřete **Ovládací panely**.
2. Otevřete **Programy a funkce**.
3. V části **Programy a funkce** vyberte **Microsoft Monitoring Agent** a klikněte na **Odinstalovat**.

Pokud chcete odstranit pracovní prostor, vyberte pracovní prostor Log Analytics, který jste vytvořili dříve, a na stránce prostředku klikněte na **Odstranit**.<br><br> ![Odstranění prostředku Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další postup
Teď, když shromažďujete data o provozu a výkonu z místního počítače s Linuxem, můžete jednoduše a *zdarma* začít zkoumat a analyzovat shromážděná data a provádět na jejich základě akce.  

Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.   

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](log-analytics-tutorial-viewdata.md)
