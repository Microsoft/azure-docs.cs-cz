---
title: 'Kurz: Konfigurace směrování přenosu s váženým cyklem v kruhovém dotazování pomocí Azure Traffic Manager'
description: Tento kurz vysvětluje, jak vyrovnávat zatížení provozu pomocí metody kruhového dotazování v Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207812"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Kurz: Konfigurace metody váženého směrování provozu v Traffic Manager

Běžným způsobem směrování provozu je poskytnutí sady stejných koncových bodů, mezi které patří cloudové služby a weby a posílání provozu do obou. Následující kroky popisují, jak nakonfigurovat tento typ metody směrování provozu.

> [!NOTE]
> Webová aplikace Azure už poskytuje funkce Vyrovnávání zatížení kruhového dotazování pro weby v oblasti Azure (která může zahrnovat několik datových Center). Traffic Manager umožňuje distribuci provozu mezi weby v různých datových centrech.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořte profil Traffic Manager s váženým směrováním.
> - Použijte profil Traffic Manager.
> - Odstranit profil Traffic Manager.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>Konfigurace metody váženého směrování provozu

1. V prohlížeči se přihlaste k webu [Azure Portal](https://portal.azure.com).

1. Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části, a v zobrazených výsledcích vyberte profil Traffic Manageru.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Vyhledat profil Traffic Manager&quot;:::

1. Vyberte **Konfigurace** a vyberte nebo zadejte následující nastavení:

    | Nastavení         | Hodnota                                              |
    | ---             | ---                                                |
    | Metoda směrování            | Vyberte **Vážená**. |    
    | Hodnota TTL (Time to Live) pro DNS | Tato hodnota určuje, jak často bude názvový server místní mezipaměti klienta dotazován na Traffic Manager systém pro aktualizované položky DNS. Jakékoli změny, ke kterým dojde u Traffic Manager, jako je například změna metody směrování provozu nebo změny v dostupnosti přidaných koncových bodů, budou tuto dobu trvat v globálním systému serverů DNS. |
    | Protokol    | Vyberte protokol pro monitorování koncového bodu. *Možnosti: HTTP, HTTPS a TCP* |
    | Port | Zadejte číslo portu. |
    | Cesta | Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko &quot;/" je platná položka relativní cesty a předpokládá, že se soubor nachází v kořenovém adresáři (výchozí). |
    | Vlastní nastavení hlaviček | Nakonfigurujte vlastní hlavičky ve formátu Host:contoso. com, newheader: NewValue. Maximální podporovaný pár je 8. Platí pro protokol HTTP a HTTPS. Platí pro všechny koncové body v profilu. |
    | Očekávané rozsahy stavových kódů (výchozí: 200) | Nakonfigurujte rozsahy stavových kódů ve formátu 200-299301-301. Maximální podporovaný rozsah je 8. Platí pro protokol HTTP a HTTPS. Platí pro všechny koncové body v profilu. |
    | Interval testování | Nakonfigurujte časový interval mezi sondami stavu koncového bodu. Můžete zvolit 10 nebo 30 sekund. |
    | Tolerovat počet selhání | Nakonfigurujte počet nedovolených selhání sondy stavu, než se aktivuje selhání koncového bodu. Můžete zadat číslo mezi 0 a 9. | 
    | Časový limit testu | Nakonfigurujte potřebný čas před vypršením časového limitu testu stavu koncového bodu. Tato hodnota musí být aspoň 5 a menší než hodnota intervalu zjišťování. |

1. Kliknutím na **Uložit** dokončete konfiguraci.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Vyhledat profil Traffic Manager&quot;:::

1. Vyberte **Konfigurace** a vyberte nebo zadejte následující nastavení:

    | Nastavení         | Hodnota                                              |
    | ---             | ---                                                |
    | Metoda směrování            | Vyberte **Vážená**. |    
    | Hodnota TTL (Time to Live) pro DNS | Tato hodnota určuje, jak často bude názvový server místní mezipaměti klienta dotazován na Traffic Manager systém pro aktualizované položky DNS. Jakékoli změny, ke kterým dojde u Traffic Manager, jako je například změna metody směrování provozu nebo změny v dostupnosti přidaných koncových bodů, budou tuto dobu trvat v globálním systému serverů DNS. |
    | Protokol    | Vyberte protokol pro monitorování koncového bodu. *Možnosti: HTTP, HTTPS a TCP* |
    | Port | Zadejte číslo portu. |
    | Cesta | Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko &quot;/"::: 

1. Vyberte **koncový bod** a nakonfigurujte váhu každého koncového bodu. Váha může být mezi 1-1000. Čím vyšší je váha, tím vyšší je priorita.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Vyhledat profil Traffic Manager&quot;:::

1. Vyberte **Konfigurace** a vyberte nebo zadejte následující nastavení:

    | Nastavení         | Hodnota                                              |
    | ---             | ---                                                |
    | Metoda směrování            | Vyberte **Vážená**. |    
    | Hodnota TTL (Time to Live) pro DNS | Tato hodnota určuje, jak často bude názvový server místní mezipaměti klienta dotazován na Traffic Manager systém pro aktualizované položky DNS. Jakékoli změny, ke kterým dojde u Traffic Manager, jako je například změna metody směrování provozu nebo změny v dostupnosti přidaných koncových bodů, budou tuto dobu trvat v globálním systému serverů DNS. |
    | Protokol    | Vyberte protokol pro monitorování koncového bodu. *Možnosti: HTTP, HTTPS a TCP* |
    | Port | Zadejte číslo portu. |
    | Cesta | Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko &quot;/"::: 

## <a name="use-the-traffic-manager-profile"></a>Použít profil Traffic Manager

V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. Název můžou použít všichni klienti (například tak, že se k němu přejde pomocí webového prohlížeče), aby se směroval do správného koncového bodu, který určuje typ směrování. V takovém případě všechny požadavky jsou směrovány do každého koncového bodu kruhové dotazování.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Vyhledat profil Traffic Manager&quot;:::

1. Vyberte **Konfigurace** a vyberte nebo zadejte následující nastavení:

    | Nastavení         | Hodnota                                              |
    | ---             | ---                                                |
    | Metoda směrování            | Vyberte **Vážená**. |    
    | Hodnota TTL (Time to Live) pro DNS | Tato hodnota určuje, jak často bude názvový server místní mezipaměti klienta dotazován na Traffic Manager systém pro aktualizované položky DNS. Jakékoli změny, ke kterým dojde u Traffic Manager, jako je například změna metody směrování provozu nebo změny v dostupnosti přidaných koncových bodů, budou tuto dobu trvat v globálním systému serverů DNS. |
    | Protokol    | Vyberte protokol pro monitorování koncového bodu. *Možnosti: HTTP, HTTPS a TCP* |
    | Port | Zadejte číslo portu. |
    | Cesta | Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko &quot;/"::: 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepotřebujete profil Traffic Manager, najděte profil a vyberte **Odstranit profil**.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Vyhledat profil Traffic Manager&quot;:::

1. Vyberte **Konfigurace** a vyberte nebo zadejte následující nastavení:

    | Nastavení         | Hodnota                                              |
    | ---             | ---                                                |
    | Metoda směrování            | Vyberte **Vážená**. |    
    | Hodnota TTL (Time to Live) pro DNS | Tato hodnota určuje, jak často bude názvový server místní mezipaměti klienta dotazován na Traffic Manager systém pro aktualizované položky DNS. Jakékoli změny, ke kterým dojde u Traffic Manager, jako je například změna metody směrování provozu nebo změny v dostupnosti přidaných koncových bodů, budou tuto dobu trvat v globálním systému serverů DNS. |
    | Protokol    | Vyberte protokol pro monitorování koncového bodu. *Možnosti: HTTP, HTTPS a TCP* |
    | Port | Zadejte číslo portu. |
    | Cesta | Chcete-li monitorovat koncové body, je nutné zadat cestu a název souboru. Lomítko &quot;/":::

## <a name="next-steps"></a>Další kroky

Další informace o metodě váženého směrování najdete v tématech:

> [!div class="nextstepaction"]
> [Metoda směrování váženého provozu](traffic-manager-routing-methods.md#weighted)