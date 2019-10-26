---
title: TDE-Azure Key Vault Integration nebo Bring Your Own Key (BYOK) – Azure SQL Database | Microsoft Docs
description: Podpora Bring Your Own Key (BYOK) pro transparentní šifrování dat (TDE) s Azure Key Vault pro SQL Database a datový sklad. TDE s BYOK přehledem, výhodami, jak funguje, požadavky a doporučení.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 07/18/2019
ms.openlocfilehash: 35e768e15aae13376ca6663ed5ca5109cb0a159b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893537"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL transparentní šifrování dat s klíči spravovanými zákazníkem v Azure Key Vault: Bring Your Own Key podpora

[Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) s Azure Key Vault Integration Key umožňuje šifrování šifrovacího klíče databáze (klíč DEK) pomocí asymetrického klíče spravovaného zákazníkem, který se nazývá TDE Protector. To se také obecně označuje jako podpora Bring Your Own Key (BYOK) pro transparentní šifrování dat.  Ve scénáři BYOK se ochrana TDE ukládá do cloudového [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)ve vlastnictví a spravovaném zákazníkem, což je cloudový externí systém správy klíčů Azure. Ochranu TDE můžete [vygenerovat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) pomocí trezoru klíčů nebo [přenést](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) do trezoru klíčů z Prem zařízení HSM. TDE klíč DEK, který je uložen na spouštěcí stránce databáze, je zašifrován a dešifrován modulem ochrany TDE uloženým v Azure Key Vault, který nikdy neopouští.  SQL Database musí mít udělená oprávnění trezoru klíčů vlastněné zákazníkem k dešifrování a šifrování klíč dek. Pokud jsou oprávnění logického serveru SQL k trezoru klíčů odvolána, databáze nebude přístupná, připojení budou odepřena a všechna data budou zašifrována. Pro Azure SQL Database ochrana TDE je nastavená na úrovni logického SQL serveru a děděna všemi databázemi přidruženými k tomuto serveru. U [spravované instance Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)je ochrana TDE nastavena na úrovni instance a zděděna všemi *šifrovanými* databázemi v této instanci. Pojem *Server* v celém tomto dokumentu odkazuje na server i na instanci, pokud není uvedeno jinak.

> [!NOTE]
> Transparentní šifrování dat s integrací Azure Key Vault (Bring Your Own Key) pro Azure SQL Database Managed instance je ve verzi Preview.


Pomocí TDE s integrací Azure Key Vault můžou uživatelé řídit úlohy správy klíčů, včetně střídání klíčů, oprávnění trezoru klíčů, záloh klíčů a povolit auditování nebo vytváření sestav pro všechny TDE ochrany pomocí funkcí Azure Key Vault. Key Vault poskytuje správu pomocí centrálních klíčů, využívá důkladně monitorované moduly hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které pomůžou splnit dodržování zásad zabezpečení.  

TDE s integrací Azure Key Vault přináší následující výhody:

- Zvýšení transparentnosti a podrobné řízení s možností samoobslužné správy ochrany TDE
- Možnost kdykoli odvolat oprávnění k vykreslování databáze jako nepřístupné
- Centrální správa TDE ochran (spolu s dalšími klíči a tajnými kódy používanými v jiných službách Azure) jejich hostováním v Key Vault
- Oddělení povinností správy klíčů a dat v rámci organizace za účelem podpory oddělení povinností
- Větší důvěra od vlastních klientů, protože Key Vault navržený tak, aby Microsoft neviděli ani neextrahuje žádné šifrovací klíče.
- Podpora pro rotaci klíčů

> [!IMPORTANT]
> Pro ty, kteří používají TDE spravované službou, kteří chtějí začít používat Key Vault, TDE zůstane během procesu přepínání do ochrany TDE v Key Vault zapnuté. Nedošlo k výpadku ani opakovanému šifrování souborů databáze. Přepnutí z klíče spravovaného službou na klíč Key Vault vyžaduje jenom opětovné šifrování šifrovacího klíče databáze (klíč DEK), což je rychlá a online operace.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Jak funguje TDE s podporou Integrace Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

![Ověřování serveru do Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Když je TDE nejdřív nakonfigurovaný tak, aby používal ochranu TDE z Key Vault, server pošle klíč DEK každé databázi s povoleným TDE, aby Key Vault pro požadavek na zabalicí klíč. Key Vault vrátí šifrovaný šifrovací klíč databáze, který je uložený v uživatelské databázi.  

> [!IMPORTANT]
> Je důležité si uvědomit, že **Jakmile se ochrana TDE ukládá v Azure Key Vault, nikdy Azure Key Vault neopouští**. Server může posílat klíčové požadavky na operace jenom na klíč TDE ochrany v rámci Key Vault a **nikdy nepřistupuje ke službě TDE Protector ani do mezipaměti**. Správce Key Vault má právo kdykoli odvolat Key Vault oprávnění serveru. v takovém případě jsou odepřena všechna připojení k databázi.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Pokyny ke konfiguraci TDE s využitím Azure Key Vault

### <a name="general-guidelines"></a>Obecné pokyny

- Zajistěte, aby se Azure Key Vault a Azure SQL Database/spravovaná instance načetly ve stejném tenantovi.  Trezor klíčů mezi klienty a interakce serveru nejsou **podporovány**.
- Pokud plánujete přesun tenanta, bude nutné překonfigurovat TDE s integrace, přečtěte si další informace o [přesouvání prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Při konfiguraci TDE s Azure Key Vault je důležité zvážit zatížení, které je umístěno v trezoru klíčů, opakovanými operacemi zalamování nebo rozbalení. Například vzhledem k tomu, že všechny databáze přidružené k serveru SQL Database používají stejné ochrany TDE, převzetí služeb při selhání tohoto serveru se aktivuje jako mnoho klíčových operací s trezorem, protože jsou databáze na serveru. Na základě našeho prostředí a podokumentovaných [omezení služby trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)doporučujeme, abyste v jednom předplatném přidružujete minimálně 500 standardních/Pro obecné účely nebo 200 databází Premium/pro důležité obchodní informace s jedním Azure Key Vaultem, abyste zajistili nepřetržitou vysokou dostupnost dostupnost při přístupu k ochraně TDE v trezoru.
- Doporučené: Udržujte si kopii ochrany TDE místně.  K tomu je potřeba, aby zařízení HSM vytvořilo místně ochranu TDE a v úschově systém klíčů pro ukládání místní kopie ochrany TDE.  Přečtěte si, [jak přenést klíč z místního modulu hardwarového zabezpečení do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Pokyny pro konfiguraci Azure Key Vault

- Vytvoření trezoru klíčů s povolenou ochranou pomocí [obnovitelného odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) a vyprázdnění chrání před ztrátou dat v případě náhodného klíče nebo trezoru klíčů – odstranění. V trezoru klíčů musíte povolit vlastnost "obnovitelné odstranění" prostřednictvím rozhraní příkazového [řádku (CLI](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete) ) nebo [PowerShellu](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) (Tato možnost není k dispozici na portálu integrace, ale vyžaduje Azure SQL):  
  - Obnovitelné odstraněné prostředky se uchovávají po nastavené časové období, 90 dní, pokud se neobnoví nebo nevyprázdní.
  - Akce **obnovit** a **Odstranit** mají svá vlastní oprávnění přidružená do zásad přístupu trezoru klíčů.
- Nastavte zámek prostředků v trezoru klíčů, abyste mohli řídit, kdo může odstranit tento důležitý prostředek a pomáhat zabránit náhodnému nebo neoprávněnému odstranění.  [Další informace o zámkech prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Udělte serveru SQL Database přístup k trezoru klíčů pomocí jeho identity Azure Active Directory (Azure AD).  Při použití uživatelského rozhraní portálu se identita Azure AD automaticky vytvoří a na server se udělí přístupová oprávnění k trezoru klíčů.  Pomocí PowerShellu ke konfiguraci TDE s BYOK se musí vytvořit identita Azure AD a musí se ověřit její dokončení. Podrobné pokyny pro použití PowerShellu najdete v tématu [Konfigurace TDE s BYOK](transparent-data-encryption-byok-azure-sql-configure.md) a [Konfigurace TDE s BYOK pro spravovanou instanci](https://aka.ms/sqlmibyoktdepowershell) .

   > [!NOTE]
   > Pokud se identita Azure AD **omylem odstraní nebo jsou oprávnění serveru odvolána** pomocí zásad přístupu trezoru klíčů nebo neúmyslně přesunutím serveru do jiného tenanta, server ztratí přístup k trezoru klíčů a TDE šifrované databáze. nebude k dispozici a přihlášení bude odepřeno, dokud neobnovíte identitu a oprávnění logického serveru Azure AD.  

- Pokud používáte brány firewall a virtuální sítě s Azure Key Vault, je nutné, aby důvěryhodné služby společnosti Microsoft mohly obejít tuto bránu firewall. Vyberte Ano.

   > [!NOTE]
   > Pokud TDE šifrované databáze SQL ztratí přístup k trezoru klíčů, protože nemůžou bránu firewall obejít, databáze nebudou přístupné a přihlášení bude odepřeno, dokud nebudou obnovena oprávnění k obejití brány firewall.

- Povolit auditování a vytváření sestav na všech šifrovacích klíčích: Key Vault poskytuje protokoly, které se dají snadno vložit do dalších nástrojů pro správu a zabezpečení událostí (SIEM). [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) Operations Management Suite (OMS) je jedním z příkladů služby, která je už integrovaná.
- Abyste zajistili vysokou dostupnost šifrovaných databází, nakonfigurujte každý SQL Database Server se dvěma trezory klíčů Azure, které se nacházejí v různých oblastech.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Pokyny pro konfiguraci ochrany TDE Protector (asymetrického klíče)

- Vytvořte šifrovací klíč lokálně na místním zařízení HSM. Ujistěte se, že se jedná o asymetrický klíč RSA 2048 nebo RSA HSM 2048 Key, aby se storable v Azure Key Vault.
- V úschově klíč v systému Key v úschově.  
- Naimportujte soubor šifrovacího klíče (. pfx,. BYOK nebo. Backup) do Azure Key Vault.

   > [!NOTE]
   > Pro účely testování je možné vytvořit klíč s Azure Key Vault, ale tento klíč nemůže být uloží, protože privátní klíč nikdy nemůže opustit Trezor klíčů.  Vždy se zálohují a v úschově klíče používané k šifrování provozních dat, protože ztráta klíče (náhodné odstranění v trezoru klíčů, vypršení platnosti atd.) vede k trvalé ztrátě dat.

- Pokud použijete klíč s datem vypršení platnosti – před vypršením platnosti klíče zavoláte systém upozornění vypršení platnosti: **Jakmile klíč vyprší, zašifrované databáze ztratí přístup ke svým TDE ochraně a nebude přístupná** a všechna přihlášení budou zamítnutá. klíč byl otočen na nový klíč a vybrán jako nový klíč a jako výchozí ochrana TDE pro logický SQL Server.
- Ujistěte se, že je klíč povolený a má oprávnění k provádění operací *získání*, *zabalení klíče*a *rozbalení klíčů* .
- Před prvním použitím klíče v Azure Key Vault vytvořte zálohu klíče Azure Key Vault. Přečtěte si další informace o příkazu [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .
- Vytvořte novou zálohu, kdykoli provedete všechny změny v klíči (například přidáte seznamy ACL, přidejte značky, přidejte klíčové atributy).
- Při střídání klíčů **ponechte předchozí verze** klíče v trezoru klíčů, aby bylo možné obnovit starší zálohy databáze. Když se ochrana TDE u databáze změní, starší zálohy databáze **se neaktualizují** , aby používaly nejnovější ochranu TDE.  Každé zálohování potřebuje ochranu TDE, která se vytvořila v době obnovení. Rotace klíčů je možné provést podle pokynů v tématu [otočení transparentní šifrování dat ochrany pomocí PowerShellu](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Po změně zpátky na klíče spravované služby Udržujte všechny dříve použité klíče v Azure Key Vault.  Tím zajistíte, že zálohy databáze budou obnoveny pomocí TDE ochrany, které jsou uloženy v Azure Key Vault.  TDE ochrany vytvořené pomocí Azure Key Vault musí být zachovány, dokud nebudou všechny uložené zálohy vytvořeny pomocí klíčů spravovaných službou.  
- Pomocí [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)udělejte obnovitelné záložní kopie těchto klíčů.
- Pokud chcete odstranit potenciálně ohrožený klíč během incidentu zabezpečení bez rizika ztráty dat, postupujte podle kroků v části [Odebrání potenciálně ohroženého klíče](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>Pokyny pro monitorování TDE s konfigurací Azure Key Vault

Pokud logický SQL Server ztratí přístup k ochraně TDE spravovaného zákazníkem v Azure Key Vault, databáze odepře všechna připojení a v Azure Portal nebude přístupná.  Mezi nejběžnější příčiny patří:
- Trezor klíčů byl omylem odstraněn nebo za bránou firewall.
- Klíč trezoru klíčů byl omylem odstraněn, zakázán nebo vypršel.
- Instance logického SQL Server AppId omylem odstraněna
- Klíčová oprávnění pro identifikátor AppId logického SQL Server instance se odvolala.

 > [!NOTE]
 > Pokud se přístup k ochraně TDE spravovanému zákazníkovi obnoví do 48 hodin, bude databáze automaticky zacelená a bude online.  Pokud je databáze nepřístupná z důvodu přerušovaného výpadku sítě, není nutná žádná akce a databáze se vrátí zpět online automaticky.
  
- Další informace o řešení potíží s existujícími konfiguracemi najdete v tématu [řešení potíží s TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- Pokud chcete monitorovat stav databáze a povolit upozorňování na ztrátu přístupu TDE Protector, nakonfigurujte následující funkce Azure:
    - [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Nepřístupná databáze, která ztratila přístup k ochraně TDE, se po odepření prvního připojení k databázi zobrazí jako nedostupná.
    - [Protokol aktivit](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , když se přístup k ochraně TDE v trezoru klíčů spravovaný zákazníkem nezdařil, přidají se do protokolu aktivit položky.  Vytváření výstrah pro tyto události vám umožní co nejdříve obnovit přístup.
    - [Skupiny akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) můžou být definované tak, aby vám poslaly oznámení a výstrahy na základě vašich požadavků, třeba E-mail/SMS/Push/Voice, Logic Apps, Webhook, ITSM nebo Automation Runbook.
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>Vysoká dostupnost, geografická replikace a zálohování a obnovení

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

Postup konfigurace vysoké dostupnosti pomocí Azure Key Vault závisí na konfiguraci databáze a serveru SQL Database a tady jsou doporučené konfigurace pro dva různé případy.  Prvním případem je samostatná databáze nebo SQL Database Server bez nakonfigurované geografické redundance.  Druhý případ je databáze nebo SQL Database Server nakonfigurovaný se skupinami převzetí služeb při selhání nebo geografickou redundancí, kde musí být zajištěné, aby každá geograficky redundantní kopie měla místní Azure Key Vault v rámci skupiny převzetí služeb při selhání, aby se zajistila funkčnost geografického převzetí služeb při selhání.

Pokud v prvním případě požadujete vysokou dostupnost databáze a SQL Database serveru bez nakonfigurované geografické redundance, důrazně se doporučuje nakonfigurovat server tak, aby používal dva různé trezory klíčů ve dvou různých oblastech se stejným materiálem klíče. To je možné dosáhnout vytvořením ochrany TDE pomocí primárního Key Vault společně umístěného ve stejné oblasti jako SQL Database Server a klonování klíče do trezoru klíčů v jiné oblasti Azure, aby měl server přístup k druhému trezoru klíčů, aby měl primární Trezor klíčů je výpadek, zatímco je databáze spuštěná a spuštěná. Pomocí rutiny Backup-AzKeyVaultKey načtěte klíč v šifrovaném formátu z primárního trezoru klíčů a pak použijte rutinu Restore-AzKeyVaultKey a v druhé oblasti zadejte Trezor klíčů.

![HA s jedním serverem a bez geografického navýšení – Dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Postup konfigurace geografického-DR s Azure Key Vault

Aby se zachovala vysoká dostupnost TDE ochran pro šifrované databáze, je nutné nakonfigurovat redundantní trezory klíčů Azure na základě stávajících nebo SQL Database požadovaných skupin převzetí služeb při selhání nebo aktivních instancí geografické replikace.  Každý geograficky replikovaný Server vyžaduje samostatný Trezor klíčů, který musí být umístěn společně se serverem ve stejné oblasti Azure. Pokud primární databáze přestane být dostupná z důvodu výpadku v jedné oblasti a aktivuje se převzetí služeb při selhání, sekundární databáze může převzít sekundární Trezor klíčů.

Pro geograficky replikované databáze SQL Azure se vyžaduje následující konfigurace Azure Key Vault:

- Jedna primární databáze s trezorem klíčů v oblasti a jednou sekundární databází s trezorem klíčů v oblasti.
- Vyžaduje se aspoň jeden sekundární objekt, podporuje se až čtyři sekundární verze.
- Nepodporují se sekundární sekundární (řetězení).

V následující části najdete podrobnější informace o instalaci a konfiguraci.

### <a name="azure-key-vault-configuration-steps"></a>Postup konfigurace Azure Key Vault

- Nainstalovat [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Vytvořte dvě trezory klíčů Azure ve dvou různých oblastech pomocí [PowerShellu, abyste povolili vlastnost "obnovitelné odstranění"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) v trezorech klíčů (Tato možnost není k dispozici na portálu integrace, ale vyžaduje SQL).
- Trezory klíčů Azure musí být umístěny ve dvou oblastech, které jsou dostupné ve stejné geografické oblasti Azure, aby bylo možné zálohovat a obnovovat klíče.  Pokud potřebujete, aby se dva trezory klíčů nacházely v různých zeměpisných oblastech, aby splňovaly požadavky SQL geograficky-DR, postupujte podle [procesu BYOK](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) , který umožňuje import klíčů z místního modulu hardwarového zabezpečení (HSM).
- V prvním trezoru klíčů vytvořte nový klíč:  
  - Klíč RSA/RSA-HSM 2048
  - Žádná data vypršení platnosti
  - Klíč je povolený a má oprávnění k provádění operací získání, zabalení klíče a rozbalení klíčů.
- Zálohujte primární klíč a obnovte klíč do druhého trezoru klíčů.  Viz [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) a [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Postup konfigurace Azure SQL Database

Následující konfigurační postup se liší od použití nového nasazení SQL nebo při práci s již existujícím nasazením SQL Geo-DR.  Nejprve seřadíme konfigurační kroky pro nové nasazení a pak vysvětlete, jak přiřadit TDE ochrany, které jsou uložené v Azure Key Vault, do stávajícího nasazení, které již má vytvořen odkaz geograficky DR.

**Postup pro nové nasazení**:

- Vytvořte dva SQL Database servery ve stejných dvou oblastech jako dříve vytvořené trezory klíčů.
- Vyberte podokno TDE serveru SQL Database a pro každý SQL Database Server:  
  - Vyberte integrace ve stejné oblasti.
  - Vyberte klíč, který se použije jako ochrana TDE – každý server bude používat místní kopii ochrany TDE.
  - K tomu na portálu se vytvoří [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) pro SQL Database Server, který slouží k přiřazení oprávnění SQL Database serveru pro přístup k trezoru klíčů – neodstraňujte tuto identitu. Přístup můžete odvolat odebráním oprávnění v Azure Key Vault místo pro SQL Database Server, který se používá k přiřazení oprávnění SQL Database serveru pro přístup k trezoru klíčů.
- Vytvořte primární databázi.
- Postupujte podle pokynů [aktivní geografické replikace](sql-database-geo-replication-overview.md) , abyste mohli scénář dokončit. Tento krok vytvoří sekundární databázi.

![Skupiny převzetí služeb při selhání a geografická Dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Před tím, než budete pokračovat v vytváření geografického propojení mezi databázemi, je důležité zajistit, aby v obou trezorech klíčů byly stejné TDE ochrany.

**Postup pro stávající databázi SQL s nasazením geografického Dr**:

Vzhledem k tomu, že SQL Database servery již existují a primární a sekundární databáze jsou již přiřazeny, je nutné provést kroky pro konfiguraci Azure Key Vault v tomto pořadí:

- Začněte s SQL Database serverem, který je hostitelem sekundární databáze:
  - Přiřaďte Trezor klíčů umístěný ve stejné oblasti.
  - Přiřazení ochrany TDE
- Teď přejdete na server SQL Database, který hostuje primární databázi:
  - Vybrat stejné ochrany TDE jako používané pro sekundární databázi

![Skupiny převzetí služeb při selhání a geografická Dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> Při přiřazování trezoru klíčů k serveru je důležité začít s sekundárním serverem.  V druhém kroku přiřadíme k primárnímu serveru Trezor klíčů a aktualizujete ochranu TDE, protože v tomto okamžiku bude odkaz geograficky DR i nadále fungovat, protože v tomto okamžiku je k dispozici ochrana TDE, kterou replikovaná databáze používá, k dispozici pro oba servery.

Než povolíte TDE se spravovanými klíči zákazníka v Azure Key Vault pro scénář SQL Database geografického zotavení po havárii, je důležité vytvořit a udržovat dva trezory klíčů Azure se shodným obsahem ve stejných oblastech, které se použijí pro SQL Database geografickou replikaci.  "Identický obsah" konkrétně znamená, že obě trezory klíčů musí obsahovat kopie stejných TDE ochrany, aby oba servery měly přístup k TDE ochranám, které používají všechny databáze.  Než se dostanou, je nutné zachovat obě trezory klíčů, což znamená, že musí po otočení klíče obsahovat stejné kopie TDE ochrany, zachovat staré verze klíčů, které se používají pro soubory protokolu nebo zálohy. TDE ochrana musí zachovat stejné klíčové vlastnosti a klíč. trezory musí udržovat stejná přístupová oprávnění pro SQL.  

Postupujte podle kroků v článku [Přehled aktivní geografické replikace](sql-database-geo-replication-overview.md) a otestujte a aktivujte převzetí služeb při selhání, které by se mělo pravidelně provádět, aby bylo zachováno oprávnění k přístupu pro SQL k trezorům klíčů.

### <a name="backup-and-restore"></a>Backup a obnovení

Jakmile je databáze zašifrovaná pomocí TDE s použitím klíče z Key Vault, vygenerované zálohy se zašifrují také stejným TDE ochrany.

Pokud chcete obnovit zálohu zašifrovanou pomocí ochrany TDE Protector z Key Vault, ujistěte se, že klíčový materiál je stále v původním trezoru pod názvem původního klíče. Když se ochrana TDE u databáze změní, starší zálohy databáze **se** neaktualizují, aby používaly nejnovější ochranu TDE. Proto doporučujeme, abyste zachovali všechny staré verze ochrany TDE v Key Vault, aby bylo možné obnovit zálohy databáze.

Pokud klíč, který může být potřeba pro obnovení zálohy, už není v původním trezoru klíčů, vrátí se tato chybová zpráva: "cílový server `<Servername>` nemá přístup ke všem identifikátorům URI integrace vytvořeným v \<m časovém razítku #1 > a \<časového razítka #2 > . Opakujte prosím operaci po obnovení všech identifikátorů URI integrace. "

Pokud to chcete zmírnit, spusťte rutinu [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) , která vrátí seznam klíčů z Key Vault přidaných na server (Pokud je neodstranil uživatel). Chcete-li zajistit, aby bylo možné obnovit všechny zálohy, ujistěte se, že cílový server pro zálohování má přístup ke všem těmto klíčům.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Další informace o obnovení záloh pro SQL Database najdete v tématu [obnovení databáze SQL Azure](sql-database-recovery-using-backups.md). Další informace o obnovení záloh pro SQL Data Warehouse najdete v tématu [obnovení Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Další aspekty zálohovaných souborů protokolu: zálohované soubory protokolu zůstávají šifrované s původním TDE šifrováním, a to i v případě, že došlo k otočení ochrany TDE, a databáze teď používá novou ochranu TDE.  V době obnovení budou pro obnovení databáze nutné oba klíče.  Pokud soubor protokolu používá ochranu TDE, která je uložená v Azure Key Vault, bude tento klíč potřeba v době obnovení, a to i v případě, že se databáze změnila tak, aby mezitím používala TDE spravovanou službou.
