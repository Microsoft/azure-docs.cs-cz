---
title: Použít konektory v obsahu moderátora Azure pro přístup k jiná rozhraní API | Microsoft Docs
description: Zjistěte, jak získat přístup k jiná rozhraní API pro vaše pracovní postupy obsahu moderátora pomocí konektorů.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: d8114457e7079ca8772cab830bd011dcddf372f5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342438"
---
# <a name="connectors"></a>Konektory

Azure obsahu moderátora pracovních postupů může používat jiná rozhraní API, kromě obsahu moderátora rozhraní API. Máte přístup k jiná rozhraní API pomocí konektoru v obsahu moderátora. Konektor poskytuje odkaz pro jiná rozhraní API.

Obsahu moderátora zahrnuje tyto konektory výchozí:

* Rozhraní API pro rozpoznávání emocí
* Rozhraní API pro rozpoznávání tváře
* PhotoDNA cloudové služby

![Obsahu moderátora dostupných konektorů](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Ověřit vaše přihlašovací údaje 

Předtím, než můžete definovat pracovní postup, ujistěte se, zda máte platné přihlašovací údaje pro konektor rozhraní API, které chcete použít:

1.  Na kontrolní nástroj řídicí panel, vyberte **nastavení** > **konektory**.

  ![Obsahu moderátora vyberte konektory](images/connectors-2.png)

2.  Vyberte **upravit** symbol vedle konektor, který chcete ověřit přihlašovací údaje pro.

  ![Obsahu moderátora vyberte Upravit symbol](images/connectors-3.png)

3.  Zobrazí se klíč předplatného. Pokud provedete veškeré úpravy, vyberte **Uložit** po dokončení.

  ![Stránky obsahu moderátora upravit konektory](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Přidejte konektor

1.  Než přidáte konektoru, je nutné klíč předplatného. Na kontrolní nástroj řídicí panel, vyberte **nastavení** > **pověření**. Vyberte a zkopírujte hodnotu, která je v **Ocp-Admin-Subscription-Key** pole.

2.  Vyberte **konektory**. Vyberte jednu z dostupných konektorů, které jsou zobrazené na nástroj zkontrolujte řídicí panel. Pak vyberte **Connect**. 

  ![Stránky obsahu moderátora přidat konektoru](images/connectors-5.png)

3.  V **Ocp-Admin-Subscription-Key** pole, vložte klíč, který jste zkopírovali. Potom vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

* Naučte se používat konektory pro [definovat vlastní pracovní postupy](workflows.md).
