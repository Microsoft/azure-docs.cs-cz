---
title: Řešení potíží s nedostupností typu rezervace Azure
description: Tento článek vám pomůže pochopit a vyřešit problémy s rezervovanými instancemi, které se zdají být na webu Azure Portalu nedostupné.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798180"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Řešení potíží s nedostupností typu rezervace

Tento článek vám pomůže pochopit a vyřešit problémy s rezervovanými instancemi, které se zdají být na webu Azure Portalu nedostupné.

## <a name="symptoms"></a>Příznaky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte k položce **Rezervace** .
2. Vyberte **+ Přidat** a pak vyberte požadovaný produkt.
3. Vyberte kartu **All Products** (Všechny produkty).
4. V seznamu produktů vyberte požadovaný produkt. Můžete se zobrazit některá z následujících zpráv:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Příklad ukazující zprávu Produkt není pro vybrané předplatné nebo oblast k dispozici" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Příklad ukazující zprávu Produkt není pro vybrané předplatné nebo oblast k dispozici" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Příčina

Některé rezervace nejsou k dispozici pro nákup z těchto důvodů:

- Některé produkty rezervovaných instancí nejde zakoupit ve všech oblastech
- Kvóta pro předplatné je omezená

### <a name="cause-1"></a>Příčina 1

Ne všechny produkty rezervovaných instancí jsou k dispozici k nákupu. Azure rozhoduje o povolení nebo nepovolení některých produktů na základě nákladů spojených s poskytováním peněžní slevy zákazníkům. V jiných případech Azure nenabízí některé produkty kvůli kapacitním podmínkám v konkrétních datových centrech. Kromě toho mohou některé skupiny pro vývoj produktů Azure nepovolovat určité produkty, protože chtějí vyřadit z provozu starší produkty.

V jiných případech Azure nastavuje kapacitní omezení pro různé produkty na základě poptávky po těchto prostředcích v některých oblastech. Podobná omezení mohou být nastavena například tehdy, pokud poptávka po konkrétní velikosti virtuálních počítačů přesahuje možnosti datového centra. V tomto příkladu Azure nezaručuje kapacitu zákazníkům, kteří si zakoupili rezervaci pro danou velikost v této oblasti. V neposlední řade existují také produkty, které jsou z různých důvodů jedinečné. Takovéto produkty se zpřístupňují jenom malé vybrané skupině zákazníků.

Proč se ale rezervace, které nejsou k dispozici pro nákup, zobrazují na webu Azure Portal? Odpověď zní, protože uživatelé vytvářejí žádosti o podporu s informací, že požadované produkty nemohou najít. Vývojový tým pro rezervace Azure zjistil, že zobrazení všech produktů se zprávou `Product unavailable` vede k menšímu počtu žádostí o podporu než jejich nezobrazení.

### <a name="cause-2"></a>Příčina 2

Kvóta pro vaše předplatné je omezená. Pro předplatná platí omezení počtu jader procesoru, která mohou využívat. Omezení platí pro některé produkty rezervovaných instancí, zejména pro virtuální počítače. Azure chce zajistit, aby lidé, kteří si zakoupili rezervovanou instanci, mohli tuto instanci také využívat. Azure provede jednoduchou zběžnou kontrolu na webu Azure Portal a zjistí, jestli máte ve vašem předplatném dostatek volných jader pro nasazení virtuálního počítače a zajištění výhody při zakoupení rezervace.

Tato kontrola umožňuje přidat konkrétní produkt do košíku. Nákup rezervace je jednoduchý. Azure vyhodnotí celkový počet jader procesoru dostupných pro vaše předplatné a ověří, jestli je tento počet větší než počet jader pro vybranou položku.

Azure nekontroluje kvótu pro rezervované instance se **sdíleným** rozsahem. Výhoda rezervované instance pro sdílený rozsah se vztahuje na všechna předplatná v rámci příslušné registrace. Azure nedokáže zjistit, jestli máte dostatek volných jader pro nasazení prostředku napříč všemi vašimi předplatnými. Bez ohledu na kvótu vám Azure vždycky umožní vybrat velikost virtuálního počítače, když je vybraný rozsah sdílený.

Azure navíc neprovádí kontrolu kvóty pro nákupy označené jako **doporučené** . Doporučení jsou založená na aktivním využití. Azure předpokládá, že máte dostatek jader pro spuštění konkrétní velikosti virtuálního počítače, protože už jste vygenerovali použití potřebné k vytvoření tohoto doporučení.

## <a name="solution"></a>Řešení

V závislosti na chybové zprávě, která se vám zobrazila, použijte pro svůj problém jedno z následujících řešení.

### <a name="solution-1"></a>Řešení 1

Pokud se zobrazuje zpráva typu _Produkt je nedostupný_ , vyberte v této chybové zprávě odkaz **Kontaktovat podporu** a vyžádejte si výjimku pro vaše předplatné. Výjimky se neudělují vždy.

### <a name="solution-2"></a>Řešení 2

Pokud se zobrazuje zpráva typu _Nedostatečná kvóta jader_ , můžete změnit rozsah na **sdílený** . Po zakoupení rezervace můžete její rozsah změnit ze **sdíleného** na **samostatný** .

Nebo v chybové zprávě vyberte odkaz **Žádost o navýšení kvóty** a vyžádejte si pro vaše předplatné další kvótu jader CPU.

## <a name="next-steps"></a>Další kroky

- Další informace o možnostech rozsahu rezervací najdete v tématu [Vymezení rozsahů rezervací](prepare-buy-reservation.md#scope-reservations).