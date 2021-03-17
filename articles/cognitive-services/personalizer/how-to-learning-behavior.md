---
title: Konfigurace chování učení
description: V režimu učni máte jistotu, že služba přizpůsobené služby a její funkce pro strojové učení a poskytuje metriky, které služba odesílá informace, které je možné zjistit z – bez rizika ohrožení online provozu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579287"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Konfigurace chování při učení přizpůsobení

V [režimu učni](concept-apprentice-mode.md) získáte důvěru a jistotu ve službě přizpůsobené pomocí služby Machine Learning a získáte jistotu, že služba odesílá informace, které je možné zjistit z – bez rizika ohrožení online provozu.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Konfigurovat režim učni

1. Přihlaste se k [Azure Portal](https://portal.azure.com)pro prostředek přizpůsobeného pro vlastníce.

1. Na stránce **Konfigurace** na kartě chování při **učení** vyberte **akci vrátit směrný plán, Naučte** se, že vyberte možnost **Uložit**.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky konfigurace chování učení v režimu učni v Azure Portal](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Změny existující aplikace

Vaše existující aplikace by neměla měnit způsob, jakým aktuálně vybírá akce, které se mají zobrazit, nebo způsob, jakým aplikace určuje hodnotu, **odměnu** této akce. Jediná změna v aplikaci může být pořadí akcí odeslaných do rozhraní API řazení pro přizpůsobení. Akce, kterou vaše aplikace aktuálně zobrazuje, je odeslána jako _první akce_ v seznamu akcí. [Rozhraní API řazení](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) používá tuto první akci ke školení modelu přizpůsobeného pro přizpůsobení.

### <a name="configure-your-application-to-call-the-rank-api"></a>Konfigurace aplikace pro volání rozhraní API pro řazení

Aby bylo možné přidat přizpůsobené aplikaci do aplikace, je nutné volat rozhraní API pro řazení a používání.

1. Přidejte volání [rozhraní API řazení](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) za místo v existující aplikační logice, kde určíte seznam akcí a jejich funkce. První akce v seznamu akce musí být akce vybraná stávající logikou.

1. Nakonfigurujte kód tak, aby zobrazoval akci spojenou s **ID akce odměnu** rozhraní API řazení.

### <a name="configure-your-application-to-call-reward-api"></a>Konfigurace aplikace pro volání API pro odměnu

1. Použijte stávající obchodní logiku pro výpočet **odměna** zobrazené akce. Hodnota musí být v rozsahu od 0 do 1. Tuto odměnu můžete přizpůsobovat pomocí [API pro odměnu](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). Hodnota odměňování se neočekává hned a v závislosti na obchodní logice se dá zpozdit za časové období.

1. Pokud nevrátíte svou odměnu v rámci nakonfigurované **doby čekání na odměnu** , použije se místo toho výchozí měna.

## <a name="evaluate-apprentice-mode"></a>Vyhodnotit režim učni

V Azure Portal na stránce **vyhodnocení** pro váš prostředek pro přizpůsobení zkontrolujte **aktuální výkon chování učení**.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s přehledem chování učení v režimu učni v Azure Portal](media/settings/evaluate-apprentice-mode.png)

V režimu učni jsou k dispozici následující **metriky vyhodnocení** :
* **Směrný plán – Průměrná odměna** : Průměrná doba využívání výchozích hodnot (standardní hodnoty) aplikace
* **Přizpůsobování – Průměrná doba odměna** : průměr z celkového počtu nedokončených akcí by byl potenciálně dosažen.
* **Poměr stran k úspěšnosti v posledních 1000 událostech** : poměr hodnot na účaří a přizpůsobování – normalizované na posledních 1000 událostech.

## <a name="switch-behavior-to-online-mode"></a>Přepnout chování do online režimu

Když určíte přidaný modul, bude vyškolený s průměrem 75-85% kumulovaných průměrů, model je připravený k přepnutí do online režimu.

V Azure Portal prostředku pro přizpůsobení na stránce **Konfigurace** na kartě **chování při učení** vyberte **vrátit nejlepší akci** a pak vyberte **Uložit**.

V volání rozhraní API pro pořadí a odměnu není nutné provádět žádné změny.

## <a name="next-steps"></a>Další kroky

* [Správa nastavení modelu a učení](how-to-manage-model.md)
