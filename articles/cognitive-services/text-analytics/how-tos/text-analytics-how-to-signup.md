---
title: Zaregistrujte si rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Pokyny pro registraci k používání analýzy textu a fungování v rámci omezení.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: 1461682e01d25e1b076ec2be130b9c3844aa0884
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208359"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Jak se zaregistrovat pro rozhraní Text Analytics API

Prostředky rozhraní API pro analýzu textu jsou k dispozici nepřetržitě v cloudu. Před nahráním obsahu k analýze se musíte zaregistrovat a získat přístupový klíč. Požadavek každého volání rozhraní API musí obsahovat přístupový klíč.

+ Začněme předplatným Azure. Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/) a experimentovat bez poplatků.

+ Vytvořte si [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a zvolte **Rozhraní API pro analýzu textu**. Po registraci se vygeneruje váš klíč.

Rozhraní API pro analýzu textu nabízí úroveň Free pro zkoumání a vyzkoušení a fakturovatelné úrovně pro produkční úlohy. V každém předplatném můžete mít několik registrací: jednu bezplatnou, jednu placenou atd. Pokud se váš požadovaný objem transakcí zvýší, můžete přepnout na úroveň, která nabízí více transakcí.

Po služby ve verzi Preview nebo na bezplatné úrovni neexistuje žádná smlouva SLA. Další informace najdete ve [smlouvě SLA pro služby Cognitive Services](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

## <a name="how-to-change-tiers"></a>Změna úrovně

Začněte s úrovní Free a pak přejděte na fakturovatelnou úroveň pro produkční úlohy. Standardní fakturace se nabízí na odstupňovaných úrovních. Mezi úrovněmi můžete přepínat a stále budete mít stejný koncový bod a přístupové klíče.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a [vyhledejte vaši službu](text-analytics-how-to-access-key.md).

2. Klikněte na **Cenová úroveň**.

   ![Příkaz Cenová úroveň v levé navigační nabídce](../media/portal-pricing-tier.png)

3. Zvolte úroveň a klikněte na **Vybrat**.  Okamžitě po zpracování výběru začnou platit nová omezení. 

   ![Dlaždice a tlačítko Vybrat na stránce pro výběr úrovně](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Jak funguje fakturace

Fakturace vychází z počtu transakcí. V měsíčním fakturačním cyklu si můžete zakoupit blok transakcí na určité úrovni, a pokud ho překročíte, bude se vám za každou transakci účtovat malý poplatek za nadlimitní využití. Pokud pravidelně překračujete maximální limit, zvažte přechod na vyšší úroveň.

Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Co tvoří transakci v rozhraní API pro analýzu textu?
Jako transakce se počítá jakákoli poznámka k dokumentu. Volání dávkového vyhodnocování také zohlední počet dokumentů, které je třeba v transakci vyhodnotit. Takže pokud je například v jednom volání rozhraní API odesláno 1 000 dokumentů pro analýzu mínění, počítá se to jako 1 000 transakcí.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získání přístupového klíče](text-analytics-how-to-access-key.md)
