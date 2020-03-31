---
title: Využití webové služby v Excelu
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasické) usnadňuje volání webových služeb přímo z Excelu bez nutnosti psát jakýkoli kód.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 333ed411ab818cff77a7cba6c7de4f42c36f5b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218214"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Náročné Azure Machine Learning Studio (klasické) webové služby z Excelu

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klasické) usnadňuje volání webových služeb přímo z Excelu bez nutnosti psát jakýkoli kód.

Pokud používáte Excel 2013 (nebo novější) nebo Excel Online, doporučujeme použít [doplněk Excel Excelu](excel-add-in-for-web-services.md).



## <a name="steps"></a>Kroky
Publikujte webovou službu. [Kurz 3: Nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md) vysvětluje, jak to udělat. V současné době je funkce sešitu aplikace Excel podporována pouze pro služby požadavku a odpovědi, které mají jeden výstup (tj. jeden popisek hodnocení). 

Jakmile máte webovou službu, klikněte na sekci **WEBOVÉ SLUŽBY** na levé straně studia a vyberte webovou službu, kterou chcete využívat z aplikace Excel.

**Klasická webová služba**

1. Na kartě **DASHBOARD** pro webovou službu je řádek pro službu **REQUEST/RESPONSE.** Pokud tato služba měla jeden výstup, měli byste vidět **odkaz Stáhnout excelový sešit** v tomto řádku.

    ![Stažení excelového sešitu pomocí portálu webové služby Studio (klasické)](./media/consuming-from-excel/excellink.png)
2. Klikněte na **Stáhnout excelový sešit**.

**Nová webová služba**

1. Na portálu Azure Machine Learning Web Service vyberte **Využívat**.
2. Na stránce Spotřebovávají klikněte v části **Možnosti spotřeby webové služby** na ikonu Aplikace Excel.

**Použití sešitu**

1. Otevřete sešit.
2. Zobrazí se upozornění zabezpečení. klikněte na tlačítko **Povolit úpravy.**

    ![Povolení úprav k odebrání upozornění zabezpečení chráněného zobrazení](./media/consuming-from-excel/enableeditting.png)
3. Zobrazí se upozornění zabezpečení. Kliknutím na tlačítko **Povolit obsah** spusťte makra v tabulce.

    ![Povolit obsahu zavření upozornění zabezpečení zakázání makra](./media/consuming-from-excel/enablecontent.png)
4. Jakmile jsou makra povolena, je vygenerována tabulka. Modré sloupce jsou vyžadovány jako vstup do webové služby RRS nebo **PARAMETRY**. Všimněte si výstupu služby **RRS, PREDIKOVANÉ HODNOTY** zeleně. Po vyplnění všech sloupců pro daný řádek sesešit automaticky zavolá rozhraní API pro vyhodnocování a zobrazí výsledky skóre.

    ![Tabulka pro vstupy parametrů a výsledné předpokládané hodnoty](./media/consuming-from-excel/sampletable.png)
5. Chcete-li získat více než jeden řádek, vyplňte druhý řádek daty a jsou vytvořeny předpokládané hodnoty. Můžete dokonce vložit několik řádků najednou.

Můžete použít některou z funkcí aplikace Excel (grafy, power map, podmíněné formátování atd.) s předpovídanými hodnotami, které vám pomohou vizualizovat data.

## <a name="sharing-your-workbook"></a>Sdílení sešitu
Aby makra fungovala, musí být klíč rozhraní API součástí tabulky. To znamená, že sešit byste měli sdílet pouze s entitami nebo jednotlivci, kterým důvěřujete.

## <a name="automatic-updates"></a>Automatické aktualizace
Volání RRS se provádí v těchto dvou situacích:

1. Poprvé řádek má obsah ve všech svých **parametrech**
2. Kdykoli se některý z **parametrů** změní v řádku, který měl všechny jeho **parametry zadány.**