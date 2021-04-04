---
title: Kurz – Nastavení pravidel ukládání do mezipaměti v Azure CDN | Microsoft Docs
description: V tomto kurzu nastavíte globální pravidlo ukládání do mezipaměti v Azure CDN a vlastní pravidlo ukládání do mezipaměti.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: ae217352b83617600fd983a747d578f8f28e7ddd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779233"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Kurz: Nastavení pravidel ukládání do mezipaměti v Azure CDN

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici pouze pro **Azure CDN Standard od Verizon** a **Azure CDN Standard od profilů Akamai** . Pro **Azure CDN z profilů Microsoftu** musíte použít modul [Standard rules](cdn-standard-rules-engine-reference.md) pro **Azure CDN Premium ze profilů Verizon** , musíte použít [modul pravidel Verizon Premium](./cdn-verizon-premium-rules-engine.md) na portálu pro **správu** pro podobné funkce.
 

Tento kurz popisuje, jak můžete použít pravidla ukládání do mezipaměti v Azure Content Delivery Network (CDN) k nastavení nebo úpravě výchozího chování při vypršení platnosti mezipaměti, a to jak u globálních, tak i u vlastních podmínek, jako je například cesta adresy URL nebo přípona souboru. Azure CDN nabízí dva typy pravidel ukládání do mezipaměti:
- Globální pravidla ukládání do mezipaměti: Pro každý koncový bod ve vašem profilu můžete nastavit jedno globální pravidlo pro ukládání do mezipaměti, které se bude vztahovat na všechny požadavky na koncový bod. Pokud jsou HTTP hlavičky direktiv pro mezipaměť nastavené, globální pravidlo ukládání do mezipaměti je přepíše.

- Vlastní pravidla ukládání do mezipaměti: pro každý koncový bod ve svém profilu můžete nastavit jedno nebo více vlastních pravidel ukládání do mezipaměti. Vlastní pravidla ukládání do mezipaměti se shodují s cestami a příponami souborů, zpracovávají se v daném pořadí, a pokud je nastavené globální pravidlo ukládání do mezipaměti, přepíší ho. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Otevřít stránku Pravidla ukládání do mezipaměti.
> - Vytvořit globální pravidlo ukládání do mezipaměti.
> - Vytvořit vlastní pravidlo ukládání do mezipaměti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto kurzu musíte nejprve vytvořit profil CDN a alespoň jeden koncový bod CDN. Další informace najdete v tématu [Rychlý start: Vytvoření profilu a koncového bodu Azure CDN](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Otevření stránky Pravidla ukládání do mezipaměti v Azure CDN

1. Na webu [Azure Portal](https://portal.azure.com) vyberte profil CDN a potom vyberte koncový bod.

2. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

   ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti**.

   ![Stránka CDN Pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Nastavení globálních pravidel ukládání do mezipaměti

Následujícím způsobem vytvořte globální pravidlo ukládání do mezipaměti:

1. V části **Globální pravidla ukládání do mezipaměti** nastavte **Chování při ukládání řetězců dotazů do mezipaměti** na **Ignorovat řetězce dotazů**.

2. **Chování ukládání do mezipaměti** nastavte na **Nastavit, pokud chybí**.
       
3. U **Doba vypršení platnosti mezipaměti** zadejte do pole **Dny** číslo 10.

    Globální pravidlo ukládání do mezipaměti se vztahuje na všechny požadavky na koncový bod. Pokud existuje původní umístění hlaviček direktiv mezipaměti (`Cache-Control` nebo `Expires`), toto pravidlo ho bude respektovat. Pokud není uvedené, nastaví pravidlo mezipaměť na 10 dní. 

    ![Globální pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Nastavení vlastních pravidel ukládání do mezipaměti

Následujícím způsobem vytvořte vlastní pravidlo ukládání do mezipaměti:

1. V části **Vlastní pravidla ukládání do mezipaměti** nastavte možnost **Podmínka shody** na **Cesta** a **Hodnoty shody** na `/images/*.jpg`.
    
2. **Chování ukládání do mezipaměti** nastavte na **Přepsat** a do pole **Dny** zadejte číslo 30.
       
    Toto vlastní pravidlo ukládání do mezipaměti nastaví pro všechny soubory obrázků `.jpg` ve složce `/images` vašeho koncového bodu 30denní dobu uložení v mezipaměti. Všechny HTTP hlavičky `Cache-Control` nebo `Expires` odeslané serverem původu se přepíší.

    ![Vlastní pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili pravidla ukládání do mezipaměti. Pokud už tato pravidla ukládání do mezipaměti používat nechcete, můžete je následujícím způsobem odebrat:
 
1. Vyberte profil CDN a potom vyberte koncový bod s pravidly ukládání do mezipaměti, které chcete odebrat.

2. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

3. V části **Globální pravidla ukládání do mezipaměti** nastavte **Chování ukládání do mezipaměti** na **Nenastaveno**.
 
4. V části **Vlastní pravidla ukládání do mezipaměti** vyberte zaškrtávací políčko vedle pravidla, které chcete odstranit.

5. Vyberte **Odstranit**.

6. V horní části stránky vyberte **Uložit**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Otevřít stránku Pravidla ukládání do mezipaměti.
> - Vytvořit globální pravidlo ukládání do mezipaměti.
> - Vytvořit vlastní pravidlo ukládání do mezipaměti.

Pokud se chcete naučit, jak nakonfigurovat další nastavení pravidel ukládání do mezipaměti, přejděte na další článek.

> [!div class="nextstepaction"]
> [Řízení chování při ukládání do mezipaměti v Azure CDN s využitím pravidel ukládání do mezipaměti](cdn-caching-rules.md)