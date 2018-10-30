---
title: Připojení k databázi Oracle – Azure Logic Apps | Dokumentace Microsoftu
description: Vložit a ke správě záznamů pomocí rozhraní REST API Oracle Database a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 78ac8f83bceef88e89edefa0eececb058e8efe2b
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230229"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Začínáme s konektorem databáze Oracle

Pomocí konektoru rozhraní Oracle Database, můžete vytvořit organizační pracovních postupů, které používají data ve stávající databázi. Tento konektor můžou připojit k databázi Oracle místní nebo virtuální počítač Azure se k databázi Oracle nainstalovanou. Pomocí tohoto konektoru můžete:

* Vytvoření pracovního postupu přidáním nového zákazníka na databázi zákazníků nebo aktualizací objednávky v databázi objednávek.
* Pomocí akce získat řádek dat vložte nový řádek a dokonce i odstranit. Například při vytvoření záznamu v Dynamics CRM Online (aktivační procedura), potom vložte řádek v databázi Oracle (akce). 

Tento článek ukazuje, jak použít konektor Oracle Database v aplikaci logiky.

## <a name="prerequisites"></a>Požadavky

* Podporované verze Oracle: 
    * Oracle 9 a novější
    * Klientský software Oracle 8.1.7 a novější

* Instalace místní brány dat. [Připojení k místním datům z aplikace logiky](../logic-apps/logic-apps-gateway-connection.md) jsou uvedené kroky. Brána se nejde připojit k databázi Oracle v místním nebo Virtuálním počítači Azure s Oracle DB nainstalovaný. 

    > [!NOTE]
    > Místní brána dat funguje jako most a poskytuje zabezpečený přenos dat mezi místními daty (data, která nejsou v cloudu) a logic apps. S několika službami a více zdrojů dat je možné stejnou bránu. Proto může stačí jednou nainstalovat bránu.

* Na počítači, kam jste nainstalovali na místní bránu dat instalace klienta Oracle. Nezapomeňte nainstalovat 64bitového poskytovatele dat Oracle pro .NET ze Oracle:  

  [64bitové součásti ODAC 12c verze 4 (12.1.0.2.4) pro Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Pokud není klient Oracle nainstalovaný, dojde k chybě při pokusu o vytvoření nebo připojení. V tématu běžné chyby v tomto článku.


## <a name="add-the-connector"></a>Přidat konektor

> [!IMPORTANT]
> Tento konektor nemá žádné aktivační události. Má pouze akce přechodu. Takže když vytvoříte aplikaci logiky, přidat další trigger spustit aplikaci logiky, například **plán – opakování**, nebo **žádosti / odpověď – odpověď**. 

1. V [webu Azure portal](https://portal.azure.com), vytvoření prázdné aplikace logiky.

2. Při spuštění aplikace logiky, vyberte **požadavku / odpovědi – žádost o** aktivační události: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Vyberte **Uložit**. Při ukládání, adresa URL požadavku není automaticky vygenerován. 

4. Vyberte **nový krok**a vyberte **přidat akci**. Zadejte `oracle` zobrazíte dostupné akce: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Také je to nejrychlejší způsob, jak zobrazit triggery a akce, které jsou k dispozici pro konektory. Zadejte v části název konektoru, třeba `oracle`. Návrhář jsou uvedeny žádné aktivační události a všechny akce. 

5. Vyberte jednu z akcí, jako je například **Oracle Database - Get řádek**. Vyberte **připojit přes místní bránu dat**. Zadejte název serveru Oracle, metodu ověřování, uživatelské jméno, heslo a vyberte bránu:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Jakmile budete připojeni, vyberte tabulku ze seznamu a zadejte ID řádku do tabulky. Je potřeba vědět identifikátor do tabulky. Pokud si nejste jisti, obraťte se na správce systému Oracle DB a získat výstup z `select * from yourTableName`. To poskytuje údaje, které potřebujete, aby bylo možné pokračovat.

    V následujícím příkladu se vrací data úlohy z databáze lidských zdrojů: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. V tomto kroku můžete použít některý z jiných konektorů k sestavení pracovního postupu. Pokud chcete otestovat získávání dat od Oraclu, pak pošlete sami sobě e-mail s Oracle data pomocí jedné z e-mailové konektory odeslat, odpovídající Office 365 nebo Gmail. Použití dynamické tokenů z tabulky Oracle k sestavení `Subject` a `Body` e-mailu:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Uložit** vaší aplikaci logiky a pak vyberte **spustit**. Zavřít návrháře a podívejte se na spuštění historie stavu. Pokud selže, vyberte řádek zprávy se nezdařilo. Otevře se Návrhář a ukazuje, které krok se nepovedlo a také zobrazuje informace o této chybě. Pokud se aktivace podaří, měli byste obdržet e-mail s informací, které jste přidali.


### <a name="workflow-ideas"></a>Nápady pracovního postupu

* Chcete monitorovat pomocí hashtagu #oracle a vložit tweety, které se v databázi tak, že se jde dotazovat a použít uvnitř jiných aplikací. V aplikaci logiky, přidejte `Twitter - When a new tweet is posted` aktivovat a zadejte **#oracle** hashtagem. Pak přidejte `Oracle Database - Insert row` akce a vyberte vaše tabulka:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Zprávy jsou odesílány do fronty služby Service Bus. Chcete získat tyto zprávy a umístit je do databáze. V aplikaci logiky, přidejte `Service Bus - when a message is received in a queue` aktivovat a vyberte frontu. Pak přidejte `Oracle Database - Insert row` akce a vyberte vaše tabulka:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Běžné chyby

#### <a name="error-cannot-reach-the-gateway"></a>**Chyba**: Nelze navázat spojení s bránou

**Příčina**: místní brána dat není možné se připojit ke cloudu. 

**Zmírnění dopadů**: Ujistěte se, že je brána spuštěna na místním počítači, kde jste nainstalovali, a že se může připojit k Internetu.  Doporučujeme vám, Ne instalaci brány na počítači, který může být vypnuté nebo v režimu spánku. Můžete také restartovat místní služba brány dat (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Chyba**: použitý zprostředkovatel je zastaralý: ' System.Data.OracleClient vyžaduje software klienta Oracle version 8.1.7 nebo vyšší. ". Zobrazit [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) a nainstalujte si oficiálního zprostředkovatele.

**Příčina**: Sada SDK není nainstalovaná na počítači, kde je spuštěná na místní bránu dat klienta Oracle.  

**Rozlišení**: stáhnout a nainstalovat na stejném počítači jako místní brána dat sada SDK klienta Oracle.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Chyba**: tabulka '[Tablename]' nedefinuje žádné klíčové sloupce

**Příčina**: tabulka nemá primární klíč.  

**Rozlišení**: konektor databáze Oracle vyžaduje použít tabulku se sloupcem primárního klíče.

#### <a name="currently-not-supported"></a>Aktuálně nejsou podporovány

* Zobrazení a uložených procedur 
* Všechny tabulky pomocí složených klíčů
* Vnořené typy objektu v tabulkách
 
## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/oracle/). 

## <a name="get-some-help"></a>Získat pomoc

[Fóru Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) je skvělé místo, kde můžete klást otázky, odpovídat na otázky a poučit jiných uživatelů Logic Apps se. 

Vám může pomoct vylepšit Logic Apps a konektorů hlasování a odešlete své nápady na [ https://aka.ms/logicapps-wish ](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)a projděte si dostupné konektory v Logic Apps na [rozhraní API seznamu](apis-list.md).
