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
ms.openlocfilehash: b8d759c7b55608be780389991e6bb393f4f3fe9f
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50981399"
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
      | - | Váš virtuální počítač není součástí vlny plánované údržby. |
      

   **Údržba – samoobslužná služba okno**: ukazuje časový interval, spustíte můžete samoobslužné údržby na virtuálních počítačích.
   
   **Údržba – naplánované okno**: Zobrazuje časový interval, když Azure, aby bylo možné dokončit údržbu zůstane váš virtuální počítač. 



## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a výstrahy na portálu

Azure komunikuje plánu kvůli plánované údržbě e-mailem do skupiny vlastník a spoluvlastníci předplatného. Můžete přidat další příjemce a kanály pro tuto komunikaci tak, že vytvoříte upozornění protokolu aktivit Azure. Další informace najdete v tématu [vytvoření upozornění protokolu aktivit pro oznámení služby](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

Ujistěte se, že nastavíte **typ události** jako **plánované údržby** a **služby** jako **Virtual Machine Scale Sets** a/nebo **Virtuálních počítačů**
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Spustit údržbu na virtuálním počítači z portálu

Při pohledu na podrobnosti o virtuálním počítači, bude moci zobrazit další podrobnosti související s údržbou.  
V horní části stránky zobrazení podrobností virtuálního počítače nového pásu karet oznámení se přidají, pokud se váš virtuální počítač je součástí vlny plánované údržby. Kromě toho je přidána nová možnost začít údržbu, pokud je to možné. 


Klikněte na oznámení o údržbě zobrazíte na stránce Údržba s dalšími podrobnostmi o plánované údržbě. Odtud můžete budou moci **spustit údržbu** na vašem virtuálním počítači.

Po spuštění údržby ve virtuálním počítači bude udržovat a stav údržby bude aktualizován tak, aby odrážel výsledek během několika minut.

Pokud jste okno samoobslužné služby, budete moct dál najdete v okně, když se zachová svůj virtuální počítač Azure. 
