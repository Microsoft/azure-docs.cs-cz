---
title: Připojení ke službě Azure Analysis Services v aplikaci Excel | Microsoft Docs
description: Zjistěte, jak se připojit k serveru Azure Analysis Services pomocí aplikace Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6585648cbb461f52a22bc6ea239c0899caa2c2e7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597259"
---
# <a name="connect-with-excel"></a>Propojit s Excelem

Jakmile jste vytvořili serveru a nasadili do ní tabulkový model, mohou klienti připojit a začít zkoumat data. 

## <a name="before-you-begin"></a>Než začnete
Účet, který protokolu se musí patřit do role databáze modelu s alespoň oprávnění ke čtení. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Připojení v aplikaci Excel

Připojení k serveru v aplikaci Excel pomocí příkazu Get Data v aplikaci Excel 2016 podporuje. Připojení pomocí Průvodce importem tabulky v doplňku Power Pivot není podporováno. 

**Pokud chcete v aplikaci Excel 2016 připojit**

1. V aplikaci Excel 2016, na **Data** pásu karet, klikněte na tlačítko **načíst externí Data** > **z jiných zdrojů** > **ze služby Analysis Services** .

2. V průvodci připojení dat v **název serveru**, zadejte název serveru, včetně protokolu a identifikátor URI. Například asazure://westcentralus.asazure.windows.net/advworks. Potom v **přihlašovací údaje**, vyberte **použít následující uživatelské jméno a heslo**a pak zadejte název organizační uživatele, například nancy@adventureworks.coma heslo.

    > [!IMPORTANT]
    > Pokud přihlásit Account Microsoft Live ID, Yahoo, Gmail, atd., nebo musíte se přihlásit pomocí služby Multi-Factor authentication, nechejte pole heslo prázdné. Zobrazí se výzva k zadání hesla po kliknutí na tlačítko Další. 

    ![Připojení z aplikace Excel přihlášení](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. V **vybrat databázi a tabulku**, vyberte databázi a modelu nebo perspektivy a pak klikněte na tlačítko **Dokončit**.
   
    ![Připojení z vyberte model aplikace Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Další informace najdete v tématech
[Knihovny klienta](analysis-services-data-providers.md)   
[Správa serveru](analysis-services-manage.md)     


