---
title: Poskytněte zpětnou vazbu o anomálii službě Advisory metrik.
titleSuffix: Azure Cognitive Services
description: Naučte se, jak odeslat zpětnou vazbu na anomálie nalezené instancí poradce metriky a vyladit výsledky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935430"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Úprava detekce anomálií pomocí zpětné vazby

Pokud nejste spokojeni s některými výsledky detekce anomálií, které nabízí monitor metriky, můžete ručně přidat zpětnou vazbu, která ovlivní model aplikovaný na vaše data. 

Pomocí tlačítek v pravém horním rohu stránky aktivujete režim poznámek zpětné vazby.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Režim anotace zpětné vazby":::

Po aktivaci režimu poznámek zpětné vazby můžete poskytnout zpětnou vazbu na jeden bod nebo několik souvislých bodů.

## <a name="give-feedback-for-one-point"></a>Sdělte nám svůj názor na jeden bod 

V případě aktivace režimu anotace zpětné vazby klikněte na určitý bod a otevřete panel **Přidat zpětnou vazbu** . Můžete nastavit typ zpětné vazby, kterou chcete použít. Tato zpětná vazba bude začleněna do zjišťování budoucích bodů.  

* Pokud se domníváte, že bod byl nesprávně označený monitorováním metrik, vyberte možnost **anomálie** . Můžete určit, zda má být bod nebo by neměl být anomálií. 
* Vyberte **ChangePoint** , pokud se domníváte, že bod indikuje začátek změny trendu.
* Vyberte **perioda** k označení sezónnost. Monitor metriky může automaticky detekovat intervaly pro sezónnost, nebo můžete tuto možnost zadat ručně. 

Zvažte možnost ponechat si komentář v textovém poli **Komentář** ve stejnou dobu a kliknutím na **Uložit** uložte svou zpětnou vazbu.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Režim anotace zpětné vazby":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Sdělte nám svůj názor na několik souvislých bodů

Zpětnou vazbu na několik souvislých bodů můžete najednou vytvořit kliknutím na dolů a přetažením myši na body, které chcete opatřit poznámkami. Zobrazí se stejná nabídka zpětná vazba, jak je uvedeno výše. Po kliknutí na **Uložit**bude stejná zpětná vazba platit pro všechny vybrané body.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Režim anotace zpětné vazby":::

## <a name="how-to-view-my-feedback"></a>Jak zobrazit moji zpětnou vazbu

Chcete-li zjistit, zda došlo ke změně detekce anomálií bodu, umístěte ukazatel myši na bod. Popisek se zobrazí na základě **zpětné vazby: true** , pokud se zjistilo, že došlo ke změně detekce. Pokud se zobrazí **hodnota false**, byl proveden výpočet zpětné vazby v okamžiku, ale výsledek detekce anomálií se nezměnil.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Režim anotace zpětné vazby":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Kdy bych měl opatřit anomálii jako "normální"

Existuje mnoho důvodů, kdy je možné zvážit, že anomálie je falešná signalizace. Pokud platí jeden z následujících scénářů, zvažte použití následujících funkcí poradce metrik:


|Scénář  |Doporučení |
|---------|---------|
|Anomálie je způsobena změnou známého zdroje dat, například změnou systému.     | Tuto anomálii nemusíte opatřit poznámkami, pokud se tento scénář neočekává pravidelně znovu.        |
|Anomálie je způsobená svátkem.     | Pomocí [přednastavených událostí](configure-metrics.md#preset-events) můžete označit detekci anomálií v zadaných časech.       |
|Je k dispozici pravidelný vzor pro zjištěné anomálie (například na víkendech) a nesmí se jednat o anomálie.      |Použijte funkci zpětné vazby nebo přednastavené události.        |

## <a name="next-steps"></a>Další kroky
- [Diagnostikujte incident](diagnose-incident.md).
- [Konfigurace metrik a doladění konfigurace zjišťování](configure-metrics.md)
- [Konfigurace výstrah a získání oznámení pomocí háku](../how-tos/alerts.md)