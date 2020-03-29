---
title: Další odkazy na Avere vFXT pro Azure
description: Odkazy na další informace o Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153764"
---
# <a name="additional-documentation"></a>Další dokumentace

Tento článek obsahuje odkazy na další dokumentaci o rozhraní pro správu ovládacích panelů Avere a souvisejících tématech.

## <a name="avere-cluster-documentation"></a>Dokumentace k clusteru Avere

Další dokumentaci ke clusteru Avere <https://azure.github.io/Avere/>naleznete na webových stránkách na adrese . Tyto dokumenty vám mohou pomoci pochopit možnosti clusteru a jak konfigurovat jeho nastavení.

* [Průvodce vytvořením clusteru FXT](<https://azure.github.io/Avere/#fxt_cluster>) je určen pro clustery tvořené uzly fyzického hardwaru, ale některé informace v dokumentu jsou relevantní i pro clustery vFXT. Zejména noví správci clusteru vFXT mohou těžit ze čtení těchto částí:
  * [Přizpůsobení nastavení podpory a monitorování](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) vysvětluje, jak přizpůsobit nastavení nahrávání podpory a povolit vzdálené monitorování.
  * [Konfigurace serverů VServers a globálního oboru názvů](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) obsahují informace o vytvoření oboru názvů směřujícího ke klientovi.
  * [Konfigurace služby DNS pro cluster Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) vysvětluje, jak konfigurovat službu DNS s každým robin.
  * Přidání dokumentů [back-end úložiště,](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) jak přidat základní filers.

* [Průvodce konfigurací clusteru](<https://azure.github.io/Avere/#operations>) je úplným odkazem na nastavení a možnosti clusteru Avere. Cluster vFXT používá podmnožinu těchto možností, ale platí většina stejných konfiguračních stránek.

* [Průvodce řídicím panelem](<https://azure.github.io/Avere/#operations>) vysvětluje, jak používat funkce monitorování clusteru ovládacího panelu Avere.

## <a name="vfxt-creation-and-management-documentation"></a>dokumentace pro tvorbu a správu vFXT

Na GitHubu: [Správa cloudových clusterů s vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)je k dispozici úplný průvodce pro používání vfxt.py, nástroj pro vytváření a správu cloudových clusterů založených na skriptech.
