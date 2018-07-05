---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 782c855ff6d28f2cd96a87893ebf74023472badc
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437973"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Zobrazovat virtuální počítače s naplánovanou údržbou na portálu

Jakmile je naplánováno vlny plánované údržby, můžete sledovat seznam virtuálních počítačů, které jsou ovlivněny wave nadcházející údržbě. 

Můžete použít na webu Azure portal a vyhledejte virtuální počítače s naplánovanou údržbou.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém navigačním panelu klikněte na tlačítko **virtuálních počítačů**.

3. V podokně virtuální počítače, klikněte **sloupce** tlačítka pro otevření seznamu dostupných sloupců.

4. Vyberte a přidejte následující sloupce:

   **Údržba**: Zobrazí stav údržby pro virtuální počítač. Toto jsou možné hodnoty:
      
      | Hodnota | Popis |
      |-------|-------------|
      | Začít teď | Virtuální počítač je v intervalu samoobslužné údržby, který umožní, abyste údržbu zahájili sami. Níže jsou na tom, jak spustit údržbu na virtuálním počítači. | 
      | Naplánované | Pro virtuální počítač je naplánovaná údržba, ale bez možnosti, abyste ji zahájili vy. Další časového období údržby tak, že vyberete Údržba – plánované časové období v tomto zobrazení, nebo kliknutím na virtuálním počítači. | 
      | Už aktualizováno | Váš virtuální počítač je už aktualizovaná a v tuto chvíli není vyžadována žádná další akce. | 
      | Opakovat později | Inicializovali jste údržby bez úspěchu. Budete moct používat samoobslužné údržby možnost kdykoli později. | 
      | Zkuste to znovu | Zkuste to znovu dříve neúspěšné Údržba svým zahájená uživatelem. | 

   **Údržba – samoobslužná služba okno**: ukazuje časový interval, spustíte můžete samoobslužné údržby na virtuálních počítačích.
   
   **Údržba – naplánované okno**: Zobrazuje časový interval, když Azure, aby bylo možné dokončit údržbu zůstane váš virtuální počítač. 



## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a výstrahy na portálu

Azure komunikuje plánu kvůli plánované údržbě e-mailem do skupiny vlastník a spoluvlastníci předplatného. Můžete přidat další příjemce a kanály pro tuto komunikaci tak, že vytvoříte upozornění protokolu aktivit Azure. Další informace najdete v tématu [monitorování aktivit předplatného se protokol aktivit Azure] (.. / articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce na levé straně vyberte **monitorování**. 
3. V **monitorování – upozornění (klasická)** podokně klikněte na tlačítko **+ přidat upozornění protokolu aktivit**.
5. Zadejte údaje **přidat upozornění protokolu aktivit** stránce a ujistěte se, že jste nastavili následující v **kritéria**:
   - **Kategorie události**: stav služby
   - **Služby**: Škálovací sady virtuálních počítačů a virtuálních počítačů
   - **Typ**: plánovaná údržba 
    
Další informace o tom, jak nakonfigurovat upozornění protokolu aktivit najdete v tématu [vytvoření upozornění protokolu aktivit](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Spustit údržbu na virtuálním počítači z portálu

Při pohledu na podrobnosti o virtuálním počítači, bude moci zobrazit další podrobnosti související s údržbou.  
V horní části stránky zobrazení podrobností virtuálního počítače nového pásu karet oznámení se přidají, pokud se váš virtuální počítač je součástí vlny plánované údržby. Kromě toho je přidána nová možnost začít údržbu, pokud je to možné. 


Klikněte na oznámení o údržbě zobrazíte na stránce Údržba s dalšími podrobnostmi o plánované údržbě. Odtud můžete budou moci **spustit údržbu** na vašem virtuálním počítači.

Po spuštění údržby ve virtuálním počítači bude udržovat a stav údržby bude aktualizován tak, aby odrážel výsledek během několika minut.

Pokud jste okno samoobslužné služby, budete moct dál najdete v okně, když se zachová svůj virtuální počítač Azure. 
