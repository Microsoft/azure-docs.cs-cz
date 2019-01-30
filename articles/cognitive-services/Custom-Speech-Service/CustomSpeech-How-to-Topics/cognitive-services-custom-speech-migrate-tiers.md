---
title: Migrace cenové úrovně koncových bodů ze služby Custom Speech Service v Azure | Dokumentace Microsoftu
description: Další informace o migraci nasazení z vrstev S0 a S1 na S2 Custom Speech Service koncových bodů ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 71aa20c779ae0c73db3d7ce6f267524c5bf71ea5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214556"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migrace nasazení na nový cenový model

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Od července 2017 nabízí služby Custom Speech Service [nový cenový model](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Je nový model *Jednoduší na porozumění*, *jednodušší pro výpočet nákladů*, a *flexibilnější* z hlediska škálování. Pro škálování, má Microsoft představil nový koncept jednotky škálování. Každá jednotka škálování dokáže zpracovat pět souběžných požadavků. Škálování pro souběžných požadavků ve starém modelu byla nastavena na 5 souběžných požadavků pro úroveň S0 a byl nastaven na 12 souběžných požadavků pro úroveň S1. Otevřeli jsme tato omezení a nabídnout vám zajistí větší flexibilitu požadavků případu použití.

Pokud spustíte staré úroveň S0 nebo S1, doporučujeme migrovat vaše stávající nasazení na novou úroveň S2. Nová úroveň S2 pokrývá S0 i S1 úrovně. Zobrazí se dostupné možnosti na následujícím obrázku:

![Na stránce "Zvolte cenovou úroveň"](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Společnost Microsoft zpracovává migrace v částečně automatizovaný způsob. Nejprve spustit migraci tak, že vyberete novou cenovou úroveň. Potom jsme při migraci nasazení automaticky.

Mapování ze staré úrovní škálování jednotek můžete vidět v následující tabulce:

| Úroveň | Souběžných požadavků (starý model) | Migrace | Souběžných požadavků |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** 1 škálovací jednotce je přidružená |   5 |
| S1 |  12  |   => **S2** s 3 jednotky škálování |  15 |

Pokud chcete migrovat na novou úroveň, postupujte takto:

## <a name="step-1-check-your-existing-deployment"></a>Krok 1: Kontrola existujícího nasazení
Přejděte [portál Custom Speech Service](http://cris.ai)a zkontrolujte stávajících nasazení. V našem příkladu jsou dvě nasazení. Jedno nasazení běží na vrstvu S0 a druhé nasazení běží na vrstvu S1. Nasazení se zobrazují v **možnosti nasazení** sloupce v následující tabulce:

![Na stránce Deployments](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Krok 2: Vyberte novou cenovou úroveň na webu Azure Portal
1. Otevře nová karta prohlížeče a přihlaste se k [webu Azure portal](http://ms.portal.azure.com/). 

2. V **služeb Cognitive Services** podokno v **předplatná** seznamu, vyberte své předplatné vlastní převod řeči. 

3. V podokně pro vaše předplatné, vyberte **cenová úroveň**.

    ![Odkaz "Cenová úroveň"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Na **volba cenové úrovně** stránce **S2 Standard**. Tato cenová úroveň je nový, zjednodušené a flexibilnější cenovou úroveň.

5. Vyberte **vyberte** tlačítko.

    ![Na stránce "Zvolte cenovou úroveň"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Krok 3: Kontrola stavu migrace na portálu služby Custom Speech Service
Vraťte se na portál Custom Speech Service a zkontrolovat vaše nasazení. (Pokud okno prohlížeče, je stále otevřen, aktualizujte ji.) 

Stav nasazení související byste přešli na *zpracování*. Můžete také ověřit migraci tak, že zkontrolujete **možnosti nasazení** sloupce. Existuje nyní najdete informace o jednotek škálování a protokolování. Jednotky škálování by měly odrážet předchozí cenovou úroveň. Protokolování by měl být také nastavená na on, jak je znázorněno v tabulce:

![Sloupec možnosti nasazení](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Pokud máte jakékoli problémy během migrace, kontaktujte nás.
>

## <a name="next-steps"></a>Další postup
Další kurzy najdete na stránce:
* [Vytvoření vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md)
* [Vytvoření vlastního jazykového modelu](cognitive-services-custom-speech-create-language-model.md)
* [Vytvoření vlastního koncového bodu převodu řeči na text](cognitive-services-custom-speech-create-endpoint.md)
