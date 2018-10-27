---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8f7eae06947a40117f3952a0a5624c22df750b34
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164689"
---
* V části **Přidat server vCenter** zadejte popisný název pro hostitele vSphere nebo server vCenter, a poté zadejte IP adresu nebo plně kvalifikovaný název domény serveru. Pokud vaše servery VMware nejsou konfigurované k naslouchání požadavkům na jiném portu, ponechte port 443. Vyberte účet, který se má připojit k serveru VMware vCenter nebo vSphere ESXi. Klikněte na **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Pokud přidáváte server VMware vCenter nebo hostitele VMware vSphere pomocí účtu, který nemá oprávnění správce na serveru vCenter nebo serveru hostitele, ujistěte se, že má účet povolená tato oprávnění: Datové centrum, Úložiště dat, Složka, Hostitel, Síť, Prostředek, Virtuální počítač a Distribuovaný přepínač vSphere. Kromě toho server VMware vCenter potřebuje povolené oprávnění Zobrazení úložišť.
