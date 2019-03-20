---
title: Připojení k databázi IBM Informix – Azure Logic Apps | Dokumentace Microsoftu
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
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165896"
---
# <a name="get-started-with-the-informix-connector"></a>Začínáme s konektor Informix
Konektor služby Microsoft pro Informix připojí k prostředkům uloženým v databázi IBM Informix Logic Apps. Konektor Informix zahrnuje klienta ke komunikaci se vzdáleným počítačům serveru Informix přes síť TCP/IP. To zahrnuje cloudových databází, jako je například IBM Informix pro Windows, které jsou spuštěné v Azure virtualizace a místních databází pomocí místní brány dat. Zobrazit [podporované seznamu](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix platforem a verzí (v tomto tématu).

Tento konektor podporuje následující databázové operace:

* Seznam tabulek databáze
* Jeden řádek pro čtení používá vyberte
* Číst všechny řádky pomocí vyberte
* Přidat jeden řádek pomocí vložení
* Příkaz ALTER pomocí aktualizace jeden řádek
* Odebrat jeden řádek použití odstranění

Toto téma ukazuje, jak použít konektor v aplikaci logiky, která proces databázových operací.

Další informace o Logic Apps najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostupné akce
Tento konektor podporuje následující akce logic Apps:

* Getables
* Getrow –
* GetRows
* Insertrow –
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Vypíše tabulky
Vytvoření aplikace logiky pro všechny operace se skládá z mnoha kroků, které se provádí prostřednictvím portálu Microsoft Azure.

V rámci aplikace logiky můžete přidat akci do seznamu tabulek v databázi Informix. Tato akce nastaví konektoru ke zpracování příkazu schématu Informix, například `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **start pro panel Azure**vyberte **+** (znaménko plus) **Web + mobilní zařízení**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixgetTables`, **předplatné**, **skupiny prostředků**, **umístění**, a **služby App Service Plánování**. Vyberte **připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační události a akce
1. V **návrhář pro Logic Apps**vyberte **prázdnou aplikaci LogicApp** v **šablony** seznamu.
2. V **triggery** seznamu vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**vyberte **frekvence** rozevíracího seznamu vyberte **den**a pak vyberte  **Interval** na typ **7**.  
4. Vyberte **+ nový krok** a potom vyberte **přidat akci**.
5. V **akce** seznamu, zadejte **informix** v **kliknutím zobrazíte další akce** textové pole a pak vyberte **Informix - Get tabulky (Preview)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. V **Informix - Get tabulky** konfigurační podokno, vyberte **zaškrtávací políčko** umožňující **připojit přes místní bránu dat**. Všimněte si, že nastavení změní z cloudu i místní.
   
   * Zadejte hodnotu pro **Server**, ve formě číslo portu dvojtečka adresa nebo alias. Zadejte například příkaz `ibmserver01:9089`.
   * Zadejte hodnotu pro **databáze**. Zadejte například příkaz `nwind`.
   * Vyberte hodnotu pro **ověřování**. Vyberte například **základní**.
   * Zadejte hodnotu pro **uživatelské jméno**. Zadejte například příkaz `informix`.
   * Zadejte hodnotu pro **heslo**. Zadejte například příkaz `Password1`.
   * Vyberte hodnotu pro **brány**. Vyberte například **datagateway01**.
7. Vyberte **vytvořit**a pak vyberte **Uložit**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. V **InformixgetTables** okno, v rámci **všechna spuštění** seznamu v části **Souhrn**, vyberte položku, první uvedená (posledního spuštění).
9. V **běh aplikace logiky** okně vyberte **detaily spuštění**. V rámci **akce** seznamu vyberte **Get_tables**. Zobrazit hodnotu pro **stav**, který by měl být **Succeeded**. Vyberte **odkaz na vstupy** zobrazíte vstupy. Vyberte **odkaz na výstupy**a zobrazit výstupy, které by měl obsahovat seznam tabulek.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Vytvoření připojení
Tento konektor podporuje připojení k místní databáze a v cloudu s využitím následující vlastnosti připojení. 

| Vlastnost | Popis |
| --- | --- |
| server |Povinná hodnota. Přijímá řetězcovou hodnotu představující adresa TCP/IP nebo alias ve formátu IPv4 nebo IPv6, a potom (dvojtečka oddělený tabulátory) podle čísla portu TCP/IP. |
| databáze |Povinná hodnota. Přijímá řetězcovou hodnotu představující architektury DRDA název pro relační databáze (RDBNAM). Informix přijímá řetězec 128 bajtů (databáze se označuje jako název databáze IBM Informix (dbname)). |
| Ověřování |Volitelné. Přijímá hodnotu položky seznamu, Basic nebo Windows (kerberos). |
| uživatelské jméno |Povinná hodnota. Přijímá řetězcovou hodnotu. |
| heslo |Povinná hodnota. Přijímá řetězcovou hodnotu. |
| brána |Povinná hodnota. Přijímá hodnotu položky seznamu, představující na místní bránu dat definované pro Logic Apps v rámci skupiny úložišť. |

## <a name="create-the-on-premises-gateway-connection"></a>Vytvořit místní připojení brány
Tento konektor můžete přístup k databázi Informix lokálně místní bránu dat používat. Další informace naleznete v tématech brány. 

1. V **brány** konfigurační podokno, vyberte **zaškrtávací políčko** umožňující **připojit přes bránu**. Podívejte se na nastavení změnit z cloudu na místní.
2. Zadejte hodnotu pro **Server**, ve formě číslo portu dvojtečka adresa nebo alias. Zadejte například příkaz `ibmserver01:9089`.
3. Zadejte hodnotu pro **databáze**. Zadejte například příkaz `nwind`.
4. Vyberte hodnotu pro **ověřování**. Vyberte například **základní**.
5. Zadejte hodnotu pro **uživatelské jméno**. Zadejte například příkaz `informix`.
6. Zadejte hodnotu pro **heslo**. Zadejte například příkaz `Password1`.
7. Vyberte hodnotu pro **brány**. Vyberte například **datagateway01**.
8. Vyberte **vytvořit** pokračujte. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Vytvoření připojení cloudu
Tento konektor můžete přístup k databázi Informix v cloudu. 

1. V **brány** konfigurační podokno, ponechejte tuto položku **zaškrtávací políčko** zakázáno (nepoužité) **připojit přes bránu**. 
2. Zadejte hodnotu pro **název připojení**. Zadejte například příkaz `hisdemo2`.
3. Zadejte hodnotu pro **název serveru Informix**, ve formě číslo portu dvojtečka adresa nebo alias. Zadejte například příkaz `hisdemo2.cloudapp.net:9089`.
4. Zadejte hodnotu pro **název databáze Informix**. Zadejte například příkaz `nwind`.
5. Zadejte hodnotu pro **uživatelské jméno**. Zadejte například příkaz `informix`.
6. Zadejte hodnotu pro **heslo**. Zadejte například příkaz `Password1`.
7. Vyberte **vytvořit** pokračujte. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Načtení všech řádků používá vyberte
Můžete vytvořit akce aplikace logiky k načtení všech řádků v tabulce Informix. Tato akce nastaví konektoru ke zpracování příkazu Informix SELECT, jako `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **start pro panel Azure**vyberte **+** (znaménko plus) **Web + mobilní zařízení**a potom **aplikace logiky**.
2. Zadejte **název** (např.) "**InformixgetRows**"), **předplatné**, **skupiny prostředků**, **umístění**, a **plán služby App Service**. Vyberte **připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační události a akce
1. V **návrhář pro Logic Apps**vyberte **prázdnou aplikaci LogicApp** v **šablony** seznamu.
2. V **triggery** seznamu vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**vyberte **frekvence** rozevíracího seznamu vyberte **den**a pak vyberte  **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a potom vyberte **přidat akci**.
5. V **akce** seznamu, zadejte **informix** v **kliknutím zobrazíte další akce** textové pole a pak vyberte **Informix – získat řádky (Preview)**.
6. V **získat řádky (Preview)** akci, vyberte **změnit připojení**.
7. V **připojení** konfigurační podokno, vyberte **vytvořit nový**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. V **brány** konfigurační podokno, ponechejte tuto položku **zaškrtávací políčko** zakázáno (nepoužité) **připojit přes bránu**.
   
   * Zadejte hodnotu pro **název připojení**. Zadejte například příkaz `HISDEMO2`.
   * Zadejte hodnotu pro **název serveru Informix**, ve formě číslo portu dvojtečka adresa nebo alias. Zadejte například příkaz `HISDEMO2.cloudapp.net:9089`.
   * Zadejte hodnotu pro **název databáze Informix**. Zadejte například příkaz `NWIND`.
   * Zadejte hodnotu pro **uživatelské jméno**. Zadejte například příkaz `informix`.
   * Zadejte hodnotu pro **heslo**. Zadejte například příkaz `Password1`.
9. Vyberte **vytvořit** pokračujte.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. V **název tabulky** seznamu, vyberte **šipka dolů**a pak vyberte **oblasti**.
11. Volitelně vyberte **zobrazit pokročilé možnosti** zadat možnosti dotazu.
12. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. V **InformixgetRows** okno, v rámci **všechna spuštění** seznamu v části **Souhrn**, vyberte položku, první uvedená (posledního spuštění).
14. V **běh aplikace logiky** okně vyberte **detaily spuštění**. V rámci **akce** seznamu vyberte **Get_rows**. Zobrazit hodnotu pro **stav**, který by měl být **Succeeded**. Vyberte **odkaz na vstupy** zobrazíte vstupy. Vyberte **odkaz na výstupy**a zobrazit výstupy, které by měl obsahovat seznam řádků.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Přidat jeden řádek pomocí vložení
Můžete vytvořit akce aplikace logiky, chcete-li přidat jeden řádek v tabulce Informix. Tato akce nastaví konektoru ke zpracování příkazu Informix INSERT, jako například `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **start pro panel Azure**vyberte **+** (znaménko plus) **Web + mobilní zařízení**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixinsertRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **služby App Service Plánování**. Vyberte **připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační události a akce
1. V **návrhář pro Logic Apps**vyberte **prázdnou aplikaci LogicApp** v **šablony** seznamu.
2. V **triggery** seznamu vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**vyberte **frekvence** rozevíracího seznamu vyberte **den**a pak vyberte  **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a potom vyberte **přidat akci**.
5. V **akce** seznamu, zadejte **informix** v **kliknutím zobrazíte další akce** textové pole a pak vyberte **Informix – vložit řádek (Preview)**.
6. V **získat řádky (Preview)** akci, vyberte **změnit připojení**. 
7. V **připojení** konfigurační podokno, vyberte k připojení k výběru. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a pak vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červená hvězdička). Zadejte například `99999` pro **AREAID**, typ `Area 99999`a typ `102` pro **REGIONID**. 
10. Vyberte **Uložit**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. V **InformixinsertRow** okno, v rámci **všechna spuštění** seznamu v části **Souhrn**, vyberte položku, první uvedená (posledního spuštění).
12. V **běh aplikace logiky** okně vyberte **detaily spuštění**. V rámci **akce** seznamu vyberte **Get_rows**. Zobrazit hodnotu pro **stav**, který by měl být **Succeeded**. Vyberte **odkaz na vstupy** zobrazíte vstupy. Vyberte **odkaz na výstupy**a zobrazit výstupy, které by měl obsahovat nový řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Načíst pomocí vyberte jeden řádek
Můžete vytvořit akce aplikace logiky se načíst jeden řádek v tabulce Informix. Tato akce nastaví konektoru ke zpracování, jako příkaz Informix vyberte kde `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **start pro panel Azure**vyberte **+** (znaménko plus) **Web + mobilní zařízení**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixgetRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **služby App Service Plánování**. Vyberte **připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační události a akce
1. V **návrhář pro Logic Apps**vyberte **prázdnou aplikaci LogicApp** v **šablony** seznamu.
2. V **triggery** seznamu vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**vyberte **frekvence** rozevíracího seznamu vyberte **den**a pak vyberte  **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a potom vyberte **přidat akci**.
5. V **akce** seznamu, zadejte **informix** v **kliknutím zobrazíte další akce** textové pole a pak vyberte **Informix – získat řádky (Preview)**.
6. V **získat řádky (Preview)** akci, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně vyberte možnost a vyberte existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a pak vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červená hvězdička). Zadejte například `99999` pro **AREAID**. 
10. Volitelně vyberte **zobrazit pokročilé možnosti** zadat možnosti dotazu.
11. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. V **InformixgetRow** okno, v rámci **všechna spuštění** seznamu v části **Souhrn**, vyberte položku, první uvedená (posledního spuštění).
13. V **běh aplikace logiky** okně vyberte **detaily spuštění**. V rámci **akce** seznamu vyberte **Get_rows**. Zobrazit hodnotu pro **stav**, který by měl být **Succeeded**. Vyberte **odkaz na vstupy** zobrazíte vstupy. Vyberte **odkaz na výstupy**a zobrazit výstupy, které by měl obsahovat řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Změňte jeden řádek pomocí UPDATE
Můžete vytvořit akce aplikace logiky, chcete-li změnit jeden řádek v tabulce Informix. Tato akce nastaví konektoru ke zpracování příkazu Informix UPDATE, jako například `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **start pro panel Azure**vyberte **+** (znaménko plus) **Web + mobilní zařízení**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixupdateRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **služby App Service Plánování**. Vyberte **připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační události a akce
1. V **návrhář pro Logic Apps**vyberte **prázdnou aplikaci LogicApp** v **šablony** seznamu.
2. V **triggery** seznamu vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**vyberte **frekvence** rozevíracího seznamu vyberte **den**a pak vyberte  **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a potom vyberte **přidat akci**.
5. V **akce** seznamu, zadejte **informix** v **kliknutím zobrazíte další akce** textové pole a pak vyberte **Informix – aktualizovat řádek (Preview)**.
6. V **získat řádky (Preview)** akci, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně vyberte možnost a vyberte existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a pak vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červená hvězdička). Zadejte například `99999` pro **AREAID**, typ `Updated 99999`a typ `102` pro **REGIONID**. 
10. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. V **InformixupdateRow** okno, v rámci **všechna spuštění** seznamu v části **Souhrn**, vyberte položku, první uvedená (posledního spuštění).
12. V **běh aplikace logiky** okně vyberte **detaily spuštění**. V rámci **akce** seznamu vyberte **Get_rows**. Zobrazit hodnotu pro **stav**, který by měl být **Succeeded**. Vyberte **odkaz na vstupy** zobrazíte vstupy. Vyberte **odkaz na výstupy**a zobrazit výstupy, které by měl obsahovat nový řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Odebrat jeden řádek použití odstranění
Můžete vytvořit akce aplikace logiky se odebrat jeden řádek v tabulce Informix. Tato akce nastaví konektoru ke zpracování příkazu odstranit Informix, například `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. V **start pro panel Azure**vyberte **+** (znaménko plus) **Web + mobilní zařízení**a potom **aplikace logiky**.
2. Zadejte **název**, jako například `InformixdeleteRow`, **předplatné**, **skupiny prostředků**, **umístění**, a **služby App Service Plánování**. Vyberte **připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační události a akce
1. V **návrhář pro Logic Apps**vyberte **prázdnou aplikaci LogicApp** v **šablony** seznamu.
2. V **triggery** seznamu vyberte **opakování**. 
3. V **opakování** aktivační událost, vyberte **upravit**vyberte **frekvence** rozevíracího seznamu vyberte **den**a pak vyberte  **Interval** na typ **7**. 
4. Vyberte **+ nový krok** a potom vyberte **přidat akci**.
5. V **akce** seznamu, zadejte **informix** v **kliknutím zobrazíte další akce** textové pole a pak vyberte **Informix – odstranit řádek (Preview)**.
6. V **získat řádky (Preview)** akci, vyberte **změnit připojení**. 
7. V **připojení** konfigurace podokně, vyberte existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V **název tabulky** seznamu, vyberte **šipka dolů**a pak vyberte **oblasti**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červená hvězdička). Zadejte například `99999` pro **AREAID**. 
10. Vyberte **Uložit**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. V **InformixdeleteRow** okno, v rámci **všechna spuštění** seznamu v části **Souhrn**, vyberte položku, první uvedená (posledního spuštění).
12. V **běh aplikace logiky** okně vyberte **detaily spuštění**. V rámci **akce** seznamu vyberte **Get_rows**. Zobrazit hodnotu pro **stav**, který by měl být **Succeeded**. Vyberte **odkaz na vstupy** zobrazíte vstupy. Vyberte **odkaz na výstupy**a zobrazit výstupy, které by měl obsahovat odstraněný řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Podporované Informix platformy a verze
Tento konektor podporuje následující verze IBM Informix, při konfiguraci pro podporu připojení klientů distribuovány relační databáze architektury (DRDA).

* Databáze IBM Informix 12.1
* Databáze IBM Informix 11,7

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/informix/). 

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte další dostupné konektory v Logic Apps na naše [rozhraní API seznamu](apis-list.md).

