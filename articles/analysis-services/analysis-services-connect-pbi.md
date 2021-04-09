---
title: Připojení k Azure Analysis Services s využitím Power BI | Microsoft Docs
description: Přečtěte si, jak se připojit k serveru Azure Analysis Services pomocí Power BI. Po připojení mohou uživatelé prozkoumat data modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ed0f3d379ca961c610958c073c27c6fc9583252b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500916"
---
# <a name="connect-with-power-bi"></a>Propojení s Power BI

Po vytvoření serveru v Azure a nasazení tabelárního modelu do něj budou uživatelé ve vaší organizaci připraveni se připojit a začít prozkoumat data. 

> [!TIP]
> Ujistěte se, že používáte nejnovější verzi [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Připojení v Power BI Desktopu

1. V Power BI Desktop klikněte na **získat data**  >    >  **databáze Azure Azure Analysis Services**.

2. Do pole **Server** zadejte název serveru. Nezapomeňte uvést úplnou adresu URL. například asazure://westcentralus.asazure.windows.net/advworks.

3. Pokud znáte název databáze tabulkového modelu nebo perspektivu, ke které se chcete připojit, v části **databáze** sem vložte. V opačném případě můžete toto pole nechat prázdné a vybrat databázi nebo perspektivu později.

4. Vyberte možnost připojení a pak stiskněte **připojit**. 

    Podporují se možnosti **připojit živě** i **importovat** . Doporučujeme však používat živá připojení, protože režim importu má určitá omezení. v průběhu importu může být ovlivněný výkon serveru. Také pokud se má model aktualizovat v služba Power BI, platí nastavení **Povolení přístupu z Power BI** pouze při výběru možnosti **připojit živě**.

5. Pokud se zobrazí výzva, zadejte přihlašovací údaje. 

   > [!NOTE]
   > Účty JEDNORÁZOVého hesla se nepodporují. 

6. V **navigátoru** rozbalte server, vyberte model nebo perspektivu, ke kterým se chcete připojit, a potom klikněte na **připojit**. Kliknutím na model nebo perspektivu zobrazíte všechny objekty pro toto zobrazení.

    Model se otevře v Power BI Desktop s prázdnou sestavou v zobrazení sestav. Seznam Pole zobrazuje všechny neskryté objekty modelu. Stav připojení se zobrazuje v pravém dolním rohu.

## <a name="connect-in-power-bi-service"></a>Připojit v Power BI (služba)

1. Vytvořte soubor Power BI Desktop s živým připojením k vašemu modelu na vašem serveru.
2. V [Power BI](https://powerbi.microsoft.com)klikněte na **načíst datové**  >  **soubory** a pak vyhledejte a vyberte soubor. pbix.

## <a name="see-also"></a>Viz také
[Připojení k Azure Analysis Services](analysis-services-connect.md)   
[Klientské knihovny](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)