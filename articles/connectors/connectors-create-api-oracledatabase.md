---
title: Připojení k Oracle Database
description: Vkládání a Správa záznamů pomocí Oracle Database rozhraní REST API a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674812"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Začínáme s konektorem Oracle Database

Pomocí konektoru Oracle Database vytvoříte organizační pracovní postupy, které používají data ve stávající databázi. Tento konektor se může připojit k místnímu Oracle Database nebo virtuálnímu počítači Azure s nainstalovaným Oracle Database. Pomocí tohoto konektoru můžete:

* Sestavte pracovní postup přidáním nového zákazníka do databáze zákazníků nebo aktualizací objednávky v databázi objednávek.
* Použijte akce pro získání řádku dat, vložení nového řádku a dokonce i odstranění. Například když se v Dynamics CRM Online vytvoří záznam (trigger), vloží se řádek do Oracle Database (akce). 

Tento konektor nepodporuje následující položky:

* Zobrazení 
* Libovolná tabulka se složenými klíči
* Typy vnořených objektů v tabulkách
* Funkce databáze bez skalárních hodnot

V tomto článku se dozvíte, jak používat konektor Oracle Database v aplikaci logiky.

## <a name="prerequisites"></a>Předpoklady

* Podporované verze Oracle: 
    * Oracle 9 a novější
    * Klientský software Oracle 8.1.7 a novější

* Nainstalujte místní bránu dat. [Připojení k místním datům z Logic Apps](../logic-apps/logic-apps-gateway-connection.md) uvádí postup. Brána se musí připojit k místnímu Oracle Database nebo virtuálnímu počítači Azure s nainstalovaným Oracle DB. 

    > [!NOTE]
    > Místní brána dat funguje jako most a poskytuje zabezpečený přenos dat mezi místními daty (data, která nejsou v cloudu) a vašimi Logic Apps. Stejnou bránu je možné použít s několika službami a více zdroji dat. Proto může být nutné bránu nainstalovat jenom jednou.

* Do počítače, kam jste nainstalovali místní bránu dat, nainstalujte klienta Oracle. Nezapomeňte nainstalovat 64 Oracle Zprostředkovatel dat pro .NET z Oracle:  

  [64bitové součásti ODAC 12c verze 4 (12.1.0.2.4) pro Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Pokud není nainstalován klient Oracle, při pokusu o vytvoření nebo použití připojení dojde k chybě. Podívejte se na nejčastější chyby v tomto článku.


## <a name="add-the-connector"></a>Přidání konektoru

> [!IMPORTANT]
> Tento konektor neobsahuje žádné triggery. Obsahuje jenom akce. Takže když vytvoříte aplikaci logiky, přidejte další Trigger pro spuštění aplikace logiky, jako je například **opakování plánu** nebo **požadavek/odpověď-odpověď** . 

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky.

2. Na začátku vaší aplikace logiky vyberte Trigger **požadavek/odpověď-žádost** : 

    ![Dialogové okno obsahuje pole pro hledání všech triggerů. K dispozici je také jedna aktivační událost "Request/response-Request" (výběr) s tlačítkem výběru.](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Vyberte **Uložit** . Při uložení se automaticky vygeneruje adresa URL požadavku. 

4. Vyberte **Nový krok** a potom **Přidat akci** . Zadáním `oracle` možnosti zobrazíte dostupné akce: 

    ![Vyhledávací pole obsahuje text "Oracle". Hledání vytvoří jeden z přístupů označených Oracle Database. Stránka s kartami obsahuje jednu kartu "TRIGGERy (0)", další ukazující "akce (6)". Uvádí se šest akcí. První z nich je "získat náhled řádku".](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > To je také nejrychlejší způsob, jak zobrazit triggery a akce, které jsou k dispozici pro libovolný konektor. Zadejte část názvu konektoru, například `oracle` . Návrhář zobrazí seznam všech triggerů a akcí. 

5. Vyberte jednu z akcí, například **Oracle Database-získat řádek** . Vyberte **připojit přes místní bránu dat** . Zadejte název serveru Oracle, metodu ověřování, uživatelské jméno, heslo a vyberte bránu:

    ![Dialogové okno má hodnotu "Oracle Database-získat řádek". Je zaškrtnuto políčko "připojit přes místní bránu dat". Níže jsou uvedeny pět dalších textových polí.](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po připojení vyberte v seznamu tabulku a zadejte ID řádku do tabulky. Musíte znát identifikátor v tabulce. Pokud to neznáte, obraťte se na správce Oracle DB a získejte výstup z `select * from yourTableName` . Získáte tak identifikovatelné informace, které potřebujete k tomu, abyste mohli pokračovat.

    V následujícím příkladu se data úlohy vracejí z databáze lidských zdrojů: 

    ![Dialogové okno s názvem získat řádek (Preview) obsahuje dvě textová pole: "název tabulky", který obsahuje "úlohy" H R "a má rozevírací seznam a" řádek i d ", který obsahuje" S A _ REP ".](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. V tomto dalším kroku můžete k sestavení pracovního postupu použít kterýkoli z dalších konektorů. Pokud chcete testovat získávání dat od Oracle, pošlete sami sobě e-mail s daty Oracle pomocí jedné z konektorů pro odesílání e-mailů, jako je Office 365 Outlook. Pomocí dynamických tokenů z tabulky Oracle Sestavte `Subject` a `Body` svůj e-mail:

    ![Existují dvě dialogová okna. Pole "Odeslat e-mail" obsahuje pole pro zadání "tělo", "Předmět" a "adresa" e-mailu. Dialogové okno Přidat dynamický obsah poskytuje hledání dynamického obsahu z aplikací a služeb daného toku.](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Uložte** aplikaci logiky a pak vyberte **Spustit** . Zavřete návrháře a podívejte se na historii spuštění daného stavu. Pokud dojde k chybě, vyberte řádek zprávy, která selhala. Otevře se Návrhář a ukáže vám, který krok se nezdařil, a také zobrazí informace o chybě. Pokud bude úspěšná, měli byste obdržet e-mail s informacemi, které jste přidali.


### <a name="workflow-ideas"></a>Návrhy pracovního postupu

* Chcete monitorovat #oracle hashtag a umístit tweety do databáze, aby se mohly dotazovat a používat v jiných aplikacích. V aplikaci logiky přidejte `Twitter - When a new tweet is posted` Trigger a zadejte **#oracle** hashtag. Pak přidejte `Oracle Database - Insert row` akci a vyberte tabulku:

    ![V dialogovém okně "když se odešle nový seznam se stavem" se zobrazí text "hashtag Oracle" jako text vyhledávání a umožňuje určit četnost kontroly. Toto dialogové okno vás zavede do dialogového okna "Oracle Database", které vám umožní vybrat akci.](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Zprávy jsou odesílány do fronty Service Bus. Tyto zprávy chcete získat a umístit je do databáze. V aplikaci logiky přidejte `Service Bus - when a message is received in a queue` Trigger a vyberte frontu. Pak přidejte `Oracle Database - Insert row` akci a vyberte tabulku:

    !["Při přijetí zprávy..." Dialogové okno zobrazí "objednávky" jako "název fronty" a umožňuje určit četnost kontroly. Toto pole vede k dialogovému oknu vložit řádek (Preview), ve kterém můžete vybrat název tabulky.](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Běžné chyby

#### <a name="error-cannot-reach-the-gateway"></a>**Chyba** : Nelze se připojit k bráně

**Příčina** : místní brána dat se nemůže připojit ke cloudu. 

**Zmírnění** : Ujistěte se, že je brána spuštěná na místním počítači, na kterém je nainstalovaná, a že se může připojit k Internetu.  Doporučujeme neinstalovat bránu na počítač, který může být vypnutý nebo v režimu spánku. Můžete také restartovat službu místní brány dat (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Chyba** : použitý zprostředkovatel je zastaralý: System. data. OracleClient vyžaduje, aby byl klientský software Oracle verze 8.1.7 nebo vyšší. Informace najdete v tématu [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) instalace oficiálního poskytovatele.

**Příčina** : sada SDK klienta Oracle není nainstalovaná na počítači, na kterém je spuštěná místní brána dat.  

**Řešení** : Stáhněte a nainstalujte sadu SDK klienta Oracle do stejného počítače, ve kterém je místní brána dat.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Chyba** : tabulka ' [TableName] ' nedefinuje žádné klíčové sloupce.

**Příčina** : tabulka neobsahuje žádný primární klíč.  

**Řešení** : konektor Oracle Database vyžaduje, aby se použila tabulka se sloupcem primárního klíče.
 
## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Zobrazit všechny triggery a akce definované v Swagger a také zobrazit omezení v [podrobnostech konektoru](/connectors/oracle/). 

## <a name="get-some-help"></a>Získat nápovědu

[Microsoft Q&Stránka s otázkou pro Azure Logic Apps](/answers/topics/azure-logic-apps.html) je skvělé místo pro pokladení otázek, odpovědi na dotazy a informace o tom, co dělají jiní uživatelé Logic Apps. 

Můžete pomoci vylepšit Logic Apps a konektory hlasováním a odesláním nápadů na [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) . 


## <a name="next-steps"></a>Další kroky
[Vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)a Prozkoumejte dostupné konektory v Logic Apps v [seznamu rozhraní API](apis-list.md).