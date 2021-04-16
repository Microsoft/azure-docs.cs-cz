---
title: BareMetal SKU pro úlohy Oracle
description: Přečtěte si informace o SKU pro úlohy infrastruktury Oracle BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 2482f8ed682a982ee3c8c4907e21b4e6c6ebbb4f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558983"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>BareMetal SKU pro úlohy Oracle

V tomto článku se podíváme na specializované SKU infrastruktury BareMetal pro úlohy Oracle.

BareMetal infrastruktura pro rozsah SKU Oracle je tvořená dvěma sokety až čtyřmi sokety. Můžete si také vybrat z nejrůznějších jader procesoru a velikostí paměti, které splňují požadavky vašich úloh. Tady je tabulka shrnující funkce dostupných SKU.
 
| **Certifikovaný****hardware** Oracle   | **Modelování** | **Celková paměť** | **Storage** | **Dostupnost** |
| --- | --- | --- | --- | --- |
| ANO | SAP HANA ve službě Azure S32m-2 x Intel® Xeon® procesorové procesory I623416 a 32 procesorových vláken | 1,5 TB | --- | K dispozici. |
| ANO | SAP HANA ve službě Azure S64m-4 x Intel® Xeon® procesorové procesory I623432 a 64 procesorových vláken | 3,0 TB | --- | K dispozici. |
| ANO | SAP HANA v Azure S96 – 2 x Intel® Xeon® procesor E7 – 8890 v448 procesorové jádro a 96 PROCESORová vlákna | 768 GB | 3,0 TB | K dispozici. |
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
