---
title: Připojení ke službám Azure Analysis Services pomocí Power BI | Dokumenty společnosti Microsoft
description: Přečtěte si, jak se připojit k serveru Azure Analysis Services pomocí Power BI. Po připojení mohou uživatelé prozkoumat data modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6205c4189abfefc2ee9c4a273ebfd6773ea609b6
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411358"
---
# <a name="connect-with-power-bi"></a>Propojení s Power BI

Jakmile vytvoříte server v Azure a nasadíte do něj tabulkový model, uživatelé ve vaší organizaci jsou připraveni se připojit a začít zkoumat data. 

> [!TIP]
> Nezapomeňte použít nejnovější verzi [Power BI Desktopu](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Připojení v Power BI Desktopu

1. V Desktopu Power BI klikněte na **Získat data** > **databáze** > **Azure Analysis Services**.

2. Do **pole Server**zadejte název serveru. Nezapomeňte uvést úplnou adresu URL. například asazure://westcentralus.asazure.windows.net/advworks.

3. Pokud v **databázi**znáte název databáze tabulkového modelu nebo perspektivy, ke které se chcete připojit, vložte jej sem. V opačném případě můžete toto pole ponechat prázdné a později vybrat databázi nebo perspektivu.

4. Vyberte možnost připojení a stiskněte **klávesu Connect**. 

    Podporovány jsou možnosti **Připojit živé** i **Importovat.** Doporučujeme však používat živá připojení, protože režim importu má určitá omezení. zejména výkon serveru může být ovlivněnběhem importu. Pokud se má model aktualizovat ve službě Power BI, nastavení **Povolit přístup z Power BI** se použije jenom při volbě Připojit **live**.

5. Po zobrazení výzvy zadejte své přihlašovací údaje. 

6. V **navigátoru**rozbalte server, vyberte model nebo perspektivu, ke které se chcete připojit, a klepněte na tlačítko **Připojit**. Kliknutím na model nebo perspektivu zobrazíte všechny objekty pro tento pohled.

    Model se otevře v Power BI Desktop u prázdné sestavy v zobrazení sestavy. Seznam Pole zobrazuje všechny neskryté objekty modelu. Stav připojení se zobrazuje v pravém dolním rohu.

## <a name="connect-in-power-bi-service"></a>Připojení v Power BI (služba)

1. Vytvořte soubor Power BI Desktop, který má živé připojení k modelu na serveru.
2. V [Power BI](https://powerbi.microsoft.com)klikněte na Získat **datové** > **soubory**a pak vyhledejte a vyberte soubor .pbix.

## <a name="see-also"></a>Viz také
[Připojení ke službám Azure Analysis Services](analysis-services-connect.md)   
[Klientské knihovny](analysis-services-data-providers.md)

