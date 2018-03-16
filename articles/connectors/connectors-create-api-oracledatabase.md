---
title: "Přidejte konektor databáze Oracle do Azure Logic Apps | Microsoft Docs"
description: "Použití konektoru databáze Oracle v aplikaci logiky"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: estfan; ladocs
ms.openlocfilehash: 57931b6c2f96debeb9ab7f1a3b6700f23e7d2a7e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-oracle-database-connector"></a>Začínáme s konektorem databáze Oracle

Pomocí konektoru databáze Oracle, vytvoření organizační pracovní postupy, které pomocí data v existující databázi. Tento konektor se může připojit k databázi Oracle místní, nebo virtuální počítač Azure s nainstalovaná databáze Oracle. Tento konektor můžete:

* Vytvořte pracovní postup přidáním nového zákazníka k databázi zákazníků nebo aktualizaci pořadí, v databázi objednávky.
* Pomocí akcí pro získání řádku dat, vložit nový řádek a i odstranění. Například v aplikaci Dynamics CRM Online (aktivační událost) je vytvořen záznam, pak vložte řádek v databázi Oracle (akce). 

Tento článek ukazuje, jak používat konektor databáze Oracle v aplikaci logiky.

## <a name="prerequisites"></a>Požadavky

* Podporované verze Oracle: 
    * Oracle 9 a novějším
    * Klientský software Oracle 8.1.7 a novější

* Nainstalujte bránu dat na místě. [Připojit k místním datům z aplikace logiky](../logic-apps/logic-apps-gateway-connection.md) jsou uvedené kroky. Brána je vyžadován pro připojení k databázi Oracle místní, nebo nainstalovat virtuální počítač Azure s databáze Oracle. 

    > [!NOTE]
    > Bránu dat místní funguje jako mostu a poskytuje přenos zabezpečených dat mezi místními daty (data, která není v cloudu) a aplikace logiky. S více služeb a více zdrojů dat lze použít stejné bráně. Ano jenom musíte nainstalovat jednou bránu.

* Na počítači, kam jste nainstalovali bránu dat na místě instalace klienta Oracle. Nezapomeňte nainstalovat 64-bit poskytovatele dat Oracle pro .NET z databáze Oracle:  

  [64-bit ODAC 12c verze 4 (12.1.0.2.4) pro Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Pokud není nainstalován Klient Oracle, dojde k chybě při pokusu o vytvoření nebo použití připojení. Najdete běžné chyby v tomto článku.


## <a name="add-the-connector"></a>Přidejte konektor

> [!IMPORTANT]
> Tento konektor nemá žádné aktivační události. Má pouze akce. Proto při vytváření aplikace logiky, přidejte další aktivační události spusťte aplikaci logiky, jako **plán - opakování**, nebo **požadavku nebo odpovědi - odpovědi**. 

1. V [portál Azure](https://portal.azure.com), vytvoření aplikace logiky prázdné.

2. Na začátku aplikace logiky, vyberte **požadavku nebo odpovědi - požadavek** aktivační události: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Vyberte **Uložit**. Při ukládání, se automaticky vygeneroval adrese URL žádosti. 

4. Vyberte **nový krok**a vyberte **přidat akci**. Zadejte `oracle` zobrazte dostupné akce: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Toto je také nejrychlejší způsob, jak zobrazit triggery a akce, které jsou k dispozici pro všechny konektory. Zadejte v části název konektoru, například `oracle`. Návrhář uvádí žádné aktivační události a všechny akce. 

5. Vyberte jednu z akcí, jako například **Oracle Database - Get řádek**. Vyberte **připojit prostřednictvím místní brána dat**. Zadejte název serveru Oracle, metodu ověřování, uživatelské jméno, heslo a vyberte bránu:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po připojení, vyberte tabulku ze seznamu a zadejte ID řádek do tabulky. Je třeba vědět identifikátor do tabulky. Pokud si nejste jisti, obraťte se na správce databáze Oracle a získat výstup `select * from yourTableName`. To vám dává osobní údaje, které potřebujete, aby bylo možné pokračovat.

    V následujícím příkladu se vrací data úlohy z databáze lidských zdrojů: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. V tomto kroku další všechny ostatní konektory můžete použít k vytvoření pracovního postupu. Pokud chcete testovat získávání dat z databáze Oracle, pošlete sami e-mailu s daty Oracle pomocí některého z konektorů e-mailu odesílání takové Office 365 nebo z Gmailu. Použít k vytvoření dynamických tokeny z tabulky Oracle `Subject` a `Body` vašeho e-mailu:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Uložit** vaší aplikace logiky a potom vyberte **spustit**. Zavřít návrháře a podívejte se na spuštění historie stavu. Pokud se nezdaří, vyberte řádek zpráv se nezdařilo. Návrhář otevře a ukáže vám, který krok se nezdařila a také zobrazuje informace o chybě. Pokud se aktivace podaří, měli byste obdržet e-mail s informací, které jste přidali.


### <a name="workflow-ideas"></a>Nápady pracovního postupu

* Chcete monitorovat #oracle hashtag a umístí tweetů v databázi, mohou být dotazována a používaných v rámci jiné aplikace. V aplikaci logiky, přidejte `Twitter - When a new tweet is posted` aktivovat a zadejte **#oracle** hashtag. Poté, přidejte `Oracle Database - Insert row` akce a vyberte tabulku:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Zprávy jsou odesílány do fronty Service Bus. Chcete získat tyto zprávy a umístí je do databáze. V aplikaci logiky, přidejte `Service Bus - when a message is received in a queue` aktivovat a vyberte frontu. Poté, přidejte `Oracle Database - Insert row` akce a vyberte tabulku:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Běžné chyby

#### <a name="error-cannot-reach-the-gateway"></a>**Chyba**: Nelze kontaktovat bránu.

**Příčina**: místní data brána není možné se připojit ke cloudu. 

**Zmírnění dopadů**: Zkontrolujte, že vaše brána je spuštěný na místním počítači, kde jste ho nainstalovali, a který se může připojit k Internetu.  Doporučujeme není bránu nainstalovat na počítač, který může být vypnutý nebo v režimu spánku. Můžete také restartovat službu brány dat místní (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Chyba**: použitý zprostředkovatel je zastaralý: ' System.Data.OracleClient vyžaduje Oracle verze klientského softwaru 8.1.7 nebo vyšší. ". V tématu [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) a nainstalujte si oficiálního zprostředkovatele.

**Příčina**: Sada SDK není nainstalovaná na počítači, kde je spuštěna místní brána dat klienta Oracle.  

**Řešení**: Stáhněte a nainstalujte klienta Oracle SDK na stejném počítači jako místní brána data gateway.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Chyba**: Tabulka [Tablename] nedefinuje žádné klíčové sloupce

**Příčina**: tabulka nemá žádné primární klíč.  

**Řešení**: databáze Oracle connector vyžaduje použít tabulku se sloupcem primárního klíče.

#### <a name="currently-not-supported"></a>Aktuálně není podporována

* Zobrazení a uložených procedur 
* Všechny tabulky s složené klíče
* Vnořené typy objektů v tabulkách
 
## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/oracle/). 

## <a name="get-some-help"></a>Získat pomoc

[Fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) je skvělým místem klást otázky, odpovědi na otázky a zjistit, co dělají jiní uživatelé Logic Apps. 

Vám může pomoct zlepšit konektory a aplikace logiky hlasování a odesílání vašich nápadů v [ http://aka.ms/logicapps-wish ](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)a seznamte se s dostupných konektorů v Logic Apps v [rozhraní API seznamu](apis-list.md).
