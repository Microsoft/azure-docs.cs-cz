---
title: Monitorování a sledování využití bezplatné služby - Azure | Microsoft Docs
description: Další informace a zkontrolujte pro bezplatné služby. Pomocí Azure portal a používání sdíleného svazku clusteru.
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
ms.author: amberb
ms.openlocfilehash: 560137d92491da46fda950dec84a0a7caa3ed720
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Zkontrolujte využití volného služby zahrnuté do vaší bezplatný účet Azure 

Vám není účtován služby zahrnuté zdarma do bezplatný účet Azure, pokud se překročí mezní hodnoty z těchto služeb. Aby byla zachovaná s limity, můžete použít buď portálu Azure nebo souboru využití pro monitorování a sledování využití bezplatné služby. 

## <a name="check-usage-on-the-azure-portal"></a>Zkontrolujte využití na portálu Azure

1.  Přihlaste se k portálu [Azure Portal]( http://portal.azure.com).

2.  Levé navigační oblasti, vyberte **všechny služby**.

3.  Vyberte **odběry**.

4.  Vyberte odběr, který jste vytvořili při registraci bezplatné účtu.

    ![Snímek obrazovky zobrazující všechna předplatná](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Přehled části se dozvíte, základní informace o vašem předplatném, jako je například ID předplatného, nabízejí typ a název odběru. Můžete také získat informace o při by platnost kreditu bezplatný účet.

    ![Snímek obrazovky, který zobrazuje základní informace o předplatném](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Posuňte se dolů a najděte informace o vaší aktuální a prognózy náklady. Náklady na zahrnuje používání překročení omezení bezplatné služby a používání služeb, které nejsou součástí bezplatný účet. 

    ![Snímek obrazovky, který zobrazuje informace o předplatném náklady](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Poslední část oddílu přehled obsahuje tabulku k použití bezplatné služby. 

    ![Snímek obrazovky, který zobrazuje využití bezplatné služby](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabulka obsahuje následující sloupce:

* **Název měřidla:** identifikuje pro měření spotřebovávanou měrné jednotky. Další informace o služby k měření mapování najdete v tématu [pochopit bezplatné služby k měření mapování](billing-understand-free-service-meter-mapping.md). 
* **Limit nebo využití:** využití a omezení pro měřidla aktuálního měsíce. Tyto informace můžete také získat ve stavovém řádku.
* **Stav:** stav využití měřidla. Na základě vaší využití vzoru, může mít jednu z těchto stanov.
  * **Není používán:** nepoužili měřidla nebo použití měřidla nedosáhla fakturačních systémů.
  * **Překročena na \<datum >:** Překročili jste limit pro měřidla na \<datum >.
  * **Pravděpodobně Exceed:** se pravděpodobně přesáhl limit pro měřidla.
  * **Překračuje na \<datum >:** budete pravděpodobně přesáhl limit pro měřidla na \<datum >.


## <a name="check-usage-through-the-usage-file"></a>Zkontrolujte využití prostřednictvím soubor využití

Váš soubor využití poskytuje podrobné informace o vašem předplatném Azure. Vaše měsíční a denní využití soubor můžete stáhnout z centra účtů Azure. Informace o stažení souboru využití a pochopit přístup požadovaný najdete v tématu [získat faktury a využití](billing-download-azure-invoice-daily-usage-date.md). Další informace o sloupcích v souboru využití najdete v tématu [pochopit podmínky na vaše používání](billing-understand-your-usage.md). 

Využití soubor obsahuje informace o využití pro bezplatných i placených služeb. Bezplatné služby měřidla by měla mít **volné** přidán na konec názvu měření. K nalezení volné měřidla, otevřete soubor v aplikaci excel a filtru **měření kategorie sloupec** pro buněk, které obsahují text **– volné** (pomocí filtrů Text &rarr; filtr obsahuje) &nbsp;

![Snímek obrazovky, který zobrazuje využití bezplatné služby](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
