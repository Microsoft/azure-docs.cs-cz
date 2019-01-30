---
title: Použití vlastních značek pro moderování obsahu – Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator zahrnuje výchozí značky a můžete vytvořit vlastní značky pro moderování obsahu, které jsou specifické pro vaše podnikání.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 942b30ab9949a28c42949913d90e7448c22b658f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206269"
---
# <a name="create-and-use-moderation-tags"></a>Vytvoření a použití značek pro moderování

Kromě značek dvě výchozí **isadult** (****) a **isracy** (**r**), můžete vytvořit vlastní značky více určených pro vyhledávání. Tyto vlastní značky jsou pak k dispozici pro lidské kontrolorům přiřadit Image nebo text.

## <a name="create-tags"></a>Vytvoření značek

1.  Na kartě Nastavení vyberte značky.

  ![Moderování obsahu značky](images/tags-1.png)

2.  Zadejte krátký kód dvoupísmenné pro značku.
3.  Zadejte název značky. Zachovejte krátký a popisný název. Například **isbullying**.
4.  Zadejte popis.
5.  Klikněte na Přidat.
6.  Po dokončení vytváření značky, klikněte na Uložit.

![Definování značek moderování obsahu](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Použití vlastní značky

Vlastní značky se používají během recenze prováděné lidmi. Zobrazí se ve verzi preview a kontrolor výběru kliknutím na.

![Pomocí značek pro moderování obsahu](images/tags-3-use.png)

Různých klíčových slov pro jiné kontroly můžete vypnout zaškrtnutím nebo zrušením zaškrtnutí je viditelné.
 
![Zakázání moderování obsahu značky](images/tags-4-disable.png)

I když nelze odstranit dvě výchozí značky, **isadult** a **isracy**, odstraníte všechny vlastní značky, které jste definovali. Klikněte na ikonu koše vedle značku, kterou chcete odstranit.

![Moderování obsahu značky](images/tags-5-delete.png)

## <a name="next-steps"></a>Další postup

Další informace o použití značek pro moderování obrázků, najdete v článku [kontroly, který image](Review-Moderated-Images.md).
