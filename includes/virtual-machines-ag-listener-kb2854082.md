---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174955"
---
Pokud na všech serverech v clusteru běží Windows Server 2008 R2 nebo Windows Server 2012, musíte ověřit, jestli je na všech místních serverech nebo virtuálních počítačích Azure, které jsou součástí clusteru, nainstalované opravy hotfix [KB2854082](https://support.microsoft.com/kb/2854082) . Tato oprava hotfix by měla být nainstalována na jakémkoli serveru nebo na virtuálním počítači, který je v clusteru, ale ne ve skupině dostupnosti.

V relaci vzdálené plochy pro každý uzel clusteru stáhněte [KB2854082](https://support.microsoft.com/kb/2854082) do místního adresáře. Pak nainstalujte opravu hotfix na každý uzel clusteru sekvenčně. Pokud je Clusterová služba aktuálně spuštěná na uzlu clusteru, server se restartuje na konci instalace oprav hotfix.

> [!WARNING]
> Zastavení Clusterové služby nebo restartování serveru má vliv na stav kvora clusteru a skupinu dostupnosti a může způsobit, že by cluster přešel do offline režimu. Pokud chcete zajistit vysokou dostupnost clusteru během instalace, ujistěte se, že:
> 
> * Cluster je ve stavu optimálního kvora. 
> * Než nainstalujete opravu hotfix na libovolný uzel, budou všechny uzly clusteru online.
> * Než nainstalujete opravu hotfix na jakýkoli jiný uzel v clusteru, umožněte spuštění instalace oprav hotfix v jednom uzlu, včetně úplného restartování serveru.
> 
> 

