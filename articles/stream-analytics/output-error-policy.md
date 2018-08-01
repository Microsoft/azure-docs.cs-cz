---
title: Zásady chyb na výstupní ve službě Azure Stream Analytics
description: Další informace o chybě výstup zpracování zásad, které jsou k dispozici ve službě Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391736"
---
# <a name="output-error-policy"></a>Výstup chybové zásady
Tento článek popisuje výstupní data chyby zpracování zásad, které se dá nakonfigurovat v Azure Stream Analytics.

Zpracování chyb výstup dat, které zásady platí jenom pro chyby převodu dat, ke kterým dochází při výstupní událost vytvářených úlohu Stream Analytics neodpovídá schématu cílové jímky. Tyto zásady můžete nakonfigurovat buď výběrem **opakujte** nebo **vyřadit**. Na webu Azure Portal Přestože v rámci úlohy Stream Analytics v části **konfigurovat**vyberte **chybové zásady** svůj výběr.

![Chybové zásady - umístění](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Retry
Když dojde k chybě, Azure Stream Analytics opakování zápis neomezeně dlouho, dokud úspěšný zápis. Neexistuje žádný časový limit opakování. Nakonec všechny další události jsou blokovány z zpracování události, ke které se opakovaně pokouší o. Tato možnost je výchozí výstupní chyba zpracování zásad.

## <a name="drop"></a>Zahodit
Azure Stream Analytics se vyřadit všechny výstupní událost, která vede k chybě převodu dat. Události vynechané riskovali nenávratnou pro přepracování později.


Všechny přechodné chyby (například chyby sítě) jsou opakovat bez ohledu na výstupu chyba zpracování zásad konfigurace.


## <a name="next-steps"></a>Další postup
[Průvodce odstraňováním potíží pro Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
