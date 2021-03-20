---
title: Parametry serveru-Azure Database for MySQL-flexibilní Server
description: Toto téma poskytuje pokyny pro konfiguraci parametrů serveru v Azure Database for MySQL-flexibilním serveru.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 58978f120578afeca129b0d8928713835def8418
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94496604"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Parametry serveru v Azure Database for MySQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento článek popisuje informace a pokyny pro konfiguraci parametrů serveru v Azure Database for MySQL flexibilním serveru.

## <a name="what-are-server-variables"></a>Co jsou proměnné serveru? 

Modul MySQL poskytuje mnoho různých [proměnných serveru/parametrů](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) , které lze použít ke konfiguraci a ladění chování modulu. Některé parametry lze nastavit dynamicky během doby běhu, zatímco jiné jsou "statické", což vyžaduje restart serveru, aby se mohl použít.

Azure Database for MySQL flexibilní server zpřístupňuje možnost změnit hodnotu různých parametrů serveru MySQL pomocí [Azure Portal](./how-to-configure-server-parameters-portal.md) a [Azure CLI](./how-to-configure-server-parameters-cli.md) tak, aby odpovídaly potřebám vašich úloh.

## <a name="configurable-server-parameters"></a>Konfigurovatelné parametry serveru

Azure Database for MySQL flexibilní konfiguraci serveru můžete spravovat pomocí parametrů serveru. Při vytváření serveru jsou parametry serveru nastaveny s výchozí a doporučenou hodnotou. Okno parametr serveru v Azure Portal zobrazuje jak upravitelná, tak i neupravitelný parametr serveru. Parametry serveru, které nelze upravovat, jsou šedé.

Seznam podporovaných parametrů serveru se neustále zvětšuje. Pomocí karty parametry serveru v Azure Portal zobrazte úplný seznam a nakonfigurujte hodnoty parametrů serveru.

Další informace o omezeních několika běžně aktualizovaných parametrů serveru najdete v následujících částech. Limity se určují na výpočetní úrovni a velikosti (virtuální jádra) serveru.

> [!NOTE]
> Pokud chcete upravit parametr serveru, který není upravitelný, ale chcete pro své prostředí zobrazit jako upravitelný, otevřete prosím položku [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) nebo Hlasujte, pokud už existuje zpětná vazba, která nám může přispět k určení priorit.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

V Azure Database for MySQL flexibilním serveru jsou binární protokoly vždycky povolené (to znamená, že `log_bin` je nastavené na zapnuto). V případě, že budete chtít použít triggery, zobrazí se chybová zpráva podobná se tomu, že nemáte *oprávnění super a binární protokolování je povolené (je možné použít méně bezpečné `log_bin_trust_function_creators` proměnné)*. 

Formát binárního protokolování je vždy **řádek** a všechna připojení k serveru **vždy** používají binární protokolování založené na řádcích. V případě binárního protokolování založeného na řádcích neexistují problémy se zabezpečením a binární protokolování nemůže přerušit, takže můžete bezpečně nastavit [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) na **hodnotu true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) .

|**Cenová úroveň**|**vCore (celkem)**|**Velikost paměti (GiB)**|**Výchozí hodnota (bajty)**|**Minimální hodnota (bajty)**|**Maximální hodnota (v bajtech)**|
|---|---|---|---|---|---|
|S shluky (B1s)|1|1|134217728|33554432|134217728|
|S shluky (B1ms)|1|2|536870912|134217728|536870912|
|S shluky|2|4|2147483648|134217728|2147483648|
|Pro obecné účely|2|8|6442450944|134217728|6442450944|
|Pro obecné účely|4|16|12884901888|134217728|12884901888|
|Pro obecné účely|8|32|25769803776|134217728|25769803776|
|Pro obecné účely|16|64|51539607552|134217728|51539607552|
|Pro obecné účely|32|128|103079215104|134217728|103079215104|
|Pro obecné účely|48|192|154618822656|134217728|154618822656|
|Pro obecné účely|64|256|206158430208|134217728|206158430208|
|Optimalizováno pro paměť|2|16|12884901888|134217728|12884901888|
|Optimalizováno pro paměť|4|32|25769803776|134217728|25769803776|
|Optimalizováno pro paměť|8|64|51539607552|134217728|51539607552|
|Optimalizováno pro paměť|16|128|103079215104|134217728|103079215104|
|Optimalizováno pro paměť|32|256|206158430208|134217728|206158430208|
|Optimalizováno pro paměť|48|384|309237645312|134217728|309237645312|
|Optimalizováno pro paměť|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL ukládá tabulku InnoDB v různých tabulkových prostorech na základě konfigurace, kterou jste zadali během vytváření tabulky. [Systémový tabulkový prostor](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) je oblast úložiště pro slovník InnoDB data Dictionary. [Tabulkový prostor pro tabulku](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) obsahuje data a indexy pro jednu tabulku InnoDB a je uložený v systému souborů ve vlastním datovém souboru. Toto chování je řízeno `innodb_file_per_table` parametrem serveru. Nastavení `innodb_file_per_table` na `OFF` způsobí, že InnoDB vytvoří tabulky v systémovém tabulkovém prostoru. V opačném případě InnoDB vytvoří tabulky v tabulkových prostorech v souborové tabulce.

Azure Database for MySQL flexibilní Server v jednom datovém souboru podporuje v největším, **4 TB**. Pokud je velikost databáze větší než 4 TB, měli byste vytvořit tabulku v [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabulkovém prostoru. Pokud máte velikost jedné tabulky větší než 4 TB, měli byste použít tabulku oddílů.

### <a name="max_connections"></a>max_connections

Hodnota max_connection je určena velikostí paměti serveru. 

|**Cenová úroveň**|**vCore (celkem)**|**Velikost paměti (GiB)**|**Výchozí hodnota**|**Min. hodnota**|**Max. hodnota**|
|---|---|---|---|---|---|
|S shluky (B1s)|1|1|85|10|171|
|S shluky (B1ms)|1|2|171|10|341|
|S shluky|2|4|341|10|683|
|Pro obecné účely|2|8|683|10|1365|
|Pro obecné účely|4|16|1365|10|2731|
|Pro obecné účely|8|32|2731|10|5461|
|Pro obecné účely|16|64|5461|10|10923|
|Pro obecné účely|32|128|10923|10|21845|
|Pro obecné účely|48|192|16384|10|32768|
|Pro obecné účely|64|256|21845|10|43691|
|Optimalizováno pro paměť|2|16|1365|10|2731|
|Optimalizováno pro paměť|4|32|2731|10|5461|
|Optimalizováno pro paměť|8|64|5461|10|10923|
|Optimalizováno pro paměť|16|128|10923|10|21845|
|Optimalizováno pro paměť|32|256|21845|10|43691|
|Optimalizováno pro paměť|48|384|32768|10|65536|
|Optimalizováno pro paměť|64|504|43008|10|86016|

Když připojení překročí limit, může se zobrazit následující chyba:
> Chyba 1040 (08004): příliš mnoho připojení

> [!IMPORTANT]
> Pro dosažení optimálního prostředí doporučujeme, abyste k efektivní správě připojení používali připojení Pooler jako ProxySQL.

Vytváření nových připojení klientů k MySQL trvá čas i po jejich navázání, tato připojení zabírají databázové prostředky i v případě nečinnosti. Většina aplikací vyžaduje mnoho krátkodobých připojení, což je v této situaci celé. Výsledkem je méně prostředků dostupných pro vaše skutečné zatížení, což vede ke snížení výkonu. Připojení Pooler, které zkracuje nečinné připojení a znovu používá existující připojení, jim pomůže vyhnout se. Další informace o nastavení ProxySQL najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL je open source nástroj pro komunitu. Společnost Microsoft je podporuje na základě optimálního úsilí. Pokud chcete získat technickou podporu s použitím autoritativních pokynů, můžete vyhodnotit a kontaktovat [podporu ProxySQL produktu](https://proxysql.com/services/support/).

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Pokud se zobrazí chybová zpráva podobná "velikost řádku je moc velká (> 8126)", možná budete chtít **innodb_strict_mode** parametru vypnout. Parametr serveru **innodb_strict_mode** není povoleno upravovat globálně na úrovni serveru, protože pokud je velikost dat řádku větší než 8k, data budou zkrácena bez chyby, což může vést k potenciální ztrátě dat. Doporučujeme upravit schéma tak, aby odpovídalo limitu velikosti stránky. 

Tento parametr lze nastavit na úrovni relace pomocí `init_connect` . Pokud chcete nastavit **innodb_strict_mode** na úrovni relace, přečtěte si téma [Nastavení parametrů není uvedené](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters).

> [!NOTE]
> Pokud máte server repliky pro čtení, nastavení **innodb_strict_mode** na úrovni relace na zdrojovém serveru zruší replikaci. Pokud máte repliky čtení, doporučujeme ponechat parametr nastavený na vypnuto.

### <a name="time_zone"></a>time_zone

Při počátečním nasazení zahrnuje Azure for MySQL Flexible Server systémové tabulky pro informace o časovém pásmu, ale tyto tabulky se neplní. Tabulky časových pásem lze naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je například příkazový řádek MySQL nebo MySQL Workbench. Informace o tom, jak volat uloženou proceduru a nastavit globální časová pásma na úrovni relace, najdete v článcích [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) nebo [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) .

## <a name="non-modifiable-server-parameters"></a>Parametry serveru, které nelze upravovat

Okno parametr serveru na Azure Portal zobrazuje jak upravitelná, tak i neupravitelný parametr serveru. Parametry serveru, které nelze upravovat, jsou šedé. Pokud chcete konfigurovat parametr serveru bez parametrů na úrovni relace, přečtěte si článek [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) nebo [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) , kde můžete nastavit parametr na úrovni připojení pomocí `init_connect` .

## <a name="next-steps"></a>Další kroky

- Postup konfigurace [parametrů serveru v Azure Portal](./how-to-configure-server-parameters-portal.md)
- Postup konfigurace [parametrů serveru v Azure CLI](./how-to-configure-server-parameters-cli.md)
