---
title: Ladění clusteru Avere vFXT – Azure
description: Přehled vlastních nastavení pro optimalizaci výkonu v Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152931"
---
# <a name="cluster-tuning"></a>Ladění clusteru

Většina clusterů vFXT může využívat vlastní nastavení výkonu. Tato nastavení pomáhají clusteru co nejlépe pracovat s konkrétním pracovním postupem, datovou sadou a nástroji.

Toto přizpůsobení by mělo být provedeno pomocí zástupce podpory, protože může zahrnovat konfiguraci funkcí, které nejsou k dispozici z ovládacího panelu Avere.

Tato část popisuje některé vlastní ladění, které lze provést.

## <a name="general-optimizations"></a>Obecné optimalizace

Tyto změny mohou být doporučeny na základě vlastností datové sady nebo stylu pracovního postupu.

* Pokud je úloha náročné na zápis, zvětšete velikost mezipaměti pro zápis z výchozího nastavení 20 %.
* Pokud datová sada zahrnuje mnoho malých souborů, zvyšte limit počtu souborů mezipaměti clusteru.
* Pokud práce zahrnuje kopírování nebo přesouvání dat mezi dvěma úložišti, upravte počet vláken používaných pro přesun dat:
  * Chcete-li zvýšit rychlost, můžete zvýšit počet paralelních vláken.
  * Pokud se objem back-endového úložiště zahltí, bude pravděpodobně nutné snížit počet paralelních vláken.
* Pokud cluster ukládá data pro základní filer, který používá akly NFSv4, povolte ukládání do mezipaměti režimu přístupu a zjednodušte autorizaci souborů pro konkrétní klienty.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Optimalizace cloudových NAS nebo cloudových bran

Ve scénáři cloudového NAS nebo brány poskytuje cluster vFXT přístup ke cloudovému kontejneru ve stylu NAS. Chcete-li využít vyšší rychlosti dat mezi clusterem vFXT a cloudovým úložištěm, může zástupce doporučit změnu nastavení tak, aby agresivněji tlačila data na svazek úložiště z mezipaměti. Například:

* Zvýšení počtu připojení TCP mezi clusterem a kontejnerem úložiště

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Optimalizace cloudového prasknutí nebo hybridní wan

Ve scénáři cloudového roztržení nebo ve scénáři optimalizace wan hybridního úložiště poskytuje cluster vFXT integraci mezi cloudovým a místním hardwarovým úložištěm. Tyto změny mohou být užitečné:

* Zvýšení počtu připojení TCP povolených mezi clusterem a základním filerem
* Povolte nastavení optimalizace sítě WAN pro vzdálený základní filer (Toto nastavení lze použít pro vzdálenémístní filer nebo filer jádra cloudu v jiné oblasti Azure.)
* Zvětšení velikosti vyrovnávací paměti soketu TCP<sup>*</sup>
* Povolení nastavení "vždy vpřed" pro snížení počtu souborů uložených v redundantní mezipaměti<sup>*</sup>

<sup>*</sup>Tyto úpravy nemusí platit pro všechny systémy, v závislosti na zatížení a potřeby výkonu.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Pomozte optimalizovat avere vFXT pro Azure

Chcete-li kontaktovat pracovníky podpory ohledně těchto optimalizací, použijte postup popsaný v části [Získat pomoc se systémem](avere-vfxt-open-ticket.md).
