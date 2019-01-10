---
title: Připojení ke službě Azure Analysis Services v aplikaci Excel | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k serveru Azure Analysis Services pomocí aplikace Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5c46d4e4d23744cf07ccf7857a33990bf405a6a1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189010"
---
# <a name="connect-with-excel"></a>Propojit s Excelem

Jakmile máte vytvořený server a tabulkový model nasadili do ní, klienti se můžete připojit a začít zkoumat data. 

## <a name="before-you-begin"></a>Před zahájením

Účet, který jste přihlášení musí patřit do role databáze modelu s minimálně oprávnění ke čtení. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Připojení v aplikaci Excel

Připojení k serveru v aplikaci Excel je podporováno pomocí získat Data v Excelu 2016 nebo novější. Připojení pomocí Průvodce importem tabulky v Power Pivotu se nepodporuje. 

1. V aplikaci Excel na **Data** pásu karet, klikněte na tlačítko **načíst externí Data** > **z jiných zdrojů** > **ze služby Analysis Services**.

2. V průvodci připojení dat v **název serveru**, zadejte název serveru, včetně protokolu a identifikátor URI. Například asazure://westcentralus.asazure.windows.net/advworks. Potom v **přihlašovací údaje**vyberte **použít následující uživatelské jméno a heslo**a pak zadejte název uživatele v organizaci, například nancy@adventureworks.coma heslo.

    > [!IMPORTANT]
    > Pokud se přihlaste se pomocí Microsoft Account, Live ID, Yahoo, Gmail, atd., nebo musíte se přihlásit pomocí služby Multi-Factor authentication, ponechejte pole pro heslo prázdné. Zobrazí se výzva k zadání hesla po kliknutí na tlačítko Další. 

    ![Připojení z aplikace Excel přihlášení](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. V **vybrat databázi a tabulku**, vyberte databázi a model nebo perspektivu a potom klikněte na **Dokončit**.
   
    ![Připojení z aplikace Excel vyberte model](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Další informace najdete v tématech

[Klientské knihovny](analysis-services-data-providers.md)   
[Správa serveru](analysis-services-manage.md)     


