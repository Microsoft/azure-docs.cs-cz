---
title: Další odkazy Avere vFXT pro Azure
description: Odkazy na další informace o Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: afba39d1af700650cfbf7226dff36729a76a0bda
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633832"
---
# <a name="additional-documentation"></a>Další dokumentace

Tento článek obsahuje odkazy na další dokumentaci o rozhraní pro správu Avere ovládací Panel a související témata. 

## <a name="avere-cluster-documentation"></a>Dokumentace ke službě Avere clusteru

Další dokumentaci clusteru Avere najdete na webu na <http://library.averesystems.com/>. Tyto dokumenty vám mohou pomoci porozumět možnosti clusteru a jeho nastavení. 

* [Průvodce vytvořením clusteru FXT](<http://library.averesystems.com/#fxt_cluster>) je určená pro clustery skládá z uzlů fyzického hardwaru, ale je relevantní pro clustery vFXT také některé informace v dokumentu. Konkrétně se novým správcům clusteru vFXT využívat ve čtení těchto oddílů:
  * [Přizpůsobení podporu a monitorování nastavení](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) vysvětluje, jak přizpůsobit nastavení nahrávání podpory a povolení vzdáleného monitorování. 
  * [Konfigurace VServers a globální Namespace](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) nemá informace o vytvoření oboru názvů klienta.
  * [Konfigurace DNS pro Avere cluster](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) vysvětluje postup konfigurace kruhové dotazování DNS.
  * [Přidání back-endu úložiště](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) tom, jak přidat základní filtrech.

* [Průvodce konfigurací clusteru](<http://library.averesystems.com/#operations>) je úplný popis nastavení a možnosti pro Avere cluster. VFXT cluster používá podmnožinu těchto možností, ale většina stejný konfigurační stránky použít.

* [Řídicí panel Průvodce](<http://library.averesystems.com/#operations>) vysvětluje, jak používat funkce ovládacího panelu Avere monitorování clusteru.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentace k vytváření a správu vFXT

Úplného Průvodce pro použití vfxt.py, vytvoření clusteru v cloudu a nástroj pro správu, se poskytuje <https://github.com/AvereSystems/vFXT.py/blob/master/docs/README.md>.  
