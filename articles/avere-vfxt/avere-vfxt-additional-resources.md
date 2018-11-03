---
title: Další odkazy Avere vFXT pro Azure
description: Odkazy na další informace o Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958830"
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

Úplného Průvodce pro použití vfxt.py, vytvoření clusteru v cloudu a nástroj pro správu, je k dispozici na Githubu: [Cloudová Správa clusteru s vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
