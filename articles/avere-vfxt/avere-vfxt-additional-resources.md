---
title: Další odkazy Avere vFXT pro Azure
description: Odkazy na další informace o Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: v-erkell
ms.openlocfilehash: 2efbe7ddc39b8bde76ee4a135f3f44af0864a374
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188566"
---
# <a name="additional-documentation"></a>Další dokumentace

Tento článek obsahuje odkazy na další dokumentaci o rozhraní pro správu Avere ovládací Panel a související témata. 

## <a name="avere-cluster-documentation"></a>Dokumentace ke službě Avere clusteru

Další dokumentaci clusteru Avere najdete na webu na <https://azure.github.io/Avere/>. Tyto dokumenty vám mohou pomoci porozumět možnosti clusteru a jeho nastavení. 

* [Průvodce vytvořením clusteru FXT](<https://azure.github.io/Avere/#fxt_cluster>) je určená pro clustery skládá z uzlů fyzického hardwaru, ale je relevantní pro clustery vFXT také některé informace v dokumentu. Konkrétně se novým správcům clusteru vFXT využívat ve čtení těchto oddílů:
  * [Přizpůsobení podporu a monitorování nastavení](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) vysvětluje, jak přizpůsobit nastavení nahrávání podpory a povolení vzdáleného monitorování. 
  * [Konfigurace VServers a globální Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) nemá informace o vytvoření oboru názvů klienta.
  * [Konfigurace DNS pro Avere cluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) vysvětluje postup konfigurace kruhové dotazování DNS.
  * [Přidání back-endu úložiště](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) tom, jak přidat základní filtrech.

* [Průvodce konfigurací clusteru](<https://azure.github.io/Avere/#operations>) je úplný popis nastavení a možnosti pro Avere cluster. VFXT cluster používá podmnožinu těchto možností, ale většina stejný konfigurační stránky použít.

* [Řídicí panel Průvodce](<https://azure.github.io/Avere/#operations>) vysvětluje, jak používat funkce ovládacího panelu Avere monitorování clusteru.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentace k vytváření a správu vFXT

Úplného Průvodce pro používání vfxt.py, vytvoření clusteru v cloudu a nástroj pro správu, je k dispozici na Githubu: [Cloudová Správa clusteru s vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
