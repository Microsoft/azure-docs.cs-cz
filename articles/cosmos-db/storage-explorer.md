---
title: Správa prostředků Azure Cosmos DB pomocí Průzkumníka služby Azure Storage
description: Zjistěte, jak se připojit ke službě Azure Cosmos DB a spravovat její prostředky pomocí Průzkumníka služby Azure Storage.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: c99b22a5dad1e64525b2b2ebcd214c3dc5bfb28f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473591"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Práce s daty pomocí Průzkumníka služby Azure Storage

Použití služby Azure Cosmos DB v Průzkumníku služby Azure Storage umožňuje uživatelům spravovat entity Azure Cosmos DB, manipulovat s daty, aktualizovat uložené procedury a triggery, stejně jako další entity Azure, jako jsou objekty blob a fronty služby Storage. Nyní můžete pomocí jednoho nástroje spravovat různé entity Azure na jednom místě. V současné době podporuje Průzkumník služby Azure Storage účty Cosmos konfigurované pro SQL, MongoDB, Graph a Table API služby.


## <a name="prerequisites"></a>Požadavky

Účet Cosmos pomocí rozhraní SQL API nebo Azure Cosmos DB: rozhraní API pro MongoDB. Pokud nemáte účet, můžete vytvořit jeden na webu Azure Portal, jak je popsáno v [služby Azure Cosmos DB: Vytvoření webové aplikace SQL API s využitím .NET a webu Azure portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalace

Nainstalujte nejnovější části Průzkumníka služby Storage Azure do tady: [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/)podporujeme verze pro Windows, Linux a MAC.

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

1. Po instalaci **Průzkumníka služby Azure Storage** klikněte na ikonu modulu **plug-in** na levé straně, jak je znázorněno na následujícím obrázku:
       
   ![Ikona modulu plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Vyberte **Přidat účet Azure** a pak klikněte na **Přihlásit**.

   ![Připojení k předplatnému Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. V dialogovém okně **Přihlášení k Azure** vyberte **Přihlásit** a zadejte své přihlašovací údaje Azure.

    ![Přihlášení](./media/storage-explorer/sign-in.png)

3. Vyberte ze seznamu své předplatné a klikněte na **Použít**.

    ![Použít](./media/storage-explorer/apply-subscription.png)

    Podokno Průzkumníka se aktualizuje a zobrazí účty ve vybraném předplatném.

    ![Seznam účtů](./media/storage-explorer/account-list.png)

    Právě jste úspěšně propojili svůj **účet služby Cosmos DB** se svým předplatným Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce

Alternativním způsobem, jak se připojit ke službě Azure Cosmos DB, je použít připojovací řetězec. Pomocí následujícího postupu se připojte pomocí připojovacího řetězce.

1. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Cosmos DB** a zvolte **Připojit ke službě Cosmos DB...**

    ![Připojení ke službě Cosmos DB pomocí připojovacího řetězce](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Aktuálně se podporují pouze rozhraní SQL a Table API. Zvolte rozhraní API, vložte **Připojovací řetězec**, zadejte **Popisek účtu**, klikněte na **Další** a zkontrolujte souhrn a pak se kliknutím na **Připojit** připojte k účtu služby Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Připojovací řetězec](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Připojení ke službě Azure Cosmos DB pomocí místního emulátoru

Pomocí následujících kroků se připojte ke službě Azure Cosmos DB pomocí emulátoru, který aktuálně podporuje pouze účet SQL.

1. Nainstalujte emulátor a spusťte ho. Pokyny k instalaci emulátoru najdete v tématu [Emulátor služby Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

2. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Cosmos DB** a zvolte **Připojit k emulátoru služby Cosmos DB...**

    ![Připojení ke službě Cosmos DB pomocí emulátoru](./media/storage-explorer/emulator-entry.png)

3. Aktuálně se podporuje pouze rozhraní SQL API. Vložte **Připojovací řetězec**, zadejte **Popisek účtu**, klikněte na **Další** a zkontrolujte souhrn a pak se kliknutím na **Připojit** připojte k účtu služby Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Dialogové okno Připojení ke službě Cosmos DB pomocí emulátoru](./media/storage-explorer/emulator-dialog.png)


## <a name="azure-cosmos-db-resource-management"></a>Správa prostředků Azure Cosmos DB

Účet služby Azure Cosmos DB můžete spravovat pomocí následujících operací:
* Otevření účtu na webu Azure Portal
* Přidání prostředku do seznamu Rychlý přístup
* Vyhledávání a aktualizace prostředků
* Vytvářet a odstraňovat databáze
* Vytvářet a odstraňovat kolekce
* Vytváření, úpravy, odstraňování a filtrování dokumentů
* Správa uložených procedur, triggerů a funkcí definovaných uživatelem

### <a name="quick-access-tasks"></a>Úlohy rychlého přístupu

Po kliknutí pravým tlačítkem na předplatné v podokně Průzkumníka můžete provádět řadu úloh rychlého přístupu:

* Po kliknutí pravým tlačítkem na účet nebo databázi Azure Cosmos DB můžete zvolit **Otevřít na portálu** a spravovat prostředek v prohlížeči na webu Azure Portal.

     ![Otevřít na portálu](./media/storage-explorer/open-in-portal.png)

* Účet, databázi nebo kolekci Azure Cosmos DB můžete také přidat do seznamu **Rychlý přístup**.
* Možnost **Hledat odsud** umožňuje hledat klíčová slova v zadané cestě.

    ![Hledat odsud](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Správa databází a kolekcí
#### <a name="create-a-database"></a>Vytvoření databáze 
-   Klikněte pravým tlačítkem na účet služby Azure Cosmos DB, zvolte **Vytvořit databázi**, zadejte název databáze a stisknutím klávesy **Enter** dokončete vytváření.
       
    ![Vytvoření databáze](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Odstranění databáze
- Klikněte pravým tlačítkem na databázi, klikněte na **Odstranit databázi** a v automaticky otevíraném okně klikněte na **Ano**. Uzel databáze se odstraní a účet služby Azure Cosmos DB se automaticky aktualizuje.

    ![Odstranění databáze 1](./media/storage-explorer/delete-database1.png)  

    ![Odstranění databáze 2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Vytvoření kolekce
1. Klikněte pravým tlačítkem na svou databázi, zvolte **Vytvořit kolekci** a zadejte následující informace, jako jsou **ID kolekce**, **Kapacita úložiště** atd. Kliknutím na **OK** vytváření dokončete. 

    ![Vytvoření kolekce 1](./media/storage-explorer/create-collection.png)

    ![Vytvoření kolekce 2](./media/storage-explorer/create-collection2.png) 

2. Vyberte **Neomezená**, abyste mohli zadat klíč oddílu, a kliknutím na **OK** vytváření dokončete.

    Pokud se při vytváření kolekce použije klíč oddílu, po vytvoření se hodnota klíče oddílu pro kolekci nedá změnit.

    ![Klíč oddílu](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Odstranění kolekce
- Klikněte pravým tlačítkem na kolekci, klikněte na **Odstranit kolekci** a v automaticky otevíraném okně pak klikněte na **Ano**. 

    Uzel kolekce se odstraní a databáze se automaticky aktualizuje.

    ![Odstranění kolekce](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Správa dokumentů

#### <a name="create-and-modify-documents"></a>Vytváření a úpravy dokumentů
- Pokud chcete vytvořit nový dokument, otevřete v levém okně **Dokumenty**, klikněte na **Nový dokument**, v pravém podokně upravte obsah a pak klikněte na **Uložit**. Můžete také aktualizovat existující dokument a pak kliknout na **Uložit**. Změny můžete zahodit kliknutím na **Zahodit**.

    ![Dokument](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Odstranění dokumentu
- Kliknutím na tlačítko **Odstranit** odstraňte vybraný dokument.

#### <a name="query-for-documents"></a>Dotazování dokumentů
- Upravte filtr dokumentů zadáním [příkazu jazyka SQL](how-to-sql-query.md) a pak klikněte na **Použít**.

    ![Filtr dokumentů](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Správa grafů

#### <a name="create-and-modify-vertex"></a>Vytvoření a úprava vrcholu
1. Pokud chcete vytvořit nový vrchol, v levém okně otevřete **Graph**, klikněte na **Nový vrchol**, upravte obsah a pak klikněte na **OK**.    
2. Pokud chcete upravit existující vrchol, klikněte na ikonu pera v pravém podokně.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Odstranění grafu
- Pokud chcete odstranit vrchol, klikněte na ikonu koše vedle názvu vrcholu.

#### <a name="filter-for-graph"></a>Filtrování grafu
- Upravte filtr grafu zadáním [dotazu Gremlin](gremlin-support.md) a pak klikněte na **Použít filtr**.

    ![Filtr grafu](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Správa tabulek

#### <a name="create-and-modify-table"></a>Vytvoření a úprava tabulky
1. Pokud chcete vytvořit novou tabulku, v levém okně otevřete **Entity**, klikněte na **Přidat**, upravte obsah dialogového okna **Přidat entitu**, přidejte vlastnost kliknutím na tlačítko **Přidat vlastnost** a pak klikněte na **Vložit**.
2. Pokud chcete upravit tabulku, klikněte na **Upravit**, upravte obsah a pak klikněte na **Aktualizovat**.

    ![Table](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Import a export tabulky
1. Pokud chcete importovat tabulku, klikněte na tlačítko **Importovat** a zvolte existující tabulku.
2. Pokud chcete exportovat tabulku, klikněte na tlačítko **Exportovat** a zvolte cíl.

    ![Import a export tabulky](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Odstranění entit
- Vyberte entity a klikněte na tlačítko **Odstranit**.

    ![Odstranění tabulky](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Dotaz na tabulku
- Klikněte na tlačítko **Dotaz**, zadejte podmínky dotazu a pak klikněte na tlačítko **Provést dotaz**. Zavřete podokno Dotaz kliknutím na tlačítko **Zavřít dotaz**.

    ![Dotaz na tabulku](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Správa uložených procedur, triggerů a funkcí definovaných uživatelem
* Pokud chcete vytvořit uloženou proceduru, v levém stromu klikněte pravým tlačítkem na **Uložená procedura**, zvolte **Vytvořit uloženou proceduru**, v levém okně zadejte název, v pravém okně zadejte skripty uložené procedury a pak klikněte na **Vytvořit**. 
* Můžete také upravovat existující uložené procedury tak, že na ně dvakrát kliknete, provedete aktualizaci a pak kliknutím na **Aktualizovat** uložíte změny nebo je kliknutím na **Zahodit** zrušíte.

    ![Uložená procedura](./media/storage-explorer/stored-procedure.png)
* Operace týkající se **triggerů** a **funkcí definovaných uživatelem** jsou podobné **uloženým procedurám**.

## <a name="troubleshooting"></a>Řešení potíží

[Azure Cosmos DB v Průzkumníku služby Storage](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) je samostatná aplikace umožňující připojení k účtům služby Azure Cosmos DB hostovaným v Azure a suverénních cloudech z Windows, macOS nebo Linuxu. Umožňuje správu entit Azure Cosmos DB, manipulaci s daty, aktualizace uložených procedur a triggerů, stejně jako dalších entit Azure, jako jsou objekty blob a fronty služby Storage.

Toto jsou řešení běžných problémů, ke kterým dochází ve službě Azure Cosmos DB v Průzkumníku služby Storage.

### <a name="sign-in-issues"></a>Problémy s přihlášením

Než budete pokračovat, zkuste svou aplikaci restartovat a podívejte se, jestli se tím problém nevyřeší.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certifikát podepsaný svým držitelem v řetězu certifikátů

Tato chyba se může zobrazit z několika důvodů, z nichž dva nejčastější jsou tyto:

+ Nacházíte se za *transparentní proxy server*, což znamená, že někdo (například vaše IT oddělení) zachycuje přenosy HTTP, dešifruje je a pak je šifruje pomocí certifikátu podepsaného svým držitelem.

+ Používáte software, jako je například antivirový software, který do přijímaných zpráv protokolu HTTPS vkládá certifikát SSL podepsaný svým držitelem.

Pokud Průzkumník služby Storage narazí na některý z těchto certifikátů podepsaných svým držitelem, už nemůže mít jistotu, že se s přijímanými zprávami protokolu HTTPS nemanipulovalo. Pokud však máte kopii příslušného certifikátu podepsaného svým držitelem, můžete Průzkumníku služby Storage sdělit, aby mu důvěřoval. Pokud si nejste jisti, kdo certifikát vkládá, můžete to sami zkusit zjistit provedením následujících kroků:

1. Nainstalujte OpenSSL.
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (stačí jakákoli z odlehčených verzí)
     - Mac a Linux: Mělo by být součástí operačního systému
2. Spusťte OpenSSL.
    - Windows: Přejděte do adresáře instalace, pak **/bin/**, pak poklikejte na **openssl.exe**.
    - Mac a Linux: V terminálu spusťte příkaz **openssl**.
3. Spusťte příkaz `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jisti, které certifikáty jsou podepsané svým držitelem, hledejte certifikáty, jejichž předmět (s:) je stejný jako vystavitel (i:).
5.  Jakmile najdete nějaké certifikáty podepsané svým držitelem, zkopírujte u jednotlivých certifikátů veškerý text začínající na **-----BEGIN CERTIFICATE-----** a končící na **-----END CERTIFICATE-----** (včetně) a zkopírujte ho do nového souboru .cer.
6.  Otevřete Průzkumníka služby Storage a přejděte do části **Úpravy** > **Certifikáty SSL** > **Importovat certifikáty**. Pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubory .cer, které jste vytvořili.

Pokud se vám podle výše uvedeného postupu nepodaří najít žádné certifikáty podepsané svým držitelem, můžete získat další nápovědu odesláním zpětné vazby.

#### <a name="unable-to-retrieve-subscriptions"></a>Nelze načíst předplatná

Pokud se vám po úspěšném přihlášení nedaří načíst vaše předplatná:

- Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a ověřte, že má váš účet přístup k předplatným.
- Ujistěte se, že jste se přihlásili s použitím správného prostředí ([Azure](https://portal.azure.com/), [Azure (Čína)](https://portal.azure.cn/), [Azure (Německo)](https://portal.microsoftazure.de/), [Azure pro vládu USA](https://portal.azure.us/) nebo vlastní prostředí nebo Azure Stack).
- Pokud se nacházíte za proxy serverem, ujistěte se, že jste správně nakonfigurovali proxy Průzkumníka služby Storage.
- Zkuste účet odebrat a znovu přidat.
- Zkuste z domovského adresáře odstranit následující soubory (například: C:\Users\ContosoUser) a pak znovu přidat účet:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Při přihlašování sledujte případné chybové zprávy v konzole vývojářských nástrojů (F12).

![konzola](./media/storage-explorer/console.png)

#### <a name="unable-to-see-the-authentication-page"></a>Ověřovací stránka se nezobrazuje 

Pokud se vám nezobrazuje ověřovací stránka:

- V závislosti na rychlosti vašeho připojení může načtení přihlašovací stránky nějakou dobu trvat. Počkejte alespoň minutu, než dialogové okno ověřování zavřete.
- Pokud se nacházíte za proxy serverem, ujistěte se, že jste správně nakonfigurovali proxy Průzkumníka služby Storage.
- Stisknutím klávesy F12 otevřete konzolu pro vývojáře. V konzole pro vývojáře sledujte odpovědi a podívejte se, jestli nenajdete nějaké vodítko k tomu, proč ověřování nefunguje.

#### <a name="cannot-remove-account"></a>Účet se nedá odebrat

Pokud nemůžete odebrat účet nebo pokud odkaz na opětovné ověření nic nedělá:

- Zkuste z domovského adresáře odstranit následující soubory a pak znovu přidat účet:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Pokud chcete odebrat prostředky služby Storage připojené sdíleným přístupovým podpisem, odstraňte:
  - Složku %AppData%/StorageExplorer ve Windows.
  - Složku /Users/ < vaše_jméno >/Library/Application SUpport/StorageExplorer v systému Mac.
  - Složku ~/.config/StorageExplorer v Linuxu.
  - Pokud tyto soubory odstraníte, **budete muset zadat všechny své přihlašovací údaje znovu**.


### <a name="httphttps-proxy-issue"></a>Problém s proxy HTTP/HTTPS

Při konfiguraci proxy HTTP/HTTPS v Průzkumníku služby Azure Storage nemůžete v levém stromu vypsat uzly služby Azure Cosmos DB. Jedná se o známý problém, který bude opravený v další vydané verzi. Prozatím můžete jako alternativní řešení použít Průzkumníka dat služby Azure Cosmos DB na webu Azure Portal. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Problém s uzlem Vývoj v uzlu Místní a připojené

Po kliknutí na uzel Vývoj v uzlu Místní a připojené v levém stromu nedojde k žádné reakci.  Jedná se o očekávané chování. Místní emulátor služby Azure Cosmos DB bude podporovaný v další vydané verzi.

![Uzel Vývoj](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Chyba připojení účtu služby Azure Cosmos DB v uzlu Místní a připojené

Pokud se po připojení účtu služby Azure Cosmos DB v uzlu Místní a připojené zobrazí následující chyba, zkontrolujte, jestli používáte správný připojovací řetězec.

![Chyba připojení účtu služby Azure Cosmos DB v uzlu Místní a připojené](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Chyba rozbalení uzlu služby Azure Cosmos DB

Při pokusu o rozbalení uzlů v levém stromu se může zobrazit následující chyba. 

![Chyba rozbalení](./media/storage-explorer/expand-error.png)

Vyzkoušejte následující návrhy:

- Zkontrolujte, jestli neprobíhá zřizování účtu služby Azure Cosmos DB, a zkuste to znovu po úspěšném vytvoření účtu.
- Pokud je účet v uzlu Rychlý přístup nebo Místní a připojené, zkontrolujte, jestli se účet neodstranil. Pokud ano, musíte uzel odebrat ručně.

## <a name="contact-us"></a>Kontaktujte nás

Pokud vám žádné z předchozích řešení nepomohlo, požádejte o pomoc s řešením problému odesláním e-mailu s podrobnostmi o problému týmu vývojářských nástrojů pro službu Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)).

## <a name="next-steps"></a>Další postup

* Podívejte se na následující video a zjistěte, jak pomocí služby Azure Cosmos DB v Průzkumníku služby Storage: [Použití služby Azure Cosmos DB v Průzkumníku služby Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Další informace o Průzkumníku služby Storage a připojení dalších služeb najdete v tématu [Začínáme s Průzkumníkem služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

