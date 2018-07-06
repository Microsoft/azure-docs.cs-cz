---
title: Připojte se k serveru FTP – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvářet, monitorovat a spravovat soubory na serveru FTP s Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/22/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 4355a767d2ecd500662cdf4522e8a7e12de86b80
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866147"
---
# <a name="get-started-with-the-ftp-connector"></a>Začínáme s konektorem serveru FTP
Konektor FTP můžete monitorovat, spravovat a vytvářet soubory na serveru FTP. 

Chcete-li použít [konektory](apis-list.md), musíte nejprve vytvořit aplikaci logiky. Můžete začít tak [vytvoření aplikace logiky teď](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>Připojte se k serveru FTP
Předtím, než aplikace logiky můžete přistupovat k libovolné službě, je nejprve potřeba vytvořit *připojení* ke službě. A [připojení](connectors-overview.md) poskytuje připojení mezi aplikace logiky a jiné služby.  

### <a name="create-a-connection-to-ftp"></a>Vytvoření připojení k serveru FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Použití aktivační procedury FTP
Trigger je událost, která umožňuje spustit pracovní postup definovaný v aplikaci logiky. [Další informace o aktivačních událostech](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> Konektor FTP vyžaduje server FTP, který je přístupný z Internetu a je nakonfigurován na provoz v PASIVNÍM režimu. Konektor FTP je také **není kompatibilní s implicitní FTPS (FTP přes protokol SSL)**. Konektor FTP podporuje pouze explicitní FTPS (FTP přes protokol SSL).  
> 
> 

V tomto příkladu, můžu se ukazují, jak používat **FTP – když se přidá nebo upraví soubor** aktivovat Pokud chcete spustit pracovní postup aplikace logiky, pokud je přidána do, nebo změny souboru na serveru FTP. V příklad organizace může pomocí této aktivační události k monitorování složky FTP pro nové soubory, které představují objednávek zákazníků.  Můžete pak použít akci konektor FTP, jako **získat obsah souboru** má být získán obsah objednávky pro další zpracování a úložiště v databázi objednávek.

1. Zadejte *ftp* do vyhledávacího pole v návrháři pro logic apps zvolte **FTP – když se přidá nebo upraví soubor** aktivační událost   
   ![Obrázek triggeru FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   **Při přidání nebo změně souboru** otevře se ovládací prvek  
   ![Obrázek triggeru FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Vyberte **...**  nachází na pravé straně ovládacího prvku. Otevře se ovládací prvek pro výběr složky  
   ![Obrázek triggeru FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Vyberte **>** (šipka doprava) a procházením vyhledejte složku, která chcete monitorovat pro nové nebo upravené soubory. Vyberte složku a Všimněte si, že složka se nyní zobrazí v **složky** ovládacího prvku.  
   ![Obrázek triggeru FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

V tomto okamžiku byla nakonfigurována svou aplikaci logiky s triggerem, který začne upravovat nebo v konkrétní složce FTP vytvoří soubor spustit z jiné triggery a akce v pracovním postupu. 

> [!NOTE]
> Pro aplikaci logiky, která byla plně funkční musí obsahovat aspoň jeden trigger a jednu akci. Postupujte podle kroků v další části přidáte akci.  
> 
> 

## <a name="use-a-ftp-action"></a>Použití akce FTP
Akce je operace prováděné pracovním postupu definovaném v aplikaci logiky. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Teď, když přidáte trigger, přidejte akci, která se zobrazí obsah souboru nové nebo upravené zjištěných aplikací aktivační událost pomocí těchto kroků.    

1. Vyberte **+ nový krok** přidáte akci, která má být získán obsah souboru na FTP server  
2. Vyberte **přidat akci** odkaz.  
   ![Obrázek akce FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Zadejte *FTP* k vyhledání všech akcí souvisejících s FTP.
4. Vyberte **FTP – získat obsah souboru** jako akci chcete provést v případě nový nebo změněný soubor naleznete ve složce serveru FTP.      
   ![Obrázek akce FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   **Získat obsah souboru** ovládací prvek se otevře. **Poznámka:**: zobrazí výzva k autorizaci aplikace logiky pro přístup k účtu FTP server, pokud jste tak dosud neučinili.  
   ![Obrázek akce FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Vyberte **soubor** ovládacího prvku (prázdné znaky nacházel pod ** soubor ***). Tady můžete různé vlastnosti ze souboru nové nebo upravené nalezena na serveru FTP.  
6. Vyberte **obsah souboru** možnost.  
   ![Obrázek akce FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Ovládací prvek se aktualizuje, což indikuje, že **FTP – získat obsah souboru** akce se zobrazí *obsah souboru* nové nebo upravené souboru na serveru FTP.      
   ![Obrázek akce FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Uložte si práci potom přidejte soubor do složky serveru FTP na testování pracovního postupu.    

V tomto okamžiku byla nakonfigurována aplikace logiky s triggerem k monitorování složky na serveru FTP a započít pracovní postup, pokud se najde soubor nový nebo upraveného souboru na serveru FTP. 

Aplikace logiky také nakonfigurovaný s akcí získat obsah souboru nové nebo změněné.

Teď můžete přidat další akce, jako [SQL Server – vložit řádek](connectors-create-api-sqlazure.md) akce Vložit obsah souboru nové nebo upravené do tabulky databáze SQL.  

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Další kroky
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

