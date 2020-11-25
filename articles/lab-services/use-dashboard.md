---
title: Použití řídicího panelu pro prostředí učebny v Azure Lab Services | Microsoft Docs
description: Naučte se používat řídicí panel pro prostředí učebny v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3834e6910faea38e87489d1ad0075d4bd9b8c6c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018303"
---
# <a name="dashboard-for-classroom-labs"></a>Řídicí panel pro učebn Labs
Tento článek popisuje zobrazení řídicího panelu pro prostředí učebny v Azure Lab Services. 

![Snímek obrazovky znázorňuje zobrazení řídicího panelu pro prostředí učebny v Azure Lab Services](./media/use-dashboard/dashboard.png)

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

![Naplánované události](./media/use-dashboard/scheduled-events.png)

