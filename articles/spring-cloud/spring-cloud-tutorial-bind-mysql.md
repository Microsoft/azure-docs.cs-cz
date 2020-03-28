---
title: Kurz – jak svázat instanci Azure Database for MySQL s vaší aplikací Azure Spring Cloud
description: V tomto kurzu se vám ukáže, jak svázat instanci Azure Database for MySQL s vaší aplikací Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277541"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Kurz: Svázání instance Azure Database for MySQL s vaší aplikací Azure Spring Cloud 

Pomocí Azure Spring Cloud můžete automaticky svázat vybrané služby Azure s vašimi aplikacemi, místo abyste museli ručně nakonfigurovat aplikaci spring boot. Tento kurz ukazuje, jak svázat aplikaci s vaší azure databáze pro mysql instance.

## <a name="prerequisites"></a>Požadavky

* Nasazená instance Azure Spring Cloud
* Databáze Azure pro účet MySQL
* Azure CLI

Pokud nemáte nasazenou instanci Azure Spring Cloud, postupujte podle pokynů na [úvodním panelu: Spusťte aplikaci Azure Spring Cloud pomocí portálu Azure portal](spring-cloud-quickstart-launch-app-portal.md) k nasazení první aplikace Spring Cloud.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Svázání aplikace s instancí Azure Database for MySQL

1. Poznamenejte si uživatelské jméno správce a heslo vašeho účtu Azure Database for MySQL. 

1. Připojte se k serveru, vytvořte databázi s názvem **testdb** z klienta MySQL a vytvořte nový účet bez správce.

1. Do souboru *pom.xml* projektu přidejte následující závislost:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. V souboru *application.properties* `spring.datasource.*` odeberte všechny vlastnosti.

1. Aktualizujte aktuální nasazení `az spring-cloud app update`spuštěním nebo vytvořte nové `az spring-cloud app deployment create`nasazení pro tuto změnu spuštěním .  Tyto příkazy buď aktualizovat nebo vytvořit aplikaci s novou závislost.

1. Na webu Azure Portal na stránce služby **Azure Spring Cloud** vyhledejte řídicí panel **aplikace**a vyberte aplikaci, která se má vázat na vaši instanci Azure Database for MySQL.  Jedná se o stejnou aplikaci, kterou jste aktualizovali nebo nasadili v předchozím kroku. 

1. Vyberte **Vazby služby**a pak vyberte tlačítko **Vytvořit vazbu služby.** 

1. Vyplňte formulář, vyberte **Azure MySQL** jako **typ vazby**, pomocí stejného názvu databáze, který jste použili dříve, a pomocí stejného uživatelského jména a hesla, které jste si poznamenali v prvním kroku.

1. Restartujte aplikaci a tato vazba by nyní měla fungovat.

1. Chcete-li zajistit, aby vazba služby byla správná, vyberte název vazby a ověřte její podrobnosti. Pole `property` by mělo vypadat takto:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak svázat vaši aplikaci Azure Spring Cloud s instancí Azure Database for MySQL.  Další informace o správě služby Azure Spring Cloud najdete v článku o zjišťování a registraci služby.

> [!div class="nextstepaction"]
> [Povolení zjišťování a registrace služeb pomocí registru služby Spring Cloud Service](spring-cloud-service-registration.md)
