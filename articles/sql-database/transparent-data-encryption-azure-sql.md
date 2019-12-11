---
title: Transparentní šifrování dat
description: Přehled transparentního šifrování dat pro SQL Database a datový sklad. Tento dokument popisuje své výhody a možnosti konfigurace, mezi které patří transparentní šifrování dat spravované službou a Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 11/01/2019
ms.openlocfilehash: 19414a6f09f4bc61cd9b1b09ae98ea070e577d7f
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995877"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparentní šifrování dat pro SQL Database a datový sklad

Transparentní šifrování dat (TDE) pomáhá chránit Azure SQL Database, Azure SQL Managed instance a Azure Data Warehouse před hrozbou neoprávněné offline aktivity šifrováním dat v klidovém stavu. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci. Ve výchozím nastavení je transparentní šifrování dat povolené pro všechny nově nasazené databáze Azure SQL. TDE nelze použít k šifrování logické **Hlavní** databáze v SQL Database.  **Hlavní** databáze obsahuje objekty, které jsou potřebné k provedení operací TDE v uživatelských databázích.

TDE se musí ručně povolit pro starší databáze Azure SQL Database, Azure SQL Managed instance nebo Azure SQL Data Warehouse.
Databáze spravované instance vytvořené prostřednictvím obnovení dědí stav šifrování ze zdrojové databáze.

Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče, který se nazývá šifrovací klíč databáze. Tento šifrovací klíč databáze je chráněn transparentní ochranou šifrování dat. Ochrana je buď certifikát spravovaný službou (transparentní šifrování dat spravovaný službou), nebo asymetrický klíč uložený v Azure Key Vault (Bring Your Own Key). Ochranu transparentního šifrování dat nastavíte na úrovni serveru pro Azure SQL Database a datový sklad a na úrovni instance pro spravovanou instanci Azure SQL. Pojem *Server* v celém tomto dokumentu odkazuje na server i na instanci, pokud není uvedeno jinak.

Při spuštění databáze se zašifrovaný šifrovací klíč databáze dešifruje a pak se použije k dešifrování a opětovnému šifrování souborů databáze v procesu SQL Server databázového stroje. Transparentní šifrování dat provádí šifrování v/v v reálném čase a dešifrování dat na úrovni stránky. Každá stránka se při načtení do paměti dešifruje a pak se před zápisem na disk zašifruje. Obecný popis transparentního šifrování dat najdete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server spuštěný na virtuálním počítači Azure taky může použít asymetrický klíč z Key Vault. Konfigurační postup se liší od použití asymetrického klíče v SQL Database a spravované instanci SQL. Další informace najdete v tématu [rozšiřitelná Správa klíčů pomocí Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Transparentní šifrování dat spravované službou

V Azure je výchozím nastavením transparentního šifrování dat, že šifrovací klíč databáze je chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je pro každý server jedinečný a použitý šifrovací algoritmus je AES 256. Pokud je databáze v relaci geografické replikace, primární i geografická sekundární databáze je chráněná nadřazeným klíčem serveru primární databáze. Pokud jsou dvě databáze připojené ke stejnému serveru, sdílejí také stejný integrovaný certifikát.  Microsoft tyto certifikáty automaticky přetočí v souladu se zásadami interního zabezpečení a kořenový klíč je chráněný úložištěm interního tajného klíče Microsoftu.  Zákazníci mohou ověřit SQL Database dodržování interních zásad zabezpečení v nezávislých sestavách auditu třetích stran, které jsou k dispozici na [webu Microsoft Trust Center](https://servicetrust.microsoft.com/).

Microsoft také bez problémů přesouvá a spravuje klíče podle potřeby pro geografickou replikaci a obnovení.

> [!IMPORTANT]
> Všechny nově vytvořené databáze SQL a databáze spravované instance jsou ve výchozím nastavení šifrované pomocí transparentního šifrování dat spravovaného službou. Existující databáze SQL vytvořené před 2017 a databáze SQL vytvořené prostřednictvím obnovení, geografické replikace a kopie databáze nejsou ve výchozím nastavení šifrované. Existující databáze spravované instance vytvořené před únorem 2019 nejsou ve výchozím nastavení šifrované. Databáze spravované instance vytvořené prostřednictvím obnovení dědí stav šifrování ze zdroje.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparentní šifrování dat spravované zákazníkem – Bring Your Own Key

[TDE s použitím klíčů spravovaných zákazníkem v Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) umožňuje šifrování šifrovacího klíče databáze (klíč DEK) pomocí asymetrického klíče spravovaného zákazníkem, který se nazývá TDE Protector.  To se také obecně označuje jako podpora Bring Your Own Key (BYOK) pro transparentní šifrování dat. Ve scénáři BYOK se ochrana TDE ukládá do cloudového [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)ve vlastnictví a spravovaném zákazníkem, což je cloudový externí systém správy klíčů Azure. Ochranu TDE můžete [vygenerovat pomocí trezoru klíčů nebo přenést do trezoru klíčů](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) z místního zařízení HSM. TDE klíč DEK, který je uložen na spouštěcí stránce databáze, je zašifrován a dešifrován modulem ochrany TDE, který je uložen v Azure Key Vault a nikdy neopouští Trezor klíčů.  SQL Database musí mít udělená oprávnění trezoru klíčů vlastněné zákazníkem k dešifrování a šifrování klíč dek. Pokud jsou oprávnění logického serveru SQL k trezoru klíčů odvolána, databáze nebude přístupná a všechna data budou zašifrována. Pro Azure SQL Database ochrana TDE je nastavená na úrovni logického SQL serveru a děděna všemi databázemi přidruženými k tomuto serveru. U [spravované instance Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)je ochrana TDE nastavena na úrovni instance a zděděna všemi *šifrovanými* databázemi v této instanci. Pojem *Server* v celém tomto dokumentu odkazuje na server i na instanci, pokud není uvedeno jinak.

Pomocí TDE s integrací Azure Key Vault můžou uživatelé řídit úlohy správy klíčů, včetně střídání klíčů, oprávnění trezoru klíčů, záloh klíčů a povolit auditování nebo vytváření sestav pro všechny TDE ochrany pomocí funkcí Azure Key Vault. Key Vault poskytuje správu pomocí centrálních klíčů, využívá důkladně monitorované moduly hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které pomůžou splnit dodržování zásad zabezpečení.
Další informace o transparentním šifrování dat s Azure Key Vault integrací (podpora Bring Your Own Key) pro Azure SQL Database, spravované instance SQL a datové sklady najdete v tématu [transparentní šifrování dat s Azure Key Vault integrací](transparent-data-encryption-byok-azure-sql.md).

Pokud chcete začít používat transparentní šifrování dat s Azure Key Vault integrací (podpora Bring Your Own Key), přečtěte si téma návod, jak [zapnout transparentní šifrování dat pomocí vlastního klíče z Key Vault pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Přesunutí transparentní databáze chráněné šifrováním dat

Nemusíte dešifrovat databáze pro operace v rámci Azure. Nastavení transparentního šifrování dat ve zdrojové databázi nebo primární databázi je transparentně děděno v cíli. Zahrnuté operace zahrnují:

- Geografické obnovení
- Samoobslužné obnovení k bodu v čase
- Obnovení odstraněné databáze
- Aktivní geografická replikace
- Vytvoření kopie databáze
- Obnovení záložního souboru do spravované instance Azure SQL

> [!IMPORTANT]
> Ruční kopírování databáze šifrované pomocí TDE spravovaného službou není ve spravované instanci SQL Azure povolené, protože certifikát použitý k šifrování není dostupný. K přesunutí tohoto typu databáze do jiné spravované instance použijte funkci obnovení k bodu v čase.

Při exportu transparentní databáze chráněné šifrováním dat není exportovaný obsah databáze zašifrovaný. Tento exportovaný obsah je uložený v nešifrovaných souborech BACPAC. Nezapomeňte vhodně chránit soubory BACPAC a po dokončení importu nové databáze povolit transparentní šifrování dat.

Například pokud je soubor BACPAC exportován z místní instance SQL Server, importovaný obsah nové databáze nebude automaticky šifrován. Podobně platí, že pokud je soubor BACPAC exportován do místní instance SQL Server, nová databáze není také automaticky šifrována.

Jedinou výjimkou je při exportu do a z databáze SQL. V nové databázi je povolené transparentní šifrování dat, ale samotný soubor BACPAC ještě není zašifrovaný.


## <a name="manage-transparent-data-encryption"></a>Správa transparentního šifrování dat
# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Správa transparentního šifrování dat v Azure Portal.

Pokud chcete pomocí Azure Portal nakonfigurovat transparentní šifrování dat, musíte být připojeni jako vlastník Azure, přispěvatel nebo správce zabezpečení SQL.

Transparentní šifrování dat můžete zapnout a vypnout na úrovni databáze. Pokud chcete v databázi povolit transparentní šifrování dat, navštivte [Azure Portal](https://portal.azure.com) a přihlaste se pomocí správce Azure nebo účtu přispěvatele. V uživatelské databázi Najděte nastavení transparentního šifrování dat. Ve výchozím nastavení se používá transparentní šifrování dat spravované službou. Transparentní certifikát pro šifrování dat se automaticky vygeneruje pro server, který obsahuje databázi. Pro spravovanou instanci Azure SQL použijte T-SQL pro zapnutí a vypnutí transparentního šifrování dat v databázi.

![Transparentní šifrování dat spravované službou](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Na úrovni serveru jste nastavili hlavní klíč transparentního šifrování dat, označovaný také jako ochrana transparentního šifrování dat. Pokud chcete používat transparentní šifrování dat s podporou Bring Your Own Key a chránit vaše databáze pomocí klíče z Key Vault, otevřete na svém serveru nastavení transparentního šifrování dat.

![Transparentní šifrování dat s podporou Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Spravujte transparentní šifrování dat pomocí PowerShellu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete konfigurovat transparentní šifrování dat prostřednictvím PowerShellu, musíte být připojeni jako vlastník Azure, přispěvatel nebo správce zabezpečení SQL.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Rutiny pro Azure SQL Database a datový sklad

Pro Azure SQL Database a datový sklad použijte následující rutiny:

| Rutina | Popis |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Povoluje nebo zakazuje transparentní šifrování dat pro databázi.|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Získá transparentní stav šifrování dat pro databázi. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Kontroluje průběh šifrování databáze. |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Přidá klíč Key Vault do instance SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Získá Key Vault klíče pro Azure SQL Database Server.  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Nastaví ochranu transparentního šifrování dat pro instanci SQL Server. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Získá transparentní ochranu šifrování dat. |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Odebere klíč Key Vault z instance SQL Server. |
|  | |

> [!IMPORTANT]
> V případě spravované instance Azure SQL použijte příkaz T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) pro zapnutí a vypnutí transparentního šifrování dat na úrovni databáze a kontrolu transparentního šifrování dat na úrovni instance pomocí [ukázkového skriptu PowerShellu](transparent-data-encryption-byok-azure-sql-configure.md) .

# <a name="transact-sqltabazure-transactsql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Spravujte transparentní šifrování dat pomocí jazyka Transact-SQL.

Připojte se k databázi pomocí přihlašovacího jména, které je správcem nebo členem role **dbmanager** v hlavní databázi.

| Příkaz | Popis |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | NASTAVENÍ šifrování ZAPNUTo nebo vypnuto šifrování nebo dešifrování databáze |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Vrátí informace o stavu šifrování databáze a přidružených šifrovacích klíčích databáze. |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Vrátí informace o stavu šifrování každého uzlu datového skladu a jeho přidružených šifrovacích klíčů databáze. |
|  | |

Nemůžete přepnout ochranu transparentního šifrování dat na klíč z Key Vault pomocí jazyka Transact-SQL. Použijte PowerShell nebo Azure Portal.

# <a name="rest-apitabazure-restapi"></a>[REST API](#tab/azure-RESTAPI)
Spravujte transparentní šifrování dat pomocí REST API.

Pokud chcete pomocí REST API nakonfigurovat transparentní šifrování dat, musíte být připojeni jako vlastník Azure, přispěvatel nebo správce zabezpečení SQL.
Pro Azure SQL Database a datový sklad použijte následující sadu příkazů:

| Příkaz | Popis |
| --- | --- |
|[Vytvořit nebo aktualizovat server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Přidá Azure Active Directoryou identitu do instance SQL Server (používá se pro udělení přístupu k Key Vault).|
|[Vytvořit nebo aktualizovat klíč serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Přidá klíč Key Vault do instance SQL Server|
|[Odstranit klíč serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Odebere klíč Key Vault z instance SQL Server.|
|[Získat klíče serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Získá konkrétní klíč Key Vault z instance SQL Server.|
|[Výpis klíčů serveru podle serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Získá Key Vault klíče pro instanci SQL Server. |
|[Vytvořit nebo aktualizovat ochranu šifrování](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Nastaví ochranu transparentního šifrování dat pro instanci SQL Server.|
|[Získání ochrany šifrování](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Získá transparentní ochranu šifrování dat pro instanci SQL Server.|
|[Vypsat ochranu šifrování pomocí serveru](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Získá transparentní ochranu šifrování dat pro instanci SQL Server. |
|[Vytvořit nebo aktualizovat konfiguraci transparentní šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Povoluje nebo zakazuje transparentní šifrování dat pro databázi.|
|[Získat konfiguraci transparentní šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Získá transparentní konfiguraci šifrování dat pro databázi.|
|[Zobrazit seznam výsledků konfigurace transparentní šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Získá výsledek šifrování pro databázi.|

## <a name="next-steps"></a>Další kroky

- Obecný popis transparentního šifrování dat najdete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Další informace o transparentním šifrování dat s podporou Bring Your Own Key Azure SQL Database, spravované instance Azure SQL a datového skladu najdete v tématu [transparentní šifrování dat s podporou Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Pokud chcete začít používat transparentní šifrování dat s podporou Bring Your Own Key, přečtěte si téma Průvodce postupy [Zapnutí transparentního šifrování dat pomocí vlastního klíče z Key Vault pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Další informace o Key Vault najdete na [stránce dokumentace Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
