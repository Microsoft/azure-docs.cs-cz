---
title: Vytvoření pracovního prostoru Log Analytics v Azure Portal | Microsoft Docs
description: Naučte se, jak vytvořit pracovní prostor Log Analytics, který umožňuje řešení pro správu a shromažďování dat z vašich cloudových a místních prostředí v Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 0cf7c89c23b3df51dce2710be50c3c62f8f1e3cf
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320086"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Vytvoření pracovního prostoru Log Analytics na webu Azure Portal
Pomocí nabídky **pracovní prostory Log Analytics** můžete vytvořit pracovní prostor Log Analytics pomocí Azure Portal. Log Analytics pracovní prostor je jedinečné prostředí pro Azure Monitor data protokolu. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru. Pokud máte v úmyslu shromažďovat data z následujících zdrojů, potřebujete Log Analytics pracovní prostor:

* Prostředky Azure ve vašem předplatném
* Místní počítače monitorované nástrojem System Center Operations Manager
* Kolekce zařízení z Configuration Manager 
* Data o diagnostice nebo protokolu z Azure Storage

Další zdroje, jako jsou virtuální počítače Azure a virtuální počítače s Windows nebo Linux ve vašem prostředí, najdete v následujících tématech:

*  [Shromažďování dat z virtuálních počítačů Azure](./quick-collect-azurevm.md) 
*  [Shromažďování dat z hybridního počítače se systémem Linux](./quick-collect-linux-computer.md)
*  [Shromažďování dat z hybridního počítače s Windows](quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
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
Když vytvoříte pracovní prostor, který byl odstraněn za posledních 14 dní a ve [stavu "obnovitelného odstranění](../platform/delete-workspace.md#soft-delete-behavior)", může mít operace v závislosti na konfiguraci pracovního prostoru jiný výsledek:
1. Pokud zadáte stejný název pracovního prostoru, skupinu prostředků, předplatné a oblast jako v odstraněném pracovním prostoru, váš pracovní prostor se obnoví včetně dat, konfigurace a připojených agentů.
2. Pokud použijete stejný název pracovního prostoru, ale odlišnou skupinu prostředků, předplatné nebo oblast, zobrazí se chyba *Tento název pracovního prostoru se už používá. Zkuste prosím nějaký jiný*. Chcete-li přepsat obnovitelné odstranění a trvale odstranit pracovní prostor a vytvořit nový pracovní prostor se stejným názvem, postupujte podle následujících kroků a obnovte nejprve pracovní prostor a proveďte trvalé odstranění:
   - [Obnovení](../platform/delete-workspace.md#recover-workspace) pracovního prostoru
   - [Trvale odstranit](../platform/delete-workspace.md#permanent-workspace-delete) pracovní prostor
   - Vytvoří nový pracovní prostor s použitím stejného názvu pracovního prostoru.

## <a name="next-steps"></a>Další kroky
Teď, když máte dostupný pracovní prostor, můžete nakonfigurovat shromažďování telemetrie monitorování, spustit prohledávání protokolů a tato data analyzovat a přidat řešení pro správu, která poskytují další data a analytické přehledy. 

* [V tématu monitorování stavu Log Analytics pracovního prostoru v Azure monitor](../platform/monitor-workspace.md) vytváření pravidel výstrah pro monitorování stavu pracovního prostoru. 
* Další informace najdete [v tématu shromáždění protokolů a metrik služby Azure pro použití v Log Analytics](../platform/resource-logs.md#send-to-log-analytics-workspace) k povolení shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo Azure Storage.
