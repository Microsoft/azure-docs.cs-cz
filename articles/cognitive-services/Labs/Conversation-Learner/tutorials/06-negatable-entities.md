---
title: Jak používat Negatable entity pomocí modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití Negatable entity s modelem Learner konverzace.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 2c758d1dd5d4d1e7ab25faccd5280963211181d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388812"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Jak používat Negatable entity s modelem Learner konverzace

Tento kurz ukazuje vlastnost "Negatable" entit.

## <a name="video"></a>Video

[![Kurz ve verzi Preview negatable entity](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurzu robota

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Vlastnost "Negatable" entity umožňuje označovat popisky obou normální (pozitivní) a zápornou instance entity, představuje založené na kladné a záporné modely a smazat hodnotu existující Entity. Entity s jejich nastavenou vlastnost "Negatable" nazývají Negatable entity v Štíhlejší konverzace.

## <a name="steps"></a>Kroky

Začněte na domovskou stránku webového uživatelského rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **nový Model**.
2. Zadejte **NegatableEntity** pro **název**.
3. Vyberte **Vytvořit**.

### <a name="entity-creation"></a>Vytvoření entity

1. Vyberte **entity** na levém panelu, pak **novou entitu**.
2. Vyberte **vlastní školení** pro **typ Entity**.
3. Zadejte **název** pro **název Entity**.
4. Zkontrolujte **Negatable** umožňující uživatelům poskytnout hodnota entity nebo Řekněme, že se něco *není* hodnota entity a odstranění odpovídající hodnota entity.
5. Vyberte **Vytvořit**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Vytvoření první akci

1. Vyberte **akce** na levém panelu, pak **novou akci**.
2. Zadejte **nevím, vaše jméno.** pro **odezvy bodu robotů také...** .
3. Zadejte **název** pro **vyřazení opravňuje**.
4. Vyberte **Vytvořit**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Vyberte **akce** na levém panelu, pak **novou akci**.
2. Zadejte **poznám, že vaše jméno. Je $name.** pro **odezvy bodu robotů také...** .
3. Vyberte **Vytvořit**.

> [!NOTE]
> **Název** Entity se automaticky přidal jako **požadované entity** odkazem v odpovědi utterance.

Nyní máte dvě akce.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Trénování modelu

1. Vyberte **trénování dialogů** na levém panelu, pak **dialogové okno Nový trénování**.
2. Zadejte **hello** pro uživatele utterance na panelu vlevo konverzace.
3. Vyberte **skóre akce**.
4. Vyberte **nevím, vaše jméno.** ze seznamu akcí. Na percentilu je 100 % jako jediné platné akce podle omezení.
5. Zadejte **jmenuju se Frank** pro uživatele utterance na panelu vlevo konverzace.
6. Zvýrazněte **Frank** vyberte **+ název**. Existují dvě negatable entity: (+) a navíc přidává nebo přepíše hodnotu parametru. (-) minus odebere hodnotu.
7. Vyberte **skóre akce**. **Název** Entity je teď definovaný jako **Frank** v paměti modelu, takže **poznám, že vaše jméno. Je $name** akce je k dispozici.
8. Vyberte **poznám, že vaše jméno. Je $name.** ze seznamu akcí.
9. Zadejte **moje jméno není Frank.** pro uživatele utterance na panelu vlevo konverzace.
10. Zvýrazněte **Frank** vyberte **– název** vymazat hodnotu z **název** Entity.
11. Vyberte **skóre akce**.
12. Vyberte **nevím, vaše jméno.** ze seznamu akcí.
13. Zadejte **jmenuju se Susan.** pro uživatele třetí utterance na panelu vlevo konverzace.
14. Zvýrazněte **Susan** pak **+ název** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vícehodnotový entity](./07-multi-value-entities.md)
