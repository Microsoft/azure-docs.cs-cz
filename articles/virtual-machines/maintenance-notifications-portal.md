---
title: Použití portálu pro oznámení o údržbě
description: Zobrazení oznámení o údržbě pro virtuální počítače spuštěné v Azure a spuštění samoobslužné údržby pomocí portálu.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115739"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Zpracování oznámení o plánované údržbě pomocí portálu

**Tento článek se vztahuje na virtuální počítače se systémem Linux i Windows.**

Po naplánování plánované vlny [údržby](maintenance-notifications.md) můžete vyhledat seznam virtuálních počítačů, které jsou ovlivněny. 

Můžete použít portál Azure a vyhledejte virtuální počítače naplánované pro údržbu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. V levém navigačním panelu klikněte na **Virtuální počítače**.

3. V podokně Virtuální počítače vyberte **tlačítko Upravit sloupce,** abyste otevřeli seznam dostupných sloupců.

4. Vyberte a přidejte následující sloupce:

   **Stav údržby**: Zobrazuje stav údržby pro virtuální ho. Potenciální hodnoty jsou následující:
      
      | Hodnota | Popis |
      |-------|-------------|
      | Spustit teď | Virtuální ho virtuální ho dispozičního systému je v okně samoobslužné údržby, které vám umožní zahájit údržbu sami. Další informace o tom, jak spustit údržbu virtuálního počítače, najdete níže. | 
      | Naplánované | Pro virtuální počítač je naplánovaná údržba, ale bez možnosti, abyste ji zahájili vy. Okno údržby se dozvíte tak, že v tomto zobrazení vyberete okno Údržba – naplánované nebo kliknete na virtuální počítač. | 
      | Již aktualizováno | Váš virtuální počítač je už aktualizovaný a v tuto chvíli není potřeba žádná další akce. | 
      | Opakovat později | Zahájili jste údržbu bez úspěchu. Možnost samoobslužné údržby budete moci využít později. | 
      | Zkuste to znovu | Můžete opakovat dříve neúspěšné samočinné údržby. | 
      | - | Virtuální počítač není součástí plánované vlny údržby. |
      

   **Údržba – samoobslužné okno**: Zobrazuje časové okno, kdy můžete na virtuálních počítačích spustit vlastní údržbu.
   
   **Údržba – naplánované okno**: Zobrazuje časové okno, kdy Azure bude udržovat váš virtuální počítač za účelem dokončení údržby. 



## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a upozornění na portálu

Azure komunikuje plán plánované údržby odesláním e-mailu vlastníkovi předplatného a skupině spoluvlastníků. K této komunikaci můžete přidat další příjemce a kanály vytvořením výstrah protokolu aktivit Azure. Další informace naleznete v [tématu Vytváření upozornění protokolu aktivit v oznámeních o službách](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

Ujistěte se, že jste **nastavili typ události** jako **plánovanou údržbu**a **služby** jako **škálovací sady virtuálních počítačů** a/nebo virtuální **počítače**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Spuštění údržby na virtuálním počítači z portálu

Při pohledu na podrobnosti o virtuálním účtu, budete moci zobrazit další podrobnosti související s údržbou.  
V horní části zobrazení podrobností o virtuálním počítači se přidá nový pás karet s oznámením, pokud je váš virtuální počítač zahrnut do plánované vlny údržby. Kromě toho je přidána nová možnost pro spuštění údržby, pokud je to možné. 


Kliknutím na oznámení o údržbě zobrazíte stránku údržby s dalšími podrobnostmi o plánované údržbě. Odtud budete moct **spustit údržbu** na vašem virtuálním počítači.

Po spuštění údržby se váš virtuální počítač uchová a stav údržby bude aktualizován tak, aby odrážel výsledek během několika minut.

Pokud jste vynechal samoobslužné okno, budete mít stále umíte zobrazit okno, když váš virtuální počítač bude spravován azure. 


## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete také zpracovat pomocí [azure cli](maintenance-notifications-cli.md) nebo [PowerShellu](maintenance-notifications-powershell.md).