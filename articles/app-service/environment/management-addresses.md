---
title: App Service Environment správu adresy – Azure
description: Seznam adres správy používá k příkazu služby App Service Environment
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7fb39886b19a2229188821eb39d4fb8a5928bb43
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276675"
---
# <a name="app-service-environment-management-addresses"></a>Adresy pro správu aplikace app Service Environment

App Service Environment (ASE) je nasazení služby Azure App Service do podsítě ve službě Azure Virtual Network (VNet).  Služba ASE musí být dostupný z rovina správy používá ve službě Azure App Service.  Tato služba ASE správu přenosy prochází sítí řízené uživatelem. Pokud tento provoz je blokován nebo nesprávně směrovanými, že služba ASE zablokuje. Podrobnosti o sítí závislostí služby ASE najdete [síťových aspekty a App Service Environment][networking]. Obecné informace služby ase, můžete začít s [Úvod do služby App Service Environment][intro].

Tento dokument obsahuje seznam zdrojové adresy služby App Service pro provoz správy do služby ASE a má dva účely důležité.  

1. Tyto adresy můžete použít k vytvoření skupiny zabezpečení sítě pro uzamčení příchozí provoz.  
2. Můžete vytvořit trasy s těmito adresami pro podporu nasazení na vynucené tunelové propojení. Podrobnosti o tom, jak nakonfigurovat službu ASE fungovaly v prostředí, ve kterém odeslání odchozího provozu v místním prostředí, najdete v článku [konfigurace služby ASE vynucené tunelování][forcedtunnel]

Všechny služby ase mají veřejnou virtuální IP Adresou stává jaký provoz správy. Příchozí provoz správy z tyto adresy pocházejí z na portech 454 a 455 na veřejných virtuálních IP adres vaší služby ase.  

## <a name="list-of-management-addresses"></a>Seznam adres pro správu ##

| Oblast | Adresy |
|--------|-----------|
| Všech veřejných oblastech | 70.37.57.58 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure Government (fairfax nebo nebo MAG) | 23.97.29.209 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>Získání adresy pro správu z rozhraní API ##

Můžete vytvořit seznam adresy pro správu, které odpovídají vaší služby ASE pomocí následujícího volání rozhraní API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Rozhraní API vrátí dokument JSON, který zahrnuje všechny příchozí adresy pro vaši službu ASE. Seznam adres zahrnuje virtuální IP adresy používané vaší služby ASE a rozsah adres podsítě služby ASE, samotný adresy pro správu.  

Pro volání rozhraní API se [armclient](https://github.com/projectkudu/ARMClient) použijte následující příkazy, ale nahraďte ID předplatného, skupiny prostředků a název služby ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
