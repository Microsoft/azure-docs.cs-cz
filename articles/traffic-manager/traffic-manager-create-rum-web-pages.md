---
title: Měření reálných uživatelů s webovými stránkami – Azure Traffic Manager
description: V tomto článku se dozvíte, jak nastavit webové stránky, aby odesílaly Měření reálných uživatelů do Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580382"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Odeslání Měření reálných uživatelů do Azure Traffic Manager pomocí webových stránek

Webové stránky můžete nakonfigurovat tak, aby odesílaly Měření reálných uživatelů k Traffic Manager získáním klíče Měření reálných uživatelů (RUM) a vložením vygenerovaného kódu na webovou stránku.

## <a name="obtain-a-real-user-measurements-key"></a>Získat Měření reálných uživatelů klíč

Měření, která provedete a odešlete Traffic Manager z klientské aplikace, bude identifikována službou pomocí jedinečného řetězce, který se nazývá **klíč měření reálných uživatelů (rum)**. Klíč rumu můžete získat pomocí Azure Portal, REST API, PowerShellu nebo rozhraní příkazového řádku Azure.

Získání klíče rumu pomocí Azure Portal:
1. V prohlížeči se přihlaste k webu Azure Portal. Pokud ještě účet nemáte, můžete si zaregistrovat zkušební verzi na měsíc zdarma.

1. Na panelu hledání na portálu vyhledejte název profilu Traffic Manager, který chcete upravit, a potom vyberte profil Traffic Manager v zobrazených výsledcích.

1. Na stránce Traffic Manager profil vyberte v části **Nastavení** možnost **měření reálných uživatelů** .

1. Vyberte vytvořit **klíč** a vytvořte nový klíč rumu.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Snímek obrazovky s postupem, jak vygenerovat klíč"::: 

   **Obrázek 1: Měření reálných uživatelů generování klíčů**

1. Stránka nyní zobrazuje vygenerovaný klíč rumu a fragment kódu JavaScriptu, který musí být vložen do stránky HTML.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Snímek obrazovky generovaného JavaScriptového kódu"::: 

    **Obrázek 2: Měření reálných uživatelů klíč a měření JavaScriptu**
 
1. Kliknutím na tlačítko **Kopírovat** zkopírujte kód JavaScriptu. 

> [!IMPORTANT]
> Pro správné fungování použijte vygenerovaný JavaScript pro funkci Měření reálných uživatelů. Jakékoli změny v tomto skriptu nebo skripty používané Měření reálných uživatelů mohou vést k nepředvídatelnému chování.

## <a name="embed-the-code-to-an-html-web-page"></a>Vložení kódu do webové stránky HTML

Po získání klíče rumu je dalším krokem vložení tohoto zkopírovaného JavaScriptu do stránky HTML, kterou koncoví uživatelé navštíví. Úpravy HTML lze provádět mnoha způsoby a pomocí různých nástrojů a pracovních postupů. Tento příklad ukazuje, jak aktualizovat stránku HTML pro přidání tohoto skriptu. Pomocí těchto pokynů můžete přizpůsobit pracovní postup správy zdrojů HTML.

1. Otevřete stránku HTML v textovém editoru.

1. Vložte kód jazyka JavaScript, který jste zkopírovali v poslední části, do části text v HTML. Zkopírovaný kód je na řádku 8 & 9, viz obrázek 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Snímek obrazovky generovaného JavaScriptu vloženého do webové stránky"::: 

    **Obrázek 3: jednoduchý HTML s vloženým Měření reálných uživatelů JavaScriptu**

1. Uložte soubor HTML a hostovat ho na serveru, který je připojený k Internetu.

1. Při příštím vygenerování této stránky ve webovém prohlížeči se načte odkazovaný JavaScript a skript spustí operace měření a generování sestav.

## <a name="next-steps"></a>Další kroky
- Další informace o [měření reálných uživatelů](traffic-manager-rum-overview.md)
- Informace [o tom, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) , které podporuje Traffic Manager
- Informace o tom, jak [vytvořit profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)
