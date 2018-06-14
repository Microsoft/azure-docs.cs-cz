---
title: Automatické škálování cloudové služby na portálu | Microsoft Docs
description: Další informace o použití portálu ke konfiguraci pravidel automatického škálování pro cloudové služby webovou roli nebo role pracovního procesu v Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 0eea38cdb9827ab6e322025ff344ebbab0e83da3
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2018
ms.locfileid: "27992268"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Postup konfigurace automatického škálování pro cloudové služby na portálu

Podmínky lze nastavit pro roli pracovního procesu cloudové služby, které aktivovat škálování nebo výstupní operace. Podmínky pro roli může být založená na využití procesoru, disku nebo zatížení sítě role. Můžete také nastavit podmínku na základě fronty zpráv nebo metriku jiný Azure prostředek spojené s vaším předplatným.

> [!NOTE]
> Tento článek se zaměřuje na webových a pracovních rolí cloudové služby. Když vytvoříte virtuální počítač (klasický) přímo, je hostovaná v cloudové službě. Standardní virtuálního počítače je možné škálovat jeho s přidružením [skupinu dostupnosti](../virtual-machines/windows/classic/configure-availability-classic.md) a ručně je zapnout nebo vypnout.

## <a name="considerations"></a>Požadavky
Před konfigurací škálování pro vaši aplikaci je třeba zvážit následující informace:

* Škálování má vliv základní použití.

    Větší instance rolí pomocí více jader. Je možné škálovat aplikaci pouze v rámci maximální počet jader pro předplatné. Řekněme například, že vaše předplatné může mít 20 jader. Pokud spouštíte aplikaci pomocí dvou středních cloudové služby (celkem 4 jádra), můžete pouze postupně škálovat ostatních nasazení cloudové služby ve vašem předplatném ve zbývajících 16 jader. Další informace o velikosti najdete v tématu [cloudové služby velikosti](cloud-services-sizes-specs.md).

* Je možné škálovat podle prahová hodnota fronty zpráv. Další informace o tom, jak používat fronty najdete v tématu [jak používat fronty služby úložiště](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Můžete škálovat také další prostředky spojené s vaším předplatným.

* Povolit vysokou dostupnost vaší aplikace, se ujistěte, že je nasazený s dvěma nebo více instancí role. Další informace najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/).

* Automatické škálování pouze se stane, když jsou všechny role v **připraven** stavu.  


## <a name="where-scale-is-located"></a>Kde se nachází škálování
Po výběru cloudové služby, měli byste v okně služby cloud viditelné.

1. V okně cloudové služby na **rolí a instancí** dlaždici, vyberte název cloudové služby.   
   **Důležité**: Ujistěte se, klikněte na roli služby cloud není instanci role, která je nižší než roli.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Vyberte **škálování** dlaždici.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatické škálování
Nastavení škálování v roli lze nakonfigurovat buď dva režimy **ruční** nebo **automatické**. Ruční je, jak jste zvyklí, nastavíte absolutní počet instancí. Automatické umožňuje ale, že je sada pravidel, které řídí způsob a jak mnohem je měli škálovat.

Nastavte **škálovat podle** možnost k **pravidla plánu a výkonu**.

![Nastavení škálování cloudové služby s profil a pravidla](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Existující profil.
2. Přidáte pravidlo pro nadřazené profil.
3. Přidejte jiný profil.

Vyberte **přidat profil**. Profil určuje režimu, který chcete použít pro měřítka: **vždy**, **opakování**, **pevným datem**.

Po dokončení konfigurace profilu a pravidla, vyberte **Uložit** horním.

#### <a name="profile"></a>Profil
Profil Nastaví minimální a maximální instancí měřítka, a také tento rozsah škálování je aktivní.

* **Vždy**

    Vždy mít tento rozsah instancí, které jsou k dispozici.  

    ![Cloudová služba, která vždy škálování](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Opakování**

    Vyberte sadu dny v týdnu škálování.

    ![Cloudové služby škálování s plán opakování](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Pevné datum**

    Pevné časové období se škálovat roli.

    ![Cloudové služby škálování s pevnou datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po nakonfigurování profil, vyberte **OK** tlačítko v dolní části okna profilu.

#### <a name="rule"></a>Pravidlo
Pravidla se přidají do profilu a představují podmínku, která aktivuje měřítka.

Aktivační událost pravidlo je založena na metrika cloudové služby (využití procesoru, disková aktivita nebo síťové aktivity) do kterého můžete přidat podmíněného hodnotu. Kromě toho může mít aktivační události na základě fronty zpráv nebo metriku jiný Azure prostředek spojené s vaším předplatným.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po nakonfigurování pravidla, vyberte **OK** tlačítko v dolní části okna pravidlo.

## <a name="back-to-manual-scale"></a>Zpět na ruční škálování
Přejděte na [nastavení škálování](#where-scale-is-located) a nastavte **škálovat podle** možnost k **počet instancí, který nastavím ručně**.

![Nastavení škálování cloudové služby s profil a pravidla](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Toto nastavení se odebere, automatické škálování z role a potom můžete nastavit počet instancí přímo.

1. (Ruční nebo automatické) možnost škálování.
2. Posuvník instance role pro nastavení instance, které chcete škálovat.
3. Instance role, kterou chcete škálovat.

Po nakonfigurování nastavení škálování, vyberte **Uložit** horním.
