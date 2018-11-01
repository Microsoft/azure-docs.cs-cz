---
title: Avere vFXT clusteru ladění – Azure
description: Přehled vlastních nastavení za účelem optimalizace výkonu v Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 640c550e82c1b883970a3ea7a374a85989cd5e21
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633818"
---
# <a name="cluster-tuning"></a>Ladění clusteru


Většina clusterů vFXT může přinést výkonu přizpůsobená nastavení. Tato nastavení pomůžou cluster tak, aby co nejlépe pracovat určitý pracovní postup, datové sady a nástroje. 

Toto přizpůsobení se má počítat spolu s zástupce podpory a vzhledem k tomu obvykle zahrnuje konfiguraci funkcí, které nejsou k dispozici prostřednictvím ovládacího panelu Avere.

Tato část vysvětluje, že některé vlastní optimalizace, které lze provést.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helfpul in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Obecné optimalizace

Tyto změny může být vhodné na základě vlastnosti datové sady nebo pracovního postupu style.

* Pokud úlohy náročné na zápis, zvětšete velikost mezipaměti zápisu z jeho výchozí hodnotu 20 %. 
* Pokud datová sada zahrnuje mnoho malých souborů, zvyšte omezení počtu souborů clusteru mezipaměti. 
* Pokud práce zahrnuje kopírování nebo přesun dat mezi dvěma úložišť, upravte počet vláken, používá k přesunutí dat: 
  * Pokud chcete zvýšit rychlost, může zvýšit počet paralelních vláken použít.
  * Pokud je Přetěžovaná svazek úložiště back-end, můžete potřebovat snižte počet paralelních vláken použít.
* Pokud cluster ukládá data do mezipaměti pro základní filtr, používající názvů NFSv4 seznamy ACL, povolte režim přístupu k ukládání do mezipaměti pro zjednodušení autorizace souboru pro konkrétní klienty.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud NAS nebo cloudové brány optimalizace

Abyste mohli využívat vyšší rychlosti dat mezi vFXT clusteru a cloudového úložiště v cloudu NAS nebo brány scénáři (kde vFXT clusteru poskytuje NAS – vizuální styl přístup do cloudového kontejneru), zástupce doporučit, změna nastavení, jako je do více agresivní vložení dat do svazku úložiště z mezipaměti:

* Zvýšit počet připojení TCP mezi clusterem a kontejner úložiště
* Snižte hodnotu časového limitu REST pro komunikaci mezi clusterem a úložiště opakovat zápisy dříve, pokud není úspěšné okamžitě  
* Zvětšete velikost segmentu, tak, aby každý back-end zápis převody segmentu 8 MB bloků dat namísto 1 MB

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Shlukování cloudu nebo hybridní optimalizace sítě WAN

V cloud bursting scénáře nebo hybridní scénář úložiště optimalizace sítě WAN (vFXT clusteru zajišťuje integraci mezi cloudovým a místním hardwaru úložiště) může být užitečné tyto změny:

* Zvyšte počet povolených mezi clusterem a vyfiltrovat základní připojení protokolu TCP
* Povolit nastavení optimalizace sítě WAN pro vzdálené core filer (Toto nastavení můžete použít vzdálený místní vyfiltrovat nebo filer core cloudu v jiné oblasti Azure.)
* Zvětšete velikost vyrovnávací paměti soket TCP (v závislosti na nároky na zatížení a výkonu)
* Povolit nastavení "vždycky forward" ke snížení soubory redundantně uložené v mezipaměti (v závislosti na nároky na zatížení a výkonu)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Nápověda optimalizace vaší Avere vFXT pro Azure

Použijte postup popsaný v [získat nápovědu k systému](avere-vfxt-open-ticket.md) kontaktovat pracovníky podpory o těchto optimalizacích. 