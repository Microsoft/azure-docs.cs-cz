---
title: Osvědčené postupy pro úložiště dotazů v Azure Database for PostgreSQL-Flex serveru
description: Tento článek popisuje osvědčené postupy pro úložiště dotazů na serveru Azure Database for PostgreSQL-Flex.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558990"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Osvědčené postupy pro úložiště dotazů – flexibilní Server

**Platí pro:** Azure Database for PostgreSQL – Server Flex verze 11, 12

Tento článek popisuje osvědčené postupy pro používání úložiště dotazů v Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Nastavení optimálního režimu zachycení dotazů
Umožněte, aby úložiště dotazů zachytává data, která vás zajímá. 

|**pg_qs pg_qs.query_capture_mode** | **Scénář**|
|---|---|
|_Vše_  |Důkladně Analyzujte své úlohy z pohledu všech dotazů a jejich frekvencí spuštění a dalších statistik. Identifikujte nové dotazy v úlohách. Zjišťuje, jestli se k identifikaci příležitostí pro uživatele nebo automatické Parametrizace používají ad hoc dotazy. _Vše_ přináší zvýšené náklady na spotřebu prostředků. |
|_Vrchol_  |Zaměřte pozornost na nejčastější dotazy, které vystavili klienti.
|_Žádný_ |Pokud je tato možnost nastavená na hodnotu None, nebude úložiště dotazů zachytit žádné nové dotazy. Již jste zaznamenali sadu dotazů a časové okno, které chcete prozkoumat, a chcete eliminovat, že by mohly zavádět další dotazy. _Žádná_ je vhodná pro testování a označování prostředí. _Žádné_ by se měly používat opatrně, protože byste mohli přijít o příležitost sledovat a optimalizovat důležité nové dotazy. |


> [!NOTE] 
> **pg_qs. query_capture_mode** nahrazuje **pgms_wait_sampling. query_capture_mode**. Pokud pg_qs. query_capture_mode je _none_, nastavení pgms_wait_sampling. query_capture_mode nemá žádný vliv. 


## <a name="keep-the-data-you-need"></a>Zachování potřebných dat
Parametr **pg_qs. retention_period_in_days** určuje ve dnech dobu uchovávání dat pro úložiště dotazů. Data ze staršího dotazu a statistiky se odstranila. Ve výchozím nastavení je úložiště dotazů nakonfigurované tak, aby zachovalo data po dobu 7 dnů. Vyhněte se zachování historických dat, která neplánujete použít. Zvyšte hodnotu, pokud potřebujete zachovat data déle.


## <a name="next-steps"></a>Další kroky
- Naučte se, jak získat nebo nastavit parametry pomocí [Azure Portal](howto-configure-server-parameters-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](howto-configure-server-parameters-using-cli.md).
