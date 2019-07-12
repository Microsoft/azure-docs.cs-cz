---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800273"
---
## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Zobrazit [jak šifrování virtuálního počítače s Linuxem v Azure](/azure/virtual-machines/linux/encrypt-disks.md) a [zašifrovat virtuální disky na virtuálním počítači s Windows](/azure/virtual-machines/windows/encrypt-disks.md). |
| Šifrování během přenosu (například šifrování ExpressRoute ve virtuální síti a šifrováním virtuálními sítěmi)| Ano | Azure Virtual Machines podporuje [ExpressRoute](/azure/expressroute) a šifrování virtuální sítě. Zobrazit [šifrování ve virtuálních počítačích v cestě](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms). |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Klíče spravované zákazníkem je scénář šifrování podporovaných Azure; Zobrazit [Přehled šifrování Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Šifrované volání rozhraní API| Ano | Via HTTPS and SSL. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | |
| Vkládání podpory virtuálních sítí| Ano | . |
| Izolace sítě a Firewalling podpory| Ano |  |
| Vynucené tunelování podpory| Ano | Zobrazit [konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manageru](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Zobrazit [monitorovat a aktualizovat virtuální počítač s Linuxem v Azure](/azure/virtual-machines/linux/tutorial-monitoring.md) a [monitorovat a aktualizovat virtuální počítač s Windows v Azure](/azure/virtual-machines/windows/tutorial-monitoring.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano |  |
| Authorization| Ano |  |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano |  |
| Protokolování roviny dat a auditu | Ne |  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano |  | 
