---
title: 'Kurz: Java Enterprise App on Linux'
description: Naučte se, jak získat podnikovou aplikaci Java pracující v WildFly Azure App Service na platformě Linux s připojením k databázi PostgreSQL v Azure.
author: JasonFreeberg
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 84f22d52e9a92707a26a4e64f194e82cca87757d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687440"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Kurz: Vytvoření webové aplikace Java EE a Postgres v Azure

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci Java Enterprise Edition (EE) na Azure App Service a připojit ji k databázi Postgres. Až budete hotovi, budete mít aplikaci [WildFly](https://www.wildfly.org/about/) , která ukládá data do [Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/) běžící na Azure [App Service v systému Linux](app-service-linux-intro.md).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nasazení aplikace v jazyce Java EE do Azure pomocí Maven
> * Vytvořit v Azure databázi Postgres
> * Konfigurace serveru WildFly pro použití Postgres
> * Aktualizace a opětovné nasazení aplikace
> * Spustit testy jednotek na WildFly

## <a name="prerequisites"></a>Předpoklady

1. [Stažení a instalace Gitu](https://git-scm.com/)
2. [Stažení a instalace Maven 3](https://maven.apache.org/install.html)
3. [Stažení a instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Klonování a úprava ukázkové aplikace

V tomto kroku naklonujte ukázkovou aplikaci a nakonfigurujte objektový model projektu Maven (POM nebo *pom. XML*) pro nasazení.

### <a name="clone-the-sample"></a>Vytvoření klonu ukázky

V okně terminálu přejděte do pracovního adresáře a naklonujte [ukázkové úložiště](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aktualizace Maven POM

Aktualizujte modul plug-in Azure Maven s požadovaným názvem a skupinou prostředků vašeho App Service. Nemusíte vytvářet App Service plán nebo instanci předem. Modul plug-in Maven vytvoří skupinu prostředků a App Service, pokud ještě neexistuje.

Chcete-li provést změny, můžete přejít dolů k části `<plugins>` souboru *pom. XML*, řádku 200.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Nahraďte `YOUR_APP_NAME` a `YOUR_RESOURCE_GROUP` názvy vašich App Service a skupiny prostředků.

## <a name="build-and-deploy-the-application"></a>Sestavení a nasazení aplikace

Nyní použijeme Maven k sestavení naší aplikace a jejímu nasazení na App Service.

### <a name="build-the-war-file"></a>Sestavení souboru. War

POM v tomto projektu je nakonfigurován pro zabalení aplikace do souboru webového archivu (WAR). Sestavte aplikaci pomocí Maven:

```bash
mvn clean install -DskipTests
```

Testovací případy v této aplikaci jsou navrženy tak, aby byly spouštěny při nasazení aplikace na WildFly. Testy budeme přeskočí k místnímu sestavení a testy spustíte po nasazení aplikace do App Service.

### <a name="deploy-to-app-service"></a>Nasazení do App Service

Teď, když je WAR připravený, můžeme k nasazení do App Service použít modul plug-in Azure:

```bash
mvn azure-webapp:deploy
```

Až se nasazení dokončí, pokračujte k dalšímu kroku.

### <a name="create-a-record"></a>Vytvoření záznamu

Otevřete prohlížeč a přejděte na adresu `https://<your_app_name>.azurewebsites.net/`. Gratulujeme, nasadili jste aplikaci Java EE do Azure App Service!

V tuto chvíli aplikace používá databázi H2 v paměti. Na navigačním panelu klikněte na správce a vytvořte novou kategorii. Při restartování instance App Service dojde ke ztrátě záznamu v databázi v paměti. V následujících krocích to opravíte tak, že zřídíte databázi Postgres v Azure a nakonfigurujete WildFly pro její použití.

![Umístění tlačítka správce](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Zřízení databáze Postgres

Pokud chcete zřídit databázový server Postgres, otevřete terminál a použijte příkaz [AZ Postgres Server Create](https://docs.microsoft.com/cli/azure/postgres/server) , jak je znázorněno v následujícím příkladu. Zástupné symboly (včetně lomených závorek) nahraďte zvolením hodnoty pomocí stejné skupiny prostředků, kterou jste předtím zadali pro instanci App Service. Přihlašovací údaje správce, které poskytnete, budou mít přístup k budoucímu přístupu, takže je nezapomeňte poznamenat pro pozdější použití.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Po spuštění tohoto příkazu přejděte na Azure Portal a přejděte do databáze Postgres. Když je okno zapnuté, zkopírujte hodnoty "název serveru" a "přihlašovací jméno správce serveru", budete je potřebovat později.

### <a name="allow-access-to-azure-services"></a>Povolení přístupu ke službám Azure

Na panelu **zabezpečení připojení** v okně databáze Azure přepněte **na pozici tlačítko** "povolení přístupu ke službám Azure".

![Povolení přístupu ke službám Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Aktualizace aplikace Java pro Postgres

Nyní provedeme změny v aplikaci Java, aby mohla používat naši databázi Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Přidání přihlašovacích údajů Postgres do POM

V *souboru pom. XML*nahraďte hodnoty zástupných symbolů velkými písmeny názvem vašeho serveru Postgres, přihlašovacím jménem správce a heslem. Tato pole jsou v rámci modulu plug-in Azure Maven. (Nezapomeňte v `<value>` značkách nahradit `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`a `YOUR_PG_PASSWORD`... není v rámci značek `<name>`.)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Aktualizace rozhraní API pro transakce Java

Dál je potřeba upravit konfiguraci rozhraní API pro JPA (Java Transaction API), aby naše aplikace Java komunikovala s Postgres namísto databáze H2 v paměti, kterou jsme používali dřív. Otevřete Editor souboru *Src/Main/Resources/META-INF/Persistence. XML*. Nahraďte hodnotu `<jta-data-source>` za `java:jboss/datasources/postgresDS`. JTA XML by teď mělo mít toto nastavení:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Konfigurace aplikačního serveru WildFly

Před nasazením naší překonfigurované aplikace je potřeba aktualizovat aplikační server WildFly pomocí modulu Postgres a jeho závislostí. Další informace o konfiguraci najdete na adrese [Konfigurace serveru WildFly](configure-language-java.md#configure-java-ee-wildfly).

Ke konfiguraci serveru budeme potřebovat čtyři soubory v *wildfly_config/* adresáři:

- **PostgreSQL-42.2.5. jar**: Tento soubor JAR je ovladač JDBC pro Postgres. Další informace najdete na [oficiální webové stránce](https://jdbc.postgresql.org/index.html).
- **Postgres-Module. XML**: Tento soubor XML deklaruje název pro modul Postgres (org. Postgres). Také určuje prostředky a závislosti, které jsou nezbytné pro použití modulu.
- **jboss_cli_commands. CLI**: Tento soubor obsahuje příkazy konfigurace, které budou spouštěny pomocí rozhraní příkazového řádku JBoss. Příkazy přidávají modul Postgres k aplikačnímu serveru WildFly, poskytují přihlašovací údaje, deklaruje název JNDI, nastaví prahovou hodnotu časového limitu atd. Pokud nejste obeznámeni s rozhraním příkazového řádku JBoss, přečtěte si [oficiální dokumentaci](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script. sh**: nakonec se spustí tento skript prostředí při každém spuštění instance App Service. Skript provádí pouze jednu funkci: potrubní příkazy v *jboss_cli_commands. CLI* do rozhraní příkazového řádku JBoss.

Důrazně doporučujeme, abyste si přečetli obsah těchto souborů, zejména *jboss_cli_commands. CLI*.

### <a name="ftp-the-configuration-files"></a>Konfigurační soubory FTP

Budeme potřebovat FTP obsah *wildfly_config/* do naší App Service instance. Pokud chcete získat přihlašovací údaje FTP, klikněte na tlačítko **získat profil publikování** v okně App Service v Azure Portal. Vaše uživatelské jméno a heslo FTP budou ve staženém dokumentu XML. Další informace o profilu publikování najdete v [tomto dokumentu](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Pomocí nástroje FTP dle vašeho výběru přeneste čtyři soubory v *wildfly_config/* do */Home/site/Deployments/Tools/* . (Všimněte si, že byste neměli přenášet adresář, jenom samotné soubory.)

### <a name="finalize-app-service"></a>Dokončit App Service

V okně App Service přejděte na panel nastavení aplikace. V části "modul runtime" nastavte pole spouštěcí soubor na */home/site/deployments/tools/startup_script. sh*. Tím se zajistí, že se skript prostředí spustí po vytvoření instance App Service, ale před spuštěním serveru WildFly.

Nakonec restartujte App Service. Tlačítko je na panelu přehled.

## <a name="redeploy-the-application"></a>Opětovné nasazení aplikace

V okně terminálu znovu sestavte a znovu nasaďte aplikaci.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Blahopřejeme! Vaše aplikace nyní používá databázi Postgres a všechny záznamy vytvořené v aplikaci budou uloženy v Postgres namísto předchozí databáze H2 v paměti. Pokud to chcete potvrdit, můžete vytvořit záznam a restartovat App Service. Až se aplikace restartuje, budou se tyto záznamy pořád nacházet.

## <a name="clean-up"></a>Vyčištění

Pokud tyto prostředky nepotřebujete pro jiný kurz (viz další kroky), můžete je odstranit spuštěním následujícího příkazu:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace v jazyce Java EE do Azure pomocí Maven
> * Vytvořit v Azure databázi Postgres
> * Konfigurace serveru WildFly pro použití Postgres
> * Aktualizace a opětovné nasazení aplikace
> * Spustit testy jednotek na WildFly

Přejděte k dalšímu kurzu, kde se dozvíte, jak namapovat vlastní název DNS na svou aplikaci.

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](../app-service-web-tutorial-custom-domain.md)

Nebo si prohlédněte další zdroje informací:

> [!div class="nextstepaction"]
> [Konfigurace aplikace Java](configure-language-java.md)
