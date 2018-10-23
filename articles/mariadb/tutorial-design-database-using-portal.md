---
title: 'Kurz: Návrh služby Azure Database for MariaDB pomocí webu Azure Portal'
description: Tento kurz vysvětluje, jak vytvořit a spravovat databázi a server Azure Database for MariaDB pomocí webu Azure Portal.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: tutorial
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 20714bed5c36cd93d11cb4ade6640798775f5055
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322052"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Kurz: Návrh databáze Azure Database for MariaDB pomocí webu Azure Portal

Azure Database for MariaDB je spravovaná služba, pomocí které můžete provozovat, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. Azure Portal umožňuje snadnou správu vašeho serveru a návrh databáze.

V tomto kurzu se naučíte, jak pomocí webu Azure Portal provést následující kroky:

> [!div class="checklist"]
> * Vytvoření databáze Azure Database for MariaDB
> * Konfigurace brány firewall serveru
> * Vytvoření databáze pomocí nástroje pro příkazový řádek mysql
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-mariadb-server"></a>Vytvoření serveru Azure Database for MariaDB

Server Azure Database for MariaDB vytvoříte s definovanou sadou [výpočetních prostředků a prostředků úložiště](concepts-pricing-tiers.md). Server se vytvoří ve [skupině prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

2. Zadáním **Azure Database for MariaDB** do vyhledávacího pole službu vyhledejte.
   
   ![Přechod k MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Vyberte dlaždici **Azure Database for MariaDB** a pak **Vytvořit**. Zadejte nebo vyberte požadované informace.
   
   ![Vytvoření formuláře](./media/tutorial-design-database-using-portal/2-create-form.png)

    Nastavení | Navrhovaná hodnota | Popis pole 
    ---|---|---
    Název serveru | *Jedinečný název serveru* | Zvolte jedinečný název, který identifikuje váš server Azure Database for MariaDB. Například **mydemoserver**. K zadanému názvu serveru se připojí název domény *.mariadb.database.azure.com*. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí mít 3 až 63 znaků.
    Předplatné | *Vaše předplatné* | Vyberte předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | **myresourcegroup** | Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků.
    Výběr zdroje | **Prázdné** | Vyberte **Prázdné** a vytvořte nový server. (Pokud vytváříte server z geografické zálohy existujícího serveru Azure Database for MariaDB, vyberte **Záloha**.)
    Přihlašovací jméno správce serveru | **myadmin** | Přihlašovací účet, který budete používat pro připojení k serveru. Přihlašovací jméno správce nemůže být **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
    Heslo | *Nějaké si zvolte* | Zadejte nové heslo pro účet správce serveru. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0–9) a jiné než alfanumerické znaky (!, $, #, % apod.).
    Potvrzení hesla | *Nějaké si zvolte*| Potvrďte heslo účtu správce.
    Umístění | *Oblast nejbližší vašim uživatelům*| Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
    Verze | *Nejnovější verze*| Nejnovější verze (pokud nemáte specifické požadavky vyžadující použití jiné verze).
    Cenová úroveň | Viz popis. | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Cenová úroveň** > **Pro obecné účely**. U následujících nastavení ponechte výchozí hodnoty:<br><ul><li>**Výpočetní generace** (Gen 5)</li><li>**Virtuální jádra** (2 virtuální jádra)</li><li>**Úložiště** (5 GB)</li><li>**Období uchování zálohy** (7 dnů)</li></ul><br>Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, v části **Možnosti redundance zálohy** vyberte **Geograficky redundantní**. <br><br>Vyberte **OK** a uložte tento výběr cenové úrovně. Další snímek zachycuje tyto výběry.
    
   ![Cenová úroveň](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Vyberte **Vytvořit**. Po jedné až dvou minutách bude server Azure Database for MariaDB spuštěný v cloudu. Pokud chcete monitorovat proces nasazení, vyberte na panelu nástrojů **Oznámení**.

## <a name="configure-the-firewall"></a>Konfigurace brány firewall

Služba Azure Database for MariaDB je chráněná bránou firewall. Ve výchozím nastavení se všechna připojení k serveru a databázím na serveru odmítají. Než se poprvé připojíte k Azure Database for MariaDB, nakonfigurujte bránu firewall tak, že do ní přidáte IP adresu veřejné sítě klientského počítače (nebo rozsah IP adres).

1. Vyberte nově vytvořený server a pak **Zabezpečení připojení**.
   
   ![Zabezpečení připojení](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Tady můžete vybrat **Přidat moji IP adresu** nebo nakonfigurovat pravidla brány firewall. Po vytvoření pravidel nezapomeňte vybrat **Uložit**.

Teď se můžete k serveru připojit pomocí nástroje pro příkazový řádek mysql nebo aplikace MySQL Workbench.

> [!TIP]
> Server Azure Database for MariaDB komunikuje přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 3306 bránou firewall vaší sítě povolený. V takovém případě musí vaše IT oddělení otevřít port 3306, abyste se mohli k serveru Azure Database for MariaDB připojit.

## <a name="get-connection-information"></a>Získání informací o připojení

Na webu Azure Portal získejte hodnoty **Název serveru** (plně kvalifikovaný) a **Přihlašovací jméno správce serveru** pro váš server Azure Database for MariaDB. Plně kvalifikovaný název serveru použijete k připojení k serveru pomocí nástroje pro příkazový řádek mysql. 

1. V levé nabídce na webu [Azure Portal](https://portal.azure.com/) vyberte **Všechny prostředky**. Zadejte název serveru a vyhledejte svůj server Azure Database for MariaDB. Výběrem názvu serveru zobrazte jeho podrobnosti.

2. Na stránce **Přehled** si poznamenejte hodnoty **Název serveru** a **Přihlašovací jméno správce serveru**. Může také vybrat tlačítko **kopírování** vedle jednotlivých polí a zkopírovat hodnoty do schránky.

   ![Vlastnosti serveru](./media/tutorial-design-database-using-portal/2-server-properties.png)

V našem příkladu je název serveru **mydemoserver.mariadb.database.azure.com** a přihlašovací jméno správce serveru je **myadmin@mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Připojení k serveru pomocí mysql

Pomocí [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) navažte připojení k serveru Azure Database for MariaDB. Nástroj pro příkazový řádek mysql můžete spustit v prohlížeči pomocí služby Azure Cloud Shell nebo na svém počítači pomocí místně nainstalovaných nástrojů mysql. Pokud chcete otevřít Azure Cloud Shell, vyberte tlačítko **Vyzkoušet** na bloku kódu v tomto článku nebo přejděte na web Azure Portal a klikněte na ikonu **>_** na panelu nástrojů vpravo nahoře. 

Zadejte příkaz pro připojení:

```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Vytvoření prázdné databáze

Po připojení k serveru vytvořte prázdnou databázi, se kterou budete pracovat:

```sql
CREATE DATABASE mysampledb;
```

Na příkazovém řádku spusťte následující příkaz pro přepnutí připojení na nově vytvořenou databázi:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi

Teď víte, jak se připojit k databázi Azure Database for MariaDB, a můžete začít provádět některé základní úlohy.

Nejdřív vytvoříte tabulku a načtete do ní data. Pojďme vytvořit tabulku, do které se budou ukládat informace o inventáři:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Načtení dat do tabulek

Teď máte vytvořenou tabulku a můžete do ní vložit data. V otevřeném okně příkazového řádku spusťte následující dotaz, který vloží několik řádků dat:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>Dotazování na data a aktualizace dat v tabulkách

Spuštěním následujícího dotazu načtete informace z databázové tabulky:

```sql
SELECT * FROM inventory;
```

Data v tabulkách můžete také aktualizovat:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Řádek se aktualizuje při načtení dat:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Obnovení databáze k dřívějšímu bodu v čase

Představte si, že jste omylem odstranili důležitou databázovou tabulku a nemůžete data snadno obnovit. Azure Database for MariaDB umožňuje obnovení serveru k určitému bodu v čase vytvořením kopie databází na novém serveru. Tento nový server můžete použít k obnovení odstraněných dat. Následující kroky obnoví ukázkový server k bodu v čase před přidáním tabulky:

1. Na webu Azure Portal vyhledejte svoji databázi Azure Database for MariaDB. Na stránce **Přehled** vyberte **Obnovit**.

   ![Obnovení databáze](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Na stránce **Obnovit** zadejte nebo vyberte následující informace:
   
   ![Formulář Obnovit](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Bod obnovení:** Vyberte v uvedeném časovém rozmezí bod v čase, ke kterému chcete provést obnovení. Nezapomeňte převést své místní časové pásmo na čas UTC.
   - **Obnovit na nový server:** Zadejte název nového serveru, na který chcete provést obnovení.
   - **Umístění:** Oblast se shoduje se zdrojovým serverem a nedá se změnit.
   - **Cenová úroveň:** Cenová úroveň se shoduje se zdrojovým serverem a nedá se změnit.
   
3. Výběrem **OK** provedete [obnovení serveru k určitému bodu v čase](./howto-restore-server-portal.md) před odstraněním tabulky. Při obnovení serveru se vytvoří nová kopie serveru k vybranému bodu v čase. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak na webu Azure Portal provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření databáze Azure Database for MariaDB
> * Konfigurace brány firewall serveru
> * Vytvoření databáze pomocí nástroje pro příkazový řádek mysql
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

> [!div class="nextstepaction"]
> [Postup připojení aplikací k Azure Database for MariaDB](./howto-connection-string.md)
