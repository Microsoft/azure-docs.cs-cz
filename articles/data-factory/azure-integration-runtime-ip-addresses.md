---
title: ID adresy modulu Azure Integration Runtime
description: Zjistěte, které IP adresy musíte povolit příchozí provoz z, aby bylo možné správně nakonfigurovat brány firewall pro zabezpečení síťového přístupu k úložištím dat.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: d64475ce524a3e859ed68a46552fedf30068f71d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514778"
---
# <a name="azure-integration-runtime-ip-addresses"></a>ID adresy modulu Azure Integration Runtime

IP adresy, které Azure Integration Runtime používá, závisí na oblasti, ve které se nachází Azure Integration runtime. *Vše* Prostředí Azure Integration runtime, která jsou ve stejné oblasti, používají stejné rozsahy IP adres.

> [!IMPORTANT]  
> Datové toky momentálně tyto IP adresy nepoužívají. 
>
> Tyto rozsahy IP adres můžete použít pro provádění přesunu dat, kanálu a externích aktivit. Tyto rozsahy IP adres se dají použít k přidávání do seznamu povolených úložišť dat/skupiny zabezpečení sítě (NSG)/bran firewall pro příchozí přístup z prostředí Azure Integration runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP adresy: konkrétní oblasti

Povolte provoz z IP adres uvedených pro Azure Integration runtime v konkrétní oblasti Azure, kde se nacházejí vaše prostředky:

|                | Region (Oblast)              | IP adresy                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Asie           | Východní Asie           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Jihovýchodní Asie      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Austrálie      | Austrálie – východ      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Austrálie – jihovýchod | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brazílie         | Brazílie – jih        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Kanada         | Střední Kanada      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Evropa         | Severní Evropa        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Západní Evropa         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Francie         | Francie – střed      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| Indie          | Střední Indie       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japonsko          | Japonsko – východ          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Korea          | Korea – střed       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Jihoafrická republika   | Jižní Afrika – sever  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Spojené království | Spojené království – jih            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Spojené státy  | Střední USA          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | Východ USA             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | USA – východ 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | Východní USA 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | Středoseverní USA    | 40.80.185.0/25,</br>40.80.185.128/26,</br>52.162.111.48/28   |
|                | Středojižní USA    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | Středozápadní USA     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | Západní USA             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | Západní USA 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |

## <a name="known-issue-with-azure-storage"></a>Známý problém s Azure Storage

* Při připojování k účtu Azure Storage nemají pravidla sítě IP žádný vliv na požadavky pocházející z prostředí Azure Integration runtime ve stejné oblasti jako účet úložiště. Další podrobnosti najdete v [tomto článku](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Místo toho doporučujeme [při připojování k Azure Storage používat důvěryhodné služby](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Další kroky

* [Otázky zabezpečení při přesunu dat v Azure Data Factory](data-movement-security-considerations.md)
