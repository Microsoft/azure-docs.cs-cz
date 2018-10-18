---
title: 'Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu'
titlesuffix: Azure Cognitive Services
description: Pokud chcete použít Vlastní vyhledávání Bingu, musíte vytvořit instanci vlastního vyhledávání, která definuje vaše zobrazení nebo řez webu. Instance obsahuje nastavení, která určují požadované veřejné domény, podřízené weby a webové stránky, které má Bing prohledávat, a případné úpravy hodnocení.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 866d32aa4de45076fcbc4e413d8c2e67d5346878
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816218"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu
Pokud chcete použít vlastní vyhledávání Bingu, musíte vytvořit instanci vlastního vyhledávání, která definuje vaše zobrazení nebo řez webu. Instance obsahuje nastavení, která určují požadované veřejné domény, weby a webové stránky, které má Bing prohledávat, a případné úpravy hodnocení. K vytvoření instance použijte [portál](https://customsearch.ai) Vlastní vyhledávání Bingu. 

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání

Vytvoření instance vlastního vyhledávání Bingu:

1.  Získejte klíč rozhraní API pro vlastní vyhledávání. Přečtěte si téma [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Klikněte na tlačítko **Přihlásit se** a přihlaste se k portálu pomocí účtu Microsoft. 
    - Pokud účet Microsoft nemáte, klikněte na **Vytvořit účet Microsoft**. Na portálu se zobrazí výzva k udělení oprávnění pro přístup k vašim datům. Klikněte na **Ano**.
    - Přijměte podmínky služeb Cognitive Services. Zaškrtněte políčko **Souhlasím** a klikněte na **Souhlasím**.  
3.  Po přihlášení klikněte na **Nová instance** a zadejte název instance. Použijte smysluplný název popisující typ obsahu, který vyhledávání vrací. Název můžete kdykoli změnit. 
4.  Na kartě **Aktivní** v části **Vyhledávací prostředí** zadejte adresy URL webů, které chcete do vyhledávání zahrnout.
5.  Pokud chcete ověřit, že vaše instance vrací výsledky, zadejte dotaz do podokna náhledu na pravé straně. Pokud se nezobrazí žádné výsledky, zadejte nový web. Bing vrátí výsledky pouze pro veřejné weby, které indexoval.
6.  Kliknutím na **Publikovat** publikujte změny konfigurace do produkčního prostředí. Po zobrazení výzvy publikování potvrďte kliknutím na **Publikovat**.
7.  Klikněte na **Produkce** > **Koncové body** a zkopírujte **ID vlastní konfigurace**. Toto ID budete potřebovat k volání rozhraní API pro vlastní vyhledávání.

## <a name="next-steps"></a>Další kroky

Pokračujte v práci s instancí vlastního vyhledávání, kterou jste právě vytvořili, a postupujte podle pokynů v těchto praktických průvodcích:

- [Konfigurace vlastního vyhledávacího prostředí](./define-your-custom-view.md)
- [Volání vlastního vyhledávání](./search-your-custom-view.md)
- [Sdílení vlastního vyhledávání](./share-your-custom-search.md)
- [Konfigurace prostředí pro hostované uživatelské rozhraní](./hosted-ui.md)
- [Zvýraznění textu pomocí dekoračních značek](./hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)
