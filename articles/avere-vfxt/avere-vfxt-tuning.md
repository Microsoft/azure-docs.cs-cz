---
title: Ladění clusteru avere vFXT – Azure
description: Přehled vlastního nastavení pro optimalizaci výkonu v avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 8e25b3408482d9be9cb870df338ba0e53af52507
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414333"
---
# <a name="cluster-tuning"></a>Ladění clusteru

K většině clusterů vFXT může dotěžit vlastní nastavení výkonu. Tato nastavení pomůžou clusteru fungovat nejlépe s vaším konkrétním pracovním postupem, datovou sadou a nástroji.

Toto přizpůsobení by se mělo provést společně s zástupcem podpory, protože obvykle zahrnuje konfiguraci funkcí, které nejsou k dispozici v Ovládacích panelech avere.

Tato část popisuje některé vlastní ladění, které je možné provést.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Obecné optimalizace

Tyto změny se můžou doporučit na základě kvality datových sad nebo stylu pracovního postupu.

* Pokud je zatížení těžké pro zápis, zvětšete velikost mezipaměti pro zápis z výchozí hodnoty 20%.
* Pokud datová sada zahrnuje mnoho malých souborů, zvyšte limit počtu souborů v mezipaměti clusteru.
* Pokud práce zahrnuje kopírování nebo přesouvání dat mezi dvěma úložištěmi, upravte počet vláken používaných pro přesun dat:
  * Pro zvýšení rychlosti můžete zvýšit počet využitých paralelních vláken.
  * Pokud je svazek úložiště back-endu přetížený, možná budete muset snížit počet využitých paralelních vláken.
* Pokud cluster ukládá data do mezipaměti pro základní souborového, která používá seznamy ACL názvů NFSv4, povolte ukládání do mezipaměti v režimu přístupu, abyste zjednodušili autorizaci souborů pro konkrétní klienty.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloudové servery NAS nebo optimalizace Cloud Gateway

Aby bylo možné využít vyšší rychlost datových přenosů mezi clusterem vFXT a cloudovým úložištěm ve scénáři cloudového nebo síťového serveru NAS (kde cluster vFXT poskytuje přístup ke každému cloudovém kontejneru pomocí serveru NAS), může váš zástupce doporučit změnu nastavení jako u více agresivní odesílání dat do svazku úložiště z mezipaměti:

* Zvyšte počet připojení TCP mezi clusterem a kontejnerem úložiště.

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloudové shlukování nebo optimalizace hybridní sítě WAN

Ve scénáři shlukování cloudu nebo v případě optimalizace hybridního úložiště sítě WAN (kde cluster vFXT zajišťuje integraci mezi cloudovým a místním hardwarovým úložištěm) můžou tyto změny pomoci:

* Zvyšte počet připojení TCP povolených mezi clusterem a základní souborového
* Povolte nastavení optimalizace sítě WAN pro vzdálenou službu Core souborového (Toto nastavení se dá použít pro vzdálené místní souborového nebo Cloud Core souborového v jiné oblasti Azure.)
* Zvyšte velikost vyrovnávací paměti soketu TCP (v závislosti na úlohách a požadavcích na výkon).
* Pokud chcete omezit redundantní soubory v mezipaměti (v závislosti na požadavcích na zatížení a výkon), povolte nastavení vždy předáno.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Usnadnění optimalizace avere vFXT pro Azure

Pomocí postupu popsaného v [tématu získáte pomoc s vaším systémem](avere-vfxt-open-ticket.md) pro kontaktování pracovníků podpory o těchto optimalizacích.
