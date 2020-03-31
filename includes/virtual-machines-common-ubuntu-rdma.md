---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67671209"
---
1. Instalace dapl, rdmacm, ibverbs a mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. V souboru /etc/waagent.conf povolte rdma zrušením komentování následujících konfiguračních řádků. K úpravě tohoto souboru potřebujete root přístup.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Přidejte nebo změňte následující nastavení paměti v kb v souboru /etc/security/limits.conf. K úpravě tohoto souboru potřebujete root přístup. Pro účely testování můžete nastavit memlock na neomezené. Například: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Nainstalujte knihovnu Intel MPI. Buď [koupit a stáhnout](https://software.intel.com/intel-mpi-library/) knihovnu od společnosti Intel nebo stáhnout [bezplatnou zkušební verzi](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Podporovány jsou pouze moduly runtimes Intel MPI 5.x.
 
   Kroky instalace naleznete v [Průvodci instalací knihovny Intel MPI](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Povolte ptrace pro nekořenové procesy bez ladicího programu (potřebné pro nejnovější verze technologie Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
