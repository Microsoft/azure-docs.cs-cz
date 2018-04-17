---
title: Připojení ke službě Azure Analysis Services s Power BI | Microsoft Docs
description: Zjistěte, jak se připojit k serveru Azure Analysis Services pomocí Power BI.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 03e654b3d03564d11db9156e6fd5793bbd78c85d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="connect-with-power-bi"></a>Připojit k Power BI

Jakmile jste vytvořili serveru v Azure a nasadili do ní tabulkový model, jste připravení připojit a začít zkoumat data uživatele ve vaší organizaci. 

> [!TIP]
> Nezapomeňte použít nejnovější verzi [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Připojení v Power BI Desktop

1. V Power BI Desktop, klikněte na **načíst Data** > **Azure** > **databáze Azure Analysis Services**.

2. V **Server**, zadejte název serveru. Nezapomeňte zahrnout úplnou adresu URL; například asazure://westcentralus.asazure.windows.net/advworks.

3. V **databáze**, pokud znáte název databázi tabulkového modelu nebo perspektivy, kterou chcete připojit, vložte jej zde. Jinak můžete ponechat toto pole prázdné a vybrat databázi nebo perspektivy později.

4. Vyberte možnost připojení a potom stiskněte klávesu **Connect**. 

    Obě **připojit za provozu** a **Import** možnosti jsou podporovány. Doporučujeme však, že používáte připojení za provozu, protože režim Import mají určitá omezení; zejména server může mít dopad na výkon při importu. Také v případě, že model je nutné je aktualizovat v rámci služby Power BI **povolit přístup z Power BI** nastavení platí pouze při výběru **připojit za provozu**.

5. Po zobrazení výzvy zadejte své přihlašovací údaje. 

6. V **Navigátor**, rozbalte server a pak vyberte modelu nebo perspektivy, kterou chcete připojit k a pak klikněte na tlačítko **Connect**. Klikněte na modelu nebo perspektivy k zobrazení všech objektů v tomto zobrazení.

    Model se otevře v Power BI Desktop s prázdnou sestavou v zobrazení sestavy. V seznamu polí zobrazí všechny objekty modelu nebyla skrytá. Stav připojení je zobrazen v pravém dolním rohu.

## <a name="connect-in-power-bi-service"></a>Připojení v Power BI (služba)

1. Vytvořte soubor Power BI Desktop, která má aktivní připojení k vašemu modelu na vašem serveru.
2. V [Power BI](https://powerbi.microsoft.com), klikněte na tlačítko **načíst Data** > **soubory**, vyhledejte a vyberte soubor .pbix.



## <a name="see-also"></a>Další informace najdete v tématech
[Připojení ke službě Azure Analysis Services](analysis-services-connect.md)   
[Knihovny klienta](analysis-services-data-providers.md)

