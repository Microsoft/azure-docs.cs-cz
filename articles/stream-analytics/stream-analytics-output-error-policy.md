---
title: Zásady chyb na výstupní ve službě Azure Stream Analytics
description: Další informace o chybě výstup zpracování zásad, které jsou k dispozici ve službě Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431613"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics výstup chybové zásady
Tento článek popisuje výstupní data chyby zpracování zásad, které se dá nakonfigurovat v Azure Stream Analytics.

Zpracování chyb výstup dat, které zásady platí jenom pro chyby převodu dat, ke kterým dochází při výstupní událost vytvářených úlohu Stream Analytics neodpovídá schématu cílové jímky. Tyto zásady můžete nakonfigurovat buď výběrem **opakujte** nebo **vyřadit**. Na webu Azure Portal Přestože v rámci úlohy Stream Analytics v části **konfigurovat**vyberte **chybové zásady** svůj výběr.

![Azure Stream Analytics výstupního umístění. Chyba zásad](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Když dojde k chybě, Azure Stream Analytics opakování zápis neomezeně dlouho, dokud úspěšný zápis. Neexistuje žádný časový limit opakování. Nakonec všechny další události jsou blokovány z zpracování události, ke které se opakovaně pokouší o. Tato možnost je výchozí výstupní chyba zpracování zásad.

## <a name="drop"></a>Zahodit
Azure Stream Analytics zahodí všechny výstupní události, které vedou k chybě převodu dat. Zahozené události není možné obnovit pro pozdější opětovné zpracování.


Všechny přechodné chyby (například chyby sítě) jsou opakovat bez ohledu na výstupu chyba zpracování zásad konfigurace.


## <a name="next-steps"></a>Další kroky
[Průvodce odstraňováním potíží pro Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
