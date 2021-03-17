---
title: 'ML Studio (Classic): využívání webové služby v Excelu – Azure'
description: Azure Machine Learning Studio (Classic) usnadňuje volání webových služeb přímo z Excelu bez nutnosti psát jakýkoli kód.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e8e7404a2b643cf90866253e5a7453381b66f73e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517854"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Spotřebovávání webové služby Azure Machine Learning Studio (Classic) z Excelu

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (Classic) usnadňuje volání webových služeb přímo z Excelu bez nutnosti psát jakýkoli kód.

Pokud používáte Excel 2013 (nebo novější) nebo Excel Online, doporučujeme použít excelový [doplněk](excel-add-in-for-web-services.md)Excelu.



## <a name="steps"></a>Postup
Publikování webové služby. [Kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md) vysvětluje, jak to provést. V současné době je funkce excelového sešitu podporována pouze pro služby požadavku a odpovědi, které mají jeden výstup (tj. jeden popisek hodnocení). 

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
4. Jakmile jsou makra povolena, je vygenerována tabulka. Sloupce v modrém jsou požadovány jako vstup do webové služby RR nebo **parametrů**. Poznamenejte si výstup služby RR, **předpovězené hodnoty** zeleně. Po vyplnění všech sloupců pro daný řádek sešit automaticky zavolá rozhraní API pro bodování a zobrazí výsledky skóre.

    ![Tabulka pro vstupy parametrů a výsledné předpovězené hodnoty](./media/consuming-from-excel/sampletable.png)
5. Chcete-li vyhodnotit více než jeden řádek, vyplní druhý řádek daty a vytvoří se předpovězené hodnoty. Můžete dokonce vložit několik řádků najednou.

K vizualizaci dat můžete použít kteroukoli z funkcí aplikace Excel (grafy, Power map, podmíněné formátování atd.) s předpokládanými hodnotami.

## <a name="sharing-your-workbook"></a>Sdílení sešitu
Aby makra fungovala, musí být klíč rozhraní API součástí tabulky. To znamená, že byste měli sešit sdílet jenom s entitami/jednotlivci, kterým důvěřujete.

## <a name="automatic-updates"></a>Automatické aktualizace
Volání RR se provádí v těchto dvou situacích:

1. První, když má řádek obsah ve všech svých **parametrech**
2. Kdykoliv se všechny **parametry** změní v řádku, který měl zadané všechny jeho **parametry** .