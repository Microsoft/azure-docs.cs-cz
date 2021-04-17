---
title: BareMetal SKU pro úlohy Oracle
description: Přečtěte si informace o SKU pro úlohy infrastruktury Oracle BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588849"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>BareMetal SKU pro úlohy Oracle

V tomto článku se podíváme na specializované SKU infrastruktury BareMetal pro úlohy Oracle.

BareMetal infrastruktura pro rozsah SKU Oracle je tvořená dvěma sokety až čtyřmi sokety. Můžete si také vybrat z nejrůznějších jader procesoru a velikostí paměti, které splňují požadavky vašich úloh. Tady je tabulka shrnující funkce dostupných SKU.
 
| **Certifikovaný****hardware** Oracle   | **Modelování** | **Celková paměť** | **Storage** | **Dostupnost** |
| --- | --- | --- | --- | --- |
| ANO | SAP HANA ve službě Azure S32m-2 x Intel® Xeon® procesory I6234 procesory 16 procesorových jader a 32 PROCESORových vláken | 1,5 TB | --- | K dispozici. |
| ANO | SAP HANA ve službě Azure S64m-4 x Intel® Xeon® procesory I6234 procesory 32 a 64 procesorových vláken | 3,0 TB | --- | K dispozici. |
| ANO | SAP HANA v Azure S96 – 2 x Intel® Xeon® E7-8890 v4 48 procesory procesorů a 96 procesorových vláken | 768 GB | 3,0 TB | K dispozici. |
| ANO | SAP HANA ve službě Azure S224 – 4 x procesory Intel® Xeon® Platinum 8276 procesor 112 CPU cores a 224 procesorová vlákna | 3,0 TB | 6,3 TB | K dispozici. |
| ANO | SAP HANA ve službě Azure S224m – 4 x procesory Intel® Xeon® Platinum 8276 procesor 112 CPU cores a 224 procesorová vlákna | 6,0 TB | 10,5 TB | K dispozici. |

- Jádra procesoru = součet PROCESORových jader bez technologie Hyper (celkový počet fyzických procesorů) jednotky serveru. 
- Vlákna procesoru = součet výpočetních vláken poskytovaných jádry procesoru s vlákny typu Hyper (celkový počet logických procesorů) jednotky serveru. Většina jednotek je ve výchozím nastavení nakonfigurována tak, aby používala Hyper-Threading technologie.
- Servery jsou vyhrazeny pro zákazníky.
- Zákazník má rootový přístup (bez hypervisoru).
- Servery nejsou přímo na Azure virtuální sítě.

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o úložišti, které nabízí BareMetal infrastruktura pro Oracle.

> [!div class="nextstepaction"]
> [Úložiště v BareMetal pro úlohy Oracle](oracle-baremetal-storage.md)
