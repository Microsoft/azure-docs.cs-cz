---
title: Vytvoření vazby instance Azure Database for MySQL k aplikaci Azure jaře Cloud
description: Přečtěte si, jak vytvořit instanci Azure Database for MySQL k aplikaci Azure jaře Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5ca8f2b003b7f2142da329a07f929ecf31fee627
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908265"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Navázání instance Azure Database for MySQL k aplikaci Azure jaře Cloud 

**Tento článek se týká:** ✔️ Java

Pomocí Azure jarního cloudu můžete automaticky navazovat vybrané služby Azure na své aplikace, místo abyste museli konfigurovat aplikaci pružinového spouštění ručně. V tomto článku se dozvíte, jak vytvořit propojení aplikace s instancí Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

* Nasazená instance cloudu Azure pro jaře
* Účet Azure Database for MySQL
* Azure CLI

Pokud nemáte nasazenou instanci cloudového cloudu Azure, postupujte podle pokynů v části [rychlý Start: spuštění aplikace jarního cloudu v Azure pomocí Azure Portal](spring-cloud-quickstart.md) k nasazení první jarní cloudové aplikace.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Svázání aplikace s instancí Azure Database for MySQL

1. Poznamenejte si uživatelské jméno a heslo správce účtu Azure Database for MySQL. 

1. Připojte se k serveru, vytvořte databázi s názvem **TestDB** z klienta MySQL a pak vytvořte nový účet bez oprávněními správce.

1. Do souboru *pom.xml* projektu přidejte následující závislost:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. V souboru *Application. Properties* odeberte všechny `spring.datasource.*` Vlastnosti.

1. Aktualizujte aktuální nasazení spuštěním `az spring-cloud app update` nebo vytvořte nové nasazení pro tuto změnu spuštěním `az spring-cloud app deployment create` .  Tyto příkazy buď aktualizují, nebo vytvoří aplikaci s novou závislostí.

1. V Azure Portal na stránce služby **jarní cloudová služba Azure** vyhledejte **řídicí panel aplikace**a pak vyberte aplikaci, kterou chcete vytvořit jako instanci Azure Database for MySQL.  Jedná se o stejnou aplikaci, kterou jste aktualizovali nebo nasadili v předchozím kroku. 

1. Vyberte možnost **vazba služby**a pak vyberte tlačítko **vytvořit vazbu služby** . 

1. Vyplňte formulář, vyberte **Azure MySQL** jako **typ vazby**a použijte stejný název databáze, který jste použili dříve, a použijte stejné uživatelské jméno a heslo, které jste si poznamenali v prvním kroku.

1. Restartujte aplikaci a tato vazba by teď měla fungovat.

1. Chcete-li zkontrolovat, zda je vazba služby správná, vyberte název vazby a ověřte jeho podrobnosti. `property`Pole by mělo vypadat takto:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak navazovat cloudovou aplikaci Azure na jaře na instanci Azure Database for MySQL. Další informace o vázání služeb na aplikaci najdete v tématu [vázání databáze Azure Cosmos DB k aplikaci Azure jaře Cloud](spring-cloud-tutorial-bind-cosmos.md).
