---
title: Měření Real User Measurements do Azure Traffic Manageru s webovými stránkami | Dokumentace Microsoftu
description: Nastavení webové stránky k odeslání měření Real User Measurements do Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 19a641ba86333047b90c36cef242d524ccad5fe1
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138161"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak odeslat měření Real User Measurements do Azure Traffic Manager pomocí webových stránek

Můžete nakonfigurovat webové stránky k odeslání měření Real User Measurements do Traffic Manageru získáním klíč měření reálných skutečných uživatelů (REÁLNÝCH) a vkládání generovaný kód pro webové stránky.

## <a name="obtain-a-real-user-measurements-key"></a>Získat klíč měření Real User Measurements

Měření trvat a odeslat do Traffic Manageru z klientské aplikace jsou označeny pomocí jedinečného řetězce, volá se, **klíč měření reálných skutečných uživatelů (REÁLNÝCH)**. Můžete získat spuštění klíče pomocí webu Azure portal, rozhraní REST API, nebo pomocí Powershellu nebo rozhraní příkazového řádku Azure.

Získání klíče REÁLNÝCH pomocí webu Azure portal:
1. Z prohlížeče Přihlaste se k webu Azure portal. Pokud ještě nemáte účet, můžete se zaregistrovat k bezplatné zkušební verzi na jeden měsíc.
2. Na panelu hledání na portálu vyhledejte název profilu Traffic Manageru, který chcete upravit a poté klikněte na profil Traffic Manageru ve výsledcích, který je zobrazeno.
3. V okně profilu Traffic Manageru, klikněte na tlačítko **měření Real User Measurements** pod **nastavení**.
4. Klikněte na tlačítko **vygenerovat klíč** a vytvořte nový klíč REÁLNÝCH.
 
  ![Vygenerovat klíč měření Real User Measurements](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Vygenerovat klíč měření reálných uživatelů**

5. V okně zobrazí teď generování klíče REÁLNÝCH a fragment kódu jazyka JavaScript, které je potřeba vložit do HTML stránky.
 
    ![Kód jazyka JavaScript pro měření Real User Measurements klíč](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Obrázek 2: Klíč měření reálných uživatelů a JavaScript měření**
 
6.  Klikněte na tlačítko **kopírování** tlačítka pro kopírování kódu jazyka JavaScript. 

>[!IMPORTANT]
> Pomocí generovaného jazyka JavaScript pro funkci měření Real User Measurements fungovat správně. Všechny změny tohoto skriptu nebo skripty používané měření Real User Measurements může způsobit nepředvídatelné chování.

## <a name="embed-the-code-to-an-html-web-page"></a>Vložení kódu pro webovou stránku HTML

Dalším krokem po spuštění klíč jste obdrželi, je tento zkopírovaný jazyka JavaScript pro vložení do stránku HTML, které vaši koncoví uživatelé navštěvovat. Úpravy HTML, lze provést mnoha způsoby a pomocí různých nástrojů a pracovních postupů. Tento příklad ukazuje, jak aktualizovat stránku HTML pro přidání tohoto skriptu. Tyto pokyny můžete použít k přizpůsobení do vašeho pracovního postupu správy zdroje HTML.

1.  V textovém editoru otevřete stránku HTML
2.  Vložte kód jazyka JavaScript v předchozím kroku jste měli zkopírovali do těla HTML (je tento zkopírovaný kód na řádku 8 a 9, viz obrázek 3).
 
    ![Vložení kódu jazyka Javascript do webové stránky pro měření Real User Measurements](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Obrázek 3: Jednoduchého kódu HTML pomocí vložený skutečné JavaScript měření reálných uživatelů**

3.  Uložte soubor ve formátu HTML a hostitele na webového serveru připojený k Internetu. 
4. Při příštím vykreslování této stránky ve webovém prohlížeči JavaScript odkazované se stáhne a skript se spustí měření a vytváření sestav operations.


## <a name="next-steps"></a>Další postup
- Další informace o [měření Real User Measurements](traffic-manager-rum-overview.md)
- Přečtěte si [jak funguje Traffic Manager](traffic-manager-overview.md)
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-create-profile.md)

