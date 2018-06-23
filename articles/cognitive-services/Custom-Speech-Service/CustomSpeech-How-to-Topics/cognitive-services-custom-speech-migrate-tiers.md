---
title: Migrace cenové úrovně koncových bodů z vlastní služby řeči v Azure | Microsoft Docs
description: Zjistěte, jak migrovat nasazení z vrstvami S0 a S1 S2 služby řeči vlastní koncové body v kognitivní služby.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 6d92459deb3464cd97c215cbf9a8320628b6da80
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342713"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migrace nasazení pro nový cenový model
Od července 2017 nabízí vlastní řeči služby [nový cenový model](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Nový model je *čtení snadněji pochopit*, *jednodušší vypočítat náklady na*, a *flexibilnější* z hlediska škálování. Pro škálování, má Microsoft zaveden koncept jednotky škálování. Jednotlivé jednotky škálování může zpracovávat pěti souběžných požadavků. Škálování pro souběžných požadavků v původním modelu byla nastavená na 5 souběžných požadavků pro úroveň S0 a byla nastavena na 12 souběžných požadavků pro vrstvu S1. Jsme otevřeli těchto omezení a nabídnout vám větší flexibilitu vašim požadavkům případ použití.

Pokud spustíte staré úroveň S0 nebo S1, doporučujeme migrovat existující nasazení do nové vrstvy S2. Novou vrstvu S2 popisuje S0 i S1 vrstev. Zobrazí se dostupné možnosti na následujícím obrázku:

![Stránka "Zvolte cenovou úroveň"](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Microsoft zpracovává migrace poloautomatických způsobem. Nejprve spustit migraci tak, že vyberete novou cenovou úroveň. Potom jsme migraci nasazení automaticky.

Mapování z původní vrstvy do jednotek škálování je uvedené v následující tabulce:

| Vrstva | Souběžných požadavků (starý model) | Migrace | Souběžných požadavků |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** s jednotky škálování 1 |   5 |
| S1 |  12  |   => **S2** u jednotek škálování, 3 |  15 |

Pokud chcete migrovat na nové vrstvy, postupujte takto:

## <a name="step-1-check-your-existing-deployment"></a>Krok 1: Kontrola stávajícího nasazení
Přejděte na [portálu vlastní řeči](http://cris.ai)a zkontrolujte stávající nasazení. V našem příkladu jsou dvě nasazení. Jedno nasazení se spustí na vrstvou S0 a druhé nasazení běží na vrstvou S1. Nasazení se zobrazují v **možnosti nasazení** sloupec v následující tabulce:

![Stránka nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Krok 2: Vyberte nové cenovou úroveň na portálu Azure
1. Otevřete novou kartu prohlížeče a přihlaste se k [portál Azure](http://ms.portal.azure.com/). 

2. V **kognitivní služby** podokně, v **odběry** seznamu, vyberte své vlastní řeči předplatné. 

3. V podokně pro vaše předplatné, vyberte **cenová úroveň**.

    ![Na odkaz "Cenová úroveň"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Na **zvolte cenovou úroveň** vyberte **S2 standardní**. Tato cenová úroveň je nové, zjednodušené a flexibilnější cenovou úroveň.

5. Vyberte **vyberte** tlačítko.

    ![Stránka "Zvolte cenovou úroveň"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Krok 3: Kontrola stavu migrace na portálu služby řeči vlastní
Vraťte se na portál služby řeči vlastní a zkontrolovat vaše nasazení. (Pokud je stále otevřen okna prohlížeče, jej aktualizujte.) 

Stav související nasazení by měl mít přepnout do *zpracování*. Migrace taky můžete ověřit kontrolou **možnosti nasazení** sloupce. Existuje může nyní najít informace o protokolování a jednotek škálování. Jednotky škálování by měla odpovídat předchozí cenovou úroveň. Protokolování by také být zapnut, jak je znázorněno v tabulce:

![Sloupec možnosti nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Pokud máte potíže při migraci, kontaktujte nás.
>

## <a name="next-steps"></a>Další postup
Další kurzy najdete v tématu:
* [Vytvoření vlastní akustickými modelu](cognitive-services-custom-speech-create-acoustic-model.md)
* [Vytvoření vlastní jazyk modelu](cognitive-services-custom-speech-create-language-model.md)
* [Vytvořte vlastní koncový bod řeči na text](cognitive-services-custom-speech-create-endpoint.md)
