---
title: 'Vlastní vyhledávání Bingu: Definování vlastních automatických návrhů návrhy | Dokumentace Microsoftu'
description: Popisuje, jak nakonfigurovat vlastní automatické návrhy vlastní návrhy
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: e7a62a79bdc2e486fb6bfca34eb4addeba2bde0e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158309"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurace prostředí pro funkci vlastních automatických návrhů
Pokud jste přihlášení k odběru vlastního vyhledávání na příslušné úrovni (najdete v článku [stránkách s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), můžete přizpůsobit návrhy hledání v prostředí pro vlastní vyhledávání. Funkci vlastních automatických návrhů vrátí seznam navrhovaných dotazů založených na řetězec částečné dotazu, který uživatel zadá. Pomocí vlastních automatických návrhů zadejte vlastní vyhledávání návrhy, které jsou relevantní pro vaše prostředí hledání. Můžete nastavit, zda vrátit pouze vlastní návrhy nebo také obsahují návrhy Bingu. Pokud zahrnete návrhy Bingu, předcházet vlastních návrhů pro návrhy Bingu. Návrhy Bingu jsou omezené na kontext vaší instance vlastního vyhledávání.

## <a name="configure-custom-autosuggest"></a>Vlastní konfigurace pro automatické návrhy
Použijte následující pokyny ke konfiguraci vlastních automatických návrhů pro vaši instanci vlastního vyhledávání.

1.  Přihlaste se k [vlastní vyhledávání](https://customsearch.ai).
2.  Klikněte na instanci vlastního vyhledávání. Vytvoření instance najdete v tématu [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).
3.  Klikněte na tlačítko **pro automatické návrhy** kartu.

## <a name="enable-bing-suggestions"></a>Povolit návrhy Bingu
Povolit návrhy Bingu, přepnout **Bing pro automatické návrhy** posuvník do polohy zapnuto. Jezdec stane modrá.

## <a name="add-suggestions"></a>Přidat návrhy
Pokud chcete přidat návrh, zadejte ho do textového pole. Stisknutím klávesy enter nebo klikněte na tlačítko **+** ikonu. Vlastní návrhy může být v libovolném jazyce a zobrazí se před návrhy Bingu.

## <a name="upload-suggestions"></a>Odeslat návrhy
Můžete nahrát seznam návrhů ze souboru. Umístěte každou návrh na samostatném řádku. Klikněte na tlačítko Nahrát ikonu a vyberte soubor.

## <a name="remove-suggestions"></a>Odeberte návrhy
Chcete-li odebrat návrh, klikněte na odebrat ikonu vedle návrh, který chcete odebrat.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Může trvat až 24 hodin vlastních automatických návrhů změny konfigurace projevily.

## <a name="next-steps"></a>Další postup

- [Získat vlastní návrhy](./get-custom-suggestions.md)
- [Hledat vlastní instance](./search-your-custom-view.md)
- [Konfigurovat a používat vlastní prostředí uživatelského rozhraní](./hosted-ui.md)