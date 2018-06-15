---
title: Použití předem domén v LEOŠ aplikace v Azure | Microsoft Docs
description: Další informace o použití předem domén v aplikacích jazyka Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 72ca0def8528adae5e46a02fa5bfccd14a3b6ab4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343829"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Použití předem domén v LEOŠ aplikace  

Principy jazyka (LEOŠ) poskytuje *předem domén*, které jsou předem sady [záměry](luis-how-to-add-intents.md) a [entity](luis-concept-entity-types.md) které pracují společně pro domény nebo běžné kategorie klientské aplikace. Předkompilované domény předem vycvičena a jsou připravené k přidání do aplikace LEOŠ. Záměry a entity v předem domény nejsou plně přizpůsobitelná, jakmile jste přidali do vaší aplikace – můžete učinit později je s utterances z vašeho systému, fungují pro vaše uživatele. Můžete použít celou doménu předem jako výchozí bod pro vlastní nastavení nebo právě půjčit několik tříd Intent nebo entity z předem domény. 

Přejděte **předem domén** karty zobrazíte další předem domény můžete v aplikaci. Klikněte na dlaždici pro doménu přidat do vaší aplikace, nebo klikněte na **Další** v jeho dlaždici Další informace o jeho tříd Intent a entity.

> [!TIP]
> Můžete najít úplný seznam předem domén v [předem domén odkaz](./luis-reference-prebuilt-domains.md).

![Přidání předem domény](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Přidání předem domény
V **předem domén** , najít RestaurantReservation domény a klikněte na **přidáním domény**. Po předem doména přidaná do vaší aplikace LEOŠ, otevřete **záměry** a klikněte na RestaurantReservation.Reserve záměr. Můžete zobrazit, že mnoho utterances příklad mít již poskytuje a označený verzí entity.

![Záměr RestaurantReservation.Reserve](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Návrh aplikace LEOŠ z předem domén
Při použití předem domén ve vaší aplikaci LEOŠ, můžete přizpůsobit celou předem doménu nebo jenom spustit s několika jeho tříd Intent a entity.

## <a name="customizing-an-entire-prebuilt-domain"></a>Přizpůsobení celou předem doménu
Předkompilované domény jsou navržené jako obecné. Obsahují mnoho tříd Intent a entitami, které je možné přizpůsobit aplikace k vašim potřebám. Pokud spustíte z přizpůsobení celou předem doménu, odstraňte záměry a entitami, které vaše aplikace nemusí využívat. Některé záměry nebo entity můžete také přidat do sady, předem domény již poskytuje. Například, pokud používáte **události** předem domény pro aplikaci událostí sportu, můžete přidat entity pro týmů. Při prvním spuštění [poskytuje utterances](luis-how-to-add-example-utterances.md) LEOŠ, zahrňte podmínky, které jsou specifické pro vaši aplikaci. LEOŠ zjišťuje rozpoznány a přizpůsobuje jim předem domény tříd Intent a entity potřebám vaší aplikace. 

> [!TIP]
> Záměry a entity v předem domény fungují společně. Je lepší kombinovat tříd Intent a entity ve stejné doméně, pokud je to možné.
> * Osvědčeným postupem je použití souvisejících tříd Intent ze stejné domény. Například, pokud jsou přizpůsobení `MakeReservation` záměrné v **místech** domény, vyberte `Cancel` záměrné z **místech** domény místo Storno záměr v **Události** nebo **nástroje** domén.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Změna chování záměr předem domény
Je možné, že předem domény obsahuje záměrem podobný záměrem, které chcete mít ve vaší aplikaci LEOŠ ale se bude chovat jinak. Například **místech** poskytuje předem domény `MakeReservation` záměr pro provedení rezervace restaurace, ale má vaše aplikace můžete používat tento záměr k vytvoření rezervace hotelů. V takovém případě můžete změnit chování tohoto záměru tím, že poskytuje utterances k LEOŠ o provádění rezervací hotelů a označování je pomocí `MakeReservation` záměrné, takže pak LEOŠ může být retrained rozpoznat `MakeReservation` záměrné v požadavku na sešit hotelů .

> [!TIP]
> Podívejte se na doménu nástrojů pro předkompilované záměry, které můžete přizpůsobit pro použití v libovolné doméně. Například můžete přidat `Utilities.Repeat` pro vaše aplikace a train rozpoznat libovolný uživatel akce chtít opakování ve vaší aplikaci.


## <a name="next-step"></a>Další krok

Přizpůsobte přidáním další příklad utterances předem domény.

> [!div class="nextstepaction"]
> [Přidat příklad utterances](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Další zdroje informací:

Najdete v článku [předem domén odkaz](./luis-reference-prebuilt-domains.md) další podrobnosti o předem domén.
