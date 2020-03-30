---
title: Připojení ke službě Azure Analysis Services pomocí Excelu | Dokumenty společnosti Microsoft
description: Zjistěte, jak se připojit k serveru Azure Analysis Services pomocí Excelu. Po připojení mohou uživatelé vytvářet kontingenční tabulky a zkoumat data.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb76accfef94327386d0ff58d003782a9fe3b0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573150"
---
# <a name="connect-with-excel"></a>Propojení s Excelem

Jakmile vytvoříte server a nasadíte do něj tabulkový model, klienti se mohou připojit a začít zkoumat data. 

## <a name="before-you-begin"></a>Než začnete

Účet, pomocí kterých se přihlašujete, musí patřit do role databáze modelu s oprávněními ke čtení alespoň. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Připojení v Excelu

Připojení k serveru v Excelu je podporované pomocí funkce Získat data v Excelu 2016 a novějším. Připojení pomocí Průvodce importem tabulky v PowerPivotu není podporováno. 

1. V Excelu klikněte na pásu karet **Data** na **Načíst externí data** > **z jiných zdrojů** > **ze služby Analysis Services**.

2. V Průvodci datovým připojením zadejte v **názvu serveru**název serveru včetně protokolu a identifikátoru URI. Například asazure://westcentralus.asazure.windows.net/advworks. Potom v **přihlašovacích pověřeních**vyberte **použít následující uživatelské jméno a heslo a**zadejte například nancy@adventureworks.comuživatelské jméno organizace a heslo.

    > [!IMPORTANT]
    > Pokud se přihlásíte pomocí účtu Microsoft, Live ID, Yahoo, Gmailu atd., nebo se musíte přihlásit pomocí vícefaktorového ověřování, ponechte pole s heslem prázdné. Po klepnutí na tlačítko Další budete vyzváni k zadání hesla. 

    ![Připojení z přihlášení k Excelu](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. V **části Vybrat databázi a tabulku**vyberte databázi a model nebo perspektivu a klepněte na tlačítko **Dokončit**.
   
    ![Připojení z vybraného modelu Excelu](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Viz také

[Klientské knihovny](analysis-services-data-providers.md)   
[Správa serveru](analysis-services-manage.md)     


