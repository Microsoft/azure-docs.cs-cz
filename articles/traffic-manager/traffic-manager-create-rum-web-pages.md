---
title: Měření skutečných uživatelů s webovými stránkami – Azure Traffic Manager
description: V tomto článku se dozvíte, jak nastavit webové stránky tak, aby odesílalo měření skutečných uživatelů do Azure Traffic Manageru.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 927d774ee30a291607a8a47fc2fd6878c1bc6fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938684"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak odeslat měření reálných uživatelů do Azure Traffic Manageru pomocí webových stránek

Webové stránky můžete nakonfigurovat tak, aby odesílalo měření skutečných uživatelů do Traffic Manageru, a to získáním klíče PRO Měření skutečných uživatelů (RUM) a vložením generovaného kódu na webovou stránku.

## <a name="obtain-a-real-user-measurements-key"></a>Získat klíč měření reálného uživatele

Měření, která provedoute a odešlete do Traffic Manageru z klientské aplikace, jsou službou identifikována pomocí jedinečného řetězce nazývaného **Klíč měření skutečných uživatelů (RUM).** Klíč RUM můžete získat pomocí portálu Azure, rozhraní REST API nebo pomocí PowerShellu nebo Azure CLI.

Získání klíče RUM pomocí portálu Azure:
1. V prohlížeči se přihlaste k webu Azure Portal. Pokud ještě účet nemáte, můžete si zaregistrovat zkušební verzi na měsíc zdarma.
2. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. V okně Profil Traffic Manageru klikněte v části **Nastavení**na **skutečná měření uživatelů** .
4. Chcete-li vytvořit nový klíč RUM, klepněte na **tlačítko Generovat klíč.**
 
   ![Klíč Generovat skutečná měření uživatelů](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Generování klíčů skutečných měření uživatelů**

5. Okno nyní zobrazuje generovaný klíč RUM a fragment kódu JavaScriptu, který je třeba vložit do stránky HTML.
 
    ![Kód Javascriptu pro klíč Měření reálných uživatelů](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Obrázek 2: Klíč pro měření skutečných uživatelů a javascript měření**
 
6. Kliknutím na tlačítko **Kopírovat** zkopírujte kód JavaScriptu. 

>[!IMPORTANT]
> Ke správnému fungování použijte vygenerovaný JavaScript pro měření skutečných uživatelů. Jakékoli změny tohoto skriptu nebo skriptů používaných měřením skutečných uživatelů mohou vést k nepředvídatelnému chování.

## <a name="embed-the-code-to-an-html-web-page"></a>Vložení kódu na webovou stránku HTML

Poté, co jste získali klíč RUM, dalším krokem je vložit tento zkopírovaný JavaScript do HTML stránky, kterou navštíví vaši koncoví uživatelé. Úpravy HTML lze provádět mnoha způsoby a pomocí různých nástrojů a pracovních postupů. Tento příklad ukazuje, jak aktualizovat stránku HTML pro přidání tohoto skriptu. Pomocí těchto pokynů jej můžete přizpůsobit pracovnímu postupu správy zdrojů HTML.

1.  Otevření stránky HTML v textovém editoru
2.  Vložte kód JavaScriptu, který jste zkopírovali v předchozím kroku, do části BODY ve formátu HTML (zkopírovaný kód je na řádku 8 & 9, viz obrázek 3).
 
    ![Vložit javascriptový kód na webovou stránku pro měření reálných uživatelů](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Obrázek 3: Jednoduché HTML s vloženým reálným uživatelským měřením JavaScriptu**

3.  Uložte soubor HTML a hostovat jej na webovém serveru připojeném k internetu. 
4. Při příštím vykreslení této stránky ve webovém prohlížeči se stáhne odkazovaný JavaScript a skript provede operace měření a vykazování.


## <a name="next-steps"></a>Další kroky
- Další informace o [měření chudiny reálných uživatelů](traffic-manager-rum-overview.md)
- [Zjistěte, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) podporovaných traffic managerem
- Přečtěte si, jak [vytvořit profil Traffic Manageru](traffic-manager-create-profile.md)

