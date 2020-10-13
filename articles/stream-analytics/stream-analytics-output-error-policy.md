---
title: Zásady chyb výstupu v Azure Stream Analytics
description: Přečtěte si o zásadách zpracování chyb výstupu dostupných v Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75431613"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Zásada pro chyby výstupu Azure Stream Analytics
Tento článek popisuje zásady zpracování chyb výstupních dat, které je možné nakonfigurovat v Azure Stream Analytics.

Zásady zpracování chyb výstupních dat se vztahují jenom na chyby převodu dat, ke kterým dochází, když výstupní událost vytvořená úlohou Stream Analytics nevyhovuje schématu cílové jímky. Tuto zásadu můžete nakonfigurovat tak, že vyberete možnost **Opakovat** nebo **vyřadit**. V Azure Portal, zatímco v Stream Analytics úlohy, v části **Konfigurovat**vyberte **zásady chyb** , aby se provedl výběr.

![Umístění zásad chyb výstupu Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Pokud dojde k chybě, Azure Stream Analytics pokusy o zápis události po neomezenou dobu, dokud zápis nebude úspěšný. Pro opakované pokusy není k dispozici žádný časový limit. Nakonec se všechny následné události zablokují ze zpracování událostí, která se opakuje. Tato možnost je výchozí zásadou zpracování chyb výstupu.

## <a name="drop"></a>Umístíte
Azure Stream Analytics zahodí všechny výstupní události, které vedou k chybě převodu dat. Zahozené události není možné obnovit pro pozdější opětovné zpracování.


Všechny přechodné chyby (například chyby sítě) se zopakují bez ohledu na konfiguraci zásad zpracování chyb výstupu.


## <a name="next-steps"></a>Další kroky
[Průvodce odstraňováním potíží pro Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
