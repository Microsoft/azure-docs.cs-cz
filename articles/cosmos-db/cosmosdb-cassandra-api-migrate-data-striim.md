---
title: Migrace dat do Azure Cosmos DB rozhraní API Cassandra účtu pomocí Striim
description: Naučte se používat Striim k migraci dat z databáze Oracle na účet Azure Cosmos DB rozhraní API Cassandra.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 7590d40085c3963a95fd251dd1291cf34fbaf4a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85262085"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migrace dat do Azure Cosmos DB rozhraní API Cassandra účtu pomocí Striim

Image Striim na webu Azure Marketplace nabízí průběžné přesuny dat z datových skladů a databází do Azure v reálném čase. Při přesouvání dat můžete provádět nenormalizované denormalizace, transformaci dat, povolit analýzy v reálném čase a scénáře generování sestav dat. Je snadné začít s Striim, aby se nepřetržitě přesunuly podniková data na Azure Cosmos DB rozhraní API Cassandra. Azure poskytuje nabídku na webu Marketplace, která usnadňuje nasazení Striim a migraci dat do Azure Cosmos DB. 

V tomto článku se dozvíte, jak pomocí Striim migrovat data z **databáze Oracle** na **účet služby Azure Cosmos DB rozhraní API Cassandra**.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte [předplatné Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

* Databáze Oracle běžící v místním prostředí s některými daty.

## <a name="deploy-the-striim-marketplace-solution"></a>Nasazení řešení Striim Marketplace

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Vyberte **vytvořit prostředek** a vyhledejte **Striim** na webu Azure Marketplace. Vyberte první možnost a **vytvořte**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Najít položku Striim Marketplace":::

1. Potom zadejte vlastnosti konfigurace instance Striim. Prostředí Striim je nasazeno na virtuálním počítači. V podokně **základy** zadejte **uživatelské jméno virtuálního počítače**, **heslo virtuálního počítače** (Toto heslo se používá pro SSH do virtuálního počítače). Vyberte své **předplatné**, **skupinu prostředků**a **Podrobnosti o umístění** , kde byste chtěli nasadit Striim. Po dokončení vyberte **OK**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Najít položku Striim Marketplace":::


1. V podokně **Nastavení clusteru Striim** vyberte typ nasazení Striim a velikost virtuálního počítače.

   |Nastavení | Hodnota | Popis |
   | ---| ---| ---|
   |Typ nasazení Striim |Standalone | Striim může běžet v **samostatném** typu nasazení nebo **clusteru** . Samostatný režim provede nasazení serveru Striim na jednom virtuálním počítači a v závislosti na objemu dat můžete vybrat velikost virtuálních počítačů. Režim clusteru nasadí Server Striim na dva nebo víc virtuálních počítačů s vybranou velikostí. Clusterová prostředí s více než dvěma uzly nabízejí automatickou vysokou dostupnost a převzetí služeb při selhání.</br></br> V tomto kurzu můžete vybrat samostatnou možnost. Použijte výchozí virtuální počítač pro Standard_F4s velikosti. | 
   | Název clusteru Striim|    <Striim_cluster_Name>|  Název clusteru Striim|
   | Heslo clusteru Striim|   <Striim_cluster_password>|  Heslo pro cluster|

   Po vyplnění formuláře pokračujte výběrem **OK** .

1. V podokně **nastavení přístupu Striim** NAKONFIGURUJTE **veřejnou IP adresu** (vyberte výchozí hodnoty), **název domény pro Striim**, **heslo správce** , které chcete použít pro přihlášení k uživatelskému rozhraní Striim. Nakonfigurujte virtuální síť a podsíť (vyberte výchozí hodnoty). Po vyplnění podrobností vyberte **OK** a pokračujte.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Najít položku Striim Marketplace":::

1. Azure ověří nasazení a zajistí, že vše vypadá dobře. dokončení ověření trvá několik minut. Po dokončení ověření vyberte **OK**.
  
1. Nakonec si přečtěte část s podmínkami použití a vyberte **vytvořit** a vytvořte instanci Striim. 

## <a name="configure-the-source-database"></a>Konfigurace zdrojové databáze

V této části nakonfigurujete databázi Oracle jako zdroj pro přesun dat.  K připojení k Oracle budete potřebovat [ovladač Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) . Pokud chcete číst změny ze zdrojové databáze Oracle, můžete použít [rozhraní API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) nebo Xstream. Aby bylo možné číst, zapisovat a uchovávat data z databáze Oracle, musí být ovladač Oracle JDBC přítomen v cestě třídy Java Striim.

Stáhněte si ovladač [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) do místního počítače. Budete ho instalovat v clusteru Striim později.

## <a name="configure-target-database"></a>Konfigurovat cílovou databázi

V této části nakonfigurujete účet Azure Cosmos DB rozhraní API Cassandra jako cíl pro přesun dat.

1. Vytvořte [účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account) pomocí Azure Portal.

1. Přejděte do podokna **Průzkumník dat** v účtu Azure Cosmos. Vyberte **Nová tabulka** a vytvořte nový kontejner. Předpokládejme, že migrujete data *produktů* a *objednávek* z databáze Oracle do Azure Cosmos DB. Vytvoří nový prostor s názvem **StriimDemo** s kontejnerem Orders. Zajistěte kontejner pomocí **1000 ru**(Tento příklad používá 1000 ru, ale měli byste použít propustnost odhadovanou pro vaše zatížení) a **/Order_Id** jako primární klíč. Tyto hodnoty se budou lišit v závislosti na zdrojových datech. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png" alt-text="Najít položku Striim Marketplace":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurace Azure Cosmos DB toku dat v Oracle

1. Nyní se vrátíme zpátky na Striim. Před spoluprací s Striim nainstalujte ovladač Oracle JDBC, který jste si stáhli dříve.

1. Přejděte do instance Striim, kterou jste nasadili v Azure Portal. V horním panelu nabídek vyberte tlačítko **připojit** a na kartě **SSH** zkopírujte adresu URL do pole přihlásit se **pomocí místního účtu virtuálního počítače** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Najít položku Striim Marketplace":::

1. Otevřete nové okno terminálu a spusťte příkaz SSH, který jste zkopírovali z Azure Portal. Tento článek používá terminál ve MacOS, můžete postupovat podle podobných pokynů pomocí výstupu nebo jiného klienta SSH na počítači s Windows. Po zobrazení výzvy zadejte **Ano** pro pokračování a zadejte **heslo** , které jste v předchozím kroku nastavili pro virtuální počítač.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Najít položku Striim Marketplace":::

1. Nyní otevřete novou kartu terminálu a zkopírujte soubor **ojdbc8. jar** , který jste předtím stáhli. Pomocí následujícího příkazu spojovacího bodu služby zkopírujte soubor JAR z místního počítače do složky TMP instance Striim spuštěné v Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Najít položku Striim Marketplace":::

1. Pak přejděte zpět do okna, kde jste provedli SSH, do instance Striim a přihlaste se jako sudo. Přesuňte soubor **ojdbc8. jar** z adresáře **adresáře/TMP** do adresáře **lib** instance Striim s následujícími příkazy:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Najít položku Striim Marketplace":::


1. Ze stejného okna terminálu restartujte server Striim spuštěním následujících příkazů:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Spuštění Striim bude trvat minutu. Pokud chcete zobrazit stav, spusťte následující příkaz: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Nyní přejděte zpět do Azure a zkopírujte veřejnou IP adresu vašeho virtuálního počítače s Striim. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Najít položku Striim Marketplace":::

1. Pokud chcete přejít k webovému uživatelskému rozhraní Striim, otevřete v prohlížeči novou kartu a zkopírujte veřejnou IP adresu následovanou: 9080. Přihlaste se pomocí uživatelského jména **správce** společně s heslem správce, které jste zadali v Azure Portal.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Najít položku Striim Marketplace":::

1. Teď přijdete na domovskou stránku Striim. Existují tři různá podokna – **řídicí panely**, **aplikace**a **SourcePreview**. Podokno řídicích panelů umožňuje přesouvat data v reálném čase a vizualizovat je. Podokno aplikace obsahuje vaše kanály streamování dat nebo datové toky. Na pravé straně stránky je SourcePreview, kde můžete před přesunutím zobrazit náhled dat.

1. Vyberte podokno **aplikace** . teď se zaměříme na toto podokno. K dispozici je celá řada ukázkových aplikací, které můžete použít k získání informací o Striim, ale v tomto článku budete vytvářet vlastní. V pravém horním rohu vyberte tlačítko **Přidat aplikaci** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Najít položku Striim Marketplace":::

1. Existuje několik různých způsobů, jak vytvářet Striim aplikace. Pro tento scénář vyberte **začít od začátku** .

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png" alt-text="Najít položku Striim Marketplace":::

1. Zadejte popisný název vaší aplikace, třeba **oraToCosmosDB** , a vyberte **Uložit**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png" alt-text="Najít položku Striim Marketplace":::

1. Dostanete se do návrháře toků, kde můžete přetáhnout a umístit konektory box a vytvořit tak aplikace streamování. Na panelu hledání zadejte **Oracle** a přetáhněte zdroj **Oracle CDC** na plátno aplikace.  

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png" alt-text="Najít položku Striim Marketplace":::

1. Zadejte vlastnosti zdrojové konfigurace vaší instance Oracle. Název zdroje je pouze konvence pro pojmenování aplikace Striim, můžete použít název, například  **src_onPremOracle**. Zadejte také další podrobnosti, jako je typ adaptéru, adresa URL připojení, uživatelské jméno, heslo a název tabulky. Pokračujte výběrem **Uložit** .

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png" alt-text="Najít položku Striim Marketplace":::

1. Nyní kliknutím na ikonu Wave streamu připojte cílovou instanci Azure Cosmos DB. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png" alt-text="Najít položku Striim Marketplace":::

1. Před konfigurací cíle se ujistěte, že jste přidali [kořenový certifikát Baltimore do prostředí Java Striim](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store).

1. Zadejte vlastnosti konfigurace vaší cílové instance Azure Cosmos DB a pokračujte výběrem možnosti **Uložit** . Tady jsou klíčové parametry k označení:

   * **Adaptér** – použijte **DatabaseWriter**. Při zápisu do Azure Cosmos DB rozhraní API Cassandra se vyžaduje DatabaseWriter. Cassandra ovladač 3.6.0 je součástí sady Striim. Pokud DatabaseWriter překročí počet Ruů zřízených v kontejneru Azure Cosmos, aplikace selže.

   * **Adresa URL připojení** – zadejte adresu URL pro připojení Azure Cosmos DB JDBC. Adresa URL má formát.     `jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Uživatelské jméno** – zadejte název svého účtu Azure Cosmos.
   
   * **Heslo** – zadejte primární klíč svého účtu Azure Cosmos.

   * **Tabulky** – cílové tabulky musí mít primární klíče a primární klíče nelze aktualizovat.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png" alt-text="Najít položku Striim Marketplace":::

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png" alt-text="Najít položku Striim Marketplace":::

1. Teď budeme pokračovat a spustíme aplikaci Striim. V horním řádku nabídek vyberte **Vytvořeno**a pak **Nasaďte aplikaci**. V okně nasazení můžete určit, jestli chcete spouštět určité části aplikace na určitých částech topologie nasazení. Vzhledem k tomu, že běží v jednoduché topologii nasazení prostřednictvím Azure, použijeme výchozí možnost.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png" alt-text="Najít položku Striim Marketplace":::


1. Nyní přejdeme do datového proudu a zobrazíme náhled toku dat pomocí Striim. Klikněte na ikonu Wave a vedle ní klikněte na ikonu oka. Po nasazení můžete zobrazit náhled datového proudu a zobrazit tok dat. Vyberte ikonu **Wave** a **Eyeball** vedle ní. V horním řádku nabídek vyberte tlačítko **nasazeno** a vyberte **Spustit aplikaci**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png" alt-text="Najít položku Striim Marketplace":::

1. Při použití čtecího modulu **CDC (Change Data Capture)** Striim vybere v databázi pouze nové změny. Pokud máte data toků ze zdrojových tabulek, uvidíte je. Protože se ale jedná o ukázkovou tabulku, zdroj, který není připojený k žádné aplikaci. Pokud používáte generátor ukázkových dat, můžete do své databáze Oracle vložit řetězec událostí.

1. Budou se vám zobrazovat data toku prostřednictvím platformy Striim. Striim také všechna metadata přidružená k vaší tabulce, což je užitečné k monitorování dat a zajištění, aby data byla na správném cíli.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png" alt-text="Najít položku Striim Marketplace":::

1. Nakonec se přihlásíme k Azure a přejdeme k vašemu účtu Azure Cosmos. Aktualizujte Průzkumník dat a uvidíte, že data dorazila. 

Pomocí řešení Striim v Azure můžete průběžně migrovat data do Azure Cosmos DB z různých zdrojů, jako jsou Oracle, Cassandra, MongoDB a různé další Azure Cosmos DB. Další informace najdete na [webu Striim](https://www.striim.com/), [Stažení bezplatné 30Denní zkušební verze Striim](https://go2.striim.com/download-free-trial)a při jakémkoli problému při nastavování cesty migrace pomocí Striimu uložte [žádost o podporu.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Další kroky

* Pokud migrujete data na Azure Cosmos DB SQL API, přečtěte si [článek jak migrovat data na účet rozhraní API Cassandra pomocí Striim](cosmosdb-sql-api-migrate-data-striim.md) .

* [Monitorování a ladění dat pomocí Azure Cosmos DB metriky](use-metrics.md)
