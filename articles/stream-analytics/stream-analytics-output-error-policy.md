---
title: Zásady chyb na výstupní ve službě Azure Stream Analytics
description: Další informace o chybě výstup zpracování zásad, které jsou k dispozici ve službě Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b31530966d2c5ca9a3f82f3e74ba349e66053a83
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109647"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics výstup chybové zásady
Tento článek popisuje výstupní data chyby zpracování zásad, které se dá nakonfigurovat v Azure Stream Analytics.

Zpracování chyb výstup dat, které zásady platí jenom pro chyby převodu dat, ke kterým dochází při výstupní událost vytvářených úlohu Stream Analytics neodpovídá schématu cílové jímky. Tyto zásady můžete nakonfigurovat buď výběrem **opakujte** nebo **vyřadit**. Na webu Azure Portal Přestože v rámci úlohy Stream Analytics v části **konfigurovat**vyberte **chybové zásady** svůj výběr.

![Azure Stream Analytics výstupního umístění. Chyba zásad](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Když dojde k chybě, Azure Stream Analytics opakování zápis neomezeně dlouho, dokud úspěšný zápis. Neexistuje žádný časový limit opakování. Nakonec všechny další události jsou blokovány z zpracování události, ke které se opakovaně pokouší o. Tato možnost je výchozí výstupní chyba zpracování zásad.

## <a name="drop"></a>Zahodit
Azure Stream Analytics se vyřadit všechny výstupní událost, která vede k chybě převodu dat. Události vynechané riskovali nenávratnou pro přepracování později.


Všechny přechodné chyby (například chyby sítě) jsou opakovat bez ohledu na výstupu chyba zpracování zásad konfigurace.


## <a name="next-steps"></a>Další postup
[Průvodce odstraňováním potíží pro Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
