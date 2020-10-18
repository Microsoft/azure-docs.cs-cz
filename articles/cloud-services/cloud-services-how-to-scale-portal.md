---
title: Automatické škálování cloudové služby na portálu | Microsoft Docs
description: Naučte se používat portál ke konfiguraci pravidel automatického škálování webové role nebo role pracovního procesu cloudové služby v Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 06a0209c2bbd0982054d33c199685d016f405b0c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165481"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Jak nakonfigurovat automatické škálování pro cloudovou službu na portálu

Můžete nastavit podmínky pro roli pracovního procesu cloudové služby, která spouští operaci horizontálního nebo vyvýšení kapacity. Podmínky role můžou být založené na procesoru, disku nebo síťové zátěži role. Můžete také nastavit podmínku na základě fronty zpráv nebo metriky nějakého jiného prostředku Azure přidruženého k vašemu předplatnému.

> [!NOTE]
> Tento článek se zaměřuje na webové role a role pracovních procesů cloudové služby. Když vytváříte virtuální počítač (Classic) přímo, je hostovaný v cloudové službě. Standardní virtuální počítač můžete škálovat tak, že ho přidružíte ke [skupině dostupnosti](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) , a ručně je zapnout nebo vypnout.

## <a name="considerations"></a>Požadavky
Před konfigurací škálování pro aplikaci byste měli zvážit následující informace:

* Škálování je ovlivněné základní spotřebou.

    Větší instance rolí používají více jader. Aplikaci můžete škálovat pouze v rámci limitu jader pro vaše předplatné. Řekněme například, že vaše předplatné má limit 20 jader. Pokud spouštíte aplikaci se dvěma středními síťovými službami (celkem 4 jádra), můžete škálovat všechna nasazení cloudových služeb v rámci vašeho předplatného ve zbývajících 16 jádrech. Další informace o velikostech najdete v tématu [velikosti cloudových služeb](cloud-services-sizes-specs.md).

* Škálování můžete škálovat na základě prahové hodnoty zprávy ve frontě. Další informace o tom, jak používat fronty, najdete v tématu [How to use the Queue Storage Service](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Můžete také škálovat jiné prostředky přidružené k vašemu předplatnému.

* Pokud chcete povolit vysokou dostupnost vaší aplikace, měli byste zajistit, aby byla nasazená se dvěma nebo více instancemi role. Další informace najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/).

* Automatické škálování se stane pouze v případě, že jsou všechny role ve stavu **připraveno** .  


## <a name="where-scale-is-located"></a>Kde se nachází škálování
Po výběru cloudové služby byste měli mít zobrazené okno cloudové služby.

1. V okně cloudová služba na dlaždici **role a instance** vyberte název cloudové služby.   
   **Důležité**: Ujistěte se, že kliknete na roli Cloudová služba, nikoli na instanci role, která se nachází pod rolí.

    ![Snímek obrazovky s dlaždicou role a instance s rolí pracovního procesu s možností fronta S B 1 popsaným červeně](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Vyberte dlaždici **škálování** .

    ![Snímek stránky operace s dlaždicí prodeje popsaný červeně](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatické škálování
Nastavení škálování pro roli můžete nakonfigurovat buď pomocí dvou režimů **ručně** , nebo **automaticky**. Ruční je to, jak byste očekávali, nastavíte absolutní počet instancí. Možnost automaticky vám ale umožní nastavit pravidla, která určují, jak a o kolik byste měli škálovat.

Nastavte možnost **škálovat podle** na **pravidla plánování a výkon**.

![Snímek obrazovky zobrazující možnost pravidla plánování a výkonu.](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Existující profil.
2. Přidejte pravidlo pro nadřazený profil.
3. Přidejte jiný profil.

Vyberte **Přidat profil**. Profil určuje, který režim má být použit pro škálování: **vždy**, **opakování**, **pevné datum**.

Po nakonfigurování profilu a pravidel vyberte ikonu **Uložit** v horní části.

#### <a name="profile"></a>Profil
Profil nastaví minimální a maximální počet instancí pro měřítko a také v případě, že je tento rozsah stupnice aktivní.

* **Vždy**

    Vždy ponechat tento rozsah instancí k dispozici.  

    ![Cloudová služba, která vždy škálovat](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Opakování**

    Vyberte sadu dnů v týdnu pro horizontální navýšení kapacity.

    ![Škálování cloudové služby s plánem opakování](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Pevné datum**

    Pevný rozsah data pro škálování role.

    ![Škálování CLoudové služby s pevným datem](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po nakonfigurování profilu klikněte na tlačítko **OK** v dolní části okna profil.

#### <a name="rule"></a>Pravidlo
Pravidla jsou přidána do profilu a představují podmínku, která spouští škálování.

Trigger pravidla vychází z metriky cloudové služby (využití procesoru, aktivity disku nebo síťové aktivity), ke kterým můžete přidat podmíněnou hodnotu. Navíc můžete mít Trigger na základě fronty zpráv nebo metriky nějakého jiného prostředku Azure přidruženého k vašemu předplatnému.

![Snímek obrazovky s dialogovým oknem pravidlo s názvem metriky popsanými červeně](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po nakonfigurování pravidla vyberte tlačítko **OK** v dolní části okna pravidla.

## <a name="back-to-manual-scale"></a>Zpět k ručnímu škálování
Přejděte do [Nastavení škálování](#where-scale-is-located) a nastavte možnost **škálovat podle** na **počet instancí, který zadáte ručně**.

![Nastavení škálování cloudových služeb pomocí profilu a pravidla](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Toto nastavení odebere z role automatické škálování a potom můžete nastavit počet instancí přímo.

1. Možnost škálování (ruční nebo automatizovaná).
2. Posuvník instance role, na kterou se mají nastavit instance pro škálování.
3. Instance role, na kterou se má škálovat

Po nakonfigurování nastavení škálování vyberte ikonu **Uložit** v horní části.