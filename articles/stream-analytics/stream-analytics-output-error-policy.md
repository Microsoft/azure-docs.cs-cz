---
title: Zásady chyb výstupu ve Službě Azure Stream Analytics
description: Přečtěte si o zásadách zpracování výstupních chyb, které jsou dostupné ve službě Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431613"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Zásady chyb výstupu Azure Stream Analytics
Tento článek popisuje zásady zpracování výstupních dat, které lze nakonfigurovat ve službě Azure Stream Analytics.

Zásady zpracování chyb výstupních dat platí pouze pro chyby převodu dat, ke kterým dochází, když výstupní událost vytvořená úlohou Stream Analytics neodpovídá schématu cílového jímky. Tuto zásadu můžete nakonfigurovat výběrem možnosti **Opakovat** nebo **Přetažení**. Na webu Azure Portal v yberte v části Konfigurace v části **Konfigurace**vyberte **zásady chyb.**

![Umístění zásad chyb výstupu služby Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Dojde-li k chybě, Azure Stream Analytics opakuje zápis události neomezeně dlouho, dokud zápis proběhne úspěšně. Neexistuje žádný časový časový osývač pro opakování. Nakonec všechny následné události jsou blokovány zpracování událostí, která se pokouší znovu. Tato možnost je výchozí zásady zpracování výstupních chyb.

## <a name="drop"></a>Drop
Azure Stream Analytics zahodí všechny výstupní události, které vedou k chybě převodu dat. Zahozené události není možné obnovit pro pozdější opětovné zpracování.


Všechny přechodné chyby (například chyby sítě) jsou opakovány bez ohledu na konfiguraci zásad zpracování výstupních chyb.


## <a name="next-steps"></a>Další kroky
[Průvodce odstraňováním potíží pro Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
