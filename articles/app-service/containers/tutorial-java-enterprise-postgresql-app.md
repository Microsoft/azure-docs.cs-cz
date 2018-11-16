---
title: Vytvoření webové aplikace Java Enterprise ve službě Azure App Service v Linuxu | Dokumentace Microsoftu
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
ms.openlocfilehash: 40bee31b7880a323a48e92912ee323c43c3a97da
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634745"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Kurz: Sestavení webové aplikace v Azure platformě Java EE určený a Postgres

Tomto kurzu se dozvíte, jak vytvořit webovou aplikaci Java Enterprise Edition (EE) ve službě Azure App Service a její připojení k databázi Postgres. Až budete hotovi, budete mít [WildFly](http://www.wildfly.org/about/) ukládající data ve [databáze Azure pro Postgres](https://azure.microsoft.com/services/postgresql/) běžící v Azure [služby App Service pro Linux](app-service-linux-intro.md).

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Nasazení aplikace na platformě Java EE určený do Azure pomocí nástroje Maven
> * Vytvořit v Azure databázi Postgres
> * Konfigurace serveru WildFly pro použití Postgres
> * Aktualizace a opětovné nasazení aplikace
> * Spouštění testů jednotek WildFly

## <a name="prerequisites"></a>Požadavky

1. [Stažení a instalace Gitu](https://git-scm.com/)
1. [Stáhněte a nainstalujte Maven 3](https://maven.apache.org/install.html)
1. [Stáhnout a nainstalovat rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Klonovat a upravit ukázkové aplikace

V tomto kroku se naklonujte ukázkovou aplikaci a nakonfigurovat Maven Project objektový Model (POM nebo pom.xml) pro nasazení.

### <a name="clone-the-sample"></a>Vytvoření klonu ukázky

V okně terminálu přejděte do pracovního adresáře a naklonujte [ukázkové úložiště](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aktualizace Maven POM

Aktualizujte Maven POM požadovaný název a skupinu prostředků služby App Service. Tyto hodnoty budou vloženy do modul plug-in Azure, která je dále dolů v _pom.xml_ souboru. Nemusíte předem vytvářet v plánu služby App Service nebo instanci. Modul plug-in Maven vytvoří skupinu prostředků a App Service, pokud ještě neexistuje.

Nahraďte zástupné symboly názvy požadovaných prostředků:
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```

Posuňte se dolů k `<plugins>` část _pom.xml_ ke kontrole modul plug-in Azure.

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

V _pom.xml_ zástupné hodnoty nahraďte Postgres název serveru, přihlašovací jméno správce a heslo. Tyto hodnoty budou vloženy jako proměnné prostředí ve vaší instanci služby App Service při opětovném nasazování aplikace.

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
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

- **postgresql 42.2.5.jar**: JAR tento soubor je ovladač JDBC pro Postgres. Další informace najdete v tématu [oficiální web](https://jdbc.postgresql.org/index.html).
- **postgres module.xml**: tento XML souboru deklaruje název modulu Postgres (org.postgres). Určuje také prostředky a závislosti, které jsou nezbytné pro modul, který se má použít.
- **jboss_cli_commands.cl**: Tento soubor obsahuje konfiguračních příkazů, které budou spuštěny na JBoss rozhraní příkazového řádku. Příkazy přidejte modul Postgres WildFly aplikační server, zadejte přihlašovací údaje, deklarovat název JNDI, nastavit mezní hodnotu časového limitu, atd. Pokud nejste obeznámeni s JBoss CLI, přečtěte si článek [oficiální dokumentaci](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.SH**: nakonec tento skript prostředí se spustí pokaždé, když je spuštěná instance vaší služby App Service. Skript provádí pouze jednu funkci: přesměrujete příkazy v `jboss_cli_commands.cli` JBoss rozhraní příkazového řádku.

Důrazně doporučujeme čtení obsahu těchto souborů, zejména _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Konfigurační soubory protokolu FTP

Budeme muset FTP obsah `wildfly_config/` naše instanci služby App Service. Chcete-li získat pověření serveru FTP, klikněte na tlačítko **získat profil publikování** tlačítka v okně App Service na webu Azure Portal. FTP uživatelské jméno a heslo bude v stažené dokumentu XML. Další informace o profilu publikování najdete v tématu [tento dokument](https://docs.microsoft.com/azure/app-service/app-service-deployment-credentials).

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