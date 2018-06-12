---
title: Připojení k databázi IBM Informix - Azure Logic Apps | Microsoft Docs
description: Správa prostředků pomocí rozhraní REST API IBM Informix a Azure Logic Apps
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d0008c19ed96f731f7b57c5d8aa41cd9f128bc20
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296032"
---
# <a name="get-started-with-the-informix-connector"></a>Začínáme s konektorem Informix
Konektor Microsoft pro Informix připojí k prostředkům uloženým v databázi IBM Informix Logic Apps. Konektor Informix zahrnuje klienta Microsoft ke komunikaci se vzdáleným počítačům serveru Informix přes síť TCP/IP. To zahrnuje cloudu databáze, například IBM Informix pro systém Windows spuštěn v Azure virtualizace a místní databáze, které používají bránu dat na místě. Najdete v článku [podporované seznamu](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix platforem a verzí (v tomto tématu).

Konektor podporuje následující databáze operace:

* Seznam tabulek databáze
* Pomocí vyberte jeden řádek pro čtení
* Číst všechny řádky pomocí vyberte
* Přidejte jeden řádek pomocí vložení
* Příkaz ALTER jeden řádek použití aktualizace
* Odeberte jeden řádek používání příkazu DELETE

Toto téma ukazuje, jak k používání konektoru v aplikaci logiky a databázové operace procesu.

Další informace o Logic Apps najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostupné akce
Tento konektor podporuje aplikace logiky takto:

* Getables
* GetRow
* Proces GetRows
* Insertrow –
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Vypíše tabulky
Vytvoření aplikace logiky pro všechny operace se skládá z mnoho kroků, které se provádí prostřednictvím portálu Microsoft Azure.

V rámci aplikace logiky můžete přidat akci do seznamu tabulek v databázi Informix. Tato akce nastaví konektor zpracování příkazu Informix schématu, jako je například `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixgetTables`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**.  
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **informix** v **vyhledejte další akce** textového pole a pak vyberte **Informix - Get tabulky (Preview)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. V **Informix - Get tabulky** konfigurace podokně, vyberte **políčko** povolit **připojit prostřednictvím místní brána dat**. Všimněte si, že nastavení změnit z cloudu na místní.
   
   * Zadejte hodnotu pro **Server**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `ibmserver01:9089`.
   * Zadejte hodnotu pro **databáze**. Můžete například zadat `nwind`.
   * Vyberte hodnotu pro **ověřování**. Vyberte například **základní**.
   * Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `informix`.
   * Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
   * Vyberte hodnotu pro **brány**. Vyberte například **datagateway01**.
7. Vyberte **vytvořit**a potom vyberte **Uložit**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. V **InformixgetTables** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
9. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_tables**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat seznam tabulek.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Vytvoření připojení
Tento konektor podporuje připojení k databázi místně a v cloudu pomocí následující vlastnosti připojení. 

| Vlastnost | Popis |
| --- | --- |
| server |Povinná hodnota. Přijme řetězcovou hodnotu představující adresu TCP/IP nebo alias, ve formátu protokolu IPv4 nebo IPv6, a potom (dvojtečkou oddělený tabulátory) podle čísla portu TCP/IP. |
| databáze |Povinná hodnota. Přijme řetězcovou hodnotu představující DRDA název pro relační databáze (RDBNAM). Informix přijímá řetězec 128 bajtů (databáze se označuje jako IBM Informix název databáze (dbname)). |
| Ověřování |Volitelné. Přijme hodnotu položky seznamu, Basic nebo Windows (kerberos). |
| uživatelské jméno |Povinná hodnota. Přijme hodnotu řetězce. |
| heslo |Povinná hodnota. Přijme hodnotu řetězce. |
| brána |Povinná hodnota. Přijme hodnotu položky seznamu reprezentující místní brána dat definované pro Logic Apps v rámci skupiny úložišť. |

## <a name="create-the-on-premises-gateway-connection"></a>Vytvořit místní připojení brány
Tento konektor mohou přistupovat k databázi Informix místně pomocí brány data místně. Další informace naleznete v tématech brány. 

1. V **brány** konfigurace podokně, vyberte **políčko** povolit **připojit prostřednictvím brány**. Podívejte se na nastavení změnit z cloudu na místní.
2. Zadejte hodnotu pro **Server**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `ibmserver01:9089`.
3. Zadejte hodnotu pro **databáze**. Můžete například zadat `nwind`.
4. Vyberte hodnotu pro **ověřování**. Vyberte například **základní**.
5. Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `informix`.
6. Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
7. Vyberte hodnotu pro **brány**. Vyberte například **datagateway01**.
8. Vyberte **vytvořit** pokračujte. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Vytvoření připojení cloudu
Tento konektor mají přístup k databázi Informix cloudu. 

1. V **brány** konfigurace podokně, ponechejte **políčko** zakázáno (nepoužité) **připojit prostřednictvím brány**. 
2. Zadejte hodnotu pro **název připojení**. Můžete například zadat `hisdemo2`.
3. Zadejte hodnotu pro **název serveru Informix**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `hisdemo2.cloudapp.net:9089`.
4. Zadejte hodnotu pro **název databáze Informix**. Můžete například zadat `nwind`.
5. Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `informix`.
6. Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
7. Vyberte **vytvořit** pokračujte. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Načíst pomocí vyberte všechny řádky
Můžete vytvořit logiku aplikace akce načíst všechny řádky v tabulce Informix. Tato akce nastaví konektor zpracování příkazu Informix SELECT, jako je například `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název** (např.) "**InformixgetRows**"), **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **informix** v **vyhledejte další akce** textového pole a pak vyberte **Informix - Get řádků (Preview)**.
6. V **získat řádky (Preview)** akce, vyberte **změnit připojení**.
7. V **připojení** konfigurace podokně, vyberte **vytvořit nový**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. V **brány** konfigurace podokně, ponechejte **políčko** zakázáno (nepoužité) **připojit prostřednictvím brány**.
   
   * Zadejte hodnotu pro **název připojení**. Můžete například zadat `HISDEMO2`.
   * Zadejte hodnotu pro **název serveru Informix**, ve formě adresu nebo alias číslo portu dvojtečkou. Můžete například zadat `HISDEMO2.cloudapp.net:9089`.
   * Zadejte hodnotu pro **název databáze Informix**. Můžete například zadat `NWIND`.
   * Zadejte hodnotu pro **uživatelské jméno**. Můžete například zadat `informix`.
   * Zadejte hodnotu pro **heslo**. Můžete například zadat `Password1`.
9. Vyberte **vytvořit** pokračujte.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
11. Volitelně vyberte **zobrazit rozšířené možnosti** k určení možnosti dotazu.
12. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. V **InformixgetRows** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
14. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat seznam řádků.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Přidejte jeden řádek pomocí vložení
Můžete vytvořit logiku aplikace akci chcete přidat jeden řádek v tabulce Informix. Tato akce nastaví konektor zpracování příkazu Informix INSERT, jako je například `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixinsertRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **informix** v **vyhledejte další akce** textového pole a pak vyberte **Informix - vložit řádek (Preview)**.
6. V **získat řádky (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte vybrat připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**, typ `Area 99999`a typ `102` pro **REGIONID**. 
10. Vyberte **Uložit**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. V **InformixinsertRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
12. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat nový řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Načíst pomocí vyberte jeden řádek
Můžete vytvořit logiku aplikace akce načíst jeden řádek v tabulce Informix. Tato akce nastaví konektor zpracování příkazu Informix vyberte kde, jako je například `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixgetRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **informix** v **vyhledejte další akce** textového pole a pak vyberte **Informix - Get řádků (Preview)**.
6. V **získat řádky (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte vybrat stávající připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**. 
10. Volitelně vyberte **zobrazit rozšířené možnosti** k určení možnosti dotazu.
11. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. V **InformixgetRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
13. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Změnit jeden řádek použití aktualizace
Můžete vytvořit logiku aplikace akce chcete-li změnit jeden řádek v tabulce Informix. Tato akce nastaví konektor zpracování výpis Informix aktualizace, jako je například `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixupdateRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **informix** v **vyhledejte další akce** textového pole a pak vyberte **Informix - aktualizace řádek (Preview)**.
6. V **získat řádky (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte vybrat stávající připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**, typ `Updated 99999`a typ `102` pro **REGIONID**. 
10. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. V **InformixupdateRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
12. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat nový řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Odeberte jeden řádek používání příkazu DELETE
Můžete vytvořit logiku aplikace akce odebrat jeden řádek v tabulce Informix. Tato akce nastaví konektor zpracování příkazu odstranit Informix, jako je například `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixdeleteRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a potom vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidání aktivační události a akce
1. V **logiku aplikace Návrhář**, vyberte **prázdné LogicApp** v **šablony** seznamu.
2. V **aktivační události** seznamu, vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**, vyberte **frekvence** rozevíracího seznamu vyberte **den**a potom vyberte **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a pak vyberte **přidat akci**.
5. V **akce** zadejte **informix** v **vyhledejte další akce** textového pole a pak vyberte **Informix - odstranit řádek (Preview)**.
6. V **získat řádky (Preview)** akce, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a potom vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičkou). Můžete například zadat `99999` pro **AREAID**. 
10. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. V **InformixdeleteRow** okno, v **všechny spustí** v rámci **Souhrn**, vyberte položku první uvedené (nejnovější spustit).
12. V **spusťte aplikaci logiky** vyberte **podrobnosti o spuštění**. V rámci **akce** seznamu, vyberte **Get_rows**. Zobrazit tak hodnotu pro **stav**, což by mělo být **úspěšné**. Vyberte **vstupy odkaz** zobrazíte vstupy. Vyberte **výstupy odkaz**a zobrazit výstupy, která by měla obsahovat odstraněných řádků.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Podporované platformy Informix a verze
Tento konektor podporuje následující verze IBM Informix, když nakonfigurovaný pro podporu připojení klientů distribuované relační databáze architektura (DRDA).

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/informix/). 

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte dalších dostupných konektorů v Logic Apps v našem [rozhraní API seznamu](apis-list.md).

