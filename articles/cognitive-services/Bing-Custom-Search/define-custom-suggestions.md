---
title: 'Hledání vlastní Bing: Definování vlastní pro automatické návrhy návrhy | Microsoft Docs'
description: Popisuje, jak nakonfigurovat pro automatické návrhy vlastní vlastní návrhy
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342853"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurace prostředí vlastní automatických návrhů
Pokud odebíráte vlastní vyhledávání na příslušné úrovni (najdete v článku [ceny stránky](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), můžete přizpůsobit návrhy vyhledávání v prostředí vlastní vyhledávání. Vlastní automatických návrhů vrátí seznam hodnot navrhované dotazy založené na dotazu částečný řetězec, který obsahuje uživatele. S vlastní pro automatické návrhy poskytnete návrhy vlastní vyhledávání, které jsou relevantní pro možnosti vyhledávání. Můžete určit, zda vrátit pouze vlastní návrhy nebo návrhy Bing zahrnout také. Pokud zahrnete Bing návrhy, zobrazí se vlastní návrhy před návrhy Bing. Návrhy Bing jsou omezeny na kontext vaší instance vlastní vyhledávání.

## <a name="configure-custom-autosuggest"></a>Vlastní konfigurace pro automatické návrhy
Použijte následující pokyny ke konfiguraci vlastní pro automatické návrhy pro vaše vlastní hledání instance.

1.  Přihlaste se k [vlastní vyhledávání](https://customsearch.ai).
2.  Klikněte na tlačítko Vlastní hledání instance. K vytvoření instance, najdete v části [vytvoření vaší první instance Bing vlastní vyhledávání](quick-start.md).
3.  Klikněte **automatických návrhů** kartě.

## <a name="enable-bing-suggestions"></a>Povolit návrhy v Bingu
Povolit návrhy Bing, přepněte **automatické Bing návrhy** posuvník do polohy zapnuto. Posuvník stane blue.

## <a name="add-suggestions"></a>Přidat návrhy
Pokud chcete přidat zlepšení, zadejte ho do textového pole. Stisknutím klávesy enter nebo klikněte na tlačítko **+** ikonu. Vlastní návrhy může být v libovolném jazyce a zobrazí se před návrhy Bing.

## <a name="upload-suggestions"></a>Nahrát návrhy
Můžete nahrát seznamu návrhů ze souboru. Každý návrh umístíte na samostatném řádku. Klikněte na ikonu nahrávání a vyberte soubor.

## <a name="remove-suggestions"></a>Odeberte návrhy
Odebrat zlepšení, klikněte na ikonu odebrat vedle návrh, který chcete odebrat.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >To může trvat až 24 hodin pro automatické návrhy vlastní změny konfigurace se projeví.

## <a name="next-steps"></a>Další postup

- [Umožňuje získat návrhy vlastní](./get-custom-suggestions.md)
- [Hledání vlastní instanci](./search-your-custom-view.md)
- [Konfigurovat a používat vlastní hostované uživatelského rozhraní](./hosted-ui.md)