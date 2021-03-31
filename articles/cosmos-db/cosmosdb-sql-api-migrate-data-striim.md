---
title: Migrace dat do Azure Cosmos DB účtu rozhraní SQL API pomocí Striim
description: Naučte se používat Striim k migraci dat z databáze Oracle na účet Azure Cosmos DB rozhraní SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 136853182e353ad5cd71981db5935fc3babe162e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339588"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrace dat do Azure Cosmos DB účtu rozhraní SQL API pomocí Striim
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Image Striim na webu Azure Marketplace nabízí průběžné přesuny dat z datových skladů a databází do Azure v reálném čase. Při přesouvání dat můžete provádět nenormalizované denormalizace, transformaci dat, povolit analýzy v reálném čase a scénáře generování sestav dat. Je snadné začít s Striim, aby se nepřetržitě přesunula podniková data na Azure Cosmos DB rozhraní SQL API. Azure poskytuje nabídku na webu Marketplace, která usnadňuje nasazení Striim a migraci dat do Azure Cosmos DB. 

Tento článek popisuje, jak pomocí Striim migrovat data z **databáze Oracle** na **účet Azure Cosmos DB rozhraní SQL API**.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

* Databáze Oracle běžící v místním prostředí s některými daty.

## <a name="deploy-the-striim-marketplace-solution"></a>Nasazení řešení Striim Marketplace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte **vytvořit prostředek** a vyhledejte **Striim** na webu Azure Marketplace. Vyberte první možnost a **vytvořte**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Najít položku Striim Marketplace":::

1. Potom zadejte vlastnosti konfigurace instance Striim. Prostředí Striim je nasazeno na virtuálním počítači. V podokně **základy** zadejte **uživatelské jméno virtuálního počítače**, **heslo virtuálního počítače** (Toto heslo se používá pro SSH do virtuálního počítače). Vyberte své **předplatné**, **skupinu prostředků** a **Podrobnosti o umístění** , kde byste chtěli nasadit Striim. Po dokončení vyberte **OK**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Konfigurace základního nastavení pro Striim":::

1. V podokně **Nastavení clusteru Striim** vyberte typ nasazení Striim a velikost virtuálního počítače.

   |Nastavení | Hodnota | Popis |
   | ---| ---| ---|
   |Typ nasazení Striim |Standalone | Striim může běžet v **samostatném** typu nasazení nebo **clusteru** . Samostatný režim provede nasazení serveru Striim na jednom virtuálním počítači a v závislosti na objemu dat můžete vybrat velikost virtuálních počítačů. Režim clusteru nasadí Server Striim na dva nebo víc virtuálních počítačů s vybranou velikostí. Clusterová prostředí s více než dvěma uzly nabízejí automatickou vysokou dostupnost a převzetí služeb při selhání.</br></br> V tomto kurzu můžete vybrat samostatnou možnost. Použijte výchozí virtuální počítač pro Standard_F4s velikosti.  | 
   | Název clusteru Striim|    <Striim_cluster_Name>|  Název clusteru Striim|
   | Heslo clusteru Striim|   <Striim_cluster_password>|  Heslo pro cluster|

   Po vyplnění formuláře pokračujte výběrem **OK** .

1. V podokně **nastavení přístupu Striim** NAKONFIGURUJTE **veřejnou IP adresu** (vyberte výchozí hodnoty), **název domény pro Striim**, **heslo správce** , které chcete použít pro přihlášení k uživatelskému rozhraní Striim. Nakonfigurujte virtuální síť a podsíť (vyberte výchozí hodnoty). Po vyplnění podrobností vyberte **OK** a pokračujte.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Nastavení přístupu Striim":::

1. Azure ověří nasazení a zajistí, že vše vypadá dobře. dokončení ověření trvá několik minut. Po dokončení ověření vyberte **OK**.
  
1. Nakonec si přečtěte část s podmínkami použití a vyberte **vytvořit** a vytvořte instanci Striim. 

## <a name="configure-the-source-database"></a>Konfigurace zdrojové databáze 

V této části nakonfigurujete databázi Oracle jako zdroj pro přesun dat.  K připojení k Oracle budete potřebovat [ovladač Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) . Pokud chcete číst změny ze zdrojové databáze Oracle, můžete použít [rozhraní API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) nebo Xstream. Aby bylo možné číst, zapisovat a uchovávat data z databáze Oracle, musí být ovladač Oracle JDBC přítomen v cestě třídy Java Striim.

Stáhněte si ovladač [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) do místního počítače. Budete ho instalovat v clusteru Striim později.

## <a name="configure-the-target-database"></a>Konfigurace cílové databáze

V této části nakonfigurujete Azure Cosmos DB účet rozhraní SQL API jako cíl pro přesun dat.

1. Vytvořte [Azure Cosmos DB účet rozhraní SQL API](create-cosmosdb-resources-portal.md) pomocí Azure Portal.

1. Přejděte do podokna **Průzkumník dat** v účtu Azure Cosmos. Vyberte **Nový kontejner** a vytvořte nový kontejner. Předpokládejme, že migrujete data *produktů* a *objednávek* z databáze Oracle do Azure Cosmos DB. Vytvořte novou databázi s názvem **StriimDemo** s kontejnerem s názvem **objednávky**. Zajistěte kontejner pomocí **1000 ru** (Tento příklad používá 1000 ru, ale měli byste použít propustnost očekávanou pro vaše zatížení) a **/Order_Id** jako klíč oddílu. Tyto hodnoty se budou lišit v závislosti na zdrojových datech. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="Vytvoření účtu rozhraní SQL API":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurace Azure Cosmos DB toku dat v Oracle

1. Nyní se vrátíme zpátky na Striim. Před spoluprací s Striim nainstalujte ovladač Oracle JDBC, který jste si stáhli dříve.

1. Přejděte do instance Striim, kterou jste nasadili v Azure Portal. V horním panelu nabídek vyberte tlačítko **připojit** a na kartě **SSH** zkopírujte adresu URL do pole přihlásit se **pomocí místního účtu virtuálního počítače** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Získat adresu URL SSH":::

1. Otevřete nové okno terminálu a spusťte příkaz SSH, který jste zkopírovali z Azure Portal. Tento článek používá terminál ve MacOS, můžete postupovat podle podobných pokynů pomocí výstupu nebo jiného klienta SSH na počítači s Windows. Po zobrazení výzvy zadejte **Ano** pro pokračování a zadejte **heslo** , které jste v předchozím kroku nastavili pro virtuální počítač.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Připojení k virtuálnímu počítači s Striim":::

1. Nyní otevřete novou kartu terminálu a zkopírujte soubor **ojdbc8. jar** , který jste předtím stáhli. Pomocí následujícího příkazu spojovacího bodu služby zkopírujte soubor JAR z místního počítače do složky TMP instance Striim spuštěné v Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Kopírovat soubor JAR z umístění počítače do Striim":::

1. Pak přejděte zpět do okna, kde jste provedli SSH, do instance Striim a přihlaste se jako sudo. Přesuňte soubor **ojdbc8. jar** z adresáře **adresáře/TMP** do adresáře **lib** instance Striim s následujícími příkazy:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Přesunout soubor JAR do složky lib":::


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

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Kopírovat IP adresu virtuálního počítače s Striim":::

1. Pokud chcete přejít k webovému uživatelskému rozhraní Striim, otevřete v prohlížeči novou kartu a zkopírujte veřejnou IP adresu následovanou: 9080. Přihlaste se pomocí uživatelského jména **správce** společně s heslem správce, které jste zadali v Azure Portal.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Přihlásit se k Striim":::

1. Teď přijdete na domovskou stránku Striim. Existují tři různá podokna – **řídicí panely**, **aplikace** a **SourcePreview**. Podokno řídicích panelů umožňuje přesouvat data v reálném čase a vizualizovat je. Podokno aplikace obsahuje vaše kanály streamování dat nebo datové toky. Na pravé straně stránky je SourcePreview, kde můžete před přesunutím zobrazit náhled dat.

1. Vyberte podokno **aplikace** . teď se zaměříme na toto podokno. K dispozici je celá řada ukázkových aplikací, které můžete použít k získání informací o Striim, ale v tomto článku budete vytvářet vlastní. V pravém horním rohu vyberte tlačítko **Přidat aplikaci** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Přidání aplikace Striim":::

1. Existuje několik různých způsobů, jak vytvářet Striim aplikace. Vyberte možnost **začít šablonou** a začněte se stávající šablonou.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="Spuštění aplikace pomocí šablony":::

1. V poli **šablony hledání** zadejte "Cosmos" a vyberte **cíl: Azure Cosmos DB** a potom vyberte **Oracle CDC pro Azure Cosmos DB**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Vyberte možnost Oracle CDC pro Cosmos DB":::

1. Na další stránce Pojmenujte svoji aplikaci. Můžete zadat název jako **oraToCosmosDB** a pak vybrat **Uložit**.

1. Potom zadejte zdrojovou konfiguraci zdrojové instance Oracle. Zadejte hodnotu pro **název zdroje**. Název zdroje je pouze konvence pojmenování aplikace Striim, můžete použít něco podobného jako **src_onPremOracle**. Zadejte hodnoty pro zbytek **adresy URL** zdrojových parametrů, **uživatelské jméno** a **heslo**. Vyberte **LogMiner** jako čtecí modul pro čtení dat z Oracle. Pokračujte výběrem tlačítka **Další**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="Konfigurovat parametry zdroje":::

1. Striim zkontroluje vaše prostředí a zajistí, že se může připojit ke zdrojové instanci Oracle, mít správná oprávnění a že je funkce CDC nakonfigurovaná správně. Po ověření všech hodnot vyberte **Další**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="Ověřit zdrojové parametry":::

1. Vyberte tabulky z databáze Oracle, které chcete migrovat. Například vyberte tabulku Orders (objednávky) a pak vyberte **Next (další**). 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="Vybrat zdrojové tabulky":::

1. Po výběru zdrojové tabulky můžete provádět složitější operace, jako je například mapování a filtrování. V takovém případě stačí vytvořit repliku zdrojové tabulky v Azure Cosmos DB. Chcete-li konfigurovat cíl, vyberte možnost **Další** .

1. Teď nakonfigurujeme cíl:

   * **Název cíle** – zadejte popisný název cíle. 
   * **Vstup z** – v rozevíracím seznamu vyberte vstupní datový proud od, který jste vytvořili v konfiguraci zdrojového Oracle. 
   * **Kolekce**– zadejte cílové vlastnosti konfigurace Azure Cosmos DB. Syntaxe kolekcí je **SourceSchema. Source, TargetDatabase. TargetContainer**. V tomto příkladu bude hodnota "SYSTEM. ORDERs, StriimDemo. Orders. 
   * **AccessKey** – PrimaryKey vašeho účtu Azure Cosmos.
   * **ServiceEndpoint** – identifikátor URI vašeho účtu Azure Cosmos, najdete ho v části **klíče** Azure Portal. 

   Vyberte **Uložit** a **Další**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="Konfigurovat cílové parametry":::


1. V dalším kroku se dostanete do návrháře toků, kde můžete přetahovat konektory box a vyřadit je z nich, abyste mohli vytvářet aplikace streamování. V tuto chvíli neprovedete žádné úpravy toku. Pokračujte tak a nasaďte aplikaci tak, že vyberete tlačítko **nasadit aplikaci** .
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="Nasazení aplikace":::

1. V okně nasazení můžete určit, zda chcete spustit určité části aplikace na určitých částech topologie nasazení. Vzhledem k tomu, že běží v jednoduché topologii nasazení prostřednictvím Azure, použijeme výchozí možnost.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="Použít výchozí možnost":::

1. Po nasazení můžete zobrazit náhled datového proudu a zobrazit tok dat. Vyberte ikonu **Wave** a Eyeball vedle ní. V horním řádku nabídek vyberte tlačítko **nasazeno** a vyberte **Spustit aplikaci**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="Spuštění aplikace":::

1. Při použití čtecího modulu **CDC (Change Data Capture)** Striim vybere v databázi pouze nové změny. Pokud máte data toků ze zdrojových tabulek, uvidíte je. Protože se však jedná o ukázkovou tabulku, zdroj není připojen k žádné aplikaci. Pokud používáte generátor ukázkových dat, můžete do své databáze Oracle vložit řetězec událostí.

1. Budou se vám zobrazovat data toku prostřednictvím platformy Striim. Striim také všechna metadata přidružená k vaší tabulce, což je užitečné k monitorování dat a zajištění, aby data byla na správném cíli.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="Konfigurace kanálu CDC":::

1. Nakonec se přihlásíme k Azure a přejdeme k vašemu účtu Azure Cosmos. Aktualizujte Průzkumník dat a uvidíte, že data dorazila.  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Ověření migrovaných dat v Azure":::

Pomocí řešení Striim v Azure můžete průběžně migrovat data do Azure Cosmos DB z různých zdrojů, jako jsou Oracle, Cassandra, MongoDB a různé další Azure Cosmos DB. Další informace najdete na [webu Striim](https://www.striim.com/), [Stažení bezplatné 30Denní zkušební verze Striim](https://go2.striim.com/download-free-trial)a při jakémkoli problému při nastavování cesty migrace pomocí Striimu uložte [žádost o podporu.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Další kroky

* Pokud migrujete data na Azure Cosmos DB SQL API, přečtěte si [článek jak migrovat data na účet rozhraní API Cassandra pomocí Striim](cosmosdb-cassandra-api-migrate-data-striim.md) .

* [Monitorování a ladění dat pomocí Azure Cosmos DB metriky](use-metrics.md)