---
title: Vytvoření první instance vlastního vyhledávání Bingu – Microsoft Cognitive Services
description: Pokud chcete použít vlastní vyhledávání Bingu, je potřeba vytvořit instanci vlastního vyhledávání, který definuje zobrazení nebo řez webu. Instance obsahuje nastavení, která určují veřejné domény, podřízené weby a webové stránky, které chcete, aby Bingu pro vyhledávání a hodnocení úpravy.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 25d622772fe47ffad001834d476e612f8c606904
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981644"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Vytvoření první instanci vlastního vyhledávání Bingu
Pokud chcete použít vlastní vyhledávání Bingu, je potřeba vytvořit instanci vlastního vyhledávání, který definuje zobrazení nebo řez webu. Instance obsahuje nastavení, která určují veřejné domény, weby a webové stránky, které chcete, aby Bingu pro vyhledávání a hodnocení úpravy. Pokud chcete vytvořit instanci, použijte vlastní vyhledávání Bingu [portál](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání

K vytvoření instance vlastního vyhledávání Bingu:

1.  Získáte klíče pro rozhraní API pro vlastní vyhledávání. Zobrazit [vyzkoušet služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Klikněte na tlačítko **přihlášení** tlačítko a přihlaste se k portálu pomocí účtu Microsoft (MSA). 
    - Pokud nemáte MSA, klikněte na tlačítko **vytvořit účet Microsoft**. Na portálu vyzve k zadání oprávnění pro přístup k datům. Klikněte na **Ano**.
    - Souhlas s podmínkami služeb Cognitive Services. Zkontrolujte **souhlasím** a klikněte na tlačítko **Agree**.  
3.  Po přihlášení klikněte na tlačítko **novou instanci** a název instance. Použijte název, který má smysl a popisuje typ obsahu, které vrátí vyhledávání. Kdykoli můžete změnit název. 
4.  Na **aktivní** kartu **vyhledávání**, zadejte adresu URL jednoho nebo více webů, které chcete zahrnout při hledání.
5.  Pokud chcete potvrdit, že vaše instance vrátí výsledky, zadejte dotaz v podokně náhledu na pravé straně. Pokud neexistují žádné výsledky, zadejte nový web. Bing vrátí výsledky pouze pro veřejné weby, které se má indexovat.
6.  Klikněte na tlačítko **publikovat** publikovat změny konfigurace do produkčního prostředí. Po zobrazení výzvy klikněte na tlačítko **publikovat** potvrďte.
7.  Klikněte na tlačítko **produkční** > **koncové body** a zkopírujte **ID vlastní konfigurace**. Budete potřebovat toto ID pro volání rozhraní API pro vlastní vyhledávání.

## <a name="next-steps"></a>Další postup

Pokračujte v práci s instancí vlastního vyhledávání, které jste právě vytvořili podle pokynů v těchto návody:

- [Konfigurace prostředí pro vlastní vyhledávání](./define-your-custom-view.md)
- [Volání vlastního vyhledávání](./search-your-custom-view.md)
- [Sdílení vašeho vlastního hledání](./share-your-custom-search.md)
- [Konfigurace prostředí uživatelského rozhraní](./hosted-ui.md)
- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
