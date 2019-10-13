---
title: Připojení k Azure Analysis Services s využitím Excelu | Microsoft Docs
description: Přečtěte si, jak se připojit k serveru Azure Analysis Services pomocí Excelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1ba6864d0bab460b270f6f27acced57d7943a17a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295328"
---
# <a name="connect-with-excel"></a>Propojit s Excelem

Po vytvoření serveru a nasazení tabelárního modelu do něj mohou klienti připojit a začít prozkoumat data. 

## <a name="before-you-begin"></a>Než začnete

Účet, pomocí kterého se přihlašujete, musí patřit k roli databáze modelu s alespoň oprávněními ke čtení. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Připojit v Excelu

Připojení k serveru v Excelu je podporované pomocí získat data v Excelu 2016 a novějším. Připojení pomocí Průvodce importem tabulky v Power Pivot není podporováno. 

1. V Excelu na pásu karet **data** klikněte na **načíst externí data** > **z jiných zdrojů** > **z Analysis Services**.

2. V Průvodci datovým připojením zadejte do pole **název serveru**název serveru včetně protokolu a identifikátoru URI. Například asazure://westcentralus.asazure.windows.net/advworks. Pak v části přihlašovací **údaje pro přihlášení**vyberte **použít následující uživatelské jméno a heslo**a zadejte uživatelské jméno organizace, například nancy@adventureworks.com a heslo.

    > [!IMPORTANT]
    > Pokud se přihlásíte pomocí účtu Microsoft, Live ID, Yahoo, Gmail atd. nebo se budete muset přihlásit pomocí služby Multi-Factor Authentication, ponechejte pole pro heslo prázdné. Po kliknutí na tlačítko Další se zobrazí výzva k zadání hesla. 

    ![Připojení z aplikace Excel přihlášení](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. V části **Vybrat databázi a tabulku**vyberte databázi a model nebo perspektivu a potom klikněte na **Dokončit**.
   
    ![Připojit z aplikace Excel vybrat model](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Další informace najdete v tématech

[Klientské knihovny](analysis-services-data-providers.md)   
[Správa serveru](analysis-services-manage.md)     


