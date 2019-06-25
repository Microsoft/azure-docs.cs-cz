---
title: zahrnout soubor
description: zahrnout soubor
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175250"
---
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Vytvoření Azure Database for PostgreSQL – velkokapacitní (Citus)

Server Azure Database for PostgreSQL vytvoříte pomocí tohoto postupu:
1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** vyberte **Databáze** a na stránce **Databáze** vyberte **Azure Database for PostgreSQL**.
3. Možnost nasazení, klikněte na tlačítko **vytvořit** tlačítko **skupinu serverů Hyperškálovatelného (Citus) – ve verzi PREVIEW.**
4. Do formuláře podrobností o novém serveru zadejte následující informace:
   - Skupina prostředků: klikněte na tlačítko **vytvořit nový** odkaz pod textové pole pro toto pole. Zadejte název, například **myresourcegroup**.
   - Název skupiny pro server: Zadejte jedinečný název pro novou skupinu serverů, který bude použit také pro subdoménu serveru.
   - Uživatelské jméno správce: aktuálně musí být hodnota **citus**a nedá se změnit.
   - Heslo: musí být alespoň osm znaků a obsahovat znaky ze tří z těchto kategorií – velká písmena anglické abecedy, malá písmena, číslice (0 – 9) a jiné než alfanumerické znaky (!, $, #, % atd.)
   - Umístění: použijte umístění co nejblíže vašim uživatelům jim zajistili nejrychlejší přístup k datům.

   > [!IMPORTANT]
   > Heslo správce serveru, který zde určíte se vyžadují pro přihlášení k serveru a jeho databázím. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.

5. Klikněte na tlačítko **konfigurovat skupinu serverů**. Ponechejte nastavení v této části beze změny a klikněte na tlačítko **Uložit**.
6. Klikněte na tlačítko **revize + vytvořit** a potom **vytvořit** ke zřízení serveru. Zřizování trvá několik minut.
7. Monitorování nasazení přesměruje na stránku. Změny za provozu stavu z **nasazení probíhá** k **vaše nasazení je kompletní**, klikněte na tlačítko **výstupy** položky nabídky na levé straně stránky.
8. Stránka výstupy bude obsahovat název hostitele koordinátor s tlačítkem vedle něj zkopírovat hodnotu do schránky. Poznamenejte si tyto údaje pro pozdější použití.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) používá službu brány firewall na úrovni serveru. Ve výchozím nastavení brána firewall brání všem externím aplikacím a nástrojům v připojení k koordinační uzel a kterékoli databázi uvnitř. Přidáme pravidlo k otevření brány firewall pro konkrétní rozsah IP adres.

1. Z **výstupy** části, kde jste dříve zkopírovali název hostitele koordinační uzel, klikněte na tlačítko zpět do **přehled** položky nabídky.

2. Najít název skupiny serverů pro vaše nasazení a klikněte na něj. (Název skupiny serveru bude *není* mít příponu. Položky s názvy, které končí na, například "-c", "-w0", nebo "-w1" nejsou skupiny serverů.)

3. Klikněte na tlačítko **brány Firewall** pod **zabezpečení** v levé nabídce.

4. Klikněte na odkaz **+ přidat pravidlo brány firewall pro aktuální IP adresa klienta**.

5. Nakonec klikněte na tlačítko **Uložit** tlačítko.

   > [!NOTE]
   > Server Azure PostgreSQL komunikuje přes port 5432. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 5432.
   >

## <a name="connect-to-the-database-using-psql"></a>Připojení k databázi pomocí nástroje psql

Při vytváření vašeho serveru Azure Database for postgresql – výchozí databázi s názvem **citus** se vytvoří. Pro připojení k vašemu databázovému serveru, budete potřebovat připojovací řetězec a heslo správce.

1. Získání připojovacího řetězce. Na stránce skupiny serveru klikněte na tlačítko **připojovací řetězce** položky nabídky. (Je podle **nastavení**.) Vyhledejte řetězec označené  **C++ (libpq)** . Bude ve tvaru:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Zkopírujte řetězec. Budete muset nahradit "{vaše\_heslo}" pomocí hesla pro správu, kterou jste zvolili dříve. V systému nejsou uloženy vaše heslo jako prostý text a proto nemůže zobrazit ho pro vás v připojovacím řetězci.

2. Otevřete okno terminálu na místním počítači.

3. Do příkazového řádku se připojit k Azure Database for PostgreSQL pomocí [psql](https://www.postgresql.org/docs/current/app-psql.html) nástroj. Předejte připojovací řetězec v uvozovkách, zkontrolujte, zda obsahuje heslo:
   ```bash
   psql "{connection_string}"
   ```

   Například následující příkaz se připojí na koordinační uzel skupiny serverů **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
