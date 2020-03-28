---
title: zahrnout soubor
description: zahrnout soubor
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: e7a6f7b4ba4219483cd3eb8f4600bc94213df131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973406"
---
Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Vytvoření databáze Azure pro PostgreSQL – hyperškálování (Citus)

Server Azure Database for PostgreSQL vytvoříte pomocí tohoto postupu:
1. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure.
2. Na stránce **Nový** vyberte **Databáze** a na stránce **Databáze** vyberte **Azure Database for PostgreSQL**.
3. Možnost nasazení získáte klepnutím na tlačítko **Vytvořit** v části **Skupina serverů Hyperscale (Citus).**
4. Do formuláře podrobností o novém serveru zadejte následující informace:
   - Skupina zdrojů: Pod textovým polem pro toto pole klikněte na vytvořit **nový** odkaz. Zadejte název, například **myresourcegroup**.
   - Název skupiny serveru: Zadejte jedinečný název pro novou skupinu serverů, která bude použita také pro subdoménu serveru.
   - Admin uživatelské jméno: v současné době musí být hodnota **citus**, a nelze změnit.
   - Heslo: musí mít trvat nejméně osm znaků a obsahovat znaky ze tří z následujících kategorií – anglická velká písmena, anglická malá písmena, číslice (0-9) a nealfanumerické znaky (!, $, #, %a tak dále.)
   - Umístění: Použijte umístění, které je uživatelům nejblíže, a podejte jim nejrychlejší přístup k datům.

   > [!IMPORTANT]
   > Heslo správce serveru, které zde zadáte, je nutné pro přihlášení k serveru a jeho databázím. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.

5. Klepněte na **tlačítko Konfigurovat skupinu serverů**. Ponechte nastavení v tomto oddíle beze změny a klepněte na **uložit**.
6. Klikněte na **Další : Síťové >** v dolní části obrazovky.

7. Na kartě **Síť** klikněte na přepínací tlačítko **Veřejný koncový bod.**
   ![Vybraný veřejný koncový bod](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klepněte na odkaz **+ Přidat aktuální IP adresu klienta**.
   ![Přidána IP adresa klienta](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Server Azure PostgreSQL komunikuje přes port 5432. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Pokud ano, nemůžete se připojit ke clusteru Hyperscale (Citus), pokud vaše IT oddělení neotevře port 5432.
   >

9. Klikněte na **Zkontrolovat + vytvořit** a potom **vytvořit** pro zřízení serveru. Zřizování trvá několik minut.
10. Stránka bude přesměrována na monitorování nasazení. Po dokončení živých změn stavu z **nasazení** na **možnost Vaše nasazení je dokončeno**, klikněte na položku nabídky **Výstupy** na levé straně stránky.
11. Stránka výstupů bude obsahovat název hostitele koordinátora s tlačítkem vedle něj, aby se hodnota zkopírovala do schránky. Zaznamenejte tyto informace pro pozdější použití.

## <a name="connect-to-the-database-using-psql"></a>Připojení k databázi pomocí psql

Při vytváření databáze Azure pro postgreSQL server se vytvoří výchozí databáze s názvem **citus.** Chcete-li se připojit k databázovému serveru, potřebujete připojovací řetězec a heslo správce.

1. Získejte připojovací řetězec. Na stránce skupiny serverů klepněte na položku nabídky **Připojovací řetězce.** (Je to **v**nastavení .) Najít řetězec označený **psql**. Bude mít formu:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Zkopírujte řetězec. Budete muset nahradit {vaše\_heslo}" s heslem pro správu, které jste zvolili dříve. Systém neukládá heslo ve formátu prostého textu, a proto jej nemůže zobrazit v připojovacím řetězci.

2. Otevřete okno terminálu v místním počítači.

3. Na výzvu se připojte k azure databázi pro postgresql server pomocí nástroje [psql.](https://www.postgresql.org/docs/current/app-psql.html) Předejte připojovací řetězec v uvozovkách a ujistěte se, že obsahuje vaše heslo:
   ```bash
   psql "host=..."
   ```

   Například následující příkaz se připojí k uzlu koordinátora skupiny serverů **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
