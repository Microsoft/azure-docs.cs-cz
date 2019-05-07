---
title: Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) možnosti výkonu
description: Možnosti pro skupinu serverů Hyperškálovatelného (Citus), včetně uzlu výpočty, úložiště a oblastech.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077287"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) možnosti výkonu

## <a name="compute-and-storage"></a>Výpočetní výkon a úložiště
 
Ve skupině serverů Hyperškálovatelného (Citus) můžete vybrat nastavení výpočty a úložiště nezávisle na pracovní uzly a koordinační uzel.  Výpočetní prostředky jsou k dispozici jako virtuální jádra, která představuje logický procesor základního hardwaru. Velikost úložiště pro zřizování odkazuje na kapacity k dispozici pro koordinátor a pracovní uzly ve skupině serveru Hyperškálovatelného (Citus). Úložiště obsahuje soubory databáze, dočasné soubory, transakční protokoly a protokoly Postgres server. Celkový objem úložiště, který zřídíte také definuje vstupně-výstupní kapacity k dispozici pro každý pracovní procesy a koordinační uzel.
 
|                       | Pracovní uzel           | Uzel koordinátora      |
|-----------------------|-----------------------|-----------------------|
| Výpočetní prostředky, virtuální jádra       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Paměť na vCore, GiB | 8                     | 4                     |
| Velikost úložiště, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Typ úložiště          | Obecné účely (SSD) | Obecné účely (SSD) |
| IOPS                  | Až 3 vstupně-výstupních operací/GiB      | Až 3 vstupně-výstupních operací/GiB      |


## <a name="regions"></a>Oblasti
Skupiny serverů Hyperškálovatelného (Citus) jsou k dispozici v následujících oblastech Azure:
* Východní USA 2
* Jihovýchodní Asie
* Západní Evropa
* Západní USA 2

## <a name="pricing"></a>Ceny
Nejnovější informace o cenách najdete v článku Služba [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/).
Zobrazit náklady pro konfigurace, kterou chcete [webu Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) ukazuje měsíční náklady na **konfigurovat** kartu podle možností, které zvolíte. Pokud nemáte předplatné Azure, můžete získat Odhadovaná cena cenovou kalkulačku Azure. Na [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/) webu, vyberte **přidat položky**, rozbalte **databází** kategorie a zvolte **– Azure Database for PostgreSQL – Velkokapacitní (Citus)** přizpůsobit možnosti.
 
## <a name="next-steps"></a>Další postup
Zjistěte, jak [vytvořit skupinu serverů Hyperškálovatelného (Citus) na portálu](quickstart-create-hyperscale-portal.md).
