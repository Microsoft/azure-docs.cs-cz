---
title: Měření reálných uživatelů s webovými stránkami – Azure Traffic Manager
description: V tomto článku se dozvíte, jak nastavit webové stránky, aby odesílaly Měření reálných uživatelů do Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 8606e89a40e9cfd2c0f55df2c65532928c0d11f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401295"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Odeslání Měření reálných uživatelů do Azure Traffic Manager pomocí webových stránek

Webové stránky můžete nakonfigurovat tak, aby odesílaly Měření reálných uživatelů k Traffic Manager získáním klíče Měření reálných uživatelů (RUM) a vložením vygenerovaného kódu na webovou stránku.

## <a name="obtain-a-real-user-measurements-key"></a>Získat Měření reálných uživatelů klíč

Měření, která provedete a odešlete Traffic Manager z klientské aplikace, je identifikována službou pomocí jedinečného řetězce, který se nazývá **klíč měření reálných uživatelů (rum)**. Klíč rumu můžete získat pomocí Azure Portal, REST API nebo pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

Získání klíče rumu pomocí Azure Portal:
1. V prohlížeči se přihlaste k webu Azure Portal. Pokud ještě účet nemáte, můžete si zaregistrovat zkušební verzi na měsíc zdarma.
2. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. V okně profil Traffic Manager klikněte v části **Nastavení**na **měření reálných uživatelů** .
4. Klikněte na **vygenerovat klíč** a vytvořte nový klíč rumu.
 
   ![Generovat klíč Měření reálných uživatelů](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Měření reálných uživatelů generování klíčů**

5. V okně se nyní zobrazí generovaný klíč rumu a fragment kódu JavaScriptu, který musí být vložen do stránky HTML.
 
    ![Kód JavaScriptu pro Měření reálných uživatelů klíč](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Obrázek 2: Měření reálných uživatelů klíč a měření JavaScriptu**
 
6. Kliknutím na tlačítko **Kopírovat** zkopírujte kód JavaScriptu. 

>[!IMPORTANT]
> Pro správné fungování použijte vygenerovaný JavaScript pro funkci Měření reálných uživatelů. Jakékoli změny v tomto skriptu nebo skripty používané Měření reálných uživatelů mohou vést k nepředvídatelnému chování.

## <a name="embed-the-code-to-an-html-web-page"></a>Vložení kódu do webové stránky HTML

Po získání klíče rumu je dalším krokem vložení tohoto zkopírovaného JavaScriptu do stránky HTML, kterou koncoví uživatelé navštíví. Úpravy HTML lze provádět mnoha způsoby a pomocí různých nástrojů a pracovních postupů. Tento příklad ukazuje, jak aktualizovat stránku HTML pro přidání tohoto skriptu. Pomocí těchto pokynů můžete přizpůsobit pracovní postup správy zdrojů HTML.

1.  Otevření stránky HTML v textovém editoru
2.  Vložte kód jazyka JavaScript, který jste zkopírovali v předchozím kroku, do části text v HTML (zkopírovaný kód je na řádku 8 & 9, viz obrázek 3).
 
    ![Vložení kódu JavaScriptu na webovou stránku pro Měření reálných uživatelů](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Obrázek 3: jednoduchý HTML s vloženým Měření reálných uživatelů JavaScriptu**

3.  Uložte soubor HTML a hostovat ho na serveru, který je připojený k Internetu. 
4. Při příštím vykreslení této stránky ve webovém prohlížeči se načte odkaz na JavaScript a skript spustí operace měření a generování sestav.


## <a name="next-steps"></a>Další kroky
- Další informace o [měření reálných uživatelů](traffic-manager-rum-overview.md)
- Informace [o tom, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) , které podporuje Traffic Manager
- Informace o tom, jak [vytvořit profil Traffic Manager](traffic-manager-create-profile.md)

