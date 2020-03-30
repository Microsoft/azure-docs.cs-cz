---
title: Transparentní šifrování dat
description: Přehled transparentního šifrování dat pro SQL Database a SQL Analytics v Azure Synapse. Dokument pokrývá jeho výhody a možnosti konfigurace, která zahrnuje transparentní šifrování dat spravovaná službou a přineste si vlastní klíč.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/06/2020
ms.openlocfilehash: 5bbb537ef6545852423bf5315b7636671c598fdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255636"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transparentní šifrování dat pro SQL Database a Azure Synapse

Transparentní šifrování dat (TDE) pomáhá chránit Azure SQL Database, Azure SQL Managed Instance a Azure Synapse před hrozbou škodlivé offline aktivity šifrováním dat v klidovém stavu. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci. Ve výchozím nastavení je transparentní šifrování dat povolené pro všechny nově nasazené databáze Azure SQL. TDE nelze použít k šifrování **hlavní** logické databáze v databázi SQL.  **Hlavní** databáze obsahuje objekty, které jsou potřebné k provádění operací TDE v uživatelských databázích.

TDE musí být ručně povolené pro starší databáze Azure SQL Database, Azure SQL Managed Instance nebo Azure Azure Synapse.
Databáze spravovaných instancí vytvořené pomocí obnovení dědí stav šifrování ze zdrojové databáze.

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze. Tento šifrovací klíč databáze je chráněn transparentní mašle šifrování dat. Ochrana je certifikát spravovaný službou (transparentní šifrování dat spravovaná službou) nebo asymetrický klíč uložený v trezoru klíčů Azure (Bring Your Own Key). Nastavíte ochranu transparentního šifrování dat na úrovni serveru pro Azure SQL Database a Azure Synapse a úroveň instance pro Azure SQL Managed Instance. Termín *server* odkazuje na server i instanci v celém dokumentu, pokud není uvedeno jinak.

Při spuštění databáze je šifrovaný šifrovací klíč databáze dešifrován a poté použit pro dešifrování a opětovné šifrování databázových souborů v procesu databázového stroje SQL Server. Transparentní šifrování dat provádí vstupně-va šifrování v reálném čase a dešifrování dat na úrovni stránky. Každá stránka se při načtení do paměti dešifruje a pak se před zápisem na disk zašifruje. Obecný popis transparentního šifrování dat naleznete v tématu [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server spuštěný na virtuálním počítači Azure taky můžete použít asymetrický klíč z trezoru klíčů. Kroky konfigurace se liší od použití asymetrického klíče v databázi SQL a spravované instanci SQL. Další informace naleznete [v tématu Rozšiřitelná správa klíčů pomocí azure key vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Transparentní šifrování dat spravované službou

V Azure je výchozím nastavením pro transparentní šifrování dat, že šifrovací klíč databáze je chráněný integrovaným certifikátem serveru. Vestavěný certifikát serveru je jedinečný pro každý server a použitý šifrovací algoritmus je AES 256. Pokud je databáze ve vztahu geografické replikace, primární i geograficky sekundární databáze jsou chráněny nadřazeným serverovým klíčem primární databáze. Pokud jsou dvě databáze připojeny ke stejnému serveru, sdílejí také stejný předdefinovaný certifikát.  Společnost Microsoft automaticky otáčí tyto certifikáty v souladu se zásadami vnitřního zabezpečení a kořenový klíč je chráněn úložištěm interních tajných klíčů společnosti Microsoft.  Zákazníci mohou ověřit soulad databáze SQL Database s interními zásadami zabezpečení v nezávislých sestavách auditu jiných výrobců, které jsou k dispozici v [Centru zabezpečení společnosti Microsoft](https://servicetrust.microsoft.com/).

Společnost Microsoft také bezproblémově přesouvá a spravuje klíče podle potřeby pro geografickou replikaci a obnovení.

> [!IMPORTANT]
> Všechny nově vytvořené databáze SQL a databáze spravovaných instancí jsou ve výchozím nastavení šifrovány pomocí transparentního šifrování dat spravovaného službou. Existující databáze SQL vytvořené před květnem 2017 a databáze SQL vytvořené prostřednictvím obnovení, geografické replikace a kopírování databáze nejsou ve výchozím nastavení šifrovány. Existující databáze spravovaných instancí vytvořené před únorem 2019 nejsou ve výchozím nastavení šifrovány. Databáze spravovaných instancí vytvořené pomocí obnovení dědí stav šifrování ze zdroje.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparentní šifrování dat spravované zákazníkem – přineste si vlastní klíč

[TDE s klíči spravovanými zákazníky v trezoru klíčů Azure](transparent-data-encryption-byok-azure-sql.md) umožňuje šifrovat šifrovací klíč databáze (DEK) pomocí asymetrického klíče spravovaného zákazníkem nazvaný mašla TDE.  To se také obecně označuje jako přineste si vlastní klíč (BYOK) podpora pro transparentní šifrování dat. Ve scénáři BYOK je ochrana TDE uložena v klientském trezoru [klíčů Azure](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), cloudovém systému správy externích klíčů Azure. Ochrana TDE může být [generována trezorem klíčů nebo přenesena do trezoru klíčů](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) z místního zařízení HSM. TDE DEK, který je uložen na spouštěcí stránce databáze, je šifrován a dešifrován pomocí ochrany TDE, která je uložena v úložišti klíčů Azure a nikdy neopustí trezor klíčů.  Sql Database musí být udělena oprávnění k trezoru klíčů vlastněné zákazníkem k dešifrování a šifrování DEK. Pokud jsou oprávnění logického serveru SQL k úložišti klíčů odvolána, bude databáze nepřístupná a všechna data budou zašifrována. Pro Azure SQL Database je ochrana TDE nastavena na úrovni logického serveru SQL a je zděděna všemi databázemi přidruženými k tomuto serveru. Pro [Azure SQL spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), ochrana TDE je nastavena na úrovni instance a je zděděna všechny *šifrované* databáze na této instanci. Termín *server* odkazuje na server i instanci v celém dokumentu, pokud není uvedeno jinak.

Díky tde s integrací Azure Key Vault mohou uživatelé řídit úlohy správy klíčů, včetně střídání klíčů, oprávnění trezoru klíčů, zálohování klíčů a povolit auditování/vytváření sestav na všech ochranami TDE pomocí funkce Azure Key Vault. Trezor klíčů poskytuje centrální správu klíčů, využívá úzce monitorované moduly hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které pomáhají plnit dodržování zásad zabezpečení.
Další informace o transparentním šifrování dat s integrací Azure Key Vault (bring your own Key support) pro Azure SQL Database, SQL Managed Instance a Azure Synapse najdete v [tématu Transparentní šifrování dat s integrací Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

Pokud chcete začít používat transparentní šifrování dat s integrací Azure Key Vault (podpora přineste si vlastní klíč), přečtěte si návod, jak [zapnout transparentní šifrování dat pomocí vlastního klíče z trezoru klíčů pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Přesunutí transparentní databáze chráněné šifrováním dat

Není nutné dešifrovat databáze pro operace v rámci Azure. Nastavení transparentního šifrování dat ve zdrojové databázi nebo primární databázi jsou transparentně zděděna v cíli. Operace, které jsou zahrnuty zahrnují:

- Geografické obnovení
- Samoobslužné obnovení bodu v čase
- Obnovení odstraněné databáze
- Aktivní geografická replikace
- Vytvoření kopie databáze
- Obnovení záložního souboru do spravované instance Azure SQL

> [!IMPORTANT]
> Ruční zálohování pouze pro kopírování databáze zašifrované službou spravované tde není v Azure SQL Managed Instance povoleno, protože certifikát používaný pro šifrování není přístupný. Pomocí funkce obnovení bodu v čase přesuňte tento typ databáze do jiné spravované instance.

Při exportu databáze chráněné šifrováním dat není exportovaný obsah databáze šifrován. Tento exportovaný obsah je uložen v nešifrovaných souborech BACPAC. Ujistěte se, že správně chránit soubory BACPAC a povolit transparentní šifrování dat po dokončení importu nové databáze.

Například pokud je soubor BACPAC exportován z místní instance serveru SQL Server, importovaný obsah nové databáze není automaticky zašifrován. Podobně pokud je soubor BACPAC exportován do místní instance serveru SQL Server, nová databáze také není automaticky zašifrována.

Jedinou výjimkou je při exportu do a z databáze SQL. Transparentní šifrování dat je povoleno v nové databázi, ale samotný soubor BACPAC stále není šifrován.


## <a name="manage-transparent-data-encryption"></a>Správa transparentního šifrování dat
# <a name="portal"></a>[Portál](#tab/azure-portal)
Spravujte transparentní šifrování dat na webu Azure Portal.

Chcete-li nakonfigurovat transparentní šifrování dat prostřednictvím portálu Azure, musíte být připojeni jako vlastník Azure, přispěvatel nebo SPRÁVCE ZABEZPEČENÍ SQL.

Transparentní šifrování dat zapínáavá a vypínána na úrovni databáze. Pokud chcete v databázi povolit transparentní šifrování dat, přejděte na [portál Azure](https://portal.azure.com) a přihlaste se pomocí účtu správce Azure nebo přispěvatele. Najděte nastavení transparentního šifrování dat v databázi uživatelů. Ve výchozím nastavení se používá transparentní šifrování dat spravované službou. Pro server, který databázi obsahuje, je automaticky generován transparentní certifikát šifrování dat. Pro Azure SQL spravované instance pomocí T-SQL zapnout a vypnout transparentní šifrování dat v databázi.

![Transparentní šifrování dat spravované službou](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

Na úrovni serveru nastavíte hlavní klíč pro transparentní šifrování dat, označovaný také jako ochrana pro transparentní šifrování dat. Chcete-li použít transparentní šifrování dat s podporou Přineste si vlastní klíč a chránit své databáze klíčem z trezoru klíčů, otevřete nastavení transparentního šifrování dat pod serverem.

![Transparentní šifrování dat s podporou přineste si vlastní klíč](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Spravujte transparentní šifrování dat pomocí PowerShellu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Chcete-li nakonfigurovat transparentní šifrování dat prostřednictvím prostředí PowerShell, musíte být připojeni jako vlastník Azure, přispěvatel nebo Správce zabezpečení SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Rutiny pro Azure SQL Database a Azure Synapse

Použijte následující rutiny pro Azure SQL Database a Azure Synapse:

| Rutina | Popis |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Povolí nebo zakáže transparentní šifrování dat pro databázi.|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Získá stav transparentního šifrování dat pro databázi. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Zkontroluje průběh šifrování databáze. |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Přidá klíč trezoru klíčů do instance serveru SQL Server. |
| [Příkaz Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Získá klíče trezoru klíčů pro server Azure SQL Database  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Nastaví ochranu proti transparentnímu šifrování dat pro instanci serveru SQL Server. |
| [Get-AzSqlServerTransparentProšifrování dat](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Získá ochranu transparentního šifrování dat. |
| [Odstranit-AzSqlServerKeyVaultKeyKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Odebere klíč trezoru klíčů z instance serveru SQL Server. |
|  | |

> [!IMPORTANT]
> Pro Azure SQL Managed Instance použijte příkaz T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) k zapnutí a vypnutí transparentního šifrování dat na úrovni databáze a zkontrolujte [ukázkový skript PowerShellu](transparent-data-encryption-byok-azure-sql-configure.md) pro správu transparentního šifrování dat na úrovni instance.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Spravujte transparentní šifrování dat pomocí Transact-SQL.

Připojte se k databázi pomocí přihlášení, které je správcem nebo členem role **dbmanager** v hlavní databázi.

| Příkaz | Popis |
| --- | --- |
| [DATABÁZE ALTER (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | NASTAVENÍ ŠIFROVÁNÍ ZAPISOVÁNÍ/VYPNUTÍ šifruje nebo dešifruje databázi |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Vrátí informace o stavu šifrování databáze a jejích přidružených šifrovacích klíčů databáze. |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Vrátí informace o stavu šifrování každého uzlu datového skladu a jeho přidružených šifrovacích klíčů databáze. |
|  | |

Pomocí aplikace Transact-SQL nelze přepnout ochranu transparentního šifrování dat na klíč z trezoru klíčů. Použijte PowerShell nebo portál Azure.

# <a name="rest-api"></a>[ROZHRANÍ API PRO ODPOČINEK](#tab/azure-RESTAPI)
Spravujte transparentní šifrování dat pomocí rozhraní REST API.

Chcete-li nakonfigurovat transparentní šifrování dat prostřednictvím rozhraní REST API, musíte být připojeni jako vlastník Azure, přispěvatel nebo Správce zabezpečení SQL.
Použijte následující sadu příkazů pro Azure SQL Database a Azure Synapse:

| Příkaz | Popis |
| --- | --- |
|[Vytvořit nebo aktualizovat server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Přidá identitu služby Azure Active Directory k instanci serveru SQL Server (používá se k udělení přístupu k trezoru klíčů).|
|[Vytvořit nebo aktualizovat klíč serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Přidá klíč trezoru klíčů do instance serveru SQL Server.|
|[Odstranit klíč serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Odebere klíč trezoru klíčů z instance serveru SQL Server.|
|[Získat klíče serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Získá konkrétní klíč trezoru klíčů z instance serveru SQL Server.|
|[Seznam klíčů serveru podle serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Získá klíče trezoru klíčů pro instanci serveru SQL Server |
|[Vytvořit nebo aktualizovat šifrování chránič](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Nastaví ochranu proti transparentnímu šifrování dat pro instanci serveru SQL Server.|
|[Získat ochranu šifrování](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Získá ochranu transparentní šifrování dat pro instanci serveru SQL Server|
|[Seznam šifrovacích chráničů podle serveru](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Získá ochrany transparentní šifrování dat pro instanci serveru SQL Server |
|[Vytvořit nebo aktualizovat konfiguraci transparentního šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Povolí nebo zakáže transparentní šifrování dat pro databázi.|
|[Získat transparentní konfiguraci šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Získá konfiguraci transparentního šifrování dat pro databázi.|
|[Vypsat výsledky konfigurace transparentního šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Získá výsledek šifrování pro databázi|

## <a name="next-steps"></a>Další kroky

- Obecný popis transparentního šifrování dat naleznete v tématu [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Další informace o transparentním šifrování dat pomocí podpory Bring Your Own Key pro Azure SQL Database, Azure SQL Managed Instance a Azure Synapse najdete v [tématu Transparentní šifrování dat s podporou Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Pokud chcete začít používat transparentní šifrování dat s podporou Bring Your Own Key, přečtěte si návod, jak [zapnout transparentní šifrování dat pomocí vlastního klíče z trezoru klíčů pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Další informace o trezoru klíčů naleznete na [stránce dokumentace k trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
