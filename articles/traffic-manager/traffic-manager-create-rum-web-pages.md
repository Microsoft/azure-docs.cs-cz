---
title: Skutečné měření uživatele do Azure Traffic Manageru s webovými stránkami | Microsoft Docs
description: Nastavení webové stránky k odeslání reálného měření uživatele do Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 71661d8640dbe14082292cb9637964adac9fa803
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30180618"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Postup odesílání reálného měření uživatele k pomocí webových stránek programem Azure Traffic Manager

Můžete nakonfigurovat webových stránek k odeslání do Traffic Manageru reálného měření uživatel získat klíč měření skutečné uživatele (RUM) a vložení generovaný kód pro webové stránky.

## <a name="obtain-a-real-user-measurements-key"></a>Získat klíč reálného měření uživatele

Měření trvat a odeslat do Traffic Manageru z klientské aplikace jsou určeny pomocí jedinečného řetězce, volá se **klíč měření skutečné uživatele (RUM)**. Můžete získat RUMU klíče pomocí portálu Azure, rozhraní REST API, nebo pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

Získat klíč RUM pomocí portálu Azure:
1. V prohlížeči Přihlaste se k portálu Azure. Pokud účet nemáte, můžete zaregistrovat k bezplatné zkušební verzi jeden měsíc.
2. V panelu vyhledávání na portálu, vyhledejte název profilu Traffic Manager, který chcete upravit a pak klikněte na profil služby Traffic Manager ve výsledcích, zobrazené.
3. V okně profil Traffic Manageru klikněte na tlačítko **reálného měření uživatele** pod **nastavení**.
4. Klikněte na tlačítko **vygenerovat klíč** se vytvořit nový klíč RUM.
 
  ![Vygenerování klíče reálného měření uživatele](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Reálný uživatel měření generování klíče**

5. V okně teď zobrazuje generování klíče RUM a fragment kódu jazyka JavaScript, který potřebuje má být vložen do stránku HTML.
 
    ![Kód jazyka JavaScript pro klíč reálného měření uživatele](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Obrázek 2: Klíč měření reálný uživatel a měření JavaScript**
 
6.  Klikněte **kopie** tlačítko zkopírujete kód jazyka JavaScript. 

>[!IMPORTANT]
> Použijte vygenerovaný JavaScript pro funkci měření skutečné uživatele fungovat správně. Všechny změny tohoto skriptu nebo skripty používané reálného měření uživatel může způsobit nepředvídatelné chování.

## <a name="embed-the-code-to-an-html-web-page"></a>Vložení kódu pro webovou stránku HTML

Po získání RUMU klíč, dalším krokem je pro vložení této zkopírovaný JavaScript na stránku HTML, které vaši koncoví uživatelé navštěvovat. Úpravy jazyka HTML, lze provést v mnoha směrech a pomocí různých nástrojů a pracovních postupů. Tento příklad ukazuje, jak aktualizovat stránku HTML, který chcete přidat tento skript. V tomto návodu můžete použít k přizpůsobení do pracovního postupu správy zdroje HTML.

1.  V textovém editoru otevřete stránku HTML
2.  Vložte kód JavaScript měl zkopírovali v předchozím kroku do části textu HTML (zkopírovaný kódu je na řádku 8 & 9, viz obrázek 3).
 
    ![Vložení kódu jazyka Javascript do webové stránky pro reálného měření uživatele](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Obrázek 3: Jednoduchý HTML s vložený JavaScript reálného měření uživatele**

3.  Uložte soubor HTML a hostitele ho na webový server připojení k Internetu. 
4. Další čas, který je vykreslen tuto stránku ve webovém prohlížeči, je stažena JavaScript odkazuje a skript spustí měření a vytváření sestav operations.


## <a name="next-steps"></a>Další postup
- Další informace o [reálného měření uživatele](traffic-manager-rum-overview.md)
- Další informace [fungování Traffic Manager](traffic-manager-overview.md)
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil správce provozu](traffic-manager-create-profile.md)

