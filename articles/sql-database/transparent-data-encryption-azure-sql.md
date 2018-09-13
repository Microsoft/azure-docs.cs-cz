---
title: Transparentní šifrování dat pro Azure SQL Database a Data Warehouse | Dokumentace Microsoftu
description: Přehled transparentní šifrování dat pro SQL Database a Data Warehouse. Dokument uvádí i jeho výhody a možnosti pro konfiguraci, která zahrnuje spravovaný službou transparentní šifrování dat a vlastního klíče.
services: sql-database
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 9e743dc2d5b6c2305bf071fecec58fcf13d5c92e
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717722"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparentní šifrování dat pro SQL Database a Data Warehouse

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit Azure SQL Database a Azure Data Warehouse. Provede v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu bez nutnosti změny aplikace. Ve výchozím nastavení je transparentní šifrování dat povolené pro všechny nově nasazenými databázemi Azure SQL. Transparentní šifrování dat nelze použít k šifrování logické **hlavní** databáze ve službě SQL Database.  **Hlavní** databáze obsahuje objekty, které jsou potřeba k provádění operací transparentní šifrování dat pro uživatelské databáze.

Transparentní šifrování dat bude nutné ručně povolit pro starší databáze nebo Azure SQL Data Warehouse.  

Transparentní šifrování dat šifruje úložiště celou databázi pomocí symetrický klíč s názvem šifrovací klíč databáze. Tento šifrovací klíč databáze je chráněn ochrana dat transparentní šifrování. Ochranného je buď spravovaný službou certifikát (spravovaný službou transparentní šifrování dat) nebo asymetrický klíč do služby Azure Key Vault (přineste si vlastní klíč). Nastavit ochrana dat transparentní šifrování na úrovni serveru. 

Při spuštění databáze je šifrovací klíč šifrovaného databázového dešifrovat a pak použít pro šifrování a znova šifrovat soubory databáze v procesu databázový stroj SQL serveru. Transparentní šifrování dat provádí v reálném čase vstupně-výstupní operace šifrování a dešifrování dat na úrovni stránky. Každou stránku lze dešifrovat, pokud se má načíst do paměti a pak se před zápisem zašifrují na disk. Obecný popis transparentní šifrování dat, naleznete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server běžící na virtuálním počítači Azure také můžete použít asymetrický klíč ze služby Key Vault. Postup konfigurace se liší od použití asymetrického klíče ve službě SQL Database. Další informace najdete v tématu [rozšiřitelná Správa klíčů s použitím služby Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Spravovat službu transparentní šifrování dat

V Azure ve výchozím nastavení transparentní šifrování dat je, že šifrovací klíč databáze je chráněno certifikátem integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server. Pokud je v relaci geografické replikace databáze, primární a geograficky sekundární databáze jsou chráněny primární databáze nadřazeného serveru klíče. Pokud dvě databáze jsou připojené ke stejnému serveru, sdílejí stejný certifikát integrované. Microsoft automaticky otočí tyto certifikáty nejméně každých 90 dní.

Microsoft také bezproblémově přesune a spravuje klíče podle potřeby pro geografickou replikaci a obnoví. 

> [!IMPORTANT]
> Všechny nově vytvořené databáze SQL jsou šifrovány ve výchozím nastavení pomocí spravované službou transparentní šifrování dat. Ve výchozím nastavení nejsou šifrovány existující databáze před. května 2017 a databází vytvořených prostřednictvím obnovení, geografická replikace a kopii databáze.
>

## <a name="bring-your-own-key"></a>Přineste si vlastní klíč

S podporou přineste si vlastní klíč, můžete provést kontrolu nad transparentní data šifrovacích klíčů a určovat, kdo k nim přistupovat a kdy. Key Vault, což je systém Azure založené na cloudu externí správu klíčů, je první služba správy klíčů tento transparentní šifrování dat obsahuje integrované podpory vlastního klíče. Díky podpoře vlastního klíče šifrovací klíč databáze je chráněn asymetrického klíče do služby Key Vault. Asymetrický klíč nikdy neopustí služby Key Vault. Po server má oprávnění k trezoru klíčů, server odesílá požadavky základní operace klíče k němu prostřednictvím Key Vaultu. Asymetrický klíč nastavíte na úrovni serveru a všechny databáze na tomto serveru dědí je.

Díky podpoře vlastního klíče teď můžete řídit úlohy správy klíčů, například rotace klíčů a oprávnění služby key vault. Můžete také odstranit klíčů a povolit auditování a generování sestav na všechny šifrovací klíče. Key Vault umožňuje centrální správu klíčů a používá modulech zabezpečení hardwaru úzce monitorované. Key Vault podporuje oddělení správy klíčů a dat, které vám pomohou splnit dodržování legislativních předpisů. Další informace o službě Key Vault najdete v tématu [stránky dokumentace ke službě Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

Další informace o transparentní šifrování dat s podporou vlastního klíče pro SQL Database a Data Warehouse, najdete v článku [transparentní šifrování dat s podporou vlastního klíče](transparent-data-encryption-byok-azure-sql.md).

Pokud chcete začít používat transparentní šifrování dat s podporou vlastního klíče, naleznete v příručce s postupy [zapnout transparentní šifrování dat s použitím vlastního klíče ze služby Key Vault pomocí Powershellu](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Přesunutí databáze chráněné heslem šifrování transparentní dat

Není nutné k dešifrování databáze pro operace v rámci Azure. Nastavení šifrování transparentní dat na zdrojové databázi nebo v primární databázi se transparentně dědí na cíli. Operace, které jsou zahrnuty zahrnují:
- Geografické obnovení.
- Samoobslužné obnovení bodu v čase.
- Obnovení odstraněné databáze.
- Aktivní geografickou replikaci.
- Vytvoření kopie databáze.

Při exportu transparentní data šifrování chráněné databáze exportovaný obsah databáze nejsou šifrována. Tento exportovaný obsah uložený v nešifrované souborů BACPAC. Ujistěte se, k ochraně souborů BACPAC odpovídajícím způsobem a povolit transparentní šifrování dat po dokončení importu se nová databáze.

Například pokud exportu souboru BACPAC z instance SQL serveru v místním, importovaný obsah nové databáze není automaticky šifrují. Podobně pokud soubor BACPAC se exportují do místní instance systému SQL Server, nové databáze také se automaticky šifrují.

Jedinou výjimkou je, když exportujete do a z databáze SQL. Transparentní šifrování dat je povoleno v nové databázi, ale samotný soubor BACPAC není šifrovaný.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Správa transparentní šifrování dat na webu Azure Portal

Pokud chcete nakonfigurovat transparentní šifrování dat na webu Azure portal, musí být připojen jako Azure vlastníkem, přispěvatelem nebo správce zabezpečení SQL. 

Nastavit transparentní šifrování dat na úrovni databáze. Pokud chcete povolit transparentní šifrování dat v databázi, přejděte na [webu Azure portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure administrátorem nebo přispěvatelem. Najdete nastavení šifrování transparentní dat v uživatelské databázi. Ve výchozím nastavení spravovat službu transparentní šifrování dat se používá. Transparentní datový šifrovací certifikát není automaticky vygenerován pro server, který obsahuje databázi. 

![Spravovat službu transparentní šifrování dat](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Nastavíte hlavní klíč šifrování dat transparentní, označované také jako ochrana šifrování transparentní dat, na úrovni serveru. Transparentní šifrování dat pomocí podpory vlastního klíče a ochrana vašich databází s využitím klíče ze služby Key Vault najdete v tématu Nastavení šifrování transparentní dat v rámci vašeho serveru. 

![Transparentní šifrování dat s podporou vlastního klíče](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Správa transparentní šifrování dat pomocí prostředí PowerShell

Pokud chcete nakonfigurovat transparentní šifrování dat pomocí Powershellu, musíte být připojeni jako Azure vlastníkem, přispěvatelem nebo správce zabezpečení SQL. 

| Rutina | Popis |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |Povolí nebo zakáže transparentní šifrování dat pro databázi|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |Získá stav šifrování transparentní dat pro databázi |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |Kontroluje, Probíhá šifrování pro databázi |
| [Přidat AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |Přidá klíč služby Key Vault k instanci systému SQL Server |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |Získá klíče služby Key Vault pro instanci SQL serveru  |
| [Rutiny Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |Nastaví ochrana dat transparentní šifrování pro instanci systému SQL Server |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Získá data transparentní ochrana šifrování |
| [Odebrat AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |Odebere klíč služby Key Vault z instance systému SQL Server |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Správa transparentní šifrování dat s použitím příkazů jazyka Transact-SQL

Připojení k databázi pomocí přihlašovacích údajů, který je správce nebo člen **dbmanager** role v hlavní databázi.

| Příkaz | Popis |
| --- | --- |
| [Příkaz ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | NASTAVENÍ nebo šifrování vypnout šifruje nebo dešifruje databáze |
| [Sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Vrátí informace o stav šifrování databáze a její přidružené databáze, šifrovacích klíčů |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Vrátí informace o šifrování stavu každého datového skladu uzel a jeho přidružená databáze šifrovací klíče | 
|  | |

Ochrana dat transparentní šifrování nelze přepnout na klíč ze služby Key Vault s použitím příkazů jazyka Transact-SQL. Pomocí Powershellu nebo na webu Azure portal.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Spravovat transparentní šifrování dat pomocí rozhraní REST API
 
Pokud chcete nakonfigurovat transparentní šifrování dat přes rozhraní REST API, musíte být připojeni jako Azure vlastníkem, přispěvatelem nebo správce zabezpečení SQL. 

| Příkaz | Popis |
| --- | --- |
|[Vytvořit nebo aktualizovat Server](/rest/api/sql/servers/createorupdate)|Přidá do instance systému SQL Server (používá k udělení přístupu ke Key Vault) identity Azure Active Directory|
|[Vytvořit nebo aktualizovat klíč serveru](/rest/api/sql/serverkeys/createorupdate)|Přidá klíč služby Key Vault k instanci systému SQL Server|
|[Odstranit klíč serveru](/rest/api/sql/serverkeys/delete)|Odebere klíč služby Key Vault z instance systému SQL Server|
|[Získat klíče serveru](/rest/api/sql/serverkeys/get)|Získá konkrétní klíč služby Key Vault z instance systému SQL Server|
|[Vypsat klíče Server serverem](/rest/api/sql/serverkeys/listbyserver)|Získá klíče služby Key Vault pro instanci systému SQL Server |
|[Vytvořit nebo aktualizovat ochrana šifrování](/rest/api/sql/encryptionprotectors/createorupdate)|Nastaví ochrana dat transparentní šifrování pro instanci systému SQL Server|
|[Získat ochrana šifrování](/rest/api/sql/encryptionprotectors/get)|Získá data transparentní ochrana šifrování pro instanci systému SQL Server|
|[Seznam ochrany pomocí šifrování serveru](/rest/api/sql/encryptionprotectors/listbyserver)|Získá ochrany dat transparentní šifrování pro instanci systému SQL Server |
|[Vytvořit nebo aktualizovat konfiguraci Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/createorupdate)|Povolí nebo zakáže transparentní šifrování dat pro databázi|
|[Získat konfiguraci Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/get)|Získá konfiguraci šifrování transparentní dat pro databázi|
|[Seznam Transparent Data Encryption konfigurace výsledků](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|Získá výsledek šifrování pro databázi|

## <a name="next-steps"></a>Další postup

- Obecný popis transparentní šifrování dat, naleznete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Další informace o transparentní šifrování dat s podporou vlastního klíče pro SQL Database a Data Warehouse, najdete v článku [transparentní šifrování dat s podporou vlastního klíče](transparent-data-encryption-byok-azure-sql.md).
- Pokud chcete začít používat transparentní šifrování dat s podporou vlastního klíče, naleznete v příručce s postupy [zapnout transparentní šifrování dat s použitím vlastního klíče ze služby Key Vault pomocí Powershellu](transparent-data-encryption-byok-azure-sql-configure.md).
- Další informace o službě Key Vault najdete v tématu [stránky dokumentace ke službě Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
