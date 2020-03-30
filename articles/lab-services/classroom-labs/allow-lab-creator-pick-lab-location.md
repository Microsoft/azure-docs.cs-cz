---
title: Povolit tvůrci testovacího prostředí vybrat umístění ve službě Azure Lab Services
description: Tento článek popisuje, jak správce účtu testovacího prostředí může povolit tvůrcům testovacího prostředí výběr umístění pro jejich testovací prostředí.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444362"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Povolit tvůrci testovacího prostředí vybrat umístění pro testovací prostředí ve službě Azure Lab Services
Ve službě Azure Lab Services může vlastník účtu testovacího prostředí povolit tvůrcům testovacího prostředí (pedagogům) vybrat umístění pro testovací prostředí, které vytvoří. Toto umístění se může lišit od umístění účtu testovacího prostředí. Umístění je skupina oblastí Azure. Například umístění ve Spojených státech je skupina oblastí, jako je například východní USA, západní USA a tak dále. 

Vy, jako vlastník účtu testovacího prostředí, můžete vybrat **možnost Povolit tvůrce testovacího prostředí vybrat umístění testovacího prostředí** možnost při vytváření účtu testovacího prostředí a po vytvoření účtu testovacího prostředí (nebo existující účet testovacího prostředí). 

## <a name="at-the-time-of-lab-account-creation"></a>V době vytvoření účtu laboratoře
Při vytváření účtu testovacího prostředí se tato možnost zobrazí na první obrazovce (karta**Základy).** 

![Povolit tuto možnost v době vytvoření testovacího prostředí](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Tato možnost je zakázána, pokud vyberete partnerskou virtuální síť pro váš účet testovacího prostředí na kartě **Upřesnit.**  

![Když je povolena virtuální síť peer](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Po vytvoření účtu testovacího prostředí
Po vytvoření účtu testovacího prostředí můžete tuto možnost povolit nebo zakázat následujícím postupem: 

1. Na stránce **Účet testovacího prostředí** vyberte v levé nabídce **nastavení testovacího prostředí.**
2. Pokud chcete autorovi testovacího prostředí povolit výběr umístění testovacího prostředí, vyberte možnost **Povolit autorovi testovacího** prostředí výběr umístění pro testovací prostředí. Pokud je zakázáno, testovací prostředí jsou automaticky vytvořeny ve stejném umístění, ve kterém existuje účet testovacího prostředí. 
    
    Toto pole je zakázáno, když vyberete virtuální síť pro pole **virtuální sítě Peer.** Je to proto, že testovací prostředí v účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí pro přístup k prostředkům ve virtuální síti druhé strany. 
1. Na panelu nástrojů vyberte **Uložit**. 

    ![Nastavení laboratoře](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Není povolen žádný výběr virtuální sítě a umístění.
V tomto scénáři jste nepovolili **možnost Povolit autorovi testovacího prostředí k výběru umístění testovacího prostředí.** 

![Žádné umístění v laboratoři](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Tvůrci testovacího prostředí (pedagogové) pak nevidí možnost vybrat místo pro testovací prostředí. Uvidí cenu za hodinu pro každou možnost velikosti, která je jim k dispozici. Když vytvoří testovací prostředí, vytvoří se v oblasti Azure, která je ve stejném umístění jako oblast Azure, ve které se nachází jejich účet testovacího prostředí. Pokud je například účet testovacího prostředí v **usa – západ**, může být vytvořeno v usa – střed **usa,** ale nebude vytvořeno v **Kanadě – východ**. Nezaručujeme nic o regionu, který si vybereme, kromě toho, že je v místě. Pokud je velikost aktuálně omezena, zobrazí se autorovi testovacího prostředí zaškrtávací políčko, ve kterém uvidí velikosti, které běžně podporujeme, ale momentálně nejsou k dispozici. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>Ve virtuální síti a výběr umístění není povolen
V tomto scénáři **povolit tvůrce testovacího prostředí vybrat umístění testovacího prostředí** možnost je zakázána, protože jste vybrali peer virtuální síť pro účet testovacího prostředí. Tvůrci testovacího prostředí pak uvidí stejnou obrazovku jako u předchozí možnosti. Protože všechny virtuální počítače musí být ve stejné oblasti Azure jako virtuální síť, testovací prostředí se vytvoří ve stejné oblasti Azure, ve které se virtuální síť nachází. Pokud je tato konkrétní oblast omezena na velikost, velikost se zobrazí jako nedostupná. 

## <a name="location-selection-is-enabled"></a>Výběr umístění je povolen.
Když vyberete **Povolit tvůrci testovacího prostředí k výběru umístění testovacího prostředí**, tvůrci testovacího prostředí (pedagogové) uvidí možnost vybrat umístění při vytváření testovacího prostředí. 

![Výběr umístění v laboratoři](../media/allow-lab-creator-pick-lab-location/location-selection.png)

Tvůrci testovacího prostředí vidí rozsah cen pro všechna místa, ve kterých je tato velikost, a mohou zvolit umístění. Testovací prostředí se vytvoří v jakékoli oblasti Azure, která se mapuje na toto umístění.

Pokud je umístění omezené, ve výchozím nastavení se v seznamu nezobrazuje. Rozbalte rozevírací seznam a vyberte **Zobrazit nedostupná umístění pro tuto velikost**. 

![Zobrazit nedostupná místa](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Náklady
Dříve byly ceny založeny na velikosti virtuálního počítače, kterou jste zvolili pro testovací prostředí. Nyní je cena založena na kombinaci operačního systému (OS), velikosti a umístění. 

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Propojení sítě testovacího prostředí s virtuální virtuální sítí druhé strany](how-to-connect-peer-virtual-network.md)
- [Připojení sdílené galerie obrázků k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidání uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazení nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
- [Konfigurace dalších nastavení testovacího prostředí](how-to-configure-lab-accounts.md)