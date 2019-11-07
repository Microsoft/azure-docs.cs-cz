---
title: Postup vytvoření vazby Azure Database for MySQL k aplikaci Azure jaře Cloud | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit propojení Azure MySQL s vaší aplikací pro jarní cloudovou službu Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607591"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Kurz: vázání služeb Azure na svoji cloudovou aplikaci Azure na jaře: Azure Database for MySQL

Pomocí Azure jaře cloudu můžete automaticky navazovat vybrané služby Azure na vaše aplikace místo ruční konfigurace aplikace pro spouštění pružiny. V tomto kurzu se dozvíte, jak vytvořit propojení aplikace s Azure MySQL.

## <a name="prerequisites"></a>Požadavky

* Nasazená instance cloudu Azure pro jaře
* Účet Azure Database for MySQL
* Azure CLI

Pokud nemáte nasazenou instanci Azure jaře cloudu, postupujte podle kroků v tomto [rychlém](spring-cloud-quickstart-launch-app-portal.md) startu a nasaďte svou první jarní cloudovou aplikaci.

## <a name="bind-azure-database-for-mysql"></a>Azure Database for MySQL vazby

1. Poznamenejte si uživatelské jméno a heslo správce vašeho účtu Azure MySQL. Připojte se k serveru a vytvořte databázi s názvem `testdb` z klienta MySQL. Vytvořte nový účet bez oprávněními správce.

1. Do `pom.xml` projektu přidejte následující závislost.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. V souboru `application.properties` odeberte vlastnosti `spring.datasource.*`, pokud existují.

1. Aktualizujte aktuální nasazení pomocí `az spring-cloud app update` nebo vytvořte nové nasazení pro tuto změnu pomocí `az spring-cloud app deployment create`.  Tyto příkazy buď aktualizují, nebo vytvoří aplikaci s novou závislostí.

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Najděte **řídicí panel aplikace** a vyberte aplikaci, kterou chcete vytvořit z Azure MySQL.  Jedná se o stejnou aplikaci, kterou jste aktualizovali nebo nasadili v předchozím kroku. V dalším kroku vyberte `Service binding` a vyberte tlačítko `Create service binding`. Vyplňte formulář, nezapomeňte vybrat **typ vazby** `Azure MySQL`, stejný název databáze, který jste použili dříve, a stejné uživatelské jméno a heslo, které jste si poznamenali v prvním kroku.

1. Restartujte aplikaci a tato vazba by teď měla fungovat.

1. Chcete-li zajistit, aby vazba služby byla správná, vyberte název vazby a ověřte jeho podrobnosti. Pole `property` by mělo vypadat takto:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili vytvořit propojení vaší aplikace pro vaši jarní cloudovou aplikaci Azure s databází MySQL.  Další informace o správě vaší jarní cloudové služby Azure najdete v článku o zjišťování a registraci služeb.

> [!div class="nextstepaction"]
> [Naučte se, jak povolit zjišťování a registraci služeb pomocí registru jarní cloudové služby](spring-cloud-service-registration.md).

