---
title: Transparentní šifrování dat
description: Přehled transparentního šifrování dat pro SQL Database a synapse SQL ve službě Azure synapse Analytics. Tento dokument popisuje své výhody a možnosti konfigurace, mezi které patří transparentní šifrování dat spravované službou a Bring Your Own Key.
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
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113540"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transparentní šifrování dat pro SQL Database a Azure synapse

[Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) pomáhá chránit Azure SQL Database, Azure SQL Managed instance a synapse SQL ve službě Azure synapse Analytics s hrozbou neoprávněné offline aktivity šifrováním dat v klidovém stavu. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci. Ve výchozím nastavení je TDE povolený pro všechny nově nasazené databáze Azure SQL a musí se ručně povolit pro starší databáze Azure SQL Database, Azure SQL Managed instance nebo Azure synapse.

TDE provádí šifrování v/v v reálném čase a dešifrování dat na úrovni stránky. Každá stránka se při načtení do paměti dešifruje a pak se před zápisem na disk zašifruje. TDE šifruje úložiště celé databáze pomocí symetrického klíče, který se nazývá šifrovací klíč databáze (klíč DEK). Při spuštění databáze se šifrované klíč DEK dešifrují a potom se používají k dešifrování a opětovnému šifrování souborů databáze v procesu SQL Server databázového stroje. KLÍČ DEK je chráněn ochranou TDE. TDE Protector je buď certifikát spravovaný službou (transparentní šifrování dat spravovaný službou), nebo asymetrický klíč uložený v [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (transparentní šifrování dat spravované zákazníkem). 

Pro Azure SQL Database a Azure synapse se ochrana TDE nastaví na úrovni logického serveru SQL Server a děděna všemi databázemi přidruženými k tomuto serveru. Pro spravovanou instanci Azure SQL (funkce BYOK ve verzi Preview) se ochrana TDE nastaví na úrovni instance a zděděná všemi šifrovanými databázemi v této instanci. Pojem *Server* v celém tomto dokumentu odkazuje na server i na instanci, pokud není uvedeno jinak.

> [!IMPORTANT]
> Všechny nově vytvořené databáze SQL Azure jsou ve výchozím nastavení šifrované pomocí transparentního šifrování dat spravovaného službou. Existující databáze SQL vytvořené před 2017 a databáze SQL vytvořené prostřednictvím obnovení, geografické replikace a kopie databáze nejsou ve výchozím nastavení šifrované. Existující databáze spravované instance vytvořené před únorem 2019 nejsou ve výchozím nastavení šifrované. Databáze spravované instance vytvořené prostřednictvím obnovení dědí stav šifrování ze zdroje.

> [!NOTE]
> TDE nelze použít k šifrování logické **Hlavní** databáze v SQL Database.  **Hlavní** databáze obsahuje objekty, které jsou potřebné k provedení operací TDE v uživatelských databázích.


## <a name="service-managed-transparent-data-encryption"></a>Transparentní šifrování dat spravované službou

V Azure je výchozím nastavením pro TDE, že klíč DEK je chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je pro každý server jedinečný a použitý šifrovací algoritmus je AES 256. Pokud je databáze v relaci geografické replikace, primární i geografická sekundární databáze je chráněna klíčem nadřazeného serveru primární databáze. Pokud jsou dvě databáze připojené ke stejnému serveru, sdílejí také stejný integrovaný certifikát.  Microsoft tyto certifikáty automaticky přetočí v souladu se zásadami interního zabezpečení a kořenový klíč je chráněný úložištěm interního tajného klíče Microsoftu.  Zákazníci mohou ověřit SQL Database dodržování interních zásad zabezpečení v nezávislých sestavách auditu třetích stran, které jsou k dispozici na [webu Microsoft Trust Center](https://servicetrust.microsoft.com/).

Microsoft také bez problémů přesouvá a spravuje klíče podle potřeby pro geografickou replikaci a obnovení.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparentní šifrování dat spravované zákazníkem – Bring Your Own Key

TDE spravovaná zákazníkem se také označuje jako podpora služby Bring Your Own Key (BYOK) pro TDE. V tomto scénáři TDE ochrana šifrování klíč DEK je asymetrický klíč spravovaný zákazníkem, který je uložený ve Azure Key Vault ve vlastnictví a spravovaném zákazníkovi (cloudový externí systém správy klíčů Azure) a nikdy neopouští Trezor klíčů. Ochranu TDE můžete [vygenerovat pomocí trezoru klíčů nebo přenést do trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) z místního zařízení modulu hardwarového zabezpečení (HSM). SQL Database musí mít udělená oprávnění trezoru klíčů vlastněné zákazníkem k dešifrování a šifrování klíč dek. Pokud jsou oprávnění logického serveru SQL k trezoru klíčů odvolána, databáze nebude přístupná a všechna data budou zašifrována.

Pomocí TDE s integrací Azure Key Vault můžou uživatelé řídit úlohy správy klíčů, včetně střídání klíčů, oprávnění trezoru klíčů, záloh klíčů a povolit auditování nebo vytváření sestav pro všechny TDE ochrany pomocí funkcí Azure Key Vault. Key Vault poskytuje správu centrálních klíčů, využívá úzce monitorovanou HSM a umožňuje oddělení povinností mezi správou klíčů a dat, aby bylo možné splnit dodržování zásad zabezpečení.
Další informace o BYOK pro Azure SQL Database a Azure synapse najdete v tématu [transparentní šifrování dat pomocí Azure Key Vault Integration](transparent-data-encryption-byok-azure-sql.md).

Pokud chcete začít používat TDE s integrací Azure Key Vault, přečtěte si téma Průvodce postupy [Zapnutí transparentního šifrování dat pomocí vlastního klíče z Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Přesunutí transparentní databáze chráněné šifrováním dat

Nemusíte dešifrovat databáze pro operace v rámci Azure. Nastavení TDE ve zdrojové databázi nebo primární databázi jsou transparentně děděna v cíli. Zahrnuté operace zahrnují:

- Geografické obnovení
- Samoobslužné obnovení k bodu v čase
- Obnovení odstraněné databáze
- Aktivní geografická replikace
- Vytvoření kopie databáze
- Obnovení záložního souboru do spravované instance Azure SQL

> [!IMPORTANT]
> Ruční kopírování databáze šifrované pomocí TDE spravovaného službou není ve spravované instanci SQL Azure povolené, protože certifikát použitý k šifrování není dostupný. K přesunutí tohoto typu databáze do jiné spravované instance použijte funkci obnovení k bodu v čase.

Pokud exportujete databázi chráněnou TDE, exportovaný obsah databáze nebude zašifrovaný. Tento exportovaný obsah je uložený v nešifrovaných souborech BACPAC. Nezapomeňte vhodně chránit soubory BACPAC a po dokončení importu nové databáze povolte TDE.

Například pokud je soubor BACPAC exportován z místní instance SQL Server, importovaný obsah nové databáze nebude automaticky šifrován. Podobně platí, že pokud je soubor BACPAC exportován do místní instance SQL Server, nová databáze není také automaticky šifrována.

Jedinou výjimkou je při exportu do a z databáze SQL. V nové databázi je povolený TDE, ale samotný soubor BACPAC ještě není zašifrovaný.


## <a name="manage-transparent-data-encryption"></a>Správa transparentního šifrování dat
# <a name="portal"></a>[Portál](#tab/azure-portal)
Správa TDE v Azure Portal.

Pokud chcete nakonfigurovat TDE prostřednictvím Azure Portal, musíte být připojeni jako vlastník Azure, přispěvatel nebo správce zabezpečení SQL.

Zapnete a vypnete TDE na úrovni databáze. Pokud chcete povolit TDE v databázi, otevřete [Azure Portal](https://portal.azure.com) a přihlaste se pomocí správce Azure nebo účtu přispěvatele. V uživatelské databázi vyhledejte nastavení TDE. Ve výchozím nastavení se používá transparentní šifrování dat spravované službou. Certifikát TDE se automaticky vygeneruje pro server, který obsahuje databázi. Pro spravovanou instanci Azure SQL použijte T-SQL k zapnutí a vypnutí TDE v databázi.

![Transparentní šifrování dat spravované službou](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Hlavní klíč TDE, který se označuje jako ochrana TDE, se nastavuje na úrovni serveru. Pokud chcete používat TDE s podporou BYOK a chránit své databáze klíčem z Key Vault, otevřete nastavení TDE v rámci vašeho serveru.

![Transparentní šifrování dat s podporou Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[Prostředí](#tab/azure-powershell)
Spravujte TDE pomocí PowerShellu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete nakonfigurovat TDE prostřednictvím PowerShellu, musíte být připojeni jako vlastník Azure, přispěvatel nebo správce zabezpečení SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Rutiny pro Azure SQL Database a Azure synapse

Pro Azure SQL Database a Azure synapse použijte následující rutiny:

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
> V případě spravované instance Azure SQL použijte příkaz T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) pro zapnutí a vypnutí TDE na úrovni databáze a kontrolu [ukázkového skriptu PowerShellu](transparent-data-encryption-byok-azure-sql-configure.md) pro správu TDE na úrovni instance.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Spravujte TDE pomocí jazyka Transact-SQL.

Připojte se k databázi pomocí přihlašovacího jména, které je správcem nebo členem role **dbmanager** v hlavní databázi.

| Příkaz | Popis |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | NASTAVENÍ šifrování ZAPNUTo nebo vypnuto šifrování nebo dešifrování databáze |
| [sys. dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Vrátí informace o stavu šifrování databáze a přidružených šifrovacích klíčích databáze. |
| [sys. dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Vrátí informace o stavu šifrování pro každý uzel Azure synapse a jeho přidružených šifrovacích klíčích databáze. |
|  | |

Ochranu TDE nejde přepnout na klíč z Key Vault pomocí jazyka Transact-SQL. Použijte PowerShell nebo Azure Portal.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
Spravujte TDE pomocí REST API.

Pokud chcete nakonfigurovat TDE prostřednictvím REST API, musíte být připojeni jako vlastník Azure, přispěvatel nebo správce zabezpečení SQL.
Pro Azure SQL Database a Azure synapse použijte následující sadu příkazů:

| Příkaz | Popis |
| --- | --- |
|[Vytvořit nebo aktualizovat server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Přidá Azure Active Directoryou identitu do instance SQL Server (používá se pro udělení přístupu k Key Vault).|
|[Vytvořit nebo aktualizovat klíč serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Přidá klíč Key Vault do instance SQL Server|
|[Odstranit klíč serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Odebere klíč Key Vault z instance SQL Server.|
|[Získat klíče serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Získá konkrétní klíč Key Vault z instance SQL Server.|
|[Výpis klíčů serveru podle serveru](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Získá Key Vault klíče pro instanci SQL Server. |
|[Vytvořit nebo aktualizovat ochranu šifrování](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Nastaví ochranu TDE pro instanci SQL Server.|
|[Získání ochrany šifrování](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Získá ochranu TDE pro instanci SQL Server.|
|[Vypsat ochranu šifrování pomocí serveru](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Získá TDE ochrany pro instanci SQL Server. |
|[Vytvořit nebo aktualizovat konfiguraci transparentní šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Povoluje nebo zakazuje TDE pro databázi.|
|[Získat konfiguraci transparentní šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Získá konfiguraci TDE pro databázi.|
|[Zobrazit seznam výsledků konfigurace transparentní šifrování dat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Získá výsledek šifrování pro databázi.|

## <a name="see-also"></a>Viz také
- SQL Server spuštěný na virtuálním počítači Azure taky může použít asymetrický klíč z Key Vault. Konfigurační postup se liší od použití asymetrického klíče v SQL Database a spravované instanci SQL. Další informace najdete v tématu [rozšiřitelná Správa klíčů pomocí Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Obecný popis TDE najdete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Další informace o TDE s podporou BYOK pro Azure SQL Database, spravované instance Azure SQL a Azure synapse najdete v tématu [transparentní šifrování dat s podporou Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Pokud chcete začít používat TDE s podporou Bring Your Own Key, přečtěte si téma Průvodce postupy, [zapněte transparentní šifrování dat pomocí vlastního klíče z Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).
- Další informace o Key Vault najdete v tématu [zabezpečený přístup k trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
