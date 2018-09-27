---
title: Monitorování a sledování využití bezplatných služeb Azure | Dokumentace Microsoftu
description: Zjistěte, jak kontrola využití bezplatných služeb. Použijte Azure portal a používání sdíleného svazku clusteru.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: cwatson
ms.openlocfilehash: df049a87763f3aae8da2db153f876b88ed39b988
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47390234"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrola využití bezplatných služeb je součástí bezplatného účtu Azure 

Se vám neúčtují poplatky pro služby zahrnuté zdarma s bezplatným účtem Azure, pokud překročíte limity, z těchto služeb. K zajištění s omezeními, můžete použít buď na portálu Azure portal nebo v souboru využití pro monitorování a sledování využití bezplatných služeb. 

## <a name="check-usage-on-the-azure-portal"></a>Kontrola využití na portálu Azure portal

1.  Přihlaste se k webu [Azure Portal]( http://portal.azure.com).

2.  V levé navigační oblasti, vyberte **všechny služby**.

3.  Vyberte **Předplatná**.

4.  Vyberte předplatné, které jste vytvořili při registraci bezplatné účtu.

    ![Snímek obrazovky zobrazující všechna předplatná](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Přehled části se dozvíte, základní informace o vašem předplatném, jako je například ID odběru nabízí typ a název předplatného. Můžete také najít informace o by vypršení kreditu vašeho bezplatného účtu.

    ![Snímek obrazovky zobrazující základní informace o předplatném](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Posuňte se dolů najít informace o aktuální i předpokládané náklady. Cena zahrnuje využití služeb, které nejsou součástí vašeho bezplatného účtu a využití překračující mezní hodnoty bezplatných služeb. 

    ![Snímek obrazovky zobrazující předplatné informace o nákladech](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Poslední část oddílu přehled obsahuje tabulku na využití bezplatných služeb. 

    ![Snímek obrazovky zobrazující využití bezplatných služeb](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabulka obsahuje následující sloupce:

* **Název měřiče:** identifikuje jednotka měření k měření spotřebovávanou. Další informace o službě Mapování měřičů najdete v tématu [pochopit bezplatnou službu pro mapování měřičů](billing-understand-free-service-meter-mapping.md). 
* **Využití a omezení:** využití a omezení pro měřidla aktuálního měsíce. Tyto informace můžete najít také ve stavovém řádku.
* **Stav:** stav využití měření. Založena na vzoru vaše využití, může mít jednu z těchto stanov.
  * **Nepoužíváte:** nepoužili měřič nebo využití měřičů nebyl zaznamenal fakturační systém.
  * **Datum překročení \<datum >:** byl překročen limit pro měřidla \<datum >.
  * **Pravděpodobně Exceed:** pravděpodobně překročí tento limit pro měřidla.
  * **Datum překročení \<datum >:** pravděpodobné překročení limitu pro měřidla v \<datum >.


## <a name="check-usage-through-the-usage-file"></a>Kontrola využití v souboru využití

Váš soubor využití poskytuje podrobné informace pro vaše předplatné Azure. Vaše měsíční a denní využití soubor si můžete stáhnout z centra účtů Azure. Zjistěte, jak stáhnout soubor využití a pochopení přístup vyžaduje, najdete v článku [získání faktury a využití](billing-download-azure-invoice-daily-usage-date.md). Další informace o sloupcích v souboru využití najdete v tématu [vysvětlení podmínek vašeho použití](billing-understand-your-usage.md). 

Využití soubor obsahuje informace o bezplatných nebo placených služeb využití. Bude mít bezplatná služba měřiče **Free** přidáván na konec názvu měřiče. Najít bezplatné měřičů, otevřete soubor v Excelu a filtr **sloupec kategorie měřiče** u buněk, které obsahují text **– Free** (použít filtry textu &rarr; filtr obsahuje) &nbsp;

![Snímek obrazovky zobrazující využití bezplatných služeb](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
