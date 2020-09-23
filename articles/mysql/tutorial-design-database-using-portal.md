---
title: 'Kurz: návrh serveru-Azure Portal-Azure Database for MySQL'
description: V tomto kurzu se dozvíte, jak vytvořit a spravovat Azure Database for MySQL server a databázi pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: tutorial
ms.date: 3/20/2020
ms.custom: mvc
ms.openlocfilehash: d34be152a0d104e688abd6e53c97353b69012670
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906540"
---
# <a name="tutorial-design-an-azure-database-for-mysql-database-using-the-azure-portal"></a>Kurz: Návrh databáze Azure Database for MySQL pomocí webu Azure Portal

Azure Database for MySQL je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. Azure Portal umožňuje snadnou správu vašeho serveru a návrh databáze.

V tomto kurzu se naučíte, jak pomocí webu Azure Portal provést následující kroky:

> [!div class="checklist"]
> * Vytvoření Azure Database for MySQL
> * Konfigurace brány firewall serveru
> * Vytvoření databáze pomocí nástroje pro příkazový řádek mysql
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete svůj oblíbený webový prohlížeč a přejděte na [webu Microsoft Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL

Server Azure Database for MySQL se vytvoří s definovanou sadou [výpočetních prostředků a prostředků úložiště](./concepts-compute-unit-and-storage.md). Server se vytvoří v rámci [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

2. Vyberte **databáze**  >  **Azure Database for MySQL**. Pokud nemůžete najít server MySQL v kategorii **databáze** , klikněte na **Zobrazit vše** , aby se zobrazily všechny dostupné databázové služby. Službu také můžete rychle vyhledat tak, že do vyhledávacího pole zadáte **Azure Database for MySQL**.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Navigate-to-MySQL.png" alt-text="Přechod na MySQL":::

3. Klikněte na **Azure Database for MySQL** dlaždici. Vyplňte formulář Azure Database for MySQL.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-create-form.png" alt-text="Vytvoření formuláře":::

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    ---|---|---
    Název serveru | Jedinečný název serveru | Zvolte jedinečný název serveru, který identifikuje váš server Azure Database for MySQL. Například mydemoserver. K zadanému názvu serveru se připojí název domény *.mysql.database.azure.com*. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
    Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | *myresourcegroup* | Zadejte název nové nebo existující skupiny prostředků.
    Zvolit zdroj | *Trhnout* | Vyberte *Prázdné* a vytvořte nový server od začátku. (Pokud vytváříte server z geografické zálohy existujícího serveru Azure Database for MySQL, vyberte *Záloha*.)
    Přihlášení správce serveru | myadmin | Přihlašovací účet, který budete používat při připojování k serveru. Přihlašovací jméno správce nemůže být **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
    Heslo | *Podle vašeho výběru* | Zadejte nové heslo pro účet správce serveru. Musí se skládat z 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0–9) a jiné než alfanumerické znaky (!, $, #, % apod.).
    Potvrzení hesla | *Podle vašeho výběru*| Potvrďte heslo účtu správce.
    Umístění | *Oblast nejblíže vašim uživatelům*| Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
    Verze | *Nejnovější verze*| Nejnovější verze (pokud nemáte specifické požadavky vyžadující jinou verzi).
    Cenová úroveň | **Obecné účely**, **Gen 5**, **2 virtuální jádra**, **5 GB**, **7 dní**, **Geograficky redundantní** | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **cenovou úroveň**. V dalším kroku vyberte kartu **pro obecné účely** . *5*, *2 virtuální jádra*, *5 GB*a *7 dní* jsou výchozí hodnoty pro **výpočetní generaci**, **Vcore**, **úložiště**a **dobu uchovávání záloh**. Můžete ponechat tyto posuvníky tak, jak jsou. Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte z **možností redundance zálohy**možnost **geograficky redundantní** . Vyberte **OK** a uložte tento výběr cenové úrovně. Další snímek zachycuje tyto výběry.

   :::image type="content" source="./media/tutorial-design-database-using-portal/3-pricing-tier.png" alt-text="Cenová úroveň":::

   > [!TIP]
   > Díky **automatickému růstu** může server zvýšit kapacitu úložiště při přístupu k přidělenému limitu, aniž by to ovlivnilo vaše zatížení.

4. Klikněte na **Zkontrolovat a vytvořit**. Můžete kliknout na tlačítko **oznámení** na panelu nástrojů a monitorovat proces nasazení. Nasazení může trvat až 20 minut.

## <a name="configure-firewall"></a>Konfigurace brány firewall

Databáze Azure Database for MySQL jsou chráněné bránou firewall. Ve výchozím nastavení se všechna připojení k serveru a databází uvnitř serveru odmítají. Než se poprvé připojíte k Azure Database for MySQL, nakonfigurujte bránu firewall tak, že do ní přidáte IP adresu veřejné sítě klienta (nebo rozsah IP adres).

1. Klikněte na nově vytvořený server a potom na **Zabezpečení připojení**.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Connection-security.png" alt-text="Zabezpečení připojení":::
2. Můžete zde zvolit **Přidat moji IP adresu** nebo nakonfigurovat pravidla brány firewall. Po vytvoření pravidel nezapomeňte kliknout na **Uložit**.
Teď se můžete připojit k serveru pomocí nástroje příkazového řádku mysql nebo nástroje grafického rozhraní MySQL Workbench.

> [!TIP]
> Server Azure Database for MySQL komunikuje prostřednictvím portu 3306. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 3306 bránou firewall vaší sítě povolený. V takovém případě se nemůžete k serveru Azure SQL připojit, dokud vaše IT oddělení port 3306 neotevře.

## <a name="get-connection-information"></a>Získání informací o připojení

Na webu Azure Portal získejte plně kvalifikovaný **Název serveru** a **Přihlašovací jméno správce serveru** pro váš server Azure Database for MySQL. Plně kvalifikovaný název serveru použijete k připojení k serveru pomocí nástroje příkazového řádku mysql.

1. Na [webu Azure Portal](https://portal.azure.com/) klikněte v levé nabídce na **Všechny prostředky**, zadejte název a vyhledejte svůj server Azure Database for MySQL. Výběrem názvu serveru zobrazte podrobnosti.

2. Na stránce **Přehled** si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Může kliknout na tlačítko kopírování vedle jednotlivých polí a zkopírovat údaje do schránky.
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-server-properties.png" alt-text="4-2 Vlastnosti serveru":::

V tomto příkladu je název serveru *mydemoserver.MySQL.Database.Azure.com*a přihlašovací jméno správce serveru *myadmin \@ mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Připojení k serveru pomocí mysql

Použijte [nástroj pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) k navázání připojení k serveru Azure Database for MySQL. Nástroj pro příkazový řádek mysql můžete spustit v prohlížeči pomocí služby Azure Cloud Shell nebo na vlastním počítači pomocí lokálně nainstalovaných nástrojů mysql. Pokud chcete spustit Azure Cloud Shell, klikněte na tlačítko `Try It` na bloku kódu v tomto článku nebo přejděte na webu Azure Portal a klikněte na ikonu `>_` na panelu nástrojů vpravo nahoře.

Zadejte příkaz pro připojení:

```azurecli-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Vytvoření prázdné databáze

Po připojení k serveru vytvořte prázdnou databázi, se kterou chcete pracovat.

```sql
CREATE DATABASE mysampledb;
```

Na příkazovém řádku spusťte následující příkaz, který přepne připojení na tuto nově vytvořenou databázi:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi

Teď víte, jak se připojit k databázi Azure Database for MySQL, a můžete začít provádět některé základní úlohy:

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

Představte si, že omylem odstraníte důležitou databázovou tabulku a nemůžete data snadno obnovit. Azure Database for MySQL umožňuje obnovení serveru do určitého bodu v čase a vytvoření kopie databází na novém serveru. Tento nový server můžete použít k obnovení odstraněných dat. Následující kroky obnoví ukázkový server do bodu před přidáním tabulky.

1. Na webu Azure Portal vyhledejte svoji databázi Azure Database for MySQL. Na stránce **Přehled** klikněte na panelu nástrojů na **Obnovit**. Otevře se stránka Obnovit.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-restore-a-db.png" alt-text="10-1 Obnovení databáze":::

2. Vyplňte formulář **obnovení** požadovanými informacemi.

   :::image type="content" source="./media/tutorial-design-database-using-portal/2-restore-form.png" alt-text="10-2 Formulář Obnovit":::

   - **Bod obnovení:** Vyberte v zadaném časovém rozmezí okamžik, který chcete obnovit. Nezapomeňte převést své místní časové pásmo na čas UTC.
   - **Obnovit na nový server:** Zadejte nový název serveru, na který chcete provést obnovení.
   - **Umístění:** Oblast se shoduje se zdrojovým serverem a nedá se změnit.
   - **Cenová úroveň:** Cenová úroveň se shoduje se zdrojovým serverem a nedá se změnit.
   
3. Kliknutím na tlačítko **OK** obnovte server, který chcete [obnovit do bodu v čase](./howto-restore-server-portal.md) před odstraněním tabulky. Při obnovení serveru se vytvoří nová kopie serveru k zadanému bodu v čase.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak na webu Azure Portal provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření Azure Database for MySQL
> * Konfigurace brány firewall serveru
> * Vytvoření databáze pomocí nástroje pro příkazový řádek mysql
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

> [!div class="nextstepaction"]
> [Postup připojení aplikací k Azure Database for MySQL](./howto-connection-string.md)
