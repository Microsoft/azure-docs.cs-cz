---
title: Připojení k Azure Analysis Services s využitím Power BI | Microsoft Docs
description: Přečtěte si, jak se připojit k serveru Azure Analysis Services pomocí Power BI. Po připojení mohou uživatelé prozkoumat data modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573102"
---
# <a name="connect-with-power-bi"></a>Propojení s Power BI

Po vytvoření serveru v Azure a nasazení tabelárního modelu do něj budou uživatelé ve vaší organizaci připraveni se připojit a začít prozkoumat data. 

> [!TIP]
> Ujistěte se, že používáte nejnovější verzi [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Připojení v Power BI Desktopu

1. V Power BI Desktopu klikněte na **Získat data** > **Azure** > **Databáze Azure Analysis Services**.

2. Do pole **Server**zadejte název serveru. Nezapomeňte uvést úplnou adresu URL. například asazure://westcentralus.asazure.windows.net/advworks.

3. Pokud znáte název databáze tabulkového modelu nebo perspektivu, ke které se chcete připojit, v části **databáze**sem vložte. V opačném případě můžete toto pole nechat prázdné a vybrat databázi nebo perspektivu později.

4. Vyberte možnost připojení a pak stiskněte **připojit**. 

    Podporují se možnosti **připojit živě** i **importovat** . Doporučujeme však používat živá připojení, protože režim importu má určitá omezení. v průběhu importu může být ovlivněný výkon serveru. Také pokud se má model aktualizovat v služba Power BI, platí nastavení **Povolení přístupu z Power BI** pouze při výběru možnosti **připojit živě**.

5. Pokud se zobrazí výzva, zadejte přihlašovací údaje. 

6. V **navigátoru**rozbalte server, vyberte model nebo perspektivu, ke kterým se chcete připojit, a potom klikněte na **připojit**. Kliknutím na model nebo perspektivu zobrazíte všechny objekty pro toto zobrazení.

    Model se otevře v Power BI Desktop s prázdnou sestavou v zobrazení sestav. Seznam pole zobrazuje všechny neskryté objekty modelu. Stav připojení se zobrazuje v pravém dolním rohu.

## <a name="connect-in-power-bi-service"></a>Připojit v Power BI (služba)

1. Vytvořte soubor Power BI Desktop s živým připojením k vašemu modelu na vašem serveru.
2. V [Power BI](https://powerbi.microsoft.com)klikněte na **získat data** > **soubory**a pak vyhledejte a vyberte soubor. pbix.

## <a name="see-also"></a>Viz také
[Připojení k Azure Analysis Services](analysis-services-connect.md)   
[Klientské knihovny](analysis-services-data-providers.md)

