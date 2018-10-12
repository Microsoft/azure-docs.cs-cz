---
title: Omezit přístup k obsahu Azure CDN podle země | Dokumentace Microsoftu
description: Informace o omezení přístupu podle země k vašemu obsahu Azure CDN pomocí funkce geografické filtrování.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 471a7e3704f10674c8a1d9bdf26df5f0aaf8519b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093302"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Omezit přístup k obsahu Azure CDN podle země

## <a name="overview"></a>Přehled
Když si uživatel vyžádá obsah, ve výchozím nastavení, obsah obsluhuje bez ohledu na umístění uživatele, který zadal žádost. Ale v některých případech může chtít omezit přístup k obsahu podle země. S *geografické filtrování* funkce, můžete vytvořit pravidla na konkrétních cestách vašeho koncového bodu CDN povolit nebo blokovat obsah ve vybraných zemích.

> [!IMPORTANT]
> **Azure CDN Standard od společnosti Microsoft** profily založené na cestě geografické filtrování nepodporují.
> 

## <a name="standard-profiles"></a>Standardní profily
Postupy v této části jsou určené pro **Azure CDN Standard od Akamai** a **Azure CDN Standard od Verizonu** pouze profily. 

Pro **Azure CDN Premium od Verizonu** profily, je nutné použít **spravovat** portál a aktivovat geografické filtrování. Další informace najdete v tématu [Azure CDN Premium od Verizonu profily](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definovat cesty k adresáři
Pro přístup k funkci geografické filtrování, vyberte koncový bod CDN na portálu a potom vyberte **geografické filtrování** podle nastavení v nabídce vlevo. 

![Geografické filtrování standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Z **cesta** zadejte relativní cestu k umístění, do které se uživatelům povoluje nebo odepírá přístup. 

Můžete provést geografické filtrování pro všechny soubory s předání lomítko (/) nebo vybrat konkrétní složky zadáním cesty k adresářům (například */obrazky/*). Můžete také provést geografické filtrování u jednoho souboru (například */pictures/city.png*). Více pravidel jsou povoleny. Po zadání pravidlo, zobrazí se prázdný řádek můžete zadat další pravidla.

Například všechny následující filtry cesta adresáře jsou platné:   
*/*                                 
*/Photos/*     
*/Photos/Štrasburku /*     
*/Photos/Strasbourg/City.PNG*

### <a name="define-the-type-of-action"></a>Definování typ akce

Z **akce** seznamu vyberte **povolit** nebo **bloku**: 

- **Povolit**: pouze ze zadaného zemí mohou uživatelé přístup k prostředkům vyžádané rekurzivní cesta.

- **Blok**: uživatelé z zadanou zemí mají odepřený přístup k prostředkům vyžádané rekurzivní cesta. Pokud pro toto umístění nebyly nakonfigurovány žádné jiné země možnosti filtrování, pak všichni ostatní uživatelé budou mít přístup povolený.

Například geografické filtrování pravidla pro blokování cestu */fotografie/Štrasburku/* filtry pro následující soubory:     
*http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Definovat zemí
Z **kódy zemí** seznamu, zvolte země, pro které chcete blokovat nebo povolit pro danou cestu. 

Po dokončení výběru země, vyberte **Uložit** k aktivaci nové geografické filtrování pravidlo. 

![Pravidla pro geografické filtrování](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odstranit pravidlo, vyberte ho ze seznamu na **geografické filtrování** stránce a pak zvolte **odstranit**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium od Verizonu profily
Pro **pro Azure CDN Premium od Verizonu** profily, uživatelské rozhraní pro vytvoření pravidla pro geografické filtrování se liší:

1. V horní nabídce ve vašem profilu Azure CDN, vyberte **spravovat**.

2. Z portálu společnosti Verizon vyberte **HTTP velké**a pak vyberte **filtrování podle země**.

    ![Geografické filtrování standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Vyberte **přidat filtr země**.

    **Jeden krok:** se zobrazí stránka.

4. Zadejte cestu k adresáři, vyberte **bloku** nebo **přidat**a pak vyberte **Další**.

    **Kroku dvě:** se zobrazí stránka. 

5. Ze seznamu vyberte jednu nebo více zemí a pak vyberte **Dokončit** aktivovat pravidlo. 
    
    Nové pravidlo se zobrazí v tabulce na **filtrování podle země** stránky.

    ![Pravidla pro geografické filtrování](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
V tabulce země filtrování pravidla vyberte ikonu Odstranit vedle pravidlo odstranit nebo na ikonu úprav ho upravit.

## <a name="considerations"></a>Požadavky
* Změny konfigurace geografické filtrování neprojevila okamžitě:
   * Šíření profilů **Azure CDN Standard od Microsoftu** trvá většinou 10 minut. 
   * V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   * V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
 
* Tato funkce nepodporuje zástupné znaky (například *).

* Konfigurace geografické filtrování spojené s relativní cestou je aplikována rekurzivně na tuto cestu.

* Na stejné relativní cesty lze použít pouze jedno pravidlo. To znamená nelze vytvořit více filtrů země, které odkazují na stejné relativní cesty. Ale protože země filtry je rekurzivní, složka může mít více filtrů země. Jinými slovy podsložka dříve konfigurované složky je možné přiřadit filtr jiné zemi.

* Funkce geografického filtrování používá k definování zemí, ze kterých se žádost o povolená nebo zablokovaná pro zabezpečený adresář kódy zemí. Přestože společností Akamai a Verizon profily podporují většinu stejné kódy zemí, existuje několik rozdílů. Další informace najdete v tématu [kódy zemí Azure CDN](https://msdn.microsoft.com/library/mt761717.aspx). 

