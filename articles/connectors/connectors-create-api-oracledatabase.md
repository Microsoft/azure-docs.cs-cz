---
title: Připojení k databázi Oracle
description: Vkládání a správa záznamů pomocí api ORACLE Database REST API a aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789422"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Začínáme s konektorem Oracle Database

Pomocí konektoru Oracle Database můžete vytvářet organizační pracovní postupy, které používají data ve vaší existující databázi. Tento konektor se může připojit k místní databázi Oracle Database nebo k virtuálnímu počítači Azure s nainstalovanou databází Oracle Database. S tímto konektorem můžete:

* Vytvořte pracovní postup přidáním nového zákazníka do databáze zákazníků nebo aktualizací objednávky v databázi objednávek.
* Pomocí akcí můžete získat řádek dat, vložit nový řádek a dokonce odstranit. Pokud je například záznam vytvořen v aplikaci Dynamics CRM Online (aktivační událost), vložte řádek do databáze Oracle Database (akce). 

Tento článek ukazuje, jak používat konektor Oracle Database v aplikaci logiky.

## <a name="prerequisites"></a>Požadavky

* Podporované verze Oracle: 
    * Oracle 9 a novější
    * Klientský software Oracle 8.1.7 a novější

* Nainstalujte místní bránu dat. [Připojení k místním datům z aplikací logiky](../logic-apps/logic-apps-gateway-connection.md) uvádí kroky. Brána je potřeba pro připojení k místní databázi Oracle Database nebo k virtuálnímu počítači Azure s nainstalovanou databází Oracle DB. 

    > [!NOTE]
    > Místní brána dat funguje jako most a poskytuje zabezpečený přenos dat mezi místními daty (data, která není v cloudu) a aplikacemi logiky. Stejnou bránu lze použít s více službami a více zdrojů dat.Takže možná budete muset nainstalovat bránu pouze jednou.

* Nainstalujte klienta Oracle do počítače, ve kterém jste nainstalovali místní bránu dat.Nezapomeňte nainstalovat 64bitového zprostředkovatele dat Oracle pro rozhraní .NET od společnosti Oracle:  

  [64bitové součásti ODAC 12c verze 4 (12.1.0.2.4) pro Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Pokud klient Oracle není nainstalován, dojde k chybě při pokusu o vytvoření nebo použití připojení. Viz běžné chyby v tomto článku.


## <a name="add-the-connector"></a>Přidání konektoru

> [!IMPORTANT]
> Tento konektor nemá žádné aktivační události. Má jen činy. Takže při vytváření aplikace logiky, přidejte další aktivační událost pro spuštění aplikace logiky, jako je **například Plán - Opakování**nebo **Požadavek / Odpověď - Odpověď**. 

1. Na [webu Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky.

2. Na začátku aplikace logiky vyberte **aktivační událost Požadavek / Odpověď - Požadavek:** 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Vyberte **Uložit**. Při uložení se automaticky vygeneruje adresa URL požadavku. 

4. Vyberte **Nový krok** a potom **Přidat akci**. Zadáním `oracle` zadejte, chcete-li zobrazit dostupné akce: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Toto je také nejrychlejší způsob, jak zobrazit aktivační události a akce, které jsou k dispozici pro všechny konektory. Zadejte část názvu konektoru, `oracle`například . Návrhář uvádí všechny aktivační události a všechny akce. 

5. Vyberte jednu z akcí, například **Oracle Database - Get row**. Vyberte **Připojit přes místní bránu dat**. Zadejte název serveru Oracle, metodu ověřování, uživatelské jméno, heslo a vyberte bránu:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po připojení vyberte tabulku ze seznamu a zadejte ID řádku do tabulky. Musíte znát identifikátor tabulky. Pokud nevíte, obraťte se na správce oracle db `select * from yourTableName`a získat výstup z . Získáte tak identifikovatelné informace, které potřebujete k pokračování.

    V následujícím příkladu jsou data úlohy vrácena z databáze lidských zdrojů: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. V tomto dalším kroku můžete použít některý z dalších konektorů k vytvoření pracovního postupu. Pokud chcete otestovat získání dat od společnosti Oracle, pošlete si e-mail s daty Oracle pomocí jednoho z konektorů pro odesílání e-mailů, jako je Office 365 nebo Gmail. Pomocí dynamických tokenů z tabulky `Subject` Oracle `Body` vytvořte e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Uložte** aplikaci logiky a pak vyberte **Spustit**. Zavřete návrháře a podívejte se na historii spuštění stavu. Pokud se nezdaří, vyberte řádek neúspěšné zprávy. Návrhář se otevře a ukáže, který krok se nezdařil, a také zobrazí informace o chybě. Pokud se to podaří, měli byste obdržet e-mail s informacemi, které jste přidali.


### <a name="workflow-ideas"></a>Nápady pracovního postupu

* Chcete sledovat #oracle hashtag a umístit tweety do databáze, aby mohly být dotazovány a používány v jiných aplikacích. V aplikaci logiky `Twitter - When a new tweet is posted` přidejte aktivační událost a zadejte **#oracle** hashtag. Potom přidejte `Oracle Database - Insert row` akci a vyberte tabulku:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Zprávy jsou odesílány do fronty služby Service Bus. Chcete získat tyto zprávy a dát je do databáze. V aplikaci logiky `Service Bus - when a message is received in a queue` přidejte aktivační událost a vyberte frontu. Potom přidejte `Oracle Database - Insert row` akci a vyberte tabulku:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Běžné chyby

#### <a name="error-cannot-reach-the-gateway"></a>**Chyba**: Nelze dosáhnout brány

**Příčina**: Místní brána dat se nemůže připojit ke cloudu. 

**Zmírnění :** Ujistěte se, že brána běží v místním počítači, kde jste ji nainstalovali, a že se může připojit k Internetu.Doporučujeme neinstalovat bránu do počítače, který může být vypnutý nebo v režimu spánku.Můžete také restartovat místní službu brány dat (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Chyba**: Používaný zprostředkovatel je zastaralý: System.Data.OracleClient vyžaduje klientský software Oracle verze 8.1.7 nebo vyšší. Viz [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) instalace oficiálního poskytovatele.

**Příčina**: Sada Oracle client SDK není nainstalována v počítači, ve kterém je spuštěna místní brána dat.  

**Řešení**: Stáhněte a nainstalujte sadku Oracle client SDK do stejného počítače jako místní bránu dat.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Chyba**: Tabulka [Název_tabulky] nedefinuje žádné klíčové sloupce.

**Příčina**: Tabulka nemá žádný primární klíč.  

**Řešení**: Konektor databáze Oracle vyžaduje, aby byla použita tabulka se sloupcem primárního klíče.

#### <a name="currently-not-supported"></a>Momentálně není podporováno.

* Zobrazení 
* Každá tabulka se složenými klávesami
* Vnořené typy objektů v tabulkách
 
## <a name="connector-specific-details"></a>Podrobnosti specifické pro konektor

Zobrazit všechny aktivační události a akce definované v naparování a také zobrazit všechny limity v [podrobnostech konektoru](/connectors/oracle/). 

## <a name="get-some-help"></a>Získejte pomoc

[Fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) je skvělé místo, kde můžete klást otázky, odpovídat na otázky a zjistit, co dělají ostatní uživatelé logic apps. 

Můžete pomoci zlepšit Logic Apps a konektory hlasováním a [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish)odesláním své nápady na . 


## <a name="next-steps"></a>Další kroky
[Vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)a prozkoumejte dostupné konektory v logic aplikacích v [seznamu rozhraní API](apis-list.md).
