---
title: Konfigurace agenta Azure Log Analytics pro počítače Windows hybridní | Dokumentace Microsoftu
description: V tomto rychlém startu dozvíte, jak nasadit agenta Log Analytics pro počítače Windows spuštěné mimo Azure a Povolit shromažďování dat pomocí služby Log Analytics.
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
ms.date: 04/09/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 15885137b9559bf34fb2b985398401af09caa629
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602982"
---
# <a name="configure-the-log-analytics-agent-for-windows-computers-in-a-hybrid-environment"></a>Konfigurace agenta Log Analytics pro počítače s Windows v hybridním prostředí
[Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) může shromažďovat data přímo z fyzických nebo virtuálních počítačů s Windows do jednoho úložiště pro účely podrobných analýz a korelace. Log Analytics může shromažďovat data z datové centrum nebo jiné cloudové prostředí. Tento rychlý start ukazuje, jak v několik snadných krocích nakonfigurovat počítač s Windows a shromažďovat z něj data.  Informace o virtuálních počítačích Windows Azure naleznete v tématu [shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Požadavky na podporovanou konfiguraci najdete v tématu [Windows podporované operační systémy](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) a [konfigurace brány firewall sítě](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal vyberte **Všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam se průběžně filtruje podle vašeho zadání. Vyberte **Log Analytics**:

    ![portál Azure](media/quick-collect-windows-computer/azure-portal-01.png)
  
2. Vyberte **vytvořit**a potom zadejte následující údaje:

   * Zadejte název pro nový **pracovní prostor Log Analytics**. Podobný **DefaultLAWorkspace**.
   * Vyberte **předplatné** propojení. Pokud se výchozí hodnota není ten, který chcete použít, zvolte jinou kolekci ze seznamu.
   * Pro **skupiny prostředků**, vyberte existující skupinu prostředků, který obsahuje jeden nebo více Azure virtual machines.  
   * Vyberte **Umístění**, do kterého jsou vaše virtuální počítače nasazené. Tady je seznam [oblasti Log Analytics je k dispozici](https://azure.microsoft.com/regions/services/).  
   * Pokud vytváříte pracovní prostor v rámci předplatného, který jste vytvořili po 2. dubnem 2018, budou automaticky používat pracovní prostor **Per GB** cenový plán. Nebudete moci vybrat cenovou úroveň. Pokud vytváříte pracovní prostor v rámci předplatného, který jste vytvořili před 2. dubnem 2018, nebo v předplatném, které se vázalo na existující registraci smlouvy Enterprise, vyberte cenovou úroveň, kterou chcete použít. Zobrazit [podrobnosti o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) informace o úrovních.

        ![Vytvoření prostředku Log Analytics](media/quick-collect-windows-computer/create-loganalytics-workspace-02.png)<br>  

3. Po zadání požadovaných informací v **pracovní prostor Log Analytics** vyberte **OK**.  

Během ověřování informací a vytváří se nový pracovní prostor, můžete sledovat průběh **oznámení** v nabídce.

## <a name="get-the-workspace-id-and-key"></a>Získejte ID pracovního prostoru a klíč
Před instalací Microsoft Monitoring Agent pro Windows, budete potřebovat ID pracovního prostoru a klíč vašeho pracovního prostoru Log Analytics. Průvodce instalací potřebuje tyto informace ke správné konfiguraci agenta a zajistěte, aby že mohl komunikovat s Log Analytics.  

1. V levém horním rohu webu Azure portal, vyberte **všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam se průběžně filtruje podle vašeho zadání. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste vytvořili dříve. (Může mít pojmenujete jej **DefaultLAWorkspace**.)
3. Vyberte **upřesňující nastavení**:

    ![Upřesňující nastavení log Analytics](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.
5. Zkopírujte hodnoty napravo od **ID pracovního prostoru** a **primární klíč**. Vložte je do oblíbeného editoru.

## <a name="install-the-agent-for-windows"></a>Instalace agenta pro Windows
Následující kroky instalace a konfigurace agenta pro Log Analytics v Azure a Azure Government. Instalace agenta v počítači budete pomocí programu Instalace agenta Microsoft Monitoring Agent.

1. Pokračováním z předchozího sadu kroků, na **servery Windows** stránky, vyberte **stáhnout agenta Windows** verzi, kterou chcete stáhnout. Vyberte příslušnou verzi pro architekturu procesoru operačního systému Windows.
2. Spusťte instalační program a nainstalujte agenta na svém počítači.
2. Na **úvodní** stránce vyberte **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak vyberte **Souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak vyberte **Další**.
5. Na **možnosti instalace agenta** stránce, připojit agenta k Azure Log Analytics a pak vyberte **Další**.
6. Na **Azure Log Analytics** stránce, proveďte následující kroky:
   1. Vložte **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** , který jste si zkopírovali dříve. Pokud má počítač hlásit do pracovního prostoru Log Analytics ve službě Azure Government, vyberte **Azure US Government** v **Azure Cloud** seznamu.  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, vyberte **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru. Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověřování pomocí serveru proxy a pak vyberte **Další**.  
7. Vyberte **Další** po přidání nastavení konfigurace:

    ![Instalace agenta Microsoft Monitoring Agent](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

8. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak vyberte **Nainstalovat**.
9. Na **konfigurace byla úspěšně dokončena** stránce **Dokončit**.

Po dokončení instalace a nastavení agenta Microsoft Monitoring Agent se zobrazí v Ovládacích panelech. Můžete zkontrolovat svou konfiguraci a ověřit připojení agenta k Log Analytics. Při připojení na **Azure Log Analytics** agenta na kartě se zobrazí tato zpráva: **Microsoft Monitoring Agent se úspěšně připojilo ke službě Microsoft Log Analytics.**<br><br> ![Stav připojení MMA](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu
Log Analytics může shromažďovat události, které zadáte z protokolu událostí Windows a čítače výkonu pro delší dlouhodobější analýzu a generování sestav. Je-li provést akci při zjištění konkrétní podmínky. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu událostí Windows a několika běžných čítačů výkonu.  

1. V levém dolním rohu webu Azure portal, vyberte **další služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam se průběžně filtruje podle vašeho zadání. Vyberte **Log Analytics**.
2. Vyberte **upřesňující nastavení**:

    ![Upřesňující nastavení log Analytics](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
 
3. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.  
4. Protokol událostí přidáte zadáním názvu protokolu. Zadejte **systému**a pak vyberte symbol plus (**+**).  
5. V tabulce, vyberte **chyba** a **upozornění** závažnosti.
6. Vyberte **Uložit** v horní části stránky.
7. Vyberte **čítače výkonu Windows** povolte shromažďování čítačů výkonu na počítači s Windows.
8. Při první konfiguraci čítačů výkonu Windows pro nový pracovní prostor Log Analytics, budete mít možnost rychle vytvořit několik běžných čítačů. Zaškrtněte políčko vedle něj jsou uvedeny jednotlivé možnosti:

    ![Čítače výkonu Windows](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Vyberte **přidat vybrané čítače výkonu**. Čítače se přidají a přednastaví s 10-ukázkového desetisekundového intervalu shromažďování.

9. Vyberte **Uložit** v horní části stránky.

## <a name="view-collected-data"></a>Zobrazit shromážděná data
Teď, když jste povolili shromažďování dat, můžeme spustit prohledávání protokolů jednoduché a zobrazit nějaká data z cílového počítače.  

1. Na portálu Azure, ve vybraném pracovním prostoru, vyberte **protokoly** dlaždici.  
2. Na **prohledávání protokolů** podokně, v dialogovém okně dotazu zadejte **výkonu** a klikněte na tlačítko **spustit** horní části pole dotazu:
 
    ![Prohledávání protokolu log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Například dotaz na tomto obrázku vrátil 735 záznamů o výkonu:

    ![Výsledek prohledávání protokolu v Log Analytics](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Můžete z počítače odebrat agenta a odstranit pracovní prostor Log Analytics, pokud je už nepotřebujete.  

Chcete agenta odebrat, proveďte tyto kroky:

1. Otevřete ovládací panely.
2. Otevřete **Programy a funkce**.
3. V **programy a funkce**vyberte **agenta Microsoft Monitoring Agent** a pak vyberte **odinstalovat**.

Pokud chcete odstranit pracovní prostor Log Analytics, které jste vytvořili dříve, vyberte ji a, vyberte na stránce prostředků **odstranit**:

![Odstranit pracovní prostor Analýzy protokolů](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Další postup
Teď, když jste shromažďování provozních dat o výkonu z počítače s Windows můžete snadno začít zkoumat, analýza a funguje na data můžete shromažďovat, pro *bezplatné*.  

Chcete-li zjistěte, jak zobrazit a analyzovat data, pokračujte na kurz:

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](tutorial-viewdata.md)
