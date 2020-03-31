---
title: Použití řídicího panelu pro testovací prostředí učebny ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat řídicí panel pro testovací prostředí učebny ve službě Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538936"
---
# <a name="dashboard-for-classroom-labs"></a>Řídicí panel pro laboratoře ve třídě
Tento článek popisuje zobrazení řídicího panelu testovacího prostředí učebny ve službě Azure Lab Services. 

![Řídicí panel](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Dlaždice nákladů a fakturace
Tato dlaždice obsahuje následující podrobnosti o odhadu nákladů:

| Nastavení | Hodnota | 
| ------- | ----- | 
| Kvótové hodiny | Maximální počet hodin, po které může uživatel virtuální ho disponizovat mimo naplánované hodiny. |
| Plánované hodiny | Hodiny, které budou vynaloženy na základě plánu nastaveného v testovacím prostředí. Tato hodnota je k dispozici pouze v případě, že je nastaveno datum od/do na všech událostech plánu. |
| Hodiny/uživatel | Součet hodin kvót a plánovaných hodin. |
| Maximální počet uživatelů | Maximální počet uživatelů v testovacím prostředí na základě všech virtuálních počítačů, které mají být nárokovány. |
| Hodiny x uživatelů | Hodiny/uživatel vynásobený počtem uživatelů. |
| Upravená kvóta | Součet hodin kvóty přidaných konkrétním uživatelům. |
| Celkový počet hodin * $/hodina | Náklady za hodinu na základě vybrané velikosti virtuálního počítače. To je založeno na pravidelné mzdy, jak jdete cenu. |
| Celkové odhadované náklady | Toto je maximální cena pro toto testovací prostředí na základě aktuálního nastavení. |

## <a name="template-tile"></a>Dlaždice Šablony
Na této dlaždici se zobrazí následující informace:

- Datum vytvoření šablony 
- Datum posledního publikování šablony 

Má také odkaz na přechod na stránku **Šablona,** kde můžete [spravovat virtuální počítače šablony](how-to-create-manage-template.md) pro třídu. 

## <a name="virtual-machine-pool-tile"></a>Dlaždice fondu virtuálních strojů

Na této dlaždici se zobrazí následující informace:

- Počet virtuálních počítačů, které jsou přiřazeny studentům (uživatelům)
- Počet virtuálních počítačů, které ještě nebyly přiřazeny studentům

Má také odkaz na přechod na stránku **fondu virtuálních počítačů,** kde můžete [spravovat fond virtuálních počítačů](how-to-set-virtual-machine-passwords.md) v testovacím prostředí. 

## <a name="users-tile"></a>Dlaždice Uživatelé

Na této dlaždici se zobrazí následující informace:

- Počet uživatelů registrovaných do třídy
- Počet uživatelů, kteří jsou přidáni do testovacího prostředí, ale nejsou registrováni do třídy 

Má také odkaz pro přechod na stránku **Uživatelé,** kde můžete [spravovat uživatele](how-to-configure-student-usage.md) pro testovací prostředí. 

## <a name="schedules-tile"></a>Dlaždice Plány
Na dlaždici se zobrazí aktuální naplánované události pro testovací prostředí. Obsahuje také odkaz na přechod na stránku **Plán,** kde můžete [vytvářet a spravovat plány](how-to-create-schedules.md). Na dlaždici se zobrazí podrobnosti pouze o dvou naplánovaných událostech a o počtu zbývajících naplánovaných událostí pro testovací prostředí. 

![Naplánované události](../media/use-dashboard/scheduled-events.png)

