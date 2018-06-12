---
title: Připojení k DB2 - Azure Logic Apps | Microsoft Docs
description: Správa prostředků pomocí rozhraní API REST DB2 a Azure Logic Apps
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, estfan
ms.suite: integration
tags: connectors
ms.openlocfilehash: 507bc48b6b775d6a6fb5f855210d33520e187a74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295087"
---
# <a name="get-started-with-the-db2-connector"></a>Začínáme s konektorem DB2
Konektor Microsoft pro DB2 připojí k prostředkům uloženým v databázi IBM DB2 Logic Apps. Tento konektor zahrnuje klienta Microsoft ke komunikaci se vzdálenými počítači server DB2 přes síť TCP/IP. To zahrnuje cloudu databáze, například IBM Bluemix dashDB nebo IBM DB2 pro systém Windows spuštěn v Azure virtualizace a místní databáze, které používají bránu dat na místě. Najdete v článku [podporované seznamu](connectors-create-api-db2.md#supported-db2-platforms-and-versions) IBM DB2 platforem a verzí (v tomto tématu).

DB2 konektor podporuje následující databáze operace:

* Seznam tabulek databáze
* Pomocí vyberte jeden řádek pro čtení
* Číst všechny řádky pomocí vyberte
* Přidejte jeden řádek pomocí vložení
* Příkaz ALTER jeden řádek použití aktualizace
* Odeberte jeden řádek používání příkazu DELETE

Toto téma ukazuje, jak k používání konektoru v aplikaci logiky a databázové operace procesu.

Další informace o Logic Apps najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostupné akce
Konektor DB2 podporuje aplikace logiky takto:

* GetTables
* GetRow
* Proces GetRows
* Insertrow –
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Vypíše tabulky
Vytvoření aplikace logiky pro všechny operace se skládá z mnoho kroků, které se provádí prostřednictvím portálu Microsoft Azure.

V rámci aplikace logiky můžete přidat akci do seznamu tabulek v databázi DB2. Akce dá pokyn konektor zpracování příkazu DB2 schématu, jako je například `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `Db2getTables`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a poté nastavte **Interval** na typ **7**.  
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte `db2` v **vyhledejte další akce** textového pole a pak vyberte **DB2 - Get tabulky (Preview)**.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. V **DB2 - Get tabulky** konfigurace podokně, vyberte **políčko** povolit **připojit prostřednictvím místní brána dat**. Všimněte si, že nastavení změnit z cloudu na místní.
   
   * Zadejte hodnotu pro **Server**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `ibmserver01:50000`.
   * Zadejte hodnotu pro **databáze**. Můžete například zadat `nwind`.
   * Vyberte hodnotu pro **ověřování**. Vyberte například **základní**.
   * Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `db2admin`.
   * Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
   * Vyberte hodnotu pro **brány**. Vyberte například **datagateway01**.
7. Vyberte **vytvořit**a potom vyberte **Uložit**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. V **Db2getTables** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
9. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_tables**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat seznam tabulek.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Vytvoření připojení
Tento konektor podporuje připojení databáze hostovaná místně a v cloudu pomocí následující vlastnosti připojení. 

| Vlastnost | Popis |
| --- | --- |
| server |Povinná hodnota. Přijme řetězcovou hodnotu, kterou představuje adresu TCP/IP nebo alias, ve formátu protokolu IPv4 nebo IPv6, a potom (oddělený středníkem) podle čísla portu TCP/IP. |
| databáze |Povinná hodnota. Přijme hodnotu řetězce, který představuje DRDA název pro relační databáze (RDBNAM). DB2 pro z/OS přijímá řetězec 16 bajtů (databáze se označuje jako IBM DB2 pro umístění z/OS). DB2 pro i5/OS přijímá řetězec 18 bajtů (databáze se označuje jako IBM DB2 pro i relační databáze). DB2 pro LUW přijme řetězec 8 bajtů. |
| Ověřování |Volitelné. Přijme hodnotu položky seznamu, Basic nebo Windows (kerberos). |
| uživatelské jméno |Povinná hodnota. Přijme hodnotu řetězce. DB2 pro z/OS přijme řetězec 8 bajtů. DB2 pro i přijímá řetězec 10 bajtů. DB2 pro Linux nebo UNIX přijme řetězec 8 bajtů. DB2 pro systém Windows přijme řetězec 30 bajtů. |
| heslo |Povinná hodnota. Přijme hodnotu řetězce. |
| brána |Povinná hodnota. Přijme hodnotu položky seznamu reprezentující místní brána dat definované pro Logic Apps v rámci skupiny úložišť. |

## <a name="create-the-on-premises-gateway-connection"></a>Vytvořit místní připojení brány
Tento konektor mohou přistupovat k místní databázi DB2 pomocí místní brána. Další informace naleznete v tématech brány. 

1. V **brány** konfigurace podokně, vyberte **políčko** povolit **připojit prostřednictvím brány**. Všimněte si, že nastavení změnit z cloudu na místní.
2. Zadejte hodnotu pro **Server**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `ibmserver01:50000`.
3. Zadejte hodnotu pro **databáze**. Můžete například zadat `nwind`.
4. Vyberte hodnotu pro **ověřování**. Vyberte například **základní**.
5. Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `db2admin`.
6. Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
7. Vyberte hodnotu pro **brány**. Vyberte například **datagateway01**.
8. Vyberte **vytvořit** pokračujte. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Vytvoření připojení cloudu
Tento konektor můžete přístup k databázi DB2 cloudu. 

1. V **brány** konfigurace podokně, ponechejte **políčko** zakázáno (nepoužité) **připojit prostřednictvím brány**. 
2. Zadejte hodnotu pro **název připojení**. Můžete například zadat `hisdemo2`.
3. Zadejte hodnotu pro **název serveru DB2**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `hisdemo2.cloudapp.net:50000`.
4. Zadejte hodnotu pro **název databáze DB2**. Můžete například zadat `nwind`.
5. Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `db2admin`.
6. Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
7. Vyberte **vytvořit** pokračujte. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Načíst pomocí vyberte všechny řádky
Můžete definovat logiku aplikace akce načíst všechny řádky v tabulce DB2. Tím se nastaví konektor zpracování příkaz DB2 SELECT, jako je například `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `Db2getRows`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte `db2` v **vyhledejte další akce** textového pole a pak vyberte **DB2 - Get řádků (Preview)**.
6. V **získat řádky (Preview)** akce, vyberte **změnit připojení**.
7. V **připojení** konfigurace podokně, vyberte **vytvořit nový**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. V **brány** konfigurace podokně, ponechejte **políčko** zakázáno (nepoužité) **připojit prostřednictvím brány**.
   
   * Zadejte hodnotu pro **název připojení**. Můžete například zadat `HISDEMO2`.
   * Zadejte hodnotu pro **název serveru DB2**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `HISDEMO2.cloudapp.net:50000`.
   * Zadejte hodnotu pro **název databáze DB2**. Můžete například zadat `NWIND`.
   * Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `db2admin`.
   * Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
9. Vyberte **vytvořit** pokračujte.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
11. Volitelně vyberte **zobrazit rozšířené možnosti** k určení možnosti dotazu.
12. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. V **Db2getRows** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
14. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat seznam řádků.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Přidejte jeden řádek pomocí vložení
Můžete definovat logiku aplikace akci chcete přidat jeden řádek v tabulce DB2. Tato akce nastaví konektor zpracování příkazu DB2 INSERT, jako je například `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `Db2insertRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **db2** v **vyhledejte další akce** textového pole a pak vyberte **DB2 - vložit řádek (Preview)**.
6. V **DB2 - vložit řádek (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**, typ `Area 99999`a typ `102` pro **REGIONID**. 
10. Vyberte **Uložit**.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. V **Db2insertRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
12. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat nový řádek.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Načíst pomocí vyberte jeden řádek
Můžete definovat logiku aplikace akce načíst jeden řádek v tabulce DB2. Tato akce nastaví konektor zpracování příkazu DB2 vyberte kde, jako je například `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název** (např.) "**Db2getRow**"), **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu. 
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **db2** v **vyhledejte další akce** textového pole a pak vyberte **DB2 - Get řádků (Preview)**.
6. V **získat řádky (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**. 
10. Volitelně vyberte **zobrazit rozšířené možnosti** k určení možnosti dotazu.
11. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. V **Db2getRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
13. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat řádek.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Změnit jeden řádek použití aktualizace
Můžete definovat akce logiku aplikace změnit jeden řádek v tabulce DB2. Tato akce nastaví konektor zpracování výpis DB2 aktualizace, jako je například `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `Db2updateRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **db2** v **vyhledejte další akce** textového pole a pak vyberte **DB2 - aktualizace řádek (Preview)**.
6. V **DB2 - aktualizace řádek (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte vybrat stávající připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**, typ `Updated 99999`a typ `102` pro **REGIONID**. 
10. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. V **Db2updateRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
12. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat nový řádek.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Odeberte jeden řádek používání příkazu DELETE
Můžete definovat logiku aplikace akce odebrat jeden řádek v tabulce DB2. Tato akce nastaví konektor zpracování výpis DB2 odstranit, jako je například `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `Db2deleteRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu. 
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** seznamu, vyberte **db2** v **vyhledejte další akce** textového pole a pak vyberte **DB2 - odstranit řádek (Preview)**.
6. V **DB2 - odstranit řádek (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**. 
10. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. V **Db2deleteRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
12. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat odstraněných řádků.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>Podporované platformy DB2 a verze
Tento konektor podporuje na následujících platformách IBM DB2 a verze, a také IBM DB2 kompatibilní produkty (např. IBM Bluemix dashDB) podporující distribuované relační databáze architektura (DRDA) SQL přístup správce (SQLAM) verze 10 a 11:

* IBM DB2 pro z 11.1 operačního systému
* IBM DB2 pro z 10.1 operačního systému
* IBM DB2 pro i 7.3
* IBM DB2 pro i 7.2
* IBM DB2 pro i 7.1
* IBM DB2 pro LUW 11
* IBM DB2 pro LUW 10.5

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/db2/). 

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte dalších dostupných konektorů v Logic Apps v našem [rozhraní API seznamu](apis-list.md).

