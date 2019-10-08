---
title: Postup vytvoření vazby Azure Database for MySQL k aplikaci Azure jaře Cloud | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit propojení Azure MySQL s vaší aplikací pro jarní cloudovou službu Azure.
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039018"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Kurz: vázání služeb Azure na svoji cloudovou aplikaci Azure na jaře: Azure Database for MySQL

Pomocí Azure jaře cloudu můžete automaticky navazovat vybrané služby Azure na vaše aplikace místo ruční konfigurace aplikace pro spouštění pružiny. V tomto kurzu se dozvíte, jak vytvořit propojení aplikace s Azure MySQL.

## <a name="prerequisites"></a>Předpoklady

* Nasazená instance cloudu Azure pro jaře
* Účet Azure Database for MySQL
* Azure CLI

V případě potřeby nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure pomocí následujícího příkazu:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

## <a name="bind-azure-database-for-mysql"></a>Azure Database for MySQL vazby

1. Poznamenejte si uživatelské jméno a heslo správce vašeho účtu Azure MySQL. Připojte se k serveru a vytvořte databázi s názvem `testdb` z klienta MySQL. Vytvořte nový účet bez oprávněními správce.

1. Přidejte následující závislost do @no__t projektu-0

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Odeberte vlastnosti `spring.datasource.*`, pokud existují, v souboru `application.properties`.

1. Aktualizujte aktuální nasazení pomocí `az spring-cloud app update` nebo vytvořte nové nasazení pro tuto změnu pomocí `az spring-cloud app deployment create`.  Tyto příkazy buď aktualizují, nebo vytvoří aplikaci s novou závislostí.

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Najděte **řídicí panel aplikace** a vyberte aplikaci, kterou chcete vytvořit z Azure MySQL.  Jedná se o stejnou aplikaci, kterou jste aktualizovali nebo nasadili v předchozím kroku. V dalším kroku vyberte `Service binding` a vyberte tlačítko `Create service binding`. Vyplňte formulář, ujistěte se, že jste vybrali **typ vazby** `Azure MySQL`, stejný název databáze, který jste použili dříve, a stejné uživatelské jméno a heslo, které jste si poznamenali v prvním kroku.

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

