---
title: Využívání webové služby v Excelu
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (Classic) usnadňuje volání webových služeb přímo z Excelu bez nutnosti psát jakýkoli kód.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 2e95c4bfbe7342e251e6d845fd4acfed6ff6109a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150098"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Spotřebovávání webové služby Azure Machine Learning Studio (Classic) z Excelu

 Azure Machine Learning Studio (Classic) usnadňuje volání webových služeb přímo z Excelu bez nutnosti psát jakýkoli kód.

Pokud používáte Excel 2013 (nebo novější) nebo Excel Online, doporučujeme použít excelový [doplněk](excel-add-in-for-web-services.md)Excelu.



## <a name="steps"></a>Kroky
Publikování webové služby. [Kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md) vysvětluje, jak to provést. Aktuálně funkce sešitu aplikace Excel je podporována pouze pro služby požadavků/odpovědí, které mají jeden výstup (tedy hodnoticí přípony). 

Jakmile máte webovou službu, klikněte na část **webové služby** na levé straně studia a pak vyberte webovou službu, kterou chcete spotřebovat z Excelu.

**Klasická webová služba**

1. Na kartě **řídicí panel** webové služby je řádek pro službu **Request/response** . Pokud má tato služba jeden výstup, měli byste vidět odkaz **Stáhnout excelový sešit** v tomto řádku.

    ![Stažení excelového sešitu pomocí portálu webové služby Studio (Classic)](./media/consuming-from-excel/excellink.png)
2. Klikněte na **Stáhnout excelový sešit**.

**Nová webová služba**

1. Na portálu Azure Machine Learning webové služby vyberte možnost **spotřebovat**.
2. Na stránce spotřebovat v části **Možnosti spotřeby webové služby** klikněte na ikonu aplikace Excel.

**Používání sešitu**

1. Otevřete sešit.
2. Zobrazí se upozornění zabezpečení. klikněte na tlačítko **Povolit úpravy** .

    ![Povolit úpravy pro odebrání upozornění zabezpečení chráněného zobrazení](./media/consuming-from-excel/enableeditting.png)
3. Zobrazí se upozornění zabezpečení. Kliknutím na tlačítko **Povolit obsah** spustíte makra v tabulce.

    ![Povolit obsahu, aby se zakázalo upozornění na zabezpečení zakázání maker](./media/consuming-from-excel/enablecontent.png)
4. Jakmile jsou povolená makra, je vygenerována tabulku. Sloupce v modrém jsou požadovány jako vstup do webové služby RR nebo **parametrů**. Poznamenejte si výstup služby RR, **předpovězené hodnoty** zeleně. Když jsou vyplněny všechny sloupce daného řádku, sešit automaticky volá rozhraní API pro vyhodnocení a zobrazí skóre výsledky.

    ![Tabulka pro vstupy parametrů a výsledné předpovězené hodnoty](./media/consuming-from-excel/sampletable.png)
5. Ke stanovení skóre pro více než jeden řádek, jsou vytvářeny výplně druhý řádek s daty a předpovězeným hodnotám. I vložením několika řádků najednou.

Vám pomůže některou z funkcí aplikace Excel (grafů, doplňkem power map, podmíněné formátování, atd.) s předpovězeným hodnotám pomáhají vizualizovat data.

## <a name="sharing-your-workbook"></a>Sdílení vašeho sešitu
Makra pro práci klíč rozhraní API musí být součástí tabulky. To znamená, že by měly sdílet sešit pouze u entity nebo jednotlivce, které důvěřujete.

## <a name="automatic-updates"></a>Automatické aktualizace
Volání rozhraní RRS se provádí v těchto dvou případů:

1. První, když má řádek obsah ve všech svých **parametrech**
2. Kdykoliv se všechny **parametry** změní v řádku, který měl zadané všechny jeho **parametry** .