---
title: Transparentní šifrování dat – přineste si vlastní klíč (BYOK) – Azure SQL Database | Dokumentace Microsoftu
description: Přeneste podporu Your Own Key (BYOK) pro transparentní šifrování dat (TDE) pomocí služby Azure Key Vault pro SQL Database a Data Warehouse. Transparentní šifrování dat s BYOK přehled výhod, jak to funguje, důležité informace a doporučení.
keywords: ''
services: sql-database
documentationcenter: ''
author: aliceku
manager: craigg
ms.prod: ''
ms.reviewer: vanto
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/30/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: d87747e60c375f844681ed6cfd40dba84f46a9b2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963607"
---
# <a name="transparent-data-encryption-with-bring-your-own-key-support-for-azure-sql-database-and-data-warehouse"></a>Transparentní šifrování dat s podporou vlastního klíče pro Azure SQL Database a Data Warehouse

Podpoře Your Own Key (BYOK) pro [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) umožňuje šifrovat databáze šifrovací klíč (DEK) s názvem ochrana TDE asymetrického klíče.  Ochrana TDE je uložen pod vaší kontrolou v [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), systém Azure založené na cloudu externí správu klíčů. Azure Key Vault je první služba správy klíčů, se kterým má TDE integrovaná podpora pro funkci BYOK. Klíč DEK transparentní šifrování dat, který je uložený na spouštěcí stránku databáze je šifrovaný a dešifrovat ochrana TDE. Ochrana TDE je uložená ve službě Azure Key Vault a nikdy neopustí trezoru klíčů. Pokud je odvolat přístup k trezoru klíčů serveru, databázi nelze dešifrovat a načíst do paměti.  Ochrana TDE je nastavená na úrovni logický server a zdědí všechny databáze, které jsou spojené s tímto serverem. 

S podporou modelu BYOK uživatelé mají nyní kontrolu nad klíčových úloh správy včetně rotace klíčů, klíče trezoru klíčů oprávnění, odstranění a povolit auditování a generování sestav na všechny ochrany transparentní šifrování dat pomocí funkce Azure Key Vault. Key Vault poskytuje centrální správu klíčů, využívá modulů úzce monitorovaných hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které vám pomohou splnit dodržování legislativních předpisů.  


Transparentní šifrování dat s BYOK poskytuje následující výhody:
- Vyšší transparentnost a detailní kontrolu umožňuje samoobslužné správě ochrana TDE   
- Centrální správa chráničů transparentní šifrování dat (spolu s vylepšeními klíče a tajné klíče používané jinými službami Azure) podle hostování ve službě Key Vault
- Oddělení povinností správy klíč a data v rámci organizace, pro podporu oddělení povinností
- Větší důvěryhodnost z vlastních klientů, protože Key Vault je navržený tak, aby Microsoft neznala ani neextrahovala nějaké šifrovací klíče. 
- Podpora pro obměna klíčů

> [!IMPORTANT]
> K těm, kteří používají spravovaná služba TDE, kteří by chtěli začít používat Key Vault transparentní šifrování dat zůstane povolen během procesu přepnutí na ochrana TDE ve službě Key Vault. Neexistuje žádný výpadek ani znova šifrovat soubory databáze. Přechod z klíče spravované službou Key Vault klíč se vyžaduje jenom znova šifrovat šifrovací klíč databáze (DEK), což je rychlé a online operace. 
>

## <a name="how-does-tde-with-byok-support-work"></a>Jak podporuje transparentní šifrování dat s BYOK práce?
 
![Ověřování serveru k trezoru klíčů](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Transparentní šifrování dat, je nejprve konfigurován pro použití ochrana TDE ze služby Key Vault, server odešle klíč DEK každou databázi povoleno TDE do služby Key Vault pro žádost o klíčových zalamování řádků. Key Vault vrátí šifrovací klíč šifrovaného databáze, která je uložena v uživatelské databázi.  

>[!IMPORTANT]
>Je důležité si uvědomit, že **Jakmile ochrana TDE je uložená ve službě Azure Key Vault, nikdy neopustí Azure Key Vault**. Logický server může odeslat pouze operace klíče požadavky materiál klíče ochrana TDE, v rámci služby Key Vault a **nikdy přistupuje k nebo ukládá do mezipaměti ochrana TDE**. Správce služby Key Vault má oprávnění k odvolání oprávnění služby Key Vault serveru kdykoli, v takovém případě všechna připojení k serveru se oříznou. 
>


## <a name="guidelines-for-configuring-tde-with-byok"></a>Pokyny ke konfiguraci transparentní šifrování dat s BYOK

### <a name="general-guidelines"></a>Obecné pokyny
- Ujistěte se služby Azure Key Vault a Azure SQL Database budou ve stejném tenantovi.  Klíče trezoru a server interakce mezi tenanty **nepodporují**.
- Rozhodněte, které odběry se bude používat pro požadované prostředky – přesun serveru mezi předplatnými později vyžaduje nové nastavení transparentní šifrování dat s BYOKs. Další informace o [přesouvání prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Při konfiguraci transparentní šifrování dat s BYOK, je důležité vzít v úvahu zatížení, které vyvíjejí pro trezor klíčů pomocí opakovaných zabalit nebo rozbalit operací. Například protože všechny databáze, které jsou přidružené k logickému serveru použít stejný ochrana TDE, převzetí služeb při selhání ze serveru se spustit, protože mnoho klíčové operace proti trezor existuje jako jsou databáze na serveru. Podle našich zkušeností a uvádí [klíče omezení služby úložiště](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits), doporučujeme přidružení maximálně 500 Standard / obecné účely nebo 200 úrovně Premium / databáze pro důležité obchodní informace s jeden Azure Key Vault v rámci jednoho předplatného k zajištění konzistentně vysokou dostupnost, když přistupoval k ochrana TDE v trezoru. 
- Doporučeno: Ponechte si kopii ochrana TDE v místním prostředí.  To vyžaduje k modulu hardwarového zabezpečení zařízení a vytvořit ochrana TDE místně a klíčů v úschově systému umožňuje ukládat místní kopii ochrana TDE.  Přečtěte si [jak pro přenos klíče z místního modulu HSM do služby Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Pokyny ke konfiguraci služby Azure Key Vault

- Vytvoření služby key vault pomocí [obnovitelného odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) chránit před ztrátou v případě náhodného klíče – nebo služby key vault – odstranění dat povolené.  Je nutné použít [Powershellu povolit vlastnost "obnovitelného odstranění"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) pro trezor klíčů (Tato možnost není dostupná z portálu, službou AZURE ještě – ale vyžadují SQL):  
  - Obnovitelně odstraněný prostředky se uchovávají nastavte časový úsek, 90 dnů Pokud jsou obnovit nebo odstraněna.
  - **Obnovit** a **vyprázdnit** akce mají své vlastní oprávnění přidružené zásady přístupu trezoru klíčů. 
- Nastavte zámek prostředku pro trezor klíčů pro ovládací prvek, který může odstranit tento prostředek kritické a pomoci zabránit neoprávněné nebo nechtěné odstranění.  [Další informace o zámky prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Udělte přístup k trezoru klíčů pomocí Azure Active Directory (Azure AD) Identity logického serveru.  Při použití uživatelského rozhraní portálu, se automaticky vytvoří identity Azure AD a serveru jsou udělena oprávnění k přístupu trezoru klíčů.  Pomocí Powershellu nakonfigurovat transparentní šifrování dat s BYOK, je nutné vytvořit identitu služby Azure AD a dokončení by se měly ověřit. Zobrazit [nakonfigurovat transparentní šifrování dat s BYOK](transparent-data-encryption-byok-azure-sql-configure.md) podrobné pokyny krok za krokem při používání prostředí PowerShell.

  >[!NOTE]
  >Pokud Azure AD Identity **je omylem odstraněn nebo byly odvolány, serveru oprávnění** pomocí zásady přístupu trezoru klíčů, server ztratí přístup k trezoru klíčů a transparentní šifrování dat šifrované databáze zařazují do 24 hodin.
  >

- Konfigurace služby Azure Key Vault bez virtuální sítě a brány firewall.  Pokud SQL ztratí přístup k trezoru klíčů, jsou vynechány TDE šifrované databáze během 24 hodin.
- Povolit auditování a vytváření sestav na všechny šifrovací klíče: Key Vault poskytuje protokoly, které se dají snadno vkládat do jiné nástroje pro správu (SIEM) událostí zabezpečení. Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) je příkladem služby, která je již integrovaná.
- K zajištění vysoké dostupnosti šifrovaným databázím, nakonfigurujte každý logický server s dvěma Azure Key Vault, které se nacházejí v různých oblastech.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key-stored-in-azure-key-vault"></a>Pokyny ke konfiguraci ochrana TDE (asymetrického klíče) do služby Azure Key Vault

- Vytvořte váš šifrovací klíč místně na místním zařízení HSM. Ujistěte se, že se že jedná se o asymetrické, klíč RSA 2048, tak, aby byl uložitelného ve službě Azure Key Vault.
- V systému klíčů v úschově úschovu používala služba klíč.  
- Importujte souboru šifrovacího klíče (.pfx, .byok nebo .backup) do služby Azure Key Vault. 
    

>[!NOTE] 
    >Pro účely testování, je možné k vytvoření klíče se službou Azure Key Vault, však nelze mezi tohoto klíče, protože privátní klíč lze nikdy neopustí služby key vault.  Vždy zálohování a úschovu používala služba klíče používané k šifrování provozními daty, jako ke ztrátě klíče (nechtěnému odstranění klíče trezoru, vypršení platnosti atd.) má za následek ztrátu trvalá data.
    >
    
- Použijte klíč bez nutnosti datum vypršení platnosti – a nikdy nastavte datum vypršení platnosti klíče již používán: **po vypršení platnosti tohoto klíče, šifrovaným databázím ztratíte přístup k jejich ochrana TDE a zařazují do 24 hodin**.
- Zkontrolujte klíč zapnutá a nemá oprávnění k provedení *získat*, *zabalit klíč*, a *rozbalit klíč* operace.
- Před použitím klíč ve službě Azure Key Vault prvním vytvoření zálohy klíče Azure Key Vault. Další informace o [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) příkazu.
- Vytvořit novou zálohu pokaždé, když se všechny změny provedené klíč (třeba přidání seznamy ACL, přidání značek, přidání klíčových atributů).
- **Zachovat předchozí verze** klíče v trezoru klíčů při obměně klíčů, můžete obnovit tak starší zálohy databáze. Při změně ochrana TDE pro databázi, starší zálohy databáze **se neaktualizují** použít nejnovější ochrana TDE.  Každá záloha musí ochrana TDE byl vytvořen v době obnovení. Střídání klíčů lze provést podle pokynů [otočit transparentní ochrana šifrování dat pomocí Powershellu](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Po změně klíče spravované zákazníkem služby zachovat všechny dříve použitých klíče ve službě Azure Key Vault.  Tím se zajistí, že zálohování databáze je možné obnovit pomocí ochrany transparentní šifrování dat uložených ve službě Azure Key Vault.  Transparentní šifrování dat ochrany vytvořené pomocí Azure Key Vault musí být zachovány, dokud všechny uložené zálohy byly vytvořeny s použitím klíčů spravovaných služeb.  
- Vytvořte obnovitelné kopie tyto klíče pomocí [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Odebrání potenciálně ohrožený klíč během incidentu zabezpečení bez rizika ztráty dat, postupujte podle kroků uvedených v [odebrání potenciálně ohrožený klíč](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).


## <a name="high-availability-geo-replication-and-backup--restore"></a>Vysoká dostupnost, geografickou replikaci a zálohování a obnově

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

Ke konfiguraci vysoké dostupnosti s využitím služby Azure Key Vault, závisí na konfiguraci databáze a logický server, a tady jsou požadavky na doporučené konfiguraci pro dva různé případy.  Prvním případě je samostatná databáze nebo logický server pomocí žádné nakonfigurovanou geografickou redundancí.  Druhou možností je databázi nebo logický server nakonfigurované skupiny převzetí služeb při selhání nebo geografická redundance, kde je třeba zajistit, že každá geograficky redundantní kopie má místní služby Azure Key Vault ve skupině převzetí služeb při selhání k zajištění pracovní geo-převzetí služeb při selhání. V prvním případě pokud požadujete vysokou dostupnost databáze a logický server pomocí žádné nakonfigurovanou geografickou redundanci, doporučujeme nakonfigurovat server pro použití dvou různých trezorů klíčů ve dvou různých oblastech pomocí stejného klíče.  Toho můžete docílit tak, že vytvoříte ochranu pomocí primární služby Key Vault společně umístěné ve stejné oblasti jako logický server transparentní šifrování dat a tak, aby server měl přístup k trezoru klíčů druhého klonování klíče do služby key vault v jiné oblasti Azure, měli byste primární klíč v Výchozí prostředí kvůli výpadku, zatímco databáze je vytvořená a spuštěná. Pomocí rutiny Backup-AzureKeyVaultKey načíst klíč v šifrovaném tvaru z primární služby key vault a pomocí rutiny Restore-AzureKeyVaultKey zadejte služby key vault v druhé oblasti.


![Jeden Server HA a geo-dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Jak nakonfigurovat Geo-DR služby Azure Key Vault

Kvůli udržení vysoké dostupnosti chráničů transparentní šifrování dat pro šifrované databáze, je potřeba nakonfigurovat redundantní trezory klíčů Azure na základě existující nebo požadované skupiny převzetí služeb při selhání databáze SQL nebo instance aktivní geografickou replikaci.  Každý server geograficky replikovaného vyžaduje samostatné služby key vault, které musí být umístěny společně se serverem ve stejné oblasti Azure. By měl být primární databáze nejsou dostupné kvůli výpadku v jedné oblasti a převzetí služeb při selhání se aktivuje, sekundární databáze je schopen převzít kontrolu nad využitím sekundární key vault. 
 
Pro databáze Geo-Replicated Azure SQL je vyžadována následující konfigurace služby Azure Key Vault:
- Jedna primární databáze pomocí služby key vault v oblasti a jedna sekundární databáze s využitím key vaultu, v oblasti. 
- Nejméně jedna sekundární je povinný, se podporují až čtyři sekundární databáze. 
- Sekundární databáze ze sekundárních replik (zřetězení) nejsou podporovány.

Následující části se přenášejí prostřednictvím kroky instalace a konfigurace podrobněji. 

### <a name="azure-key-vault-configuration-steps"></a>Postup konfigurace služby Azure Key Vault

- Nainstalujte [prostředí PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0) 
- Vytvoření dvou Azure Key Vault ve dvou různých oblastech pomocí [Powershellu povolit vlastnost "obnovitelného odstranění"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) v trezorech klíčů (Tato možnost není dostupná z portálu, službou AZURE ještě – ale vyžadují SQL).
- Jak Azure Key Vault se musí nacházet ve dvou oblastech, které jsou k dispozici ve stejné zeměpisné oblasti Azure v pořadí pro zálohování a obnovování klíčů pro práci.  Pokud budete potřebovat dva trezorům klíčů umístěné v různých zeměpisných oblastech k požadavkům SQL Geo-DR, postupujte [BYOK procesu](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) klíče nelze importovat z místního modulu hardwarového zabezpečení, který umožňuje.
- Vytvořte nový klíč v první služby key vault:  
  - Klíč RSA/RSA-HSA 2048 
  - Žádná data vypršení platnosti 
  - Klíč je povolená a má oprávnění k provedení get, zabalit klíč a rozbalit klíčové operace 
- Zálohování primárního klíče a obnovení klíče na druhý trezoru klíčů.  Zobrazit [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) a [Restore-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0). 

### <a name="azure-sql-database-configuration-steps"></a>Postup konfigurace Azure SQL Database

Následující kroky konfigurace se liší, ať už s nové nasazení SQL, nebo pokud funguje s již existujícím nasazením SQL Geo-DR.  Jsme první popisují kroky konfigurace pro nové nasazení a vysvětluje, jak přiřadit chrániče transparentní šifrování dat uložených ve službě Azure Key Vault do stávajícího nasazení, který se má vytvořit odkaz Geo-DR. 

Postup pro nové nasazení:
- Vytvoření dvou logických SQL serverů ve stejné dvou oblastech jako dříve vytvořeného trezorům klíčů. 
- Vyberte v podokně TDE logický server a pro každý logický SQL server:  
   - Vyberte službou AZURE ve stejné oblasti 
   - Vyberte klíč pro použití jako ochrana TDE – každý server bude používat místní kopii ochrana TDE. 
   - To na portálu se vytvoří [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) pro logický SQL server, který slouží k přiřazení logického serveru SQL Server oprávnění pro přístup k trezoru klíčů – neodstraňujte tuto identitu. Místo toho odebráním oprávnění ve službě Azure Key Vault pro logický SQL server, který slouží k přiřazení logického serveru SQL Server oprávnění pro přístup k trezoru klíčů můžete odvolat přístup.
- Vytvořte primární databáze. 
- Postupujte podle [aktivní geografickou replikaci pokyny](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) k dokončení tohoto scénáře, tento krok vytvoří sekundární databáze.

![Skupiny převzetí služeb při selhání a geo-dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

>[!NOTE]
>Je důležité zajistit, aby byly stejné chrániče transparentní šifrování dat v obou trezorů klíčů, než budete pokračovat k navázání geo propojení mezi databázemi.
>

Postup pro existující databázi SQL s Geo-DR nasazení:

Protože logických SQL serverů již existují a jsou už přiřazené primární a sekundární databáze, musíte v uvedeném pořadí provést postup pro konfiguraci služby Azure Key Vault: 
- Začněte s logického SQL serveru, který je hostitelem sekundární databázi: 
   - Přiřazení trezoru klíčů, které jsou umístěné ve stejné oblasti 
   - Přiřadit ochrana TDE 
- Teď přejděte k logickému serveru SQL, který je hostitelem primární databáze: 
   - Vyberte stejné ochrana TDE jako používané pro sekundární databáze
   
![Skupiny převzetí služeb při selhání a geo-dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>Při přiřazování služby key vault k serveru, je důležité začít pomocí sekundárního serveru.  V přiřazení druhý krok klíč trezoru na primární server a aktualizovat ochrana TDE Geo-DR odkaz bude nadále fungovat, protože v tomto okamžiku je k dispozici pro oba servery ochrana TDE replikované databáze používá.
>

Předtím, než povoluje transparentní šifrování dat se zákazníkem spravované klíče ve službě Azure Key Vault pro SQL Database Geo-DR scénář, je potřeba vytvořit a udržovat dva Azure Key Vault se shodným obsahem ve stejné oblasti, které se použijí pro geografickou replikaci databáze SQL.  "Shodným obsahem" konkrétně znamená, že oba trezorům klíčů musí obsahovat kopie stejného Protector(s) transparentní šifrování dat tak, aby oba servery mají přístup k použití ochrany pomocí transparentní šifrování dat ve všech databázích.  Do budoucna, je nutné zachovat obě trezorům klíčů synchronizaci, což znamená, že po obměna klíčů, musí obsahovat stejné kopie ochrany transparentní šifrování dat pomocí zachovat starší verze klíče používané pro soubory protokolů nebo zálohování, chrániče transparentní šifrování dat, musíte mít stejné vlastnosti klíče a klíč trezory, musíte mít stejná přístupová oprávnění pro server SQL.  
 
Postupujte podle kroků v [přehled aktivní geografické replikace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) testování a aktivovat převzetí služeb při selhání, který se má počítat v pravidelných intervalech potvrďte přístupová oprávnění pro SQL do obou trezorům klíčů pravděpodobně nebyla zachována. 


### <a name="backup-and-restore"></a>Zálohování a obnovení

Jakmile databáze je zašifrovaný pomocí transparentní šifrování dat pomocí klíče ze služby Key Vault, všechny generované zálohy je také šifrovaný pomocí stejné ochrana TDE.

Obnovení zálohy šifrované pomocí ochrana TDE ze služby Key Vault, ujistěte se, že materiál klíče je stále v původní trezoru v původní název klíče. Při změně ochrana TDE pro databázi, starší zálohy databáze **nejsou** aktualizované, aby používaly nejnovější ochrana TDE. Proto doporučujeme ponechat všechny starší verze ochrana TDE ve službě Key Vault, takže je možné obnovit zálohy databáze. 

Pokud klíč, který může být nutné pro obnovení zálohy je už v jeho původní trezoru klíčů, vrátí se následující chybová zpráva: "cílový server <Servername> nemá přístup ke všem identifikátory URI AKV vytvořit mezi < časové razítko #1 > a < časové razítko #2 >. Zkuste prosím operaci po obnovení všechny identifikátory URI AKV."

Chcete-li tento problém zmírnit, spusťte [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) rutina vrátí seznam klíčů z trezoru klíčů, které byly přidány na server (pokud nebyly odstraněny uživatelem). K zajištění, že je možné obnovit všechny zálohy, ujistěte se, že cílový server pro zálohování má přístup ke všem těmto klíčům.

   ```powershell
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
Další informace o obnovení záloh SQL Database najdete v tématu [obnovit databázi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups). Další informace o obnovení zálohování pro SQL Data Warehouse, najdete v článku [obnovení Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-overview).


Pečlivě zvážit u zálohovat soubory protokolu: zálohování protokolu soubory zůstávají šifrovaná s původní šifrování TDE, i když jsme si ukázali ochrana TDE a databáze je nyní pomocí nové ochrana TDE.  V době obnovení bude potřeba oba klíče k obnovení databáze.  Používáte-li soubor protokolu je ochrana TDE uložené ve službě Azure Key Vault, tento klíč budete potřebovat v době obnovení, i v případě, že se databáze změnila používat transparentní šifrování dat spravovaným službou do té doby.


