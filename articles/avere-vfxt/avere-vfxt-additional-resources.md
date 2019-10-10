---
title: Další odkazy týkající se avere vFXT pro Azure
description: Odkazy na Další informace o avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: rohogue
ms.openlocfilehash: c8ac08e3d03e8eb525cad7d73bece40c515e31a1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256295"
---
# <a name="additional-documentation"></a>Další dokumentace

Tento článek obsahuje odkazy na další dokumentaci k rozhraní pro správu ovládacího panelu avere a související témata. 

## <a name="avere-cluster-documentation"></a>Dokumentace ke clusteru avere

Další dokumentaci ke clusteru avere najdete na webu na adrese <https://azure.github.io/Avere/>. Tyto dokumenty vám pomůžou pochopit možnosti clusteru a nakonfigurovat jeho nastavení. 

* [Průvodce vytvořením clusteru FXT](<https://azure.github.io/Avere/#fxt_cluster>) je určený pro clustery tvořené fyzickými hardwarovými uzly, ale některé informace v dokumentu jsou relevantní i pro clustery vFXT. Nové Správce clusteru vFXT mohou zejména těžit z těchto částí:
  * [Přizpůsobení nastavení podpory a monitorování](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) vysvětluje, jak přizpůsobit nastavení nahrávání podpory a povolit vzdálené monitorování. 
  * [Konfigurace VServers a globálního oboru názvů](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) obsahuje informace o vytvoření oboru názvů pro klientské aplikace.
  * [Konfigurace DNS pro cluster avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) vysvětluje, jak nakonfigurovat DNS s kruhovým dotazováním.
  * [Přidání dokumentů back-end úložiště](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) , jak přidat základní filers

* [Průvodce konfigurací clusteru](<https://azure.github.io/Avere/#operations>) je úplným odkazem na nastavení a možnosti pro cluster avere. Cluster vFXT používá podmnožinu těchto možností, ale platí většinu stejných konfiguračních stránek.

* [Průvodce řídicím panelem](<https://azure.github.io/Avere/#operations>) vysvětluje, jak používat funkce monitorování clusteru v Ovládacích panelech avere.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentace k vytváření a správě vFXT

K dispozici je kompletní příručka k používání vfxt.py, nástroje pro tvorbu a správu cloudového clusteru na GitHubu: [Správa cloudových clusterů pomocí vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
