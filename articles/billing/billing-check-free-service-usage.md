---
title: Monitorování a sledování využití služeb Azure zdarma
description: Zjistěte, jak kontrolovat využití bezplatné služby v souboru CSV Azure portal a využití.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491424"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Kontrola využití bezplatné služby je součástí bezplatného účtu Azure

Se vám neúčtují poplatky pro služby zahrnuté zdarma s bezplatným účtem Azure, pokud překročíte limity služby. Tak si zachováte omezení, můžete použít pro monitorování a sledování využití bezplatné služby na webu Azure portal nebo v souboru využití.

## <a name="check-usage-in-the-azure-portal"></a>Kontrola využití na webu Azure Portal

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2.  V levé navigační oblasti, vyberte **všechny služby**.

3.  Vyberte **Předplatná**.

4.  Vyberte předplatné, které jste vytvořili při registraci bezplatné účtu.

    ![Snímek obrazovky zobrazující všechna předplatná](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  V části Přehled zobrazuje důležité informace o vašem předplatném. Například ID předplatného, typ nabídky a názvu předplatného. Informace najdete taky když vyprší platnost kreditu vašeho bezplatného účtu.

    ![Snímek obrazovky zobrazující základní informace o předplatném](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Posuňte se dolů najít informace o aktuální i předpokládané náklady. Cena zahrnuje využití služeb, které nejsou součástí vašeho bezplatného účtu a využití překročení limitů bezplatné služby.

    ![Snímek obrazovky zobrazující předplatné informace o nákladech](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Poslední část oddílu přehled má tabulka zobrazující využití bezplatné služby.

    ![Snímek obrazovky zobrazující využití bezplatných služeb](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabulka obsahuje následující sloupce:

* **Název měřiče:** Určuje měrnou jednotku pro měření spotřebovávanou. Další informace o službě Mapování měřičů najdete v tématu [pochopit bezplatnou službu pro mapování měřičů](billing-understand-free-service-meter-mapping.md).
* **Využití a omezení:** Aktuální měsíc využití a omezení pro měřidla. Tyto informace můžete najít také ve stavovém řádku.
* **Stav:** Stav využití měření. Založena na vzoru vaše využití, může mít jednu z následujících stanov:
  * **Nepoužíváte:** Jste nepoužili měřič nebo využití měřičů nebyl zaznamenal fakturační systém.
  * **Datum překročení \<datum >:** Byl překročen limit pro měřidla \<datum >.
  * **Pravděpodobně se nepřekročí:** Už brzo budete pravděpodobně se nepřekročí. limit pro měřidla.
  * **Datum překročení \<datum >:** Budete pravděpodobně překročí tento limit pro měřidla v \<datum >.

## <a name="check-usage-with-the-usage-file"></a>Zkontrolujte použití se souborem využití

Váš soubor využití poskytuje podrobné informace o vašem předplatném Azure. Váš denní a měsíční využití soubor si můžete stáhnout z centra účtů Azure. Zjistěte, jak stáhnout soubor využití a pochopení přístup vyžaduje, najdete v článku [získání faktury a využití](billing-download-azure-invoice-daily-usage-date.md). Další informace o sloupcích v souboru využití najdete v tématu [vysvětlení podmínek vašeho použití](billing-understand-your-usage.md).

Využití soubor obsahuje informace o bezplatných nebo placených služeb využití. Bude mít bezplatná služba měřiče **Free** přidáván na konec názvu měřiče. Najít bezplatné měřičů, otevřete soubor v Excelu a filtr **sloupec kategorie měřiče** u buněk, které obsahují text **– Free** (použít filtry textu &rarr; filtr obsahuje).


![Snímek obrazovky zobrazující využití bezplatných služeb](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup
- [Upgradujte vaše předplatné](billing-upgrade-azure-subscription.md)
