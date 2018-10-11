---
title: 'Kurz: Návrh databáze Azure Database for MariaDB pomocí webu Azure Portal'
description: Tento kurz vysvětluje, jak vytvořit a spravovat databázi a server Azure Database for MariaDB pomocí webu Azure Portal.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: tutorial
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 2e1cd0d28c544b2e4c5dc86c7a6db39a5d20c1ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991007"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-using-the-azure-portal"></a>Kurz: Návrh databáze Azure Database for MariaDB pomocí webu Azure Portal
Azure Database for MariaDB je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. Azure Portal umožňuje snadnou správu vašeho serveru a návrh databáze.

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
Otevřete svůj oblíbený webový prohlížeč a přejděte na [webu Microsoft Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-mariadb-server"></a>Vytvoření serveru Azure Database for MariaDB
Server Azure Database for MariaDB se vytvoří s definovanou sadou výpočetních prostředků a prostředků úložiště <!--[compute and storage resources](./concepts-compute-unit-and-storage.md)-->. Server se vytvoří v rámci [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

2. Zadáním **Azure Database for MariaDB** do vyhledávacího pole službu vyhledejte.
   
   ![Přechod na MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Klikněte na dlaždici **Azure Database for MariaDB** a pak na **Vytvořit**. Vyplňte formulář Azure Database for MariaDB.
   
   ![Vytvoření formuláře](./media/tutorial-design-database-using-portal/2-create-form.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole** 
    ---|---|---
    Název serveru | Jedinečný název serveru | Zvolte jedinečný název, který identifikuje váš server Azure Database for MariaDB. Například mydemoserver. K zadanému názvu serveru se připojí název domény *.mariadb.database.azure.com*. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
    Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | *myresourcegroup* | Zadejte název nové nebo existující skupiny prostředků.    Skupina prostředků|*myresourcegroup*| Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.
    Výběr zdroje | *Prázdné* | Vyberte *Prázdné* a vytvořte nový server od začátku. (Pokud vytváříte server z geografické zálohy existujícího serveru Azure Database for MariaDB, vyberte *Záloha*.)
    Přihlašovací jméno správce serveru | myadmin | Přihlašovací účet, který budete používat při připojování k serveru. Přihlašovací jméno správce nemůže být **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
    Heslo | *Nějaké si zvolte* | Zadejte nové heslo pro účet správce serveru. Musí se skládat z 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0–9) a jiné než alfanumerické znaky (!, $, #, % apod.).
    Potvrzení hesla | *Nějaké si zvolte*| Potvrďte heslo účtu správce.
    Umístění | *Oblast nejbližší vašim uživatelům*| Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
    Verze | *Nejnovější verze*| Nejnovější verze (pokud nemáte specifické požadavky vyžadující jinou verzi).
    Cenová úroveň | **Obecné účely**, **Gen 5**, **2 virtuální jádra**, **5 GB**, **7 dní**, **Geograficky redundantní** | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Cenová úroveň**. Potom vyberte kartu **Obecné účely**. *Gen 5*, *2 virtuální jádra*, *5 GB* a *7 dní* jsou výchozí hodnoty pro **Výpočetní generaci**, **Virtuální jádra**, **Úložiště** a **Období uchování zálohy**. Můžete ponechat tyto posuvníky tak, jak jsou. Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte v **Možnosti redundance zálohy** možnost **Geograficky redundantní**. Vyberte **OK** a uložte tento výběr cenové úrovně. Další snímek zachycuje tyto výběry.
    
   ![Cenová úroveň](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Klikněte na možnost **Vytvořit**. Po jedné až dvou minutách bude server Azure Database for MariaDB spuštěný v cloudu. Pokud chcete monitorovat proces nasazení, klikněte na tlačítko **Oznámení** na panelu nástrojů.

## <a name="configure-firewall"></a>Konfigurace brány firewall
Databáze Azure Database for MariaDB jsou chráněné bránou firewall. Ve výchozím nastavení se všechna připojení k serveru a databází uvnitř serveru odmítají. Než se poprvé připojíte k Azure Database for MariaDB, nakonfigurujte bránu firewall tak, že do ní přidáte IP adresu veřejné sítě klientského počítače (nebo rozsah IP adres).

1. Klikněte na nově vytvořený server a potom na **Zabezpečení připojení**.
   
   ![Zabezpečení připojení](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Můžete zde zvolit **Přidat moji IP adresu** nebo nakonfigurovat pravidla brány firewall. Po vytvoření pravidel nezapomeňte kliknout na **Uložit**.
Teď se můžete připojit k serveru pomocí nástroje příkazového řádku mysql nebo nástroje grafického rozhraní MySQL Workbench.

> [!TIP]
> Server Azure Database for MariaDB komunikuje přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 3306 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru Azure Database for MariaDB, dokud vaše IT oddělení neotevře port 3306.

## <a name="get-connection-information"></a>Získání informací o připojení
Na webu Azure Portal získejte plně kvalifikovaný **Název serveru** a **Přihlašovací jméno správce serveru** pro váš server Azure Database for MariaDB. Plně kvalifikovaný název serveru použijete k připojení k serveru pomocí nástroje příkazového řádku mysql. 

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte v levé nabídce na **Všechny prostředky**, zadejte název a vyhledejte svůj server Azure Database for MariaDB. Výběrem názvu serveru zobrazte podrobnosti.

2. Na stránce **Přehled** si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Může kliknout na tlačítko kopírování vedle jednotlivých polí a zkopírovat údaje do schránky.
   ![4-2 Vlastnosti serveru](./media/tutorial-design-database-using-portal/2-server-properties.png)

V tomto příkladu je název serveru *mydemoserver.mariadb.database.azure.com* a přihlašovací jméno správce serveru je *myadmin@mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Připojení k serveru pomocí mysql
Použijte [nástroj pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) k navázání připojení k serveru Azure Database for MariaDB. Nástroj pro příkazový řádek mysql můžete spustit v prohlížeči pomocí služby Azure Cloud Shell nebo na vlastním počítači pomocí lokálně nainstalovaných nástrojů mysql. Pokud chcete spustit Azure Cloud Shell, klikněte na tlačítko `Try It` na bloku kódu v tomto článku nebo přejděte na webu Azure Portal a klikněte na ikonu `>_` na panelu nástrojů vpravo nahoře. 

Zadejte příkaz pro připojení:
```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Vytvoření prázdné databáze
Po připojení k serveru vytvořte prázdnou databázi, se kterou budete pracovat.
```sql
CREATE DATABASE mysampledb;
```

Na příkazovém řádku spusťte následující příkaz, který přepne připojení na tuto nově vytvořenou databázi:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi
Teď víte, jak se připojit k databázi Azure Database for MariaDB, a můžete začít provádět některé základní úlohy:

Nejdřív vytvoříte tabulku a načtete do ní data. Pojďme vytvořit tabulku, ve které jsou uložené informace o inventáři.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek
Teď máte vytvořenou tabulku a můžete do ní vložit data. V otevřeném okně příkazového řádku spusťte následující dotaz, který vloží několik řádků dat.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tabulka, kterou jste vytvořili dříve, teď obsahuje dva řádky ukázkových dat.

## <a name="query-and-update-the-data-in-the-tables"></a>Dotazování na data a aktualizace dat v tabulkách
Spuštěním následujícího příkazu načtete informace z databázové tabulky.
```sql
SELECT * FROM inventory;
```

Data v tabulce můžete také aktualizovat.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Při načtení dat se řádek příslušným způsobem aktualizuje.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Obnovení databáze k dřívějšímu bodu v čase
Představte si, že omylem odstraníte důležitou databázovou tabulku a nemůžete data snadno obnovit. Azure Database for MariaDB umožňuje obnovení serveru do určitého bodu v čase a vytvoření kopie databází na novém serveru. Tento nový server můžete použít k obnovení odstraněných dat. Následující kroky obnoví ukázkový server do bodu před přidáním tabulky.

1. Na webu Azure Portal vyhledejte svoji databázi Azure Database for MariaDB. Na stránce **Přehled** klikněte na panelu nástrojů na **Obnovit**. Otevře se stránka Obnovit.

   ![10-1 Obnovení databáze](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Do formuláře **Obnovit** zadejte požadované údaje:
   
   ![10-2 Formulář Obnovit](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Bod obnovení:** Vyberte v zadaném časovém rozmezí okamžik, který chcete obnovit. Nezapomeňte převést své místní časové pásmo na čas UTC.
   - **Obnovit na nový server:** Zadejte nový název serveru, na který chcete provést obnovení.
   - **Umístění:** Oblast se shoduje se zdrojovým serverem a nedá se změnit.
   - **Cenová úroveň:** Cenová úroveň se shoduje se zdrojovým serverem a nedá se změnit.
   
3. Kliknutím na **OK** provedete obnovení serveru k určitému bodu v čase <!--[restore to a point in time](./howto-restore-server-portal.md)--> před odstraněním tabulky. Při obnovení serveru se vytvoří nová kopie serveru k zadanému bodu v čase. 

<!--## Next steps
In this tutorial, you use the Azure portal to learned how to:

> [!div class="checklist"]
> * Create an Azure Database for MariaDB
> * Configure the server firewall
> * Use mysql command-line tool to create a database
> * Load sample data
> * Query data
> * Update data
> * Restore data

><> [!div class="nextstepaction"]
> [How to connect applications to Azure Database for MariaDB](./howto-connection-string.md)-->
