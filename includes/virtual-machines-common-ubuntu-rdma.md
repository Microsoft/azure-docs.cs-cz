---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016116"
---
1. Instalace dapl, rdmacm, ibverbs a mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. V/etc/waagent.conf povolte RDMA tím, že Odkomentujete následující konfigurační řádky. Pro úpravu tohoto souboru potřebujete root Access.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. V souboru/etc/Security/Limits.conf přidejte nebo změňte následující nastavení paměti v KB. Pro úpravu tohoto souboru potřebujete root Access. Pro účely testování můžete nastavit memlock na neomezené. Příklad: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Nainstalujte knihovnu Intel MPI. Buď si můžete [koupit a stáhnout](https://software.intel.com/intel-mpi-library/) knihovnu od společnosti Intel, nebo stáhnout [bezplatnou zkušební verzi](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Podporují se jenom moduly runtime Intel MPI 5. x.
 
   Pokyny k instalaci najdete v [příručce k instalaci knihovny Intel MPI Library](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Povolí ptrace pro nekořenové procesy bez ladicího programu (nutné pro nejnovější verze Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
