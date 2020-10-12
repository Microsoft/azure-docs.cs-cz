---
title: Připojení k Azure Analysis Services s využitím Excelu | Microsoft Docs
description: Přečtěte si, jak se připojit k serveru Azure Analysis Services pomocí Excelu. Po připojení můžou uživatelé vytvářet kontingenční tabulky pro zkoumání dat.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 123e271ae1b83603d599b9ef0381e25b3c963def
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85361553"
---
# <a name="connect-with-excel"></a>Propojení s Excelem

Po vytvoření serveru a nasazení tabelárního modelu do něj mohou klienti připojit a začít prozkoumat data. 

## <a name="before-you-begin"></a>Než začnete

Účet, pomocí kterého se přihlašujete, musí patřit k roli databáze modelu s alespoň oprávněními ke čtení. Další informace najdete v článku o [ověřování a uživatelských oprávněních](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Připojit v Excelu

Připojení k serveru v Excelu je podporované pomocí získat data v Excelu 2016 a novějším. Připojení pomocí Průvodce importem tabulky v Power Pivot není podporováno. 

1. V Excelu na pásu karet **data** klikněte na **Načíst externí data**  >  **z jiných zdrojů**  >  **z Analysis Services**.

2. V Průvodci datovým připojením zadejte do pole **název serveru**název serveru včetně protokolu a identifikátoru URI. Například asazure://westcentralus.asazure.windows.net/advworks. Pak v části přihlašovací **údaje pro přihlášení**vyberte **použít následující uživatelské jméno a heslo**a potom zadejte uživatelské jméno organizace, například nancy@adventureworks.com a heslo.

    > [!IMPORTANT]
    > Pokud se přihlásíte pomocí účtu Microsoft, Live ID, Yahoo, Gmail atd. nebo se budete muset přihlásit pomocí služby Multi-Factor Authentication, ponechejte pole pro heslo prázdné. Po kliknutí na tlačítko Další se zobrazí výzva k zadání hesla. 

    ![Připojení z aplikace Excel přihlášení](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. V části **Vybrat databázi a tabulku**vyberte databázi a model nebo perspektivu a potom klikněte na **Dokončit**.
   
    ![Připojit z aplikace Excel vybrat model](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Viz také

[Klientské knihovny](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[Správa serveru](analysis-services-manage.md)     


