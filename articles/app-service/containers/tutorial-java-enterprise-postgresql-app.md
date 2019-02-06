---
title: Vytvoření webové aplikace Java Enterprise na Linux – Azure App Service | Dokumentace Microsoftu
description: Naučte se zprovoznit aplikaci Java Enterprise v Wildfly ve službě Azure App Service v Linuxu.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: a6e6dfb70182d8b4924a184dcebd1d06695911a5
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747002"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Kurz: Vytvoření webové aplikace v Azure platformě Java EE určený a Postgres

Tomto kurzu se dozvíte, jak vytvořit webovou aplikaci Java Enterprise Edition (EE) ve službě Azure App Service a její připojení k databázi Postgres. Až budete hotovi, budete mít [WildFly](https://www.wildfly.org/about/) ukládající data ve [databáze Azure pro Postgres](https://azure.microsoft.com/services/postgresql/) běžící v Azure [App Service v Linuxu](app-service-linux-intro.md).

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Nasazení aplikace na platformě Java EE určený do Azure pomocí nástroje Maven
> * Vytvořit v Azure databázi Postgres
> * Konfigurace serveru WildFly pro použití Postgres
> * Aktualizace a opětovné nasazení aplikace
> * Spouštění testů jednotek WildFly

## <a name="prerequisites"></a>Požadavky

1. [Stažení a instalace Gitu](https://git-scm.com/)
2. [Stáhněte a nainstalujte Maven 3](https://maven.apache.org/install.html)
3. [Stáhnout a nainstalovat rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Klonovat a upravit ukázkové aplikace

V tomto kroku se naklonujte ukázkovou aplikaci a nakonfigurovat Maven Project objektový Model (POM nebo pom.xml) pro nasazení.

### <a name="clone-the-sample"></a>Vytvoření klonu ukázky

V okně terminálu přejděte do pracovního adresáře a naklonujte [ukázkové úložiště](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aktualizace Maven POM

Modul plug-in Maven Azure aktualizujte požadovaný název a skupinu prostředků služby App Service. Nemusíte předem vytvářet v plánu služby App Service nebo instanci. Modul plug-in Maven vytvoří skupinu prostředků a App Service, pokud ještě neexistuje. 

Posuňte se dolů k `<plugins>` část _pom.xml_, řádek 200, aby se změny. 

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
Nahraďte `YOUR_APP_NAME` a `YOUR_RESOURCE_GROUP` s názvy vaší služby App Service a skupinu prostředků.

## <a name="build-and-deploy-the-application"></a>Sestavení a nasazení aplikace

Maven nyní použije k naší aplikaci sestavit a nasadit ho do služby App Service.

### <a name="build-the-war-file"></a>Soubor .war sestavení

POM v tomto projektu je nakonfigurována pro zabalení aplikace do souboru webového archivu (WAR). Vytvoření aplikace pomocí nástroje Maven:

```bash
mvn clean install -DskipTests
```

Testovací případy v této aplikaci jsou navržené ke spuštění, když je aplikace nasazená na WildFly. Přeskočíme testy místně sestavit a spustit testy po nasazení aplikace do služby App Service.

### <a name="deploy-to-app-service"></a>Nasazení do App Service

Teď, když WAR připravený, můžeme použít modul plug-in Azure k nasazení do služby App Service:

```bash
mvn azure-webapp:deploy
```

Po dokončení nasazení pokračujte k dalšímu kroku.

### <a name="create-a-record"></a>Vytvoření záznamu

Otevřete prohlížeč a přejděte na adresu `https://<your_app_name>.azurewebsites.net/`. Blahopřejeme, úspěšně jste nasadili aplikace v jazyce Java EE do služby Azure App Service!

Aplikace je v tuto chvíli používá H2 databázi v paměti. Klikněte na tlačítko "admin" na navigačním panelu a vytvořit novou kategorii. Záznam v databázi v paměti, budou ztraceny, pokud restartování vaší instance služby App Service. V následujících krocích se tento problém vyřešit zřizování Postgres databáze v Azure a konfigurace WildFly jeho použití.

![Správce – místo](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Zřízení databáze Postgres

Ke zřízení Postgres databázový server, otevřete terminál a spusťte následující příkaz s požadované hodnoty pro název serveru, uživatelské jméno, heslo a umístění. Použijte stejnou skupinu prostředků, které vaše služba App Service je v. Poznamenejte heslo pro pozdější!

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Přejděte na portál a vyhledejte vaši databázi Postgres. Když je okno, zkopírujte "Název_serveru" a "Přihlašovací jméno správce serveru" hodnoty, budete je potřebovat později.

### <a name="allow-access-to-azure-services"></a>Povolit přístup ke službám Azure

V **zabezpečení připojení** panelu okna – Azure Database přepínací tlačítko "Povolit přístup ke službám Azure" pro **ON** pozici.

![Povolení přístupu ke službám Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Aktualizace aplikace v Javě pro Postgres

Pro aplikace Java, který umožňuje používat naši databázi Postgres jsme teď bude provést nějaké změny.

### <a name="add-postgres-credentials-to-the-pom"></a>Přidat přihlašovací údaje Postgres POM

V _pom.xml_, velká písmena zástupné hodnoty nahraďte Postgres název serveru, přihlašovací jméno správce a heslo. Tato pole jsou v rámci modulu plug-in Maven pro Azure. (Nezapomeňte nahradit `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`, a `YOUR_PG_PASSWORD` v `<value>` značky... není v rozsahu `<name>` značky!)

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

### <a name="update-the-java-transaction-api"></a>Aktualizovat transakce Java API

Dále musíme upravit konfiguraci naše Java transakcí API (JPA) tak, aby naše aplikace v Javě bude komunikovat s Postgres místo databáze H2 v paměti, kterou jsme použili dříve. Otevřete editor pro _src/main/resources/META-INF/persistence.xml_. Nahraďte hodnotu `<jta-data-source>` za `java:jboss/datasources/postgresDS`. Toto nastavení by měl mít nyní JTA soubor XML:

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>Konfigurace serveru WildFly aplikací

Před nasazením překonfigurovaná aplikace, musí aktualizujeme WildFly aplikačním serverem Postgres modul a jeho závislosti. Konfigurace serveru, potřebujeme čtyři soubory v `wildfly_config/` adresáře:

- **postgresql-42.2.5.jar**: Tento soubor JAR je ovladač JDBC pro Postgres. Další informace najdete v tématu [oficiální web](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: Tento soubor XML deklaruje název modulu Postgres (org.postgres). Určuje také prostředky a závislosti, které jsou nezbytné pro modul, který se má použít.
- **jboss_cli_commands.cl**: Tento soubor obsahuje konfiguračních příkazů, které budou spuštěny na JBoss rozhraní příkazového řádku. Příkazy přidejte modul Postgres WildFly aplikační server, zadejte přihlašovací údaje, deklarovat název JNDI, nastavit mezní hodnotu časového limitu, atd. Pokud nejste obeznámeni s JBoss CLI, přečtěte si článek [oficiální dokumentaci](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: Nakonec tento skript prostředí se spustí pokaždé, když je spuštěná instance vaší služby App Service. Skript provádí pouze jednu funkci: přesměrujete příkazy v `jboss_cli_commands.cli` JBoss rozhraní příkazového řádku.

Důrazně doporučujeme čtení obsahu těchto souborů, zejména _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Konfigurační soubory protokolu FTP

Budeme muset FTP obsah `wildfly_config/` naše instanci služby App Service. Chcete-li získat pověření serveru FTP, klikněte na tlačítko **získat profil publikování** tlačítka v okně App Service na webu Azure Portal. FTP uživatelské jméno a heslo bude v stažené dokumentu XML. Další informace o profilu publikování najdete v tématu [tento dokument](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Pomocí FTP nástroje podle vašeho výběru, přenášet čtyři soubory v `wildfly_config/` k `/home/site/deployments/tools/`. (Všimněte si, že by neměla přenos adresář pouze soubory sami.)


### <a name="finalize-app-service"></a>Dokončení služby App Service

V okně App Service, přejděte na panel "Nastavení aplikace". V části "Runtime", "Spouštěcí soubor" pole nastavte `/home/site/deployments/tools/startup_script.sh`. Tím se zajistí, že skript prostředí se spustí po vytvoření instance služby App Service, ale před WildFly spuštění serveru.

A konečně restartujte službu App Service. Toto tlačítko je v panelu "Přehled".

## <a name="redeploy-the-application"></a>Opětovné nasazení aplikace

V okně terminálu znovu sestavit a znova nasazovat aplikaci.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Blahopřejeme! Aplikace nyní používá databázi Postgres a záznamy, které v aplikaci se uloží v Postgres, spíše než předchozí H3 databázi v paměti. Potvrdit to můžete provést záznam a restartujte službu App Service. Záznamy bude stále existovat po restartování aplikace.

## <a name="clean-up"></a>Vyčištění

Pokud tyto prostředky nepotřebujete pro další kurz (viz další kroky), můžete je odstranit spuštěním následujícího příkazu:

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>Další postup

Teď, když platformě Java EE určený aplikace nasazené do služby App Service, najdete [– Příručka pro vývojáře Java Enterprise](https://aka.ms/wildfly-quickstart) Další informace o nastavení služby, řešení potíží a škálování vaší aplikace.
