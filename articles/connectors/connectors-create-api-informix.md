---
title: Připojení k databázi IBM Informix
description: Správa prostředků pomocí rozhraní REST API IBM Informix a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/26/2016
tags: connectors
ms.openlocfilehash: d6f768bc76d19c0aa21a245c008a4b05588f8f43
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789726"
---
# <a name="get-started-with-the-informix-connector"></a>Začínáme s konektorem Informix
Microsoft Connector pro Informix připojuje Logic Apps k prostředkům uloženým v databázi IBM Informix. Konektor pro Informix obsahuje klienta Microsoftu, který komunikuje se vzdálenými počítači Informix serveru přes síť TCP/IP. To zahrnuje cloudové databáze, jako je například IBM Informix pro Windows spuštěné v virtualizaci Azure a místní databáze s využitím místní brány dat. Podívejte se na [seznam podporovaných](connectors-create-api-informix.md#supported-informix-platforms-and-versions) platforem a verzí IBM Informix (v tomto tématu).

Konektor podporuje následující databázové operace:

* Výpis tabulek databáze
* Čtení jednoho řádku pomocí SELECT
* Čtení všech řádků pomocí SELECT
* Přidání jednoho řádku pomocí vložení
* Změna jednoho řádku pomocí aktualizace
* Odebrání jednoho řádku pomocí odstranění

V tomto tématu se dozvíte, jak pomocí konektoru v aplikaci logiky zpracovat operace databáze.

Další informace o Logic Apps najdete v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Dostupné akce
Tento konektor podporuje následující akce aplikace logiky:

* Getschopnost
* GetRow –
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Seznam tabulek
Vytvoření aplikace logiky pro všechny operace se skládá z mnoha kroků provedených prostřednictvím portál Microsoft Azure.

V rámci aplikace logiky můžete přidat akci pro výpis tabulek v databázi Informix. Tato akce instruuje konektor ke zpracování příkazu Informix schématu, jako je například `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. Na panelu **Start Azure**vyberte **+** (znaménko plus), **web a mobilní zařízení**a pak **aplikaci logiky**.
2. Zadejte **název**, například `InformixgetTables`, **předplatné**, **skupinu prostředků**, **umístění**a **plán App Service**. Vyberte **Připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační událost a akci
1. V **návrháři Logic Apps**v seznamu **šablony** vyberte **prázdné LogicApp** .
2. V seznamu **triggery** vyberte **opakování**. 
3. V triggeru **opakování** vyberte možnost **Upravit**, vyberte možnost **frekvence** rozevíracího seznamu a vyberte možnost **den**a pak vyberte možnost **interval** na typ **7**.  
4. Vyberte pole **+ Nový krok** a potom vyberte **přidat akci**.
5. V seznamu **Akce** v poli **Hledat další akce** zadejte **Informix** a pak vyberte **Informix-Get Tables (Preview)** .
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. V podokně Konfigurace **Informix – získat tabulky** **zaškrtněte políčko** pro povolení **připojení přes místní bránu dat**. Všimněte si, že nastavení se změní z cloudu do místního prostředí.
   
   * Zadejte hodnotu pro **Server**v podobě čísla portu nebo dvojtečky (adresa). Zadejte například `ibmserver01:9089`.
   * Hodnota typu pro **databázi** Zadejte například `nwind`.
   * Vyberte hodnotu pro **ověřování**. Vyberte například **Basic**.
   * Zadejte hodnotu pro **uživatelské jméno**. Zadejte například `informix`.
   * Zadejte hodnotu pro **heslo**. Zadejte například `Password1`.
   * Vyberte hodnotu pro **bránu**. Vyberte například **datagateway01**.
7. Vyberte **vytvořit**a pak vyberte **Uložit**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. V okně **InformixgetTables** vyberte v seznamu **všechny běhy** v části **Souhrn**položku první položka (poslední spuštění).
9. V okně **spuštění aplikace logiky** vyberte **Spustit podrobnosti**. V seznamu **akcí** vyberte možnost **Get_tables**. Podívejte se na hodnotu **stav**, který by měl být **úspěšný**. Pokud chcete zobrazit vstupy, vyberte **odkaz vstupy** . Vyberte **odkaz výstupy**a zobrazte výstupy. které by měly obsahovat seznam tabulek.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Vytvoření připojení
Tento konektor podporuje připojení k místní databázi a v cloudu pomocí následujících vlastností připojení. 

| Vlastnost | Popis |
| --- | --- |
| server |Povinná hodnota. Přijímá hodnotu řetězce představující adresu TCP/IP nebo alias ve formátu IPv4 nebo IPv6 následovaný (dvojtečkou oddělený) číslem portu TCP/IP. |
| databáze |Povinná hodnota. Akceptuje řetězcovou hodnotu představující název relační databáze DRDA (RDBNAM). Informix akceptuje řetězec 128 bajtů (databáze je známá jako název databáze IBM Informix). |
| ověřování |Volitelné. Přijímá hodnotu položky seznamu, buď Basic, nebo Windows (Kerberos). |
| uživatelské jméno |Povinná hodnota. Akceptuje řetězcovou hodnotu. |
| heslo |Povinná hodnota. Akceptuje řetězcovou hodnotu. |
| brány |Povinná hodnota. Přijímá hodnotu položky seznamu, která představuje místní bránu dat definovanou k Logic Apps v rámci skupiny úložišť. |

## <a name="create-the-on-premises-gateway-connection"></a>Vytvoření připojení k místní bráně
Tento konektor má přístup k místní databázi Informix pomocí místní brány dat. Další informace najdete v tématech o bráně. 

1. V podokně Konfigurace **brány** **zaškrtněte políčko** pro povolení **připojení přes bránu**. Podívejte se na téma Změna nastavení z cloudu do místního prostředí.
2. Zadejte hodnotu pro **Server**v podobě čísla portu nebo dvojtečky (adresa). Zadejte například `ibmserver01:9089`.
3. Hodnota typu pro **databázi** Zadejte například `nwind`.
4. Vyberte hodnotu pro **ověřování**. Vyberte například **Basic**.
5. Zadejte hodnotu pro **uživatelské jméno**. Zadejte například `informix`.
6. Zadejte hodnotu pro **heslo**. Zadejte například `Password1`.
7. Vyberte hodnotu pro **bránu**. Vyberte například **datagateway01**.
8. Pokračujte výběrem **vytvořit** . 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Vytvoření cloudového připojení
Tento konektor má přístup ke cloudové databázi Informix. 

1. V podokně Konfigurace **bran** ponechejte **políčko** zakázáno (bez kliknutí) **připojit prostřednictvím brány**. 
2. Zadejte hodnotu pro **název připojení**. Zadejte například `hisdemo2`.
3. Hodnota typu pro **název serveru Informix**, ve formě adresy nebo dvojtečky na čísle portu. Zadejte například `hisdemo2.cloudapp.net:9089`.
4. Hodnota typu pro **název databáze Informix** Zadejte například `nwind`.
5. Zadejte hodnotu pro **uživatelské jméno**. Zadejte například `informix`.
6. Zadejte hodnotu pro **heslo**. Zadejte například `Password1`.
7. Pokračujte výběrem **vytvořit** . 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Načíst všechny řádky pomocí SELECT
Můžete vytvořit akci aplikace logiky pro načtení všech řádků v tabulce Informix. Tato akce nastaví konektor pro zpracování příkazu Informix SELECT, například `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. Na panelu **Start Azure**vyberte **+** (znaménko plus), **web a mobilní zařízení**a pak **aplikaci logiky**.
2. Zadejte **název** (například "**InformixgetRows**"), **předplatné**, **skupinu prostředků**, **umístění**a **App Service plán**. Vyberte **Připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační událost a akci
1. V **návrháři Logic Apps**v seznamu **šablony** vyberte **prázdné LogicApp** .
2. V seznamu **triggery** vyberte **opakování**. 
3. V triggeru **opakování** vyberte možnost **Upravit**, vyberte možnost **frekvence** rozevíracího seznamu a vyberte možnost **den**a pak vyberte možnost **interval** na typ **7**. 
4. Vyberte pole **+ Nový krok** a potom vyberte **přidat akci**.
5. V seznamu **Akce** v poli **Hledat další akce** zadejte **Informix** a pak vyberte **Informix-získat řádky (Preview)** .
6. V akci **získat řádky (Preview)** vyberte **změnit připojení**.
7. V podokně Konfigurace **připojení** vyberte **vytvořit novou**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. V podokně Konfigurace **bran** ponechejte **políčko** zakázáno (bez kliknutí) **připojit prostřednictvím brány**.
   
   * Zadejte hodnotu pro **název připojení**. Zadejte například `HISDEMO2`.
   * Hodnota typu pro **název serveru Informix**, ve formě adresy nebo dvojtečky na čísle portu. Zadejte například `HISDEMO2.cloudapp.net:9089`.
   * Hodnota typu pro **název databáze Informix** Zadejte například `NWIND`.
   * Zadejte hodnotu pro **uživatelské jméno**. Zadejte například `informix`.
   * Zadejte hodnotu pro **heslo**. Zadejte například `Password1`.
9. Pokračujte výběrem **vytvořit** .
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. V seznamu **název tabulky** vyberte **šipku dolů**a pak vyberte **oblast**.
11. Volitelně můžete vybrat možnost **Zobrazit upřesňující možnosti** a zadat možnosti dotazu.
12. Vyberte **Save** (Uložit). 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. V okně **InformixgetRows** vyberte v seznamu **všechny běhy** v části **Souhrn**položku první položka (poslední spuštění).
14. V okně **spuštění aplikace logiky** vyberte **Spustit podrobnosti**. V seznamu **akcí** vyberte možnost **Get_rows**. Podívejte se na hodnotu **stav**, který by měl být **úspěšný**. Pokud chcete zobrazit vstupy, vyberte **odkaz vstupy** . Vyberte **odkaz výstupy**a zobrazte výstupy. které by měly obsahovat seznam řádků.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Přidání jednoho řádku pomocí vložení
Můžete vytvořit akci aplikace logiky pro přidání jednoho řádku do tabulky Informix. Tato akce dá konektoru pokyn ke zpracování příkazu pro vložení do Informix, například `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. Na panelu **Start Azure**vyberte **+** (znaménko plus), **web a mobilní zařízení**a pak **aplikaci logiky**.
2. Zadejte **název**, například `InformixinsertRow`, **předplatné**, **skupinu prostředků**, **umístění**a **plán App Service**. Vyberte **Připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační událost a akci
1. V **návrháři Logic Apps**v seznamu **šablony** vyberte **prázdné LogicApp** .
2. V seznamu **triggery** vyberte **opakování**. 
3. V triggeru **opakování** vyberte možnost **Upravit**, vyberte možnost **frekvence** rozevíracího seznamu a vyberte možnost **den**a pak vyberte možnost **interval** na typ **7**. 
4. Vyberte pole **+ Nový krok** a potom vyberte **přidat akci**.
5. V seznamu **Akce** v poli **Hledat další akce** zadejte **Informix** a pak vyberte **Informix-vložit řádek (Preview)** .
6. V akci **získat řádky (Preview)** vyberte **změnit připojení**. 
7. V podokně Konfigurace **připojení** vyberte připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V seznamu **název tabulky** vyberte **šipku dolů**a pak vyberte **oblast**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičku). Zadejte například `99999` pro **AREAID**, zadejte `Area 99999`a zadejte `102` pro **REGIONID**. 
10. Vyberte **Save** (Uložit).
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. V okně **InformixinsertRow** vyberte v seznamu **všechny běhy** v části **Souhrn**položku první položka (poslední spuštění).
12. V okně **spuštění aplikace logiky** vyberte **Spustit podrobnosti**. V seznamu **akcí** vyberte možnost **Get_rows**. Podívejte se na hodnotu **stav**, který by měl být **úspěšný**. Pokud chcete zobrazit vstupy, vyberte **odkaz vstupy** . Vyberte **odkaz výstupy**a zobrazte výstupy. který by měl obsahovat nový řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Načtení jednoho řádku pomocí SELECT
Můžete vytvořit akci aplikace logiky, která načte jeden řádek v tabulce Informix. Tato akce dá konektoru pokyn, aby zpracoval příkaz WHERE pro výběr, například `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. Na panelu **Start Azure**vyberte **+** (znaménko plus), **web a mobilní zařízení**a pak **aplikaci logiky**.
2. Zadejte **název**, například `InformixgetRow`, **předplatné**, **skupinu prostředků**, **umístění**a **plán App Service**. Vyberte **Připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační událost a akci
1. V **návrháři Logic Apps**v seznamu **šablony** vyberte **prázdné LogicApp** .
2. V seznamu **triggery** vyberte **opakování**. 
3. V triggeru **opakování** vyberte možnost **Upravit**, vyberte možnost **frekvence** rozevíracího seznamu a vyberte možnost **den**a pak vyberte možnost **interval** na typ **7**. 
4. Vyberte pole **+ Nový krok** a potom vyberte **přidat akci**.
5. V seznamu **Akce** v poli **Hledat další akce** zadejte **Informix** a pak vyberte **Informix-získat řádky (Preview)** .
6. V akci **získat řádky (Preview)** vyberte **změnit připojení**. 
7. V podokně Konfigurace **připojení** vyberte, pokud chcete vybrat existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V seznamu **název tabulky** vyberte **šipku dolů**a pak vyberte **oblast**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičku). Zadejte například `99999` pro **AREAID**. 
10. Volitelně můžete vybrat možnost **Zobrazit upřesňující možnosti** a zadat možnosti dotazu.
11. Vyberte **Save** (Uložit). 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. V okně **InformixgetRow** vyberte v seznamu **všechny běhy** v části **Souhrn**položku první položka (poslední spuštění).
13. V okně **spuštění aplikace logiky** vyberte **Spustit podrobnosti**. V seznamu **akcí** vyberte možnost **Get_rows**. Podívejte se na hodnotu **stav**, který by měl být **úspěšný**. Pokud chcete zobrazit vstupy, vyberte **odkaz vstupy** . Vyberte **odkaz výstupy**a zobrazte výstupy. které by měly obsahovat řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Změna jednoho řádku pomocí aktualizace
Můžete vytvořit akci aplikace logiky pro změnu jednoho řádku v tabulce Informix. Tato akce nastaví konektor pro zpracování příkazu aktualizace Informix, například `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. Na panelu **Start Azure**vyberte **+** (znaménko plus), **web a mobilní zařízení**a pak **aplikaci logiky**.
2. Zadejte **název**, například `InformixupdateRow`, **předplatné**, **skupinu prostředků**, **umístění**a **plán App Service**. Vyberte **Připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační událost a akci
1. V **návrháři Logic Apps**v seznamu **šablony** vyberte **prázdné LogicApp** .
2. V seznamu **triggery** vyberte **opakování**. 
3. V triggeru **opakování** vyberte možnost **Upravit**, vyberte možnost **frekvence** rozevíracího seznamu a vyberte možnost **den**a pak vyberte možnost **interval** na typ **7**. 
4. Vyberte pole **+ Nový krok** a potom vyberte **přidat akci**.
5. V seznamu **Akce** v poli **Hledat další akce** zadejte **Informix** a pak vyberte položku **Informix – aktualizovat řádek (Preview)** .
6. V akci **získat řádky (Preview)** vyberte **změnit připojení**. 
7. V podokně Konfigurace **připojení** vyberte, pokud chcete vybrat existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V seznamu **název tabulky** vyberte **šipku dolů**a pak vyberte **oblast**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičku). Zadejte například `99999` pro **AREAID**, zadejte `Updated 99999`a zadejte `102` pro **REGIONID**. 
10. Vyberte **Save** (Uložit). 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. V okně **InformixupdateRow** vyberte v seznamu **všechny běhy** v části **Souhrn**položku první položka (poslední spuštění).
12. V okně **spuštění aplikace logiky** vyberte **Spustit podrobnosti**. V seznamu **akcí** vyberte možnost **Get_rows**. Podívejte se na hodnotu **stav**, který by měl být **úspěšný**. Pokud chcete zobrazit vstupy, vyberte **odkaz vstupy** . Vyberte **odkaz výstupy**a zobrazte výstupy. který by měl obsahovat nový řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Odebrání jednoho řádku pomocí odstranění
Můžete vytvořit akci aplikace logiky pro odebrání jednoho řádku v tabulce Informix. Tato akce nastaví konektor pro zpracování příkazu Informix DELETE, například `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
1. Na panelu **Start Azure**vyberte **+** (znaménko plus), **web a mobilní zařízení**a pak **aplikaci logiky**.
2. Zadejte **název**, například `InformixdeleteRow`, **předplatné**, **skupinu prostředků**, **umístění**a **plán App Service**. Vyberte **Připnout na řídicí panel**a pak vyberte **vytvořit**.

### <a name="add-a-trigger-and-action"></a>Přidat aktivační událost a akci
1. V **návrháři Logic Apps**v seznamu **šablony** vyberte **prázdné LogicApp** .
2. V seznamu **triggery** vyberte **opakování**. 
3. V triggeru **opakování** vyberte možnost **Upravit**, vyberte možnost **frekvence** rozevíracího seznamu a vyberte možnost **den**a pak vyberte možnost **interval** na typ **7**. 
4. Vyberte pole **+ Nový krok** a potom vyberte **přidat akci**.
5. V seznamu **Akce** v poli **Hledat další akce** zadejte **Informix** a pak vyberte **Informix-odstranit řádek (Preview)** .
6. V akci **získat řádky (Preview)** vyberte **změnit připojení**. 
7. V podokně Konfigurace **připojení** vyberte existující připojení. Vyberte například **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. V seznamu **název tabulky** vyberte **šipku dolů**a pak vyberte **oblast**.
9. Zadejte hodnoty pro všechny požadované sloupce (viz červenou hvězdičku). Zadejte například `99999` pro **AREAID**. 
10. Vyberte **Save** (Uložit). 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. V okně **InformixdeleteRow** vyberte v seznamu **všechny běhy** v části **Souhrn**položku první položka (poslední spuštění).
12. V okně **spuštění aplikace logiky** vyberte **Spustit podrobnosti**. V seznamu **akcí** vyberte možnost **Get_rows**. Podívejte se na hodnotu **stav**, který by měl být **úspěšný**. Pokud chcete zobrazit vstupy, vyberte **odkaz vstupy** . Vyberte **odkaz výstupy**a zobrazte výstupy. který by měl obsahovat odstraněný řádek.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Podporované platformy a verze Informix
Tento konektor podporuje následující verze IBM Informix, pokud je nakonfigurovaná tak, aby podporovala připojení klientů DRDA (Distributed relační Database Architecture).

* IBM Informix 12,1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Zobrazit všechny triggery a akce definované v Swagger a také zobrazit omezení v [podrobnostech konektoru](/connectors/informix/). 

## <a name="next-steps"></a>Další kroky
[Vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte další dostupné konektory v Logic Apps v našem [seznamu rozhraní API](apis-list.md).

