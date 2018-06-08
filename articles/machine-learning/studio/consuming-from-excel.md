---
title: Webová služba Machine Learning z Excelu využívat | Microsoft Docs
description: Využívají webové služby Azure Machine Learning z Excelu
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2018
ms.openlocfilehash: 14621e50a397bc1f1922a4c8fae638d6b42ab8ba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837061"
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Využívání webové služby Azure Machine Learning z Excelu
 Azure Machine Learning Studio usnadňuje volání webové služby přímo z aplikace Excel bez nutnosti psaní jakéhokoli kódu.

Pokud používáte aplikace Excel 2013 (nebo novější) nebo Excel Online, pak doporučujeme použít v aplikaci Excel [doplněk aplikace Excel](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Kroky
Publikování webové služby. [Tato stránka](walkthrough-5-publish-web-service.md) vysvětluje, jak to udělat. Funkce sešitu aplikace Excel se aktuálně podporuje jen pro služby požadavků a odpovědí, které mají jediného výstupu (tedy jediný vyhodnocování štítek). 

Až budete mít webovou službu, klikněte na **webové služby** části na levé straně nástroje studio a potom vyberte webovou službu využívat z Excelu.

**Classic webové služby**

1. Na **řídicí panel** kartě pro webovou službu je řádek pro **požadavků a odpovědí** služby. Pokud tato služba měli jediného výstupu, měli byste vidět **stáhnout sešitu aplikace Excel** odkaz v daném řádku.
   
    ![][1]
2. Klikněte na **stáhnout sešitu aplikace Excel**.

**Novou webovou službu**

1. Na portálu Azure Machine Learning webové služby, vyberte **spotřebě**.
2. Na stránce spotřebě v **webové služby spotřeba možnosti** části, klikněte na ikonu aplikace Excel.

**V práci se sešitem**

1. Otevřete sešit.
2. Zobrazí se upozornění zabezpečení; Klikněte na **povolit úpravy** tlačítko.
   
    ![][2]
3. Zobrazí se upozornění zabezpečení. Klikněte na **povolit obsah** tlačítko spustit makra tabulky.
   
    ![][3]
4. Jakmile jsou povolené makra, je generována tabulku. Sloupce v modré jsou požadované jako vstup do webovou službu RRS nebo **parametry**. Všimněte si výstup RRS služby **PŘEDPOVĚDĚT hodnoty** zeleně. Když jsou vyplněny všechny sloupce pro daný řádek, sešit automaticky zavolá rozhraní API vyhodnocování a zobrazí scored výsledky.
   
    ![][4]
5. Ke stanovení skóre více než jeden řádek, vznikají výplně druhý řádek s daty a předpovězené hodnoty. Několik řádků můžete vložit i najednou.

Žádnou z funkcí aplikace Excel (grafů, map napájení, podmíněného formátování atd.) můžete použít s předpovězené hodnoty můžete vizualizovat data.    

## <a name="sharing-your-workbook"></a>Sdílení sešitu
Makra pro práci musí být klíč rozhraní API součást tabulky. To znamená, že by měly sdílet sešit pouze s entit nebo jednotlivce, kterým důvěřujete.

## <a name="automatic-updates"></a>Automatické aktualizace
Volání na záznamy o prostředku se provádí v těchto dvou situacích:

1. První řádek má obsah ve všech jeho **parametry**
2. Kdykoli se některé z **parametry** změny v řádku, který měl všechny jeho **parametry** zadali.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
