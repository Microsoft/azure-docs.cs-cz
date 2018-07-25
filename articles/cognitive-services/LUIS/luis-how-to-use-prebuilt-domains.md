---
title: Použití předem připravených domén v aplikacích LUIS v Azure | Dokumentace Microsoftu
description: Další informace o použití předem připravených domén v aplikacích Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: diberry
ms.openlocfilehash: df57f9adf5bf7f5f652a77ddeafe950463c6a9fc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224173"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Použití předem připravených domén v aplikacích LUIS  

Language Understanding (LUIS) poskytuje *předem připravených domén*, které jsou předem připravených sad [záměry](luis-how-to-add-intents.md) a [entity](luis-concept-entity-types.md) , které pracují společně pro domény nebo běžné kategorie klientské aplikace. Předem připravených domén předem školení a jsou připravené k přidání do aplikace LUIS. Záměry a entity v doméně předem připravených jsou plně přizpůsobitelné po jejich přidání do vaší aplikace – můžete tréninku je s projevy z vašeho systému, fungují pro vaše uživatele. Můžete použít celou doménu předem připravených jako výchozí bod pro vlastní nastavení nebo stačí si půjčte několik záměry a entity z předem připravených domény. 

Přejděte **předem připravených domén** kartu pro zobrazení dalších předem připravených domén můžete použít ve vaší aplikaci. Kliknutím na dlaždici pro doménu se přidá do vaší aplikace, nebo klikněte na **Další** na její dlaždici na další informace o jeho záměry a entity.

> [!TIP]
> Najdete úplný seznam předem připravených domén v [předem připravených domén odkaz](./luis-reference-prebuilt-domains.md).

![Přidání předem připravených domény](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Přidání předem připravené domény
V **předem připravených domén** kartu, najít RestaurantReservation doménu a klikněte na tlačítko **přidáním domény**. Po předem připravená doména přidala do vaší aplikace LUIS, otevřete **záměry** a klikněte na RestaurantReservation.Reserve záměr. Uvidíte, že mnoho projevy příkladu již byly k dispozici a označené jako s entitami.

![RestaurantReservation.Reserve záměr](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Návrh aplikace LUIS z předem připravených domén
Při použití předem připravených domén ve vaší aplikaci LUIS, můžete přizpůsobit celou doménu předem připravených nebo přímo začít s několika jeho záměry a entity.

## <a name="customizing-an-entire-prebuilt-domain"></a>Přizpůsobení celou předem připravených doménu
Předem připravených domén mají být obecné. Obsahují mnoho záměry a entity, které můžete vybírat pro přizpůsobení aplikace podle vašich potřeb. Když spustíte z přizpůsobení celou předem připravených doménu, odstraňte záměry a entity, které vaše aplikace nemusí používat. Můžete také přidat některé záměry a entity na sadu předem připravených domény již poskytuje. Například, pokud používáte **události** předem připravených domény aplikace sportovní událost, můžete přidat entity pro týmy, sportu. Při spuštění [poskytuje projevy](luis-how-to-add-example-utterances.md) LUIS, zahrňte podmínky, které jsou specifické pro vaši aplikaci. Služba LUIS se naučí rozeznat je a přizpůsobuje jim předem připravených domény záměry a entity, které vaše aplikace potřebuje. 

> [!TIP]
> Záměry a entity v doméně předem připravených fungují nejlépe společně. Je lepší zkombinovat záměry a entity ve stejné doméně, pokud je to možné.
> * Osvědčeným postupem je použití související záměry ze stejné domény. Například, pokud se přizpůsobení `MakeReservation` záměru v **míst** domény, vyberte `Cancel` záměru od **míst** domény namísto Storno záměr v **Události** nebo **nástroje** domén.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Změna chování záměr předem připravených domény
Můžete zjistit, že předem připravená doména obsahuje záměru, který je podobný záměru, který chcete mít ve vaší aplikaci LUIS, ale chcete, aby chovat odlišně. Například **míst** předem připravená doména poskytuje `MakeReservation` záměr pro provedení rezervace restaurace, ale má vaše aplikace použít k vytvoření hotelových rezervací tohoto záměru. V takovém případě můžete změnit chování tohoto záměru poskytnutím projevy k LUIS informace o vytváření hotelových rezervací a označování je pomocí `MakeReservation` záměru, takže pak LUIS může být retrained rozpoznat `MakeReservation` v žádosti o rezervovat hotelu záměru .

> [!TIP]
> Projděte si domény nástroje pro předem připravených záměrů, které můžete přizpůsobit pro použití v libovolné doméně. Například můžete přidat `Utilities.Repeat` do vaší aplikace a trénování rozpoznat libovolné akce uživatele může být vhodné opakování ve vaší aplikaci.


## <a name="next-step"></a>Další krok

Přizpůsobení předem připravených domény tak, že přidáte další příklad projevy na ni.

> [!div class="nextstepaction"]
> [Přidání projevů příklad](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Další zdroje informací:

Zobrazit [předem připravených domén odkaz](./luis-reference-prebuilt-domains.md) pro další podrobnosti o předem připravených domén.
