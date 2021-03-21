---
title: Omezení obsahu Azure CDN podle země nebo oblasti
description: Naučte se, jak omezit přístup podle země nebo oblasti k vašemu Azure CDN obsahu pomocí funkce geografického filtrování.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539495"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Omezení obsahu Azure CDN podle země nebo oblasti

## <a name="overview"></a>Přehled
Když uživatel požádá o obsah, obsah se doplní uživatelům ve všech umístěních. Můžete chtít omezit přístup k vašemu obsahu podle země nebo oblasti. 

Díky funkci *geografického filtrování* můžete vytvořit pravidla pro konkrétní cesty ke koncovému bodu CDN. Můžete nastavit pravidla pro povolení nebo blokování obsahu ve vybraných zemích nebo oblastech.

> [!IMPORTANT]
> **Azure CDN Standard od profilů Microsoft** nepodporují geografické filtrování založené na cestách.
> 

## <a name="standard-profiles"></a>Standardní profily

Tyto pokyny jsou pro **Azure CDN Standard od Akamai** a **Azure CDN Standard od profilů Verizon** .

Pro **Azure CDN Premium ze profilů Verizon** je nutné pomocí portálu pro **správu** aktivovat geografické filtrování. Další informace najdete v tématu [Azure CDN Premium ze profilů Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definujte cestu k adresáři.
Pokud chcete získat přístup k funkci geografického filtrování, vyberte koncový bod CDN na portálu a potom v části nastavení v nabídce na levé straně vyberte **geografická filtrování** . 

![Snímek obrazovky znázorňující geografické filtrování vybrané z nabídky pro koncový bod](./media/cdn-filtering/cdn-geo-filtering-standard.png)

V poli **cesta** zadejte relativní cestu k umístění, do kterého budou mít uživatelé povolený nebo odepřený přístup. 

Můžete použít geografické filtrování pro všechny soubory pomocí lomítka (/) nebo vybrat konkrétní složky zadáním cest k adresářům (například */Pictures/*). Můžete také použít geografické filtrování na jeden soubor (například */pictures/city.png*). Je povoleno více pravidel. Po zadání pravidla se zobrazí prázdný řádek, kde můžete zadat další pravidlo.

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

Pro **Azure CDN Premium ze profilů Verizon** se uživatelské rozhraní pro vytvoření pravidla geografického filtrování liší:

1. V horní nabídce profilu Azure CDN vyberte **Spravovat**.

2. Na portálu Verizon vyberte možnost **http Velká** a pak vyberte **filtrování země**.

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Snímek obrazovky ukazuje, jak vybrat filtrování země v Azure CDN" border="true":::
  
3. Vyberte **Přidat filtr země**.

4. V **kroku One:** zadejte cestu k adresáři. Vyberte možnost **blokovat** nebo **Přidat** a pak vyberte možnost **Další**.

    > [!IMPORTANT]
    > Název koncového bodu musí být v cestě.  Příklad: **/myendpoint8675/myFolder**.  Nahraďte **myendpoint8675** názvem vašeho koncového bodu.
    > 
    
5. V **kroku 2** vyberte ze seznamu jednu nebo více zemí nebo oblastí. Kliknutím na **Dokončit** aktivujte pravidlo. 
    
    Nové pravidlo se zobrazí v tabulce na stránce **filtrování země** .
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="Snímek obrazovky ukazuje, kde se pravidlo zobrazuje při filtrování země." border="true":::
 
### <a name="clean-up-resources"></a>Vyčištění prostředků
V tabulce pravidla filtrování země nebo oblasti vyberte ikonu Odstranit vedle pravidla, které chcete odstranit, nebo ikonu Upravit a upravte ji.

## <a name="considerations"></a>Požadavky
* Změny v konfiguraci geografického filtrování se projeví okamžitě:
   * U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
   * V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   * V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
 
* Tato funkce nepodporuje zástupné znaky (například *).

* Konfigurace geografického filtrování přidružená k relativní cestě se v této cestě aplikuje rekurzivně.

* Pro stejnou relativní cestu lze použít pouze jedno pravidlo. To znamená, že nemůžete vytvořit více filtrů zemí nebo oblastí, které odkazují na stejnou relativní cestu. Vzhledem k tomu, že filtry země nebo oblasti jsou rekurzivní, může mít složka více filtrů zemí/oblastí. Jinými slovy je možné přiřadit podsložku dříve nakonfigurované složky k jinému filtru země/oblasti.

* Funkce geografického filtrování používá kódy zemí k definování zemí nebo oblastí, ze kterých je požadavek povolen nebo zablokován pro zabezpečený adresář. I když profily Akamai a Verizon podporují většinu stejných kódů zemí, existuje několik rozdílů. Další informace najdete v tématu [Azure CDN kódů zemí](/previous-versions/azure/mt761717(v=azure.100)). 

