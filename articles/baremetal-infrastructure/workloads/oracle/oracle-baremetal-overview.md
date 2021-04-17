---
title: Co je BareMetal infrastruktura pro Oracle?
description: Přečtěte si o funkcích BareMetal infrastruktury pro úlohy Oracle.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559044"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Co je BareMetal infrastruktura pro Oracle?

Tento článek nabízí přehled funkcí infrastruktury BareMetal pro úlohy Oracle.

BareMetal infrastruktura pro Oracle je založená na standardu UCS (Unified Computing System) a FLexPod s certifikací Oracle. Platforma FlexPod zajišťuje předem ověřené technologie úložiště, sítě a serveru. Nabízí úložiště NFS, které poskytuje integraci pomocí protokolu DirectNFS. Servery BareMetal jsou pro vás vyhrazené, bez hypervisoru na instancích BareMetal. 

Tyto instance slouží ke spouštění důležitých aplikací, které vyžadují úlohu Oracle. Instance BareMetal poskytují nízkou latenci (0,35 MS) vašim aplikacím běžícím na virtuálních počítačích Azure (VM). BareMetal poskytuje sdílený disk úložiště a podporuje vícenásobné přetypování vyžadované pro komunikaci mezi uzly a pomocí vyhrazené privátní sítě propojení. 

Mezi další funkce infrastruktury BareMetal pro Oracle patří:

- Oracle Certified UCS – Blade – UCSB200-M5, UCSB460-M4, UCSB480-M5
- Komunikace mezi uzly a aplikacemi Oracle Real Application Clusters (RAC) pomocí privátní virtuální sítě LAN (VLAN) – 40 GB.
- Hardware spravovaný společností Microsoft
  - Redundantní úložiště, síť, napájení, Správa
  - Monitorování pro infračervené, opravné a náhradní
  - Zahrnuje Azure ExpressRoute k řadiči domény zákazníka.
  - Zabezpečené fyzické zabezpečení a zabezpečení sítě mají přístup ke všem cloudovým službám Azure.

### <a name="supported-protocols"></a>Podporované protokoly

Následující protokoly se používají pro různé přípojné body na BareMetal serverech pro úlohy Oracle.

- Připojení operačního systému – iSCSI
- Data/protokol – NFSv3
- Backup/archivovat – názvů NFSv4

### <a name="licensing"></a>Licencování

- Přenesete vlastní místní operační systém a licence Oracle.

### <a name="operating-system"></a>Operační systém

Servery jsou předem načteny s operačním systémem RHEL 7,6.

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o SKU pro úlohy Oracle BareMetal.

> [!div class="nextstepaction"]
> [BareMetal SKU pro úlohy Oracle](oracle-baremetal-skus.md)
