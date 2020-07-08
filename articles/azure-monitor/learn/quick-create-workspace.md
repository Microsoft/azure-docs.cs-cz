---
title: Vytvoření pracovního prostoru Log Analytics na webu Azure Portal | Microsoft Docs
description: Naučte se, jak vytvořit pracovní prostor Log Analytics, který umožňuje řešení pro správu a shromažďování dat z vašich cloudových a místních prostředí v Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: e6ef8fb7dfd98a509b0b89e049c70169dafbe134
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83860558"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Vytvoření pracovního prostoru Log Analytics v Azure Portal
Pomocí nabídky **pracovní prostory Log Analytics** můžete vytvořit pracovní prostor Log Analytics pomocí Azure Portal. Log Analytics pracovní prostor je jedinečné prostředí pro Azure Monitor data protokolu. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru. Pokud máte v úmyslu shromažďovat data z následujících zdrojů, potřebujete Log Analytics pracovní prostor:

* Prostředky Azure ve vašem předplatném
* Místní počítače monitorované nástrojem System Center Operations Manager
* Kolekce zařízení z Configuration Manager 
* Data o diagnostice nebo protokolu z Azure Storage

Další zdroje, jako jsou virtuální počítače Azure a virtuální počítače s Windows nebo Linux ve vašem prostředí, najdete v následujících tématech:

*  [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md) 
*  [Shromažďování dat z hybridního počítače se systémem Linux](../learn/quick-collect-linux-computer.md)
*  [Shromažďování dat z hybridního počítače s Windows](quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

    ![portál Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Klikněte na **Přidat**a pak vyberte volby pro následující položky:

   * Zadejte název nového **pracovního prostoru služby Log Analytics**, například *DefaultLAWorkspace*. Tento název musí být globálně jedinečný v rámci všech předplatných Azure Monitor.
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * V případě **skupiny prostředků**vyberte možnost použít existující skupinu prostředků, která už je nastavená, nebo vytvořte novou.  
   * Vyberte dostupné **umístění**.  Další informace najdete v tématu které [oblasti Log Analytics jsou k dispozici v](https://azure.microsoft.com/regions/services/) článku a vyhledejte Azure monitor z **hledání pole produkt** .  
   * Pokud vytváříte pracovní prostor v novém předplatném vytvořeném po 2. dubnu 2018, automaticky se použije cenový plán *podle počtu GB* a možnost vybrat cenovou úroveň nebude dostupná.  Pokud vytváříte pracovní prostor pro existující předplatné vytvořené před 2. dubna nebo do předplatného, které bylo vázáno na existující registraci smlouva Enterprise (EA), vyberte upřednostňovanou cenovou úroveň.  Další informace o jednotlivých vrstvách najdete v článku [informace o cenách Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Okno pro vytvoření Log Analytics prostředku](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po zadání požadovaných informací v podokně **log Analyticsho pracovního prostoru** klikněte na tlačítko **OK**.  

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**. 

## <a name="troubleshooting"></a>Řešení potíží
Když vytvoříte pracovní prostor, který byl odstraněn za posledních 14 dní a ve [stavu "obnovitelného odstranění](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#soft-delete-behavior)", může mít operace v závislosti na konfiguraci pracovního prostoru jiný výsledek:
1. Pokud zadáte stejný název pracovního prostoru, skupinu prostředků, předplatné a oblast jako v odstraněném pracovním prostoru, váš pracovní prostor se obnoví včetně dat, konfigurace a připojených agentů.
2. Pokud použijete stejný název pracovního prostoru, ale odlišnou skupinu prostředků, předplatné nebo oblast, zobrazí se chyba *Tento název pracovního prostoru se už používá. Zkuste prosím nějaký jiný*. Chcete-li přepsat obnovitelné odstranění a trvale odstranit pracovní prostor a vytvořit nový pracovní prostor se stejným názvem, postupujte podle následujících kroků a obnovte nejprve pracovní prostor a proveďte trvalé odstranění:
   - [Obnovení](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) pracovního prostoru
   - [Trvale odstranit](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) pracovní prostor
   - Vytvoří nový pracovní prostor s použitím stejného názvu pracovního prostoru.

## <a name="next-steps"></a>Další kroky
Teď, když máte dostupný pracovní prostor, můžete nakonfigurovat shromažďování telemetrie monitorování, spustit prohledávání protokolů a tato data analyzovat a přidat řešení pro správu, která poskytují další data a analytické přehledy. 

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo úložiště Azure, přečtěte si téma [shromáždění protokolů služby Azure a metrik pro použití v Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Přidejte System Center Operations Manager jako zdroj dat](../platform/om-agents.md) , abyste mohli shromažďovat data z agentů, kteří hlásí skupinu pro správu Operations Manager, a uložit je do pracovního prostoru Log Analytics. 
* Připojte [Configuration Manager](../platform/collect-sccm.md) k importu počítačů, které jsou členy kolekcí v hierarchii.  
* Projděte si dostupná [řešení pro monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.
