---
title: Vytvoření první instance Bing vlastní vyhledávání - kognitivní služby společnosti Microsoft
description: Pokud chcete používat vlastní vyhledávání Bing, potřebujete vytvořit vlastní hledání instance, která určuje zobrazení nebo řez webu. Instance obsahuje nastavení, která veřejné domény, podřízené weby a webové stránky, které chcete Bing k vyhledání a případné úpravy hodnocení.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 35f0bca01de1c2087f6ae30949cca9b03192b838
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342865"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Vytvoření vaší první instance Bing vlastní vyhledávání
Pokud chcete používat vlastní vyhledávání Bing, potřebujete vytvořit vlastní hledání instance, která určuje zobrazení nebo řez webu. Instance obsahuje nastavení, která veřejné domény, weby a webové stránky, které chcete Bing k vyhledání a případné úpravy hodnocení. Pokud chcete vytvořit instanci, použijte funkci vyhledávání vlastní Bing [portál](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastní vyhledávání

K vytvoření instance služby Bing vlastní vyhledávání:

1.  Získáte klíč rozhraní API vlastní vyhledávání. V tématu [vyzkoušení služeb kognitivní](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Přihlaste se k portálu pomocí účtu Microsoft (MSA). Klikněte **přihlášení** tlačítko. Pokud je vaše první přihlášení pomocí portálu postupujte podle následujících dalších kroků, v opačném případě pokračujte krokem 3.
    - Pokud nemáte účtu spravované služby, klikněte na tlačítko **vytvořit účet Microsoft**. Na portálu požádá o oprávnění k přístupu k datům. Klikněte na **Ano**.
    - Souhlas s podmínkami kognitivní služby. Zkontrolujte **souhlasím** a klikněte na tlačítko **Agree**.  
3.  Po přihlášení, klikněte na tlačítko **novou instanci** a název instance. Použijte název, který má smysl a popisuje typ obsahu, které vrátí vyhledávání. Kdykoli můžete změnit název. 
4.  Na **Active** kartě v **vyhledávání**, zadejte adresu URL jednoho nebo více webů, které chcete zahrnout do hledání.
5.  Potvrďte, že vaše instance vrátí výsledky, zadejte dotaz v podokně náhledu na pravé straně. Pokud nebyly nalezeny žádné výsledky, zadejte novou lokalitu. Bing vrátí výsledky pouze pro veřejné weby, které má indexovat.
6.  Klikněte na tlačítko **publikovat** publikovat změny konfigurace do produkčního prostředí. Po zobrazení výzvy klikněte na tlačítko **publikovat** k potvrzení.
7.  Klikněte na tlačítko **produkční** > **koncové body** a zkopírujte **ID vlastní konfigurace**. Musíte toto ID pro volání rozhraní API pro vyhledávání vlastní.

## <a name="next-steps"></a>Další postup

Pokračujte v práci s vlastní hledání instance, kterou jste právě vytvořili podle pokynů v těchto návody:

- [Konfigurovat možnosti vlastní vyhledávání](./define-your-custom-view.md)
- [Volání vlastní hledání](./search-your-custom-view.md)
- [Sdílet vlastní hledání](./share-your-custom-search.md)
- [Konfigurace prostředí hostované uživatelského rozhraní](./hosted-ui.md)
- [Použití decoration značky zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
