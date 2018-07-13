---
title: Automatické škálování cloudové služby na portálu | Dokumentace Microsoftu
description: Další informace o použití portálu ke konfiguraci pravidel automatického škálování pro webové role cloudové služby nebo role pracovního procesu v Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 29ee71e7946145e50cc875df96b674abec3e12df
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004332"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Konfigurace automatického škálování pro Cloudovou službu na portálu

Podmínky můžete nastavit pro roli pracovního procesu služby cloud, který aktivovat škálovací snížení nebo navýšení kapacity operace. Podmínky pro roli můžete být podle procesoru, disku nebo zatížení sítě role. Můžete také nastavit podmínky na základě fronty zpráv nebo metriky některých dalších prostředků Azure spojené s předplatným.

> [!NOTE]
> Tento článek se zaměřuje na webové a pracovní role cloudové služby. Když vytváříte virtuální počítač (klasický) přímo, je hostovaná v cloudové službě. Standardní virtuální počítač je možné škálovat tak, že spojíte se [dostupnosti](../virtual-machines/windows/classic/configure-availability-classic.md) a ručně je zapnout nebo vypnout.

## <a name="considerations"></a>Požadavky
Před konfigurací škálování pro vaši aplikaci, byste měli zvážit následující informace:

* Škálování je ovlivněna využití jader.

    Větší instance rolí pomocí více jader. Škálování aplikace pouze v rámci limit jader pro vaše předplatné. Řekněme například, že vaše předplatné má limit 20 jader. Při spuštění aplikace pomocí dvou střední cloudových služeb (celkem 4 jádra), je možné pouze škálovat i jiné nasazení cloudové služby v rámci vašeho předplatného ve zbývajících 16 jádry. Další informace o velikosti najdete v tématu [velikost cloudových služeb](cloud-services-sizes-specs.md).

* Můžete škálovat podle prahové hodnoty fronty zpráv. Další informace o tom, jak používat fronty, naleznete v tématu [k používání služby Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Můžete škálovat také další prostředky spojené s vaším předplatným.

* Pokud chcete povolit vysokou dostupnost vaší aplikace, měli byste zajistit, že je nasazen s dvě nebo víc instancí rolí. Další informace najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/).

* Automatické škálování pouze se stane, když jsou všechny role v **připravené** stavu.  


## <a name="where-scale-is-located"></a>Kde se nachází škálování
Po výběru cloudové služby byste měli mít okna služby cloud viditelné.

1. V okně cloudové služby na **rolemi a instancemi** dlaždice, vyberte název cloudové služby.   
   **Důležité**: Ujistěte se, že klikněte na tlačítko role cloudové služby, není instancí role, která je nižší než roli.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Vyberte **škálování** dlaždici.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatické škálování
Nastavení škálování pro roli můžete nakonfigurovat buď dva režimy **ruční** nebo **automatické**. Příručka je dle očekávání, nastavte absolutní počet instancí. Automatické ale umožňuje vám sada pravidla, která řídí způsob a jak mnohem vám by se měly škálovat.

Nastavte **škálovat** umožňuje **pravidla výkonu a plánování**.

![Nastavení škálování cloudové služby profil a pravidla](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Existující profil.
2. Přidáte pravidlo pro nadřazené profil.
3. Přidejte jiný profil.

Vyberte **přidat profil**. Profil určuje režimu, který chcete použít pro škálování: **vždy**, **opakování**, **pevné datum**.

Po nakonfigurování profil a pravidla, vyberte **Uložit** ikonu v horní části.

#### <a name="profile"></a>Profil
Profil, který nastaví minimální a maximální instance pro škálování, a také tento rozsah škálování je aktivní.

* **Vždy**

    Vždy ponechte tento rozsah instance k dispozici.  

    ![Cloudová služba, která vždy škálování](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Opakování**

    Vyberte sadu dny v týdnu, kdy chcete škálovat.

    ![Škálování cloudové služby s plán opakování](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Pevné datum**

    Škálování role pevné časové období.

    ![Škálování cloudové služby s pevného data](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po nakonfigurování profil, vyberte **OK** tlačítko v dolní části okna profil.

#### <a name="rule"></a>Pravidlo
Pravidla jsou přidány do profilu a představují podmínku, která aktivuje stupnice.

Pravidlo triggeru je podle metriky cloudové služby (využití procesoru, aktivit disku nebo síťové aktivity) ke kterému můžete přidat podmíněný hodnotu. Kromě toho může mít aktivační události na základě fronty zpráv nebo metriky některých dalších prostředků Azure spojené s předplatným.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po nakonfigurování pravidla, vyberte **OK** tlačítko v dolní části okna pravidlo.

## <a name="back-to-manual-scale"></a>Zpět na ruční škálování
Přejděte [škálování nastavení](#where-scale-is-located) a nastavte **škálovat** umožňuje **počet instancí, který nastavím ručně**.

![Nastavení škálování cloudové služby profil a pravidla](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Toto nastavení se odebere z role automatické škálování a pak můžete nastavit počet instancí přímo.

1. (Ruční nebo automatizované) možnost škálování.
2. Posuvník instanci role pro nastavení instance tak, aby umožňovala.
3. Instance role tak, aby umožňovala.

Po nakonfigurování nastavení škálování, vyberte **Uložit** ikonu v horní části.
