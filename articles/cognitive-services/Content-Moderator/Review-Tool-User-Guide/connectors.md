---
title: Připojení k jiným službám při moderování obsahu – Content Moderator
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak získat přístup k další rozhraní API pro pracovní postupy Content Moderator pomocí konektorů.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 99d8b3603278a9c6c432ca32a1d85e9abe34e1da
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265579"
---
# <a name="connect-to-other-cognitive-services"></a>Připojte se k jiné služby cognitive services

Azure Content Moderator pracovních postupů může používat další rozhraní API, kromě rozhraní API Content Moderatoru. Získáte přístup k další rozhraní API pomocí konektoru v Content Moderatoru. Tento konektor poskytuje odkaz na rozhraní API.

Content Moderator zahrnuje tyto výchozí konektory:

* Rozhraní API pro rozpoznávání emocí
* Rozhraní API pro rozpoznávání tváře
* PhotoDNA Cloud Service

![Content Moderator dostupné konektory](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Ověřit vaše přihlašovací údaje 

Před definováním pracovního postupu, ujistěte se, že máte platné přihlašovací údaje pro konektor API, které chcete použít:

1.  Na nástroj pro recenze řídicího panelu, vyberte **nastavení** > **konektory**.

  ![Content Moderator vyberte konektory](images/connectors-2.png)

2.  Vyberte **upravit** symbol vedle konektor, který chcete ověřit přihlašovací údaje.

  ![Content Moderator, vyberte symbol úpravy](images/connectors-3.png)

3.  Zobrazí se klíč předplatného. Pokud provedete jakékoli úpravy, vyberte **Uložit** až budete hotoví.

  ![Stránka obsahu moderátor upravit konektory](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Přidat konektor

1.  Předtím, než přidáte konektor, potřebujete klíč předplatného. Na nástroj pro recenze řídicího panelu, vyberte **nastavení** > **pověření**. Vyberte a zkopírujte hodnotu, která je v **Ocp-Admin-Subscription-Key** pole.

2.  Vyberte **konektory**. Vyberte jednu z dostupných konektorů, které jsou zobrazeny v řídicím panelu Nástroje pro recenze. Vyberte **připojit**. 

  ![Stránka obsahu moderátor přidat konektor](images/connectors-5.png)

3.  V **Ocp-Admin-Subscription-Key** vložte klíč, který jste zkopírovali. Potom vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

* Další informace o použití konektorů pro [definovat vlastní pracovní postupy](workflows.md).
