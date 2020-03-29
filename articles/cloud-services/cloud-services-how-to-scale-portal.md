---
title: Automatické škálování cloudové služby na portálu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí portálu nakonfigurovat pravidla automatického škálování pro webovou roli cloudové služby nebo roli pracovního procesu v Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 5880544137855a2ea5bcd6d6e4bada46563564ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360833"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Jak nakonfigurovat automatické škálování pro cloudovou službu na portálu

Podmínky lze nastavit pro roli pracovníka cloudové služby, které aktivují horizontální navýšení kapacity v operaci nebo out. Podmínky pro roli může být založena na procesoru, disku nebo zatížení sítě role. Můžete také nastavit podmínku na základě fronty zpráv nebo metriky některého jiného prostředku Azure přidruženého k vašemu předplatnému.

> [!NOTE]
> Tento článek se zaměřuje na webové a pracovní role cloudové služby. Když vytvoříte virtuální počítač (klasický) přímo, je hostovaný v cloudové službě. Standardní virtuální počítač můžete škálovat tak, že ho přisunete k [sadě dostupnosti](../virtual-machines/windows/classic/configure-availability-classic.md) a ručně je zapnete nebo vypnete.

## <a name="considerations"></a>Požadavky
Před konfigurací škálování pro vaši aplikaci byste měli zvážit následující informace:

* Škálování je ovlivněno použitím jádra.

    Větší role instance použít více jader. Můžete škálovat aplikace pouze v rámci limitu jader pro vaše předplatné. Řekněme například, že vaše předplatné má limit 20 jader. Pokud spustíte aplikaci se dvěma středně velkými cloudovými službami (celkem 4 jádra), můžete vertikálně rozšířit pouze nasazení jiných cloudových služeb ve vašem předplatném podle zbývajících 16 jader. Další informace o velikostech naleznete v [tématu Velikosti cloudových služeb](cloud-services-sizes-specs.md).

* Můžete škálovat na základě prahové hodnoty zprávy fronty. Další informace o použití front naleznete [v tématu Jak používat službu úložiště front](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Můžete také škálovat další prostředky přidružené k vašemu předplatnému.

* Chcete-li povolit vysokou dostupnost vaší aplikace, měli byste zajistit, že je nasazena se dvěma nebo více instancemi role. Další informace naleznete v [tématu Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

* Automatické škálování se stane pouze v případě, že všechny role jsou ve stavu **Připraveno.**  


## <a name="where-scale-is-located"></a>Kde se nachází měřítko
Po výběru cloudové služby byste měli mít viditelné okno cloudové služby.

1. Na okně cloudové **služby** na dlaždici Role a instance vyberte název cloudové služby.   
   **DŮLEŽITÉ:** Nezapomeňte kliknout na roli cloudové služby, ne na instanci role, která je pod rolí.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Vyberte **dlaždici měřítka.**

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatické měřítko
Nastavení měřítka pro roli můžete nakonfigurovat buď pomocí dvou **režimů ručně** nebo **automaticky**. Manuál je, jak byste očekávali, nastavíte absolutní počet instancí. Automatické však umožňuje nastavit pravidla, která řídí, jak a podle toho, jak moc byste měli škálovat.

Nastavte **možnost Měřítko podle** **naplánování a výkonu pravidel**.

![Škálování cloudových služeb pomocí profilu a pravidla](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Existující profil.
2. Přidejte pravidlo pro nadřazený profil.
3. Přidejte další profil.

Vyberte **Přidat profil**. Profil určuje, který režim chcete použít pro měřítko: **vždy**, **opakování**, **pevné datum**.

Po konfiguraci profilu a pravidel vyberte nahoře ikonu **Uložit.**

#### <a name="profile"></a>Profil
Profil nastaví minimální a maximální instance pro měřítko a také když je aktivní tento rozsah měřítka.

* **Vždy**

    Vždy mějte tento rozsah instancí k dispozici.  

    ![Cloudová služba, která se vždy škáluje](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Opakování**

    Zvolte sadu dní v týdnu, které chcete škálovat.

    ![Škálování cloudových služeb s plánem opakování](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Pevné datum**

    Pevné časové období pro škálování role.

    ![Servisní měřítko CLoud s pevným datem](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po konfiguraci profilu vyberte tlačítko **OK** v dolní části okna profilu.

#### <a name="rule"></a>Pravidlo
Pravidla jsou přidány do profilu a představují podmínku, která aktivuje měřítko.

Aktivační událost pravidla je založena na metriku cloudové služby (využití procesoru, aktivita disku nebo aktivita sítě), ke které můžete přidat podmíněnou hodnotu. Kromě toho můžete mít aktivační událost na základě fronty zpráv nebo metriky některé jiné prostředky Azure přidružené k vašemu předplatnému.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po konfiguraci pravidla vyberte tlačítko **OK** v dolní části okna pravidla.

## <a name="back-to-manual-scale"></a>Zpět na ruční váhu
Přejděte na [nastavení měřítka](#where-scale-is-located) a nastavte **měřítko podle možnosti** na **počet instancí, který zadám ručně**.

![Škálování cloudových služeb pomocí profilu a pravidla](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Toto nastavení odebere automatické škálování z role a pak můžete nastavit počet instancí přímo.

1. Možnost měřítka (ruční nebo automatizovaná).
2. Jezdec instance role pro nastavení velikosti instancí.
3. Instance role, na kterou se má škálovat.

Po konfiguraci nastavení měřítka vyberte nahoře ikonu **Uložit.**



