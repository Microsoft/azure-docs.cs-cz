---
title: Použití portálu pro oznámení o údržbě
description: Zobrazení oznámení o údržbě pro virtuální počítače běžící v Azure a spuštění samoobslužné údržby pomocí portálu.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557705"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Zpracování oznámení o plánované údržbě pomocí portálu

**Tento článek se týká virtuálních počítačů, na kterých běží Linux i Windows.**

Po naplánování naplánovaných vln [údržby](maintenance-notifications.md) můžete vyhledat seznam virtuálních počítačů, které jsou ovlivněné. 

Můžete použít Azure Portal a vyhledat virtuální počítače s naplánovanou údržbou.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V levém navigačním panelu klikněte na **Virtual Machines**.

3. V podokně Virtual Machines vyberte tlačítko **Upravit sloupce** a otevřete seznam dostupných sloupců.

4. Vyberte a přidejte následující sloupce:

   **Stav údržby**: zobrazuje stav údržby pro virtuální počítač. Možné hodnoty jsou následující:
      
    | Hodnota | Popis |
    |-------|-------------|
    | Spustit teď | Virtuální počítač je v okně samoobslužné údržby, které umožňuje zahájit údržbu sami. Níže najdete informace o tom, jak na VIRTUÁLNÍm počítači spustit údržbu. | 
    | Plánované | Pro virtuální počítač je naplánovaná údržba bez možnosti iniciace údržby. Okno údržby můžete zjistit tak, že v tomto zobrazení vyberete okno naplánované údržby nebo kliknutím na virtuální počítač. | 
    | Již Aktualizováno | Váš virtuální počítač už je aktualizovaný a v tuto chvíli se nevyžaduje žádná další akce. | 
    | Opakovat později | Zahájili jste údržbu bez úspěchu. Možnost samoobslužná údržba bude možné používat později. | 
    | Opakovat nyní | Můžete opakovat dříve neúspěšnou údržbu, kterou jste zahájili sami. | 
    | - | Váš virtuální počítač není součástí plánovaného formátu Wave pro údržbu. |

   **Údržba – samoobslužné okno**: zobrazuje časový interval, po kterém můžete na virtuálních počítačích sami začít údržbu.
   
   **Údržba – okno naplánované**: zobrazuje časový interval, po kterém Azure udržuje váš virtuální počítač za účelem dokončení údržby. 



## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a výstrahy na portálu

Azure komunikuje s plánem plánované údržby odesláním e-mailu vlastníkovi předplatného a spoluvlastníci. Můžete přidat další příjemce a kanály do této komunikace vytvořením výstrah protokolu aktivit Azure. Další informace najdete v tématu [vytváření výstrah protokolu aktivit u oznámení služby](../service-health/alerts-activity-log-service-notifications-portal.md).

Ujistěte se, že jste nastavili **Typ události** jako **plánovaná údržba** a **služby** jako **Virtual Machine Scale Sets** nebo **Virtual Machines**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Spuštění údržby virtuálního počítače z portálu

Při prohlížení podrobností o virtuálním počítači budete moci zobrazit další podrobnosti související s údržbou.  
V horní části zobrazení podrobností o virtuálním počítači se přidá nový pás karet oznámení, pokud je váš virtuální počítač zahrnutý do plánovaného Wave údržby. Kromě toho je přidána nová možnost pro zahájení údržby, pokud je to možné. 


Kliknutím na oznámení o údržbě zobrazíte stránku Údržba s dalšími podrobnostmi o plánované údržbě. Odtud budete moct **začít s údržbou** na svém virtuálním počítači.

Jakmile začnete s údržbou, váš virtuální počítač se zachová a stav údržby se aktualizuje tak, aby odrážel výsledek během několika minut.

Pokud okno samoobslužné služby vynecháte, budete ho moct zobrazit, když ho Azure zachová na svém VIRTUÁLNÍm počítači. 


## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete také zvládnout pomocí [Azure CLI](maintenance-notifications-cli.md) nebo [PowerShellu](maintenance-notifications-powershell.md).
