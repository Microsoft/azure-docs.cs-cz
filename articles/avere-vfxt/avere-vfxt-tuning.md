---
title: Ladění clusteru avere vFXT – Azure
description: Přehled vlastního nastavení pro optimalizaci výkonu v avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 2d06e3cc80f05cc86980dd2570d1d4d203295ff2
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85505286"
---
# <a name="cluster-tuning"></a>Ladění clusteru

K většině clusterů vFXT může dotěžit vlastní nastavení výkonu. Tato nastavení pomůžou clusteru fungovat nejlépe s vaším konkrétním pracovním postupem, datovou sadou a nástroji.

Toto přizpůsobení by se mělo provádět s technickou podporou od zástupce podpory, protože může zahrnovat konfiguraci funkcí, které nejsou k dispozici v Ovládacích panelech avere.

Tato část popisuje některé vlastní ladění, které je možné provést.

## <a name="general-optimizations"></a>Obecné optimalizace

Tyto změny se můžou doporučit na základě kvality datových sad nebo stylu pracovního postupu.

* Pokud je zatížení těžké pro zápis, zvětšete velikost mezipaměti pro zápis z výchozí hodnoty 20%.
* Pokud datová sada zahrnuje mnoho malých souborů, zvyšte limit počtu souborů v mezipaměti clusteru.
* Pokud práce zahrnuje kopírování nebo přesouvání dat mezi dvěma úložištěmi, upravte počet vláken používaných pro přesun dat:
  * Pro zvýšení rychlosti můžete zvýšit počet využitých paralelních vláken.
  * Pokud je svazek úložiště back-endu přetížený, možná budete muset snížit počet využitých paralelních vláken.
* Pokud cluster ukládá data do mezipaměti pro základní souborového, která používá seznamy ACL názvů NFSv4, povolte ukládání do mezipaměti v režimu přístupu, abyste zjednodušili autorizaci souborů pro konkrétní klienty.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloudové servery NAS nebo optimalizace Cloud Gateway

V případě cloudového serveru NAS nebo brány cluster vFXT poskytuje přístup ve stylu serveru NAS ke kontejneru cloudu. Aby bylo možné využít vyšší rychlosti dat mezi clusterem vFXT a cloudovým úložištěm, může váš zástupce doporučit změnu nastavení na více agresivních nabízených dat do svazku úložiště z mezipaměti. Například:

* Zvyšte počet připojení TCP mezi clusterem a kontejnerem úložiště.

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloudové shlukování nebo optimalizace hybridní sítě WAN

Ve scénáři shlukování cloudu nebo v případě optimalizace hybridního úložiště pro sítě WAN poskytuje cluster vFXT integraci mezi cloudovým a místním hardwarovým úložištěm. Tyto změny mohou být užitečné:

* Zvyšte počet připojení TCP povolených mezi clusterem a základní souborového
* Povolte nastavení optimalizace sítě WAN pro vzdálenou službu Core souborového (Toto nastavení se dá použít pro vzdálené místní souborového nebo Cloud Core souborového v jiné oblasti Azure.)
* Zvýšit velikost vyrovnávací paměti soketu TCP<sup>*</sup>
* Povolit nastavení "vždy předáno" pro omezení redundantních souborů v mezipaměti<sup>*</sup>

<sup>*</sup>Tyto úpravy se nemusí vztahovat na všechny systémy v závislosti na potřebách úloh a výkonu.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Usnadnění optimalizace avere vFXT pro Azure

Pokud chcete kontaktovat pracovníky podpory o těchto optimalizacích, použijte postup popsaný v tématu o [Získání pomoci s vaším systémem](avere-vfxt-open-ticket.md).
