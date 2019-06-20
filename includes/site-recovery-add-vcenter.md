---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175536"
---
V části **Přidat server vCenter** zadejte popisný název pro hostitele vSphere nebo server vCenter, a poté zadejte IP adresu nebo plně kvalifikovaný název domény serveru. Pokud vaše servery VMware nejsou konfigurované k naslouchání požadavkům na jiném portu, ponechte port 443. Vyberte účet, který se má připojit k serveru VMware vCenter nebo vSphere ESXi. Klikněte na **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Pokud přidáváte VMware vCenter server nebo hostitele VMware vSphere pomocí účtu, který nemá oprávnění správce na serveru vCenter nebo hostitele, ujistěte se, že má účet povolená tato oprávnění: Datové centrum, úložiště dat, složka, hostitele, sítě, prostředků, virtuální počítač a distribuovaný přepínač vSphere. Kromě toho server VMware vCenter potřebuje povolené oprávnění Zobrazení úložišť.
