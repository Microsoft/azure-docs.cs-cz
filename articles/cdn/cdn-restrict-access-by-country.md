---
title: Omezit Azure CDN obsahu podle země nebo oblasti | Microsoft Docs
description: Naučte se, jak omezit přístup podle země nebo oblasti k vašemu Azure CDN obsahu pomocí funkce geografického filtrování.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: ed82adcc1432bde27042d5775c454bfabcdb96ca
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358130"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Omezení obsahu Azure CDN podle země nebo oblasti

## <a name="overview"></a>Přehled
Když si uživatel požádá o váš obsah, ve výchozím nastavení se obsah obsluhuje bez ohledu na umístění uživatele, který požadavek odeslal. V některých případech však můžete chtít omezit přístup k vašemu obsahu podle země nebo oblasti. Funkce *geografického filtrování* vám umožní vytvořit pravidla pro konkrétní cesty ke koncovému bodu CDN, aby bylo možné obsah ve vybraných zemích nebo oblastech zablokovat nebo blokovat.

> [!IMPORTANT]
> **Azure CDN Standard od profilů Microsoft** nepodporují geografické filtrování založené na cestách.
> 

## <a name="standard-profiles"></a>Standardní profily
Postupy v této části jsou pro **Azure CDN Standard od Akamai** a **Azure CDN Standard pouze ze profilů Verizon** . 

Pro **Azure CDN Premium ze profilů Verizon** je nutné pomocí portálu pro **správu** aktivovat geografické filtrování. Další informace najdete v tématu [Azure CDN Premium ze profilů Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definujte cestu k adresáři.
Pokud chcete získat přístup k funkci geografického filtrování, vyberte koncový bod CDN na portálu a potom v části nastavení v nabídce na levé straně vyberte **geografická filtrování** . 

![Snímek obrazovky znázorňující geografické filtrování vybrané z nabídky pro koncový bod](./media/cdn-filtering/cdn-geo-filtering-standard.png)

V poli **cesta** zadejte relativní cestu k umístění, do kterého budou mít uživatelé povolený nebo odepřený přístup. 

Můžete použít geografické filtrování pro všechny soubory pomocí lomítka (/) nebo vybrat konkrétní složky zadáním cest k adresářům (například */Pictures/*). Můžete také použít geografické filtrování na jeden soubor (například */pictures/city.png*). Je povoleno více pravidel; Po zadání pravidla se zobrazí prázdný řádek, kde můžete zadat další pravidlo.

Například všechny následující filtry cest adresářů jsou platné:   
*/*                                 
*Fotografování*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Definovat typ akce

V seznamu **Akce** vyberte možnost **Povolení** nebo **zablokování**: 

- **Povolit**: přístup k prostředkům vyžádanému z rekurzivní cesty mají povolený jenom uživatelé ze zadaných zemí nebo oblastí.

- **Blokovat**: uživatelé ze zadaných zemí nebo oblastí mají odepřený přístup k požadovaným prostředkům z rekurzivní cesty. Pokud pro toto umístění nejsou nakonfigurované žádné možnosti filtrování jiných zemí nebo oblastí, bude přístup povolen všem ostatním uživatelům.

Například pravidlo geografického filtrování pro blokování cesty */Photos/Strasbourg/* filtruje následující soubory:     
*http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definování zemí nebo oblastí
V seznamu **kódy země** vyberte země nebo oblasti, které chcete pro cestu zablokovat nebo zakázat. 

Po dokončení výběru zemí nebo oblastí vyberte **Uložit** a aktivujte nové pravidlo geografického filtrování. 

![Snímek obrazovky se zobrazí kódy zemí, které se použijí k blokování nebo povolení zemí nebo oblastí.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pravidlo odstranit, vyberte ho ze seznamu na stránce **geografické filtrování** a pak zvolte **Odstranit**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium z profilů Verizon
Pro **Azure CDN Premium od profilů Verizon** se uživatelské rozhraní pro vytvoření pravidla geografického filtrování liší:

1. V horní nabídce profilu Azure CDN vyberte **Spravovat**.

2. Na portálu Verizon vyberte možnost **http Velká**a pak vyberte **filtrování země**.

    ![Snímek obrazovky ukazuje, jak vybrat filtrování země v Azure C D N.](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Vyberte **Přidat filtr země**.

    Zobrazí se stránka **Krok 1:** .

4. Zadejte cestu k adresáři, vyberte možnost **blokovat** nebo **Přidat**a pak vyberte možnost **Další**.

    Zobrazí se stránka **Krok dvě:** . 

5. Vyberte ze seznamu jednu nebo více zemí nebo oblastí a pak kliknutím na **Dokončit** aktivujte pravidlo. 
    
    Nové pravidlo se zobrazí v tabulce na stránce **filtrování země** .

    ![Snímek obrazovky ukazuje, kde se pravidlo zobrazuje při filtrování země.](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
V tabulce pravidla filtrování země nebo oblasti vyberte ikonu Odstranit vedle pravidla, které chcete odstranit, nebo ikonu Upravit a upravte ji.

## <a name="considerations"></a>Požadavky
* Změny v konfiguraci geografického filtrování se neprojeví okamžitě:
   * U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
   * V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   * V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
 
* Tato funkce nepodporuje zástupné znaky (například *).

* Konfigurace geografického filtrování přidružená k relativní cestě se v této cestě aplikuje rekurzivně.

* Pro stejnou relativní cestu lze použít pouze jedno pravidlo. To znamená, že nemůžete vytvořit více filtrů zemí nebo oblastí, které odkazují na stejnou relativní cestu. Vzhledem k tomu, že filtry země nebo oblasti jsou rekurzivní, může mít složka více filtrů zemí/oblastí. Jinými slovy je možné přiřadit podsložku dříve nakonfigurované složky k jinému filtru země/oblasti.

* Funkce geografického filtrování používá kódy zemí k definování zemí nebo oblastí, ze kterých je požadavek povolen nebo zablokován pro zabezpečený adresář. I když profily Akamai a Verizon podporují většinu stejných kódů zemí, existuje několik rozdílů. Další informace najdete v tématu [Azure CDN kódů zemí](/previous-versions/azure/mt761717(v=azure.100)). 

