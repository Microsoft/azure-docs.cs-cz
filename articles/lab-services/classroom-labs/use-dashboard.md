---
title: Použití řídicího panelu pro prostředí učebny v Azure Lab Services | Microsoft Docs
description: Naučte se používat řídicí panel pro prostředí učebny v Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77538936"
---
# <a name="dashboard-for-classroom-labs"></a>Řídicí panel pro učebn Labs
Tento článek popisuje zobrazení řídicího panelu pro prostředí učebny v Azure Lab Services. 

![Řídicí panel](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Dlaždice náklady a fakturace
Tato dlaždice nabízí následující podrobné informace odhadu nákladů:

| Nastavení | Hodnota | 
| ------- | ----- | 
| Hodiny kvóty | Maximální počet hodin, po které může uživatel virtuální počítač použít mimo naplánovanou dobu. |
| Naplánované hodiny | Hodiny, které budou vynaloženy na základě plánu nastaveného v testovacím prostředí. Tato hodnota je k dispozici pouze v případě, že je pro všechny události plánu nastaveno datum od/k. |
| Hodiny/uživatel | Součet hodin a naplánovaných hodin v kvótě. |
| Maximální počet uživatelů | Maximální počet uživatelů v testovacím prostředí na základě všech virtuálních počítačů, které mají být vyžádány. |
| Hodiny x uživatelů | Hodiny/uživatel vynásobený počtem uživatelů. |
| Upravená kvóta | Součet kvótových hodin přidaných pro konkrétní uživatele. |
| Celkový počet hodin * $ za hodinu | Cena za hodinu na základě vybrané velikosti virtuálního počítače Tato volba je založena na běžné platbě za cenu. |
| Celkové odhadované náklady | Toto je maximální cena pro toto testovací prostředí na základě aktuálního nastavení. |

## <a name="template-tile"></a>Dlaždice šablony
Na této dlaždici vidíte následující informace:

- Datum, kdy byla šablona vytvořena 
- Datum, kdy byla šablona naposledy publikována 

Má také odkaz pro přechod na stránku **šablony** , kde můžete [spravovat virtuální počítač šablony](how-to-create-manage-template.md) pro třídu. 

## <a name="virtual-machine-pool-tile"></a>Dlaždice fondu virtuálních počítačů

Na této dlaždici vidíte následující informace:

- Počet virtuálních počítačů, které jsou přiřazeny studentům (uživatelům)
- Počet virtuálních počítačů, které ještě nebyly přiřazeny studentům

Obsahuje taky odkaz pro přechod na stránku **fondu virtuálních počítačů** , kde můžete [Spravovat fond virtuálních počítačů](how-to-set-virtual-machine-passwords.md) v testovacím prostředí. 

## <a name="users-tile"></a>Dlaždice uživatelé

Na této dlaždici vidíte následující informace:

- Počet uživatelů registrovaných ve třídě
- Počet uživatelů, kteří jsou přidáni do testovacího prostředí, ale nejsou zaregistrovaní ve třídě 

Obsahuje také odkaz pro přechod na stránku **Uživatelé** , kde můžete [Spravovat uživatele](how-to-configure-student-usage.md) pro testovací prostředí. 

## <a name="schedules-tile"></a>Dlaždice plány
Na dlaždici vidíte aktuální naplánované události pro testovací prostředí. Má také odkaz pro přechod na stránku **plánu** , kde můžete [vytvářet a spravovat plány](how-to-create-schedules.md). Na dlaždici se zobrazí podrobnosti jenom pro dvě naplánované události a počet zbývajících naplánovaných událostí pro testovací prostředí. 

![Naplánované události](../media/use-dashboard/scheduled-events.png)

