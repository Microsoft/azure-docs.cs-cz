---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174955"
---
Dále pokud všechny servery v clusteru používají Systém Windows Server 2008 R2 nebo Windows Server 2012, je nutné ověřit, zda je oprava hotfix [KB2854082](https://support.microsoft.com/kb/2854082) nainstalována na všech místních serverech nebo virtuálních počítačích Azure, které jsou součástí clusteru. U všech serverů nebo virtuálních aplikací, které jsou v clusteru, ale nejsou ve skupině dostupnosti, by měla být nainstalována také tato oprava hotfix.

V relaci vzdálené plochy pro každý uzel clusteru stáhněte [KB2854082](https://support.microsoft.com/kb/2854082) do místního adresáře. Potom postupně nainstalujte opravu hotfix do každého uzlu clusteru. Pokud je clusterová služba aktuálně spuštěna v uzlu clusteru, je server restartován na konci instalace opravy hotfix.

> [!WARNING]
> Zastavení clusterové služby nebo restartování serveru ovlivňuje stav kvora clusteru a skupiny dostupnosti a může způsobit, že cluster přejde do offline. Chcete-li zachovat vysokou dostupnost clusteru během instalace, ujistěte se, že:
> 
> * Cluster je v optimálním stavu kvora. 
> * Před instalací opravy hotfix na libovolný uzel jsou všechny uzly clusteru online.
> * Před instalací opravy hotfix do libovolného jiného uzlu v clusteru povolte spuštění instalace opravy hotfix na jednom uzlu, včetně úplného restartování serveru.
> 
> 

