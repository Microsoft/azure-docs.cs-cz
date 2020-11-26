---
title: Shromažďování dat z hybridního počítače s Windows pomocí Azure Monitor
description: V tomto rychlém startu se dozvíte, jak nasadit agenta Log Analytics pro počítače se systémem Windows, na kterých běží mimo Azure, a jak povolit shromažďování dat pomocí protokolů Azure Monitor.
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
ms.openlocfilehash: 9cd92075c7e0c4450dbee750fd31efb394dd24f7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186741"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Shromažďování dat z počítače s Windows v hybridním prostředí pomocí Azure Monitor

[Azure monitor](../overview.md) může shromažďovat data přímo z vašich fyzických nebo virtuálních počítačů s Windows ve vašem prostředí do pracovního prostoru Log Analytics a získat tak podrobnou analýzu a korelaci. Instalace [agenta Log Analytics](../platform/log-analytics-agent.md) umožňuje Azure monitor shromažďovat data z datového centra nebo jiného cloudového prostředí. Tento rychlý start ukazuje, jak v několik snadných krocích nakonfigurovat počítač s Windows a shromažďovat z něj data. Informace o virtuálních počítačích Azure s Windows najdete v tématu [shromažďování dat o virtuálních počítačích Azure](./quick-collect-azurevm.md).  

Další informace o podporované konfiguraci najdete v tématu [podporované operační systémy](../platform/agents-overview.md#supported-operating-systems) a [Konfigurace brány firewall sítě](../platform/log-analytics-agent.md#network-requirements).
 
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

    ![portál Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Vyberte **vytvořit** a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*.  
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.  
   * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené.  Další informace najdete na stránce uvádějící [oblasti, ve kterých je dostupná služba Log Analytics](https://azure.microsoft.com/regions/services/).
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubnem nebo pro předplatné, které se vázalo na existující registraci smlouvy Enterprise, vyberte si cenovou úroveň, které dáváte přednost.  Další informace o jednotlivých úrovních najdete v [podrobnostech o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Okno pro vytvoření Log Analytics prostředku](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po zadání požadovaných informací v podokně **log Analyticsho pracovního prostoru** vyberte **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 


## <a name="get-the-workspace-id-and-key"></a>Získat ID a klíč pracovního prostoru

Než nainstalujete agenta Log Analytics pro Windows (také označovaný jako Microsoft Monitoring Agent (MMA)), budete potřebovat ID a klíč pracovního prostoru pro váš Log Analytics pracovní prostor. Průvodce instalací potřebuje tyto informace, aby mohl správně nakonfigurovat agenta a zajistit, aby mohl komunikovat s Azure Monitor.  

1. V levém horním rohu Azure Portal vyberte **všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory**.

2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste vytvořili dříve. (Je možné, že jste nastavili název **DefaultLAWorkspace**.)

3. Vybrat **upřesňující nastavení**:

    ![Log Analytics Upřesnit nastavení](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.

5. Zkopírujte hodnoty napravo od ID a **primárního klíče** **pracovního prostoru** . Vložte je do svého oblíbeného editoru.

## <a name="install-the-agent-for-windows"></a>Instalace agenta pro Windows

Následující postup nainstaluje a nakonfiguruje agenta pro Log Analytics v Azure a Azure Government. Pomocí instalačního programu Microsoft Monitoring Agent nainstalujete agenta do svého počítače.

1. Po pokračování předchozí sady kroků na stránce **Windows servery** vyberte **Stáhnout verzi agenta pro Windows** , kterou chcete stáhnout. Vyberte příslušnou verzi pro architekturu procesoru operačního systému Windows.

2. Spusťte instalační program a nainstalujte agenta na svém počítači.

3. Na **úvodní** stránce vyberte **Další**.

4. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak vyberte **Souhlasím**.

5. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak vyberte **Další**.

6. Na stránce **Možnosti instalace agenta** Připojte agenta k Azure Log Analytics a pak vyberte **Další**.

7. Na stránce **Azure Log Analytics** proveďte tyto kroky:

   1. Vložte do **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** , který jste zkopírovali dříve. Pokud by měl počítač nahlásit do pracovního prostoru Log Analytics v Azure Government, vyberte v seznamu **cloud Azure** možnost **státní správa Azure USA** .  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, vyberte **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru. Pokud vaše proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověřování pomocí proxy server a pak vyberte **Další**.  

8. Po přidání nastavení konfigurace vyberte **Další** :

    ![Instalace Microsoft Monitoring Agent](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak vyberte **Nainstalovat**.

10. Na stránce **Konfigurace byla úspěšně dokončena** vyberte **Dokončit**.

Po dokončení instalace a nastavení se Microsoft Monitoring Agent zobrazí v Ovládacích panelech. Můžete zkontrolovat svou konfiguraci a ověřit, zda je agent připojen k pracovnímu prostoru Log Analytics. Po připojení se na kartě **Azure Log Analytics** Agent zobrazí tato zpráva: **Microsoft Monitoring Agent se úspěšně připojil ke službě Microsoft Log Analytics.**<br><br> ![Stav připojení MMA](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Azure Monitor mohou shromažďovat události, které zadáte z protokolu událostí systému Windows, a čítače výkonu pro dlouhodobé analýzy a vytváření sestav. Může také provést akci při zjištění konkrétní podmínky. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu událostí Windows a několika běžných čítačů výkonu.  

1. V levém dolním rohu Azure Portal vyberte **Další služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory**.

2. Vybrat **upřesňující nastavení**:

    ![Log Analytics Upřesnit nastavení](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.  

4. Protokol událostí můžete přidat zadáním názvu protokolu. Zadejte **systém** a potom vyberte znaménko plus ( **+** ).  

5. V tabulce vyberte závažnost **chyby** a **varování** .

6. V horní části stránky vyberte **Save (Uložit** ).

7. Pokud chcete povolit shromažďování čítačů výkonu na počítači s Windows, vyberte **čítače výkonu systému Windows** .

8. Když poprvé nakonfigurujete čítače výkonu Windows pro nový pracovní prostor Log Analytics, budete mít možnost rychle vytvořit několik běžných čítačů. Každá možnost je uvedena vedle sebe s zaškrtávací políčko:

    ![Čítače výkonu Windows](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Vyberte **Přidat vybrané čítače výkonu**. Čítače jsou přidány a přednastaveny s intervalem vzorkování kolekce deseti sekund.

9. V horní části stránky vyberte **Save (Uložit** ).

## <a name="view-collected-data"></a>Zobrazit shromážděná data

Teď, když jste povolili shromažďování dat, můžeme spustit jednoduché prohledávání protokolů, abyste viděli nějaká data z cílového počítače.  

1. Ve vybraném pracovním prostoru v levém podokně vyberte **protokoly**.

2. Na stránce dotaz na protokoly zadejte `Perf` do editoru dotazů a vyberte **Spustit**.
 
    ![Log Analytics prohledávání protokolu](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Například dotaz na tomto obrázku vrátil 10 000 záznamů o výkonu. Vašich výsledků bude výrazně méně.

    ![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Agenta můžete z počítače odebrat a odstranit Log Analytics pracovní prostor, pokud je už nepotřebujete.  

Chcete-li odebrat agenta, proveďte následující kroky:

1. Otevřete okno Ovládací panely.

2. Otevřete **Programy a funkce**.

3. V **programech a funkcích** vyberte **Microsoft Monitoring Agent** a pak vyberte **odinstalovat**.

Pokud chcete odstranit pracovní prostor Log Analytics, který jste vytvořili dříve, vyberte ho a na stránce prostředek vyberte **Odstranit**:

![Odstranit Log Analytics pracovní prostor](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další kroky

Teď, když shromažďujete data o provozu a výkonu z počítače s Windows, můžete snadno začít zkoumat, analyzovat a vystupovat data, která shromáždíte, a to *zdarma*.  

Pokud se chcete dozvědět, jak zobrazit a analyzovat data, přejděte k kurzu:

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../log-query/log-analytics-tutorial.md)