---
title: Správa prostředků Azure Cosmos DB pomocí Průzkumník služby Azure Storage
description: Naučte se, jak se připojit k Azure Cosmos DB a spravovat jeho prostředky pomocí Průzkumník služby Azure Storage.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 61ae446d180a86b42bc52ac3244b96b7b347b68f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025391"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Správa prostředků Azure Cosmos DB pomocí Průzkumník služby Azure Storage
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

K připojení k Azure Cosmos DB můžete použít aplikaci Azure Storage Explorer. Umožňuje připojení k účtům Azure Cosmos DB hostovaným na Azure a v cloudech z Windows, macOS nebo Linux.

Použijte stejný nástroj ke správě různých entit Azure na jednom místě. Můžete spravovat Azure Cosmos DB entit, manipulovat s daty, aktualizovat uložené procedury a triggery spolu s dalšími entitami Azure, jako jsou objekty BLOB a fronty služby Storage. Průzkumník služby Azure Storage podporuje účty Cosmos nakonfigurované pro rozhraní API SQL, MongoDB, Graph a Table.

> [!NOTE]
> Integrace služby Azure Cosmos DB s Průzkumníkem služby Storage je zastaralá. Všechny stávající funkce nebudou odebrány z tohoto vydání nejméně po dobu jednoho roku. Místo toho byste měli používat portál [Azure Portal](https://portal.azure.com/), [desktopovou aplikaci Azure Portal](https://portal.azure.com/App/Download) nebo samostatnou aplikaci [Azure Cosmos Explorer](data-explorer.md). Tyto alternativní možnosti obsahují mnoho nových funkcí, které Průzkumník služby Storage v současnosti nepodporuje.

## <a name="prerequisites"></a>Požadavky

Účet Cosmos s rozhraním API SQL nebo rozhraním API Azure Cosmos DB pro MongoDB. Pokud účet nemáte, můžete ho vytvořit v Azure Portal. Další informace najdete v tématu [Azure Cosmos DB: sestavení webové aplikace SQL API s využitím .NET a Azure Portal](create-sql-api-dotnet.md) .

## <a name="installation"></a>Instalace

Chcete-li nainstalovat nejnovější Průzkumník služby Azure Storage bitů, přečtěte si téma [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Podporujeme verze pro Windows, Linux a macOS.

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

1. Po instalaci **Průzkumník služby Azure Storage** v levém podokně vyberte ikonu **modulu plug-in** .

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Snímek obrazovky znázorňující ikonu modulu plug-in v levém podokně.":::

1. Vyberte **Přidat účet Azure** a pak vyberte **Přihlásit**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Snímek obrazovky okna připojit k Azure Storage, kde se zobrazuje přepínač Přidat účet Azure a rozevírací nabídka prostředí Azure.":::

1. V dialogovém okně **přihlášení do Azure** vyberte **Přihlásit** se a zadejte svoje přihlašovací údaje Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Snímek obrazovky okna přihlásit, kde se zobrazí údaje o tom, kde zadat přihlašovací údaje pro vaše předplatné Azure":::

1. Vyberte ze seznamu své předplatné a pak vyberte **Použít**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Snímek obrazovky s podoknem Správa účtů zobrazující seznam předplatných a tlačítka použít.":::

    Podokno Průzkumník aktualizuje a zobrazí účty ve vybraném předplatném.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Snímek obrazovky podokna Průzkumníka, který se aktualizuje a zobrazí účty ve vybraném předplatném.":::

    Váš **účet Cosmos DB** je připojený k vašemu předplatnému Azure.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Připojení k Azure Cosmos DB pomocí připojovacího řetězce

Připojovací řetězec můžete použít pro připojení k Azure Cosmos DB. Tato metoda podporuje jenom rozhraní API SQL a Table. Pomocí těchto kroků se připojte s připojovacím řetězcem:

1. V levém stromě vyhledejte **místní a připojené** , klikněte pravým tlačítkem na **Cosmos DB účty** a pak vyberte **připojit k Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Snímek obrazovky s rozevírací nabídkou po kliknutí pravým tlačítkem myši se zvýrazněnou možností připojit k Azure Cosmos D B.":::

2. V okně **připojit k Cosmos DB** :
   1. V rozevírací nabídce vyberte rozhraní API.
   1. Do pole **připojovací řetězec** vložte připojovací řetězec. Informace o tom, jak načíst primární připojovací řetězec, najdete v tématu [získání připojovacího řetězce](manage-with-powershell.md#list-keys).
   1. Zadejte **popisek účtu** a kliknutím na tlačítko **Další** zkontrolujte souhrn.
   1. Vyberte **připojit** a připojte účet Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Snímek obrazovky okna připojit k Cosmos D B zobrazující rozevírací nabídku rozhraní API, pole Připojovací řetězec a popisek účtu.":::

> [!NOTE]
> Pokud Průzkumník služby Azure Storage ukáže, že připojovací řetězec Azure Cosmos DB má neplatný formát, ujistěte se, že připojovací řetězec obsahuje středník ( `;` ) na konci. Příklad platného připojovacího řetězce Azure Cosmos DB by byl: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Připojení k Azure Cosmos DB pomocí místního emulátoru

Pomocí následujících kroků se připojte k Azure Cosmos DB pomocí emulátoru. Tato metoda podporuje pouze účty SQL.

1. Nainstalujte emulátor Cosmos DB a pak ho otevřete. Postup instalace emulátoru najdete v tématu [Cosmos DB emulátor](./local-emulator.md).

1. V levém stromě vyhledejte **místní a připojené** , klikněte pravým tlačítkem na **Cosmos DB účty** a pak vyberte **připojit k Cosmos DB emulátoru**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Snímek obrazovky zobrazující nabídku, která se zobrazí po kliknutí pravým tlačítkem myši se zvýrazněným emulátorem pro připojení ke službě Azure Cosmos D B.":::

1. V okně **připojit k Cosmos DB** :
   1. Do pole **připojovací řetězec** vložte připojovací řetězec. Informace o načtení primárního připojovacího řetězce najdete v tématu [získání připojovacího řetězce](manage-with-powershell.md#list-keys).
   1. Zadejte **popisek účtu** a kliknutím na tlačítko **Další** zkontrolujte souhrn.
   1. Vyberte **připojit** a připojte účet Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Snímek obrazovky s oknem připojit k Cosmos D B se zobrazeným polem připojovací řetězec a popiskem účtu":::

## <a name="azure-cosmos-db-resource-management"></a>Správa prostředků Azure Cosmos DB

Ke správě účtu Azure Cosmos DB použijte následující operace:

* Otevřete účet v Azure Portal.
* Přidejte prostředek do seznamu rychlý přístup.
* Hledání a aktualizace prostředků
* Vytváření a odstraňování databází.
* Vytvářejte a odstraňujte kolekce.
* Vytváření, úpravy, odstraňování a filtrování dokumentů.
* Spravujte uložené procedury, triggery a uživatelsky definované funkce.

### <a name="quick-access-tasks"></a>Úlohy rychlého přístupu

Můžete kliknout pravým tlačítkem na předplatné v podokně Průzkumník a provést mnoho běžných úloh, například:

* Klikněte pravým tlačítkem myši na účet Azure Cosmos DB nebo databázi a potom vyberte **otevřít na portálu** pro správu prostředků v prohlížeči v Azure Portal.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Snímek obrazovky zobrazující nabídku, která se zobrazí po kliknutí pravým tlačítkem myši s zvýrazněnou možností otevřít na portálu":::

* Klikněte pravým tlačítkem myši na účet Azure Cosmos DB, databázi nebo kolekci a pak vyberte možnost **Přidat k rychlému přístupu** a přidejte ji do nabídky rychlý přístup.

* Pokud chcete povolit hledání klíčových slov v rámci vybrané cesty, vyberte **Hledat** odsud.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Snímek obrazovky znázorňující zvýrazněné pole hledání":::

### <a name="database-and-collection-management"></a>Správa databází a kolekcí

#### <a name="create-a-database"></a>Vytvoření databáze

1. Klikněte pravým tlačítkem na účet Azure Cosmos DB a pak vyberte **vytvořit databázi**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Snímek obrazovky zobrazující nabídku, která se zobrazí po kliknutí pravým tlačítkem myši s zvýrazněnou možností vytvořit databázi.":::

1. Zadejte název databáze a potom stisknutím klávesy **ENTER** dokončete akci.

#### <a name="delete-a-database"></a>Odstranění databáze

1. Klikněte pravým tlačítkem na databázi a pak vyberte **odstranit databázi**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Snímek obrazovky zobrazující nabídku, která se zobrazí po kliknutí pravým tlačítkem myši s zvýrazněnou možností odstranit databázi.":::

1. V automaticky otevíraném okně vyberte **Ano** . Uzel databáze se odstraní a účet služby Azure Cosmos DB se automaticky aktualizuje.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Snímek obrazovky s oknem potvrzení se zvýrazněným tlačítkem Ano":::

#### <a name="create-a-collection"></a>Vytvoření kolekce

1. Klikněte pravým tlačítkem na databázi a potom vyberte **vytvořit kolekci**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Snímek obrazovky zobrazující nabídku, která se zobrazí po kliknutí pravým tlačítkem myši se zvýrazněnou možností vytvořit kolekci.":::

1. V okně vytvořit kolekci zadejte požadované informace, jako je **ID kolekce** a **kapacita úložiště** atd. Kliknutím na **tlačítko OK** dokončete.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Snímek obrazovky okna vytvořit kolekci, kde se zobrazí pole kolekce I D a kapacita úložiště":::

1. Vyberte možnost **neomezeno** , abyste mohli zadat klíč oddílu, a potom kliknutím na **tlačítko OK** dokončete akci.

   > [!NOTE]
   > Pokud se při vytváření kolekce použije klíč oddílu, po vytvoření se už hodnota klíče oddílu v kolekci nedá změnit.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Snímek obrazovky okna vytvořit kolekci s neomezeným počtem vybraným pro kapacitu úložiště a zvýrazněným polem klíče oddílu":::

#### <a name="delete-a-collection"></a>Odstranění kolekce

- Klikněte pravým tlačítkem na kolekci, vyberte **Odstranit kolekci** a pak v automaticky otevíraném okně vyberte **Ano** .

    Uzel kolekce se odstraní a databáze se automaticky aktualizuje.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Snímek obrazovky zobrazující nabídku, která se zobrazí po kliknutí pravým tlačítkem myši s zvýrazněnou možností odstranit kolekci":::

### <a name="document-management"></a>Správa dokumentů

#### <a name="create-and-modify-documents"></a>Vytváření a úpravy dokumentů

- V levém podokně otevřete **dokumenty** , vyberte **Nový dokument**, upravte obsah v pravém podokně a pak vyberte **Uložit**.
- Můžete také aktualizovat existující dokument a pak vybrat **Uložit**. Pokud chcete zahodit změny, vyberte **Zrušit**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Snímek obrazovky znázorňující zvýrazněné dokumenty v levém podokně V pravém podokně se zvýrazní nový dokument, Uložit a zrušit.":::

#### <a name="delete-a-document"></a>Odstranění dokumentu

* Kliknutím na tlačítko **Odstranit** odstraníte vybraný dokument.

#### <a name="query-for-documents"></a>Dotazování dokumentů

* Chcete-li upravit filtr dokumentu, zadejte [dotaz SQL](./sql-query-getting-started.md)a pak vyberte **použít**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Snímek obrazovky pravého podokna zobrazující tlačítka filtrovat a použít, číslo ID a zvýrazněné pole dotazu.":::

### <a name="graph-management"></a>Správa grafů

#### <a name="create-and-modify-a-vertex"></a>Vytvoření a úprava vrcholu

* Pokud chcete vytvořit nový vrchol, otevřete v levém podokně **graf** , vyberte **nový vrchol**, upravte obsah a pak vyberte **OK**.
* Pokud chcete upravit existující vrchol, vyberte ikonu pera v pravém podokně.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Snímek obrazovky znázorňující graf vybraný v levém podokně a zobrazení nového vrcholu a ikony pera zvýrazněné v pravém podokně":::

#### <a name="delete-a-graph"></a>Odstranění grafu

* Chcete-li odstranit vrchol, vyberte ikonu Koš vedle názvu vrcholu.

#### <a name="filter-for-graph"></a>Filtrování grafu

* Chcete-li upravit filtr grafu, zadejte [dotaz Gremlin](gremlin-support.md)a pak vyberte **použít filtr**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Snímek obrazovky znázorňující graf vybraný v levém podokně a zobrazený filtr použít a pole dotazu, které se zvýrazní v pravém podokně":::

### <a name="table-management"></a>Správa tabulek

#### <a name="create-and-modify-a-table"></a>Vytvoření a úprava tabulky

* Vytvoření nové tabulky:
   1. V levém podokně otevřete **entity** a pak vyberte **Přidat**.
   1. V dialogovém okně **Přidat entitu** upravte obsah.
   1. Kliknutím na tlačítko **Přidat vlastnost** přidejte vlastnost.
   1. Vyberte **Vložit**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Snímek obrazovky znázorňující entity zvýrazněné v levém podokně a zobrazení položky přidat, upravit, přidat vlastnost a vložit zvýrazněný v pravém podokně":::

* Chcete-li upravit tabulku, vyberte možnost **Upravit**, upravit obsah a pak vyberte možnost **aktualizovat**.

   

#### <a name="import-and-export-table"></a>Import a export tabulky

* Chcete-li importovat, vyberte tlačítko **Import** a pak zvolte existující tabulku.
* Pro export vyberte tlačítko **exportovat** a pak zvolte cíl.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Snímek obrazovky znázorňující tlačítka pro import a export zvýrazněná v pravém podokně":::

#### <a name="delete-entities"></a>Odstranění entit

* Vyberte entity a pak vyberte tlačítko **Odstranit** .

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Snímek obrazovky s zvýrazněným tlačítkem odstranit v pravém podokně a automaticky otevírané okno pro potvrzení se zvýrazněnou možností Ano":::

#### <a name="query-a-table"></a>Dotazování tabulky

- Vyberte tlačítko **dotaz** , zadejte podmínku dotazu a potom vyberte tlačítko **Spustit dotaz** . Chcete-li zavřít podokno dotazu, vyberte tlačítko **Zavřít dotaz** .

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Snímek obrazovky pravého podokna zobrazující tlačítko spustit dotaz a zvýrazněný tlačítko Zavřít dotaz":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Správa uložených procedur, triggerů a funkcí definovaných uživatelem

* Postup vytvoření uložené procedury:
  1. V levém stromu klikněte pravým tlačítkem na **uložené procedury** a pak vyberte **vytvořit uloženou proceduru**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Snímek obrazovky levého podokna zobrazující nabídku, která se zobrazí po kliknutí pravým tlačítkem myši s zvýrazněnou možností vytvořit uloženou proceduru":::
  
  1. Do levého podokna zadejte název, zadejte v pravém podokně skripty uložené procedury a pak vyberte **vytvořit**.
  
* Chcete-li upravit existující uloženou proceduru, dvakrát klikněte na postup, proveďte aktualizaci a pak vyberte **aktualizovat** , aby se uložilo. Můžete také vybrat možnost **Zahodit** a zrušit tak změnu.

* Operace pro **aktivační události** a systém **souborů UDF** jsou podobné **uloženým procedurám**.

## <a name="troubleshooting"></a>Řešení potíží

Níže jsou uvedena řešení běžných potíží, které vznikají při použití Azure Cosmos DB v Průzkumník služby Storage.

### <a name="sign-in-issues"></a>Problémy s přihlášením

Nejdřív restartujte aplikaci, abyste viděli, jestli problém vyřeší. Pokud problém přetrvává, pokračujte v řešení potíží.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certifikát podepsaný svým držitelem v řetězu certifikátů

Existuje několik důvodů, proč se tato chyba může zobrazit, dva nejběžnější jsou:

* Jste za *transparentním proxy serverem*. Někdo, podobně jako vaše IT oddělení, zachycuje provoz HTTPS, dešifruje ho a pak ho zašifruje pomocí certifikátu podepsaného svým držitelem.

* Spouštíte software, například antivirový software. Software vloží certifikát TLS/SSL podepsaný svým držitelem do zpráv HTTPS, které obdržíte.

Když Průzkumník služby Storage najde certifikát podepsaný svým držitelem, neví, jestli je zpráva HTTPS, kterou obdrží, úmyslně poškozená. Pokud máte kopii certifikátu podepsaného svým držitelem, můžete mu sdělit Průzkumník služby Storage, aby mu důvěřoval. Pokud si nejste jistí, kdo certifikát vložil, můžete pomocí těchto kroků zkusit zjistit:

1. Nainstalovat OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): všechny verze světla jsou v pořádku.
     - macOS a Linux: měla by být součástí vašeho operačního systému.

1. Spusťte OpenSSL:
    * Windows: přejděte do instalačního adresáře a pak **/bin/** dvakrát klikněte na **openssl.exe**.
    * Mac a Linux: Spusťte **OpenSSL** z terminálu.
1. Provést `s_client -showcerts -connect microsoft.com:443` .
1. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jistí, které jsou podepsané svým držitelem, pak se podíváme na jakékoli místo, kde předmět ("s:") a Vystavitel ("i:") jsou stejné.
1. Pokud najdete všechny certifikáty podepsané svým držitelem, zkopírujte a vložte vše z a včetně **-----Spustit certifikát-----** , aby se **-----koncových certifikátů-----** k novému. Soubor CER pro každé z nich.
1. Otevřete Průzkumník služby Storage a pak pokračujte v **úpravách**  >  **certifikátů SSL**  >  **Import certifikátů**. Pomocí nástroje pro výběr souborů vyhledejte, vyberte a pak otevřete. Soubory CER, které jste vytvořili.

Pokud nenajdete žádné certifikáty podepsané svým držitelem, můžete pro další nápovědu odeslat zpětnou vazbu.

#### <a name="unable-to-retrieve-subscriptions"></a>Nelze načíst předplatná

Pokud po přihlášení nemůžete načíst vaše předplatná, zkuste tyto návrhy:

* Ověřte, že váš účet má přístup k předplatným. Uděláte to tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/).
* Ujistěte se, že jste se přihlásili ke správnému prostředí:
  * [Azure](https://portal.azure.com/)
  * [Azure (Čína)](https://portal.azure.cn/)
  * [Azure (Německo)](https://portal.microsoftazure.de/)
  * [Azure pro vládu USA](https://portal.azure.us/)
  * Vlastní prostředí/Azure Stack
* Pokud jste za proxy serverem, ujistěte se, že je proxy serveru Průzkumník služby Storage správně nakonfigurovaný.
* Odeberte účet a pak ho znovu přidejte.
* Odstraňte následující soubory z domovského adresáře (například: C:\Users\ContosoUser) a pak znovu přidejte účet:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Stisknutím klávesy F12 otevřete konzolu pro vývojáře. Pokud se přihlašujete, podívejte se na konzolu s případnými chybovými zprávami.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Snímek obrazovky s konzolou nástroje pro vývojáře, která zobrazuje zvýrazněnou konzolu":::

#### <a name="unable-to-see-the-authentication-page"></a>Ověřovací stránka se nezobrazuje

Pokud se vám nezobrazuje ověřovací stránka:

* V závislosti na rychlosti připojení může trvat nějakou dobu, než se přihlašovací stránka načte. Před zavřením dialogového okna ověřování počkejte alespoň jednu minutu.
* Pokud jste za proxy serverem, ujistěte se, že je proxy serveru Průzkumník služby Storage správně nakonfigurovaný.
* V konzole nástroje pro vývojáře (F12) Sledujte odpovědi a podívejte se, jestli můžete najít jakékoli potvrzení, proč ověřování nefunguje.

#### <a name="cant-remove-an-account"></a>Nejde odebrat účet.

Pokud nemůžete odebrat účet, nebo pokud odkaz na opětovné ověření nedělá cokoli:

* Odstraňte následující soubory z domovského adresáře a pak znovu přidejte účet:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Pokud chcete odebrat prostředky služby Storage připojené sdíleným přístupovým podpisem, odstraňte:
  * Složku %AppData%/StorageExplorer ve Windows.
  * /Users/<your_name>/Library/Application Support podpora/StorageExplorer pro macOS
  * Složku ~/.config/StorageExplorer v Linuxu.
  
  > [!NOTE]
  > Pokud tyto soubory odstraníte, **musíte znovu zadat všechny přihlašovací údaje**.

### <a name="httphttps-proxy-issue"></a>Problém s proxy HTTP/HTTPS

Když nakonfigurujete proxy server HTTP/HTTPS v pomocném mechanismu služby, nemůžete v levém stromu zobrazit seznam uzlů Azure Cosmos DB. Azure Cosmos DB Průzkumníku dat v Azure Portal můžete použít jako práci.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problém s uzlem Vývoj v uzlu Místní a připojené

Po výběru uzlu pro **vývoj** pod **místním a připojeným** uzlem v levém stromu není žádná odpověď. Jedná se o očekávané chování.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Snímek obrazovky znázorňující vybraný vývojový uzel":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Při chybě **místního a připojeného** uzlu připojit Azure Cosmos DB účet

Pokud se po připojení Azure Cosmos DB účtu v **místním a připojeném** uzlu zobrazí následující chyba, ujistěte se, že používáte správný připojovací řetězec.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Snímek obrazovky s automaticky otevíraných oknem chyby při načítání podřízených prostředků, který označuje getaddrinfo ENOTFOUND.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Chyba rozbalení uzlu služby Azure Cosmos DB

Při pokusu o rozbalení uzlů v levém stromu se může zobrazit následující chyba.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Snímek obrazovky s místním oknem pro chyby nelze načíst podřízené prostředky, což značí, že se nemůže připojit k tomuto účtu Cosmos D B.":::

Vyzkoušejte tyto návrhy:

* Ověřte, zda Azure Cosmos DB účet probíhá zřizování. Zkuste to znovu, až se účet úspěšně vytvoří.
* Pokud je účet pod **rychlým přístupem** nebo **místními a připojenými** uzly, ověřte, jestli je účet odstraněný. Pokud ano, budete muset uzel odebrat ručně.

## <a name="next-steps"></a>Další kroky

* Podívejte se na toto video, kde se dozvíte, jak používat Azure Cosmos DB v Průzkumník služby Azure Storage: [použijte Azure Cosmos DB v Průzkumník služby Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Další informace o Průzkumníku služby Storage a připojení dalších služeb najdete v tématu [Začínáme s Průzkumníkem služby Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).
