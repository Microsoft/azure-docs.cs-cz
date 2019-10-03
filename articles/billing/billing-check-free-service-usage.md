---
title: Monitorování a sledování využití bezplatných služeb Azure
description: Zjistěte, jak zkontrolovat využití bezplatných služeb na webu Azure Portal a v souboru CSV s informacemi o využití.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ae8b26f0032c6659da18b822f4f8568995eff82b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709773"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Kontrola využití bezplatných služeb, které jsou součástí bezplatného účtu Azure

Za služby zahrnuté bezplatně v rámci bezplatného účtu Azure se neúčtují žádné poplatky, pokud nepřekročíte limity těchto služeb. Pokud chcete zůstat v mezích těchto limitů, můžete monitorovat a sledovat využití bezplatných služeb pomocí webu Azure Portal nebo souboru s informacemi o využití.

## <a name="check-usage-in-the-azure-portal"></a>Kontrola využití na webu Azure Portal

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2.  V levé navigační oblasti vyberte **Všechny služby**.

3.  Vyberte **Předplatná**.

4.  Vyberte předplatné, které jste vytvořili při registraci bezplatného účtu.

    ![Snímek obrazovky ukazující všechna předplatná](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  V části Přehled se zobrazí základní informace o vašem předplatném. Například ID předplatného, typ nabídky a název předplatného. Najdete tu také informace o datu vypršení platnosti vašeho kreditu bezplatného účtu.

    ![Snímek obrazovky ukazující základní informace o předplatném](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Posuňte se dolů, kde najdete informace o vašich aktuálních a předpokládaných nákladech. Náklady zahrnují využití služeb nad rámec bezplatného účtu a využití překračující limity bezplatných služeb.

    ![Snímek obrazovky ukazující informace o nákladech na předplatné](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Na konci části Přehled najdete tabulku ukazující využití bezplatných služeb.

    ![Snímek obrazovky ukazující využití bezplatných služeb](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabulka obsahuje následující sloupce:

* **Název měřiče:** Určuje měrnou jednotku spotřebovávaného měřiče. Další informace o mapování služeb na měřiče najdete v tématu [Principy mapování bezplatných služeb na měřiče](billing-understand-free-service-meter-mapping.md).
* **Využití/limit:** Využití za aktuální měsíc a limit daného měřiče. Tyto informace najdete také na stavovém řádku.
* **Stav:** Stav využití daného měřiče. V závislosti na vašem vzorci využití se může zobrazit jeden z následujících stavů:
  * **Nepoužívá se:** Měřič nepoužíváte nebo fakturační systém ještě nepřijal informace o využití z měřiče.
  * **Překročeno dne \<Date>:** Dne \<Date> jste překročili limit měřiče.
  * **Pravděpodobně se nepřekročí:** Pravděpodobně nepřekročíte limit měřiče.
  * **Překročí se dne \<Date>:** Dne \<Date> pravděpodobně překročíte limit měřiče.

## <a name="check-usage-with-the-usage-file"></a>Kontrola využití v souboru s informacemi o využití

V souboru s informacemi o využití najdete podrobné informace týkající se vašeho předplatného Azure. Soubor s informacemi o měsíčním a denním využití si můžete stáhnout z Centra účtů Azure. Pokyny ke stažení souboru s informacemi o využití a informace o požadované úrovni přístupu najdete v tématu [Získání faktury a dat o využití](billing-download-azure-invoice-daily-usage-date.md). Informace o jednotlivých sloupcích v souboru s informacemi o využití najdete v tématu [Vysvětlení výrazů týkajících se využití](billing-understand-your-usage.md).

Soubor s informacemi o využití obsahuje informace o bezplatných i placených službách. Měřiče bezplatných služeb mají ke konci svého názvu připojený text **Free**. Pokud chcete vyhledat měřiče bezplatných služeb, otevřete soubor v aplikaci Excel a ve sloupci **Meter Category** (Kategorie měřiče) vyfiltrujte buňky obsahující text **- Free** (použijte Textové filtry &rarr; filtr Obsahuje).


![Snímek obrazovky ukazující využití bezplatných služeb](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- [Upgrade předplatného](billing-upgrade-azure-subscription.md)
