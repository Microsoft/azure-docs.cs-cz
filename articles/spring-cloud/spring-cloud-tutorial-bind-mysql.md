---
title: Vytvoření vazby instance Azure Database for MySQL k aplikaci Azure jaře Cloud | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit instanci instance Azure Database for MySQL k aplikaci Azure jaře Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 6c5cd4ac384affaedbd813f9395f997f92eb69c4
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151116"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Kurz: vytvoření vazby instance Azure Database for MySQL k aplikaci Azure jaře Cloud 

Pomocí Azure jarního cloudu můžete automaticky navazovat vybrané služby Azure na své aplikace, místo abyste museli konfigurovat aplikaci pružinového spouštění ručně. V tomto kurzu se dozvíte, jak vytvořit propojení aplikace s instancí Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

* Nasazená instance cloudu Azure pro jaře
* Účet Azure Database for MySQL
* Azure CLI

Pokud nemáte nasazenou instanci cloudového cloudu Azure, postupujte podle pokynů v části [rychlý Start: spuštění aplikace jarního cloudu v Azure pomocí Azure Portal](spring-cloud-quickstart-launch-app-portal.md) k nasazení první jarní cloudové aplikace.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Svázání aplikace s instancí Azure Database for MySQL

1. Poznamenejte si uživatelské jméno a heslo správce účtu Azure Database for MySQL. 

1. Připojte se k serveru, vytvořte databázi s názvem **TestDB** z klienta MySQL a pak vytvořte nový účet bez oprávněními správce.

1. V souboru *pom. XML* projektu přidejte následující závislost:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. V souboru *Application. Properties* odeberte vlastnosti `spring.datasource.*`.

1. Aktualizujte aktuální nasazení spuštěním `az spring-cloud app update`, nebo vytvořte nové nasazení pro tuto změnu spuštěním `az spring-cloud app deployment create`.  Tyto příkazy buď aktualizují, nebo vytvoří aplikaci s novou závislostí.

1. V Azure Portal na stránce služby **jarní cloudová služba Azure** vyhledejte **řídicí panel aplikace**a pak vyberte aplikaci, kterou chcete vytvořit jako instanci Azure Database for MySQL.  Jedná se o stejnou aplikaci, kterou jste aktualizovali nebo nasadili v předchozím kroku. 

1. Vyberte možnost **vazba služby**a pak vyberte tlačítko **vytvořit vazbu služby** . 

1. Vyplňte formulář, vyberte **Azure MySQL** jako **typ vazby**a použijte stejný název databáze, který jste použili dříve, a použijte stejné uživatelské jméno a heslo, které jste si poznamenali v prvním kroku.

1. Restartujte aplikaci a tato vazba by teď měla fungovat.

1. Chcete-li zkontrolovat, zda je vazba služby správná, vyberte název vazby a ověřte jeho podrobnosti. Pole `property` by mělo vypadat takto:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak vytvořit instanci cloudové aplikace Azure ve vaší jarní službě Azure Database for MySQL.  Další informace o správě vaší jarní cloudové služby Azure najdete v článku o zjišťování a registraci služby.

> [!div class="nextstepaction"]
> [Povolení zjišťování a registrace služeb pomocí registru jarní cloudové služby](spring-cloud-service-registration.md)

