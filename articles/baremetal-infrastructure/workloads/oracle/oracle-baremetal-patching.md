---
title: Požadavky na opravy pro BareMetal pro Oracle
description: Přečtěte si informace o požadavcích na operační systém/opravy jádra pro BareMetal infrastrukturu pro Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558985"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Požadavky na opravy pro BareMetal pro Oracle

V tomto článku se podíváme na důležité informace týkající se oprav operačního systému/jádra pro BareMetal infrastrukturu pro Oracle.

Pro zajištění správného výkonu sítě a stability systému nainstalujte verzi eNIC a ovladače pro konkrétní operační systém, jak je znázorněno v následující tabulce kompatibility. 

Servery jsou doručovány zákazníkům s kompatibilními verzemi. Během/kernel oprav operačního systému (OS) se ale ovladače dají vrátit zpátky na výchozí verze ovladačů. Nezapomeňte tedy potvrdit, že příslušná verze ovladače běží po operacích opravy operačního systému/jádra.

| Dodavatel operačního systému | Verze balíčku operačního systému | Verze firmwaru | Ovladač eNIC | Ovladač fNIC |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7,6 | 3.2.3 i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7,6 | 4.1.1 b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Další kroky

Přečtěte si o konfiguraci sítě Ethernet pro BareMetal pro Oracle.

> [!div class="nextstepaction"]
> [Konfigurace sítě Ethernet pro BareMetal pro Oracle](oracle-baremetal-ethernet.md)

