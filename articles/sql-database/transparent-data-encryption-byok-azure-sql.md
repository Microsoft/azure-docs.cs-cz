---
title: Transparentní šifrování dat spravované zákazníkem (TDE)
description: Přineste si vlastní klíč (BYOK) podporu pro transparentní šifrování dat (TDE) s Azure Key Vault pro SQL Database a Azure Synapse. TDE s byok přehled, výhody, jak to funguje, úvahy a doporučení.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528724"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Transparentní šifrování dat Azure SQL s klíčem spravovaným zákazníkem

Azure SQL [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) s klíčem spravovaným zákazníkem umožňuje bring your own key (BYOK) scénář pro ochranu dat v klidovém stavu a umožňuje organizacím implementovat oddělení povinností při správě klíčů a dat. Díky transparentnímu šifrování dat spravovanému zákazníkem je zákazník zodpovědný za správu životního cyklu klíče (vytváření klíčů, nahrávání, střídání, mazání), oprávnění k používání klíčů a auditování operací na klíčích.

V tomto scénáři klíč používaný pro šifrování šifrovacího klíče databáze (DEK), nazvaný Ochrana TDE, je zákazníkem spravovaný asymetrický klíč uložený v [trezoru Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)vlastněném zákazníkem a spravovaným zákazníkem , cloudovém systému pro správu externích klíčů. Trezor klíčů je vysoce dostupné a škálovatelné zabezpečené úložiště pro kryptografické klíče RSA, volitelně podpořené moduly zabezpečení hardwarového zabezpečení (HSM) standardizovaného hardwaru FIPS 140-2 Level 2. Neumožňuje přímý přístup k uloženému klíči, ale poskytuje služby šifrování/dešifrování pomocí klíče pro autorizované entity. Klíč lze vygenerovat trezorem klíčů, importovat nebo [přenést do trezoru klíčů ze zařízení s ním na předprodejním připojením](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)k souborům .

Pro Azure SQL Database a Azure Synapse je ochrana TDE nastavena na úrovni logického serveru a je zděděna všemi šifrovanými databázemi přidruženými k tomuto serveru. Pro Azure SQL spravované instance ochrany TDE je nastavena na úrovni instance a je zděděn všechny šifrované databáze na této instanci. Termín *server* odkazuje na logický server databáze SQL a spravovanou instanci v celém tomto dokumentu, pokud není uvedeno jinak.

> [!IMPORTANT]
> Pro ty, kteří používají služby spravované TDE, kteří by chtěli začít používat tde spravované zákazníkem, data zůstávají šifrována během procesu přepnutí a nedochází k prostojům ani opětovnému šifrování databázových souborů. Přepnutí z klíče spravovaného službou na klíč spravovaný zákazníkem vyžaduje pouze opětovné šifrování sady DEK, což je rychlá a online operace.

## <a name="benefits-of-the-customer-managed-tde"></a>Výhody TDE řízeného zákazníkem

TDE spravovaná zákazníkem poskytuje zákazníkovi následující výhody:

- úplná a podrobná kontrola nad používáním a správou ochrany TDE;

- Průhlednost používání chrániče TDE;

- Schopnost realizovat oddělení povinností při správě klíčů a dat v rámci organizace;

- Správce trezoru klíčů může odvolat přístupová oprávnění klíče, aby byla šifrovaná databáze nepřístupná.

- Centrální správa klíčů v AKV;

- Větší důvěryhodnost od vašich koncových zákazníků, protože AKV je navržen tak, že Společnost Microsoft nemůže vidět ani extrahovat šifrovací klíče;

## <a name="how-customer-managed-tde-works"></a>Jak funguje TDE spravovaná zákazníkem

![Nastavení a fungování TDE spravovaného zákazníkem](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Aby server mohl používat ochranu TDE uloženou v AKV pro šifrování sady DEK, musí správce trezoru klíčů udělit serveru následující přístupová práva pomocí jeho jedinečné identity AAD:

- **get** - pro načtení veřejné části a vlastností klíče v trezoru klíčů

- **wrapKey** - aby bylo možné chránit (šifrovat) DEK

- **unwrapKey** - aby bylo možné odkreslovat (dešifrovat) DEK

Správce trezoru klíčů může také [povolit protokolování událostí auditu trezoru klíčů](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault), takže je lze auditovat později.

Pokud je server nakonfigurován tak, aby používal ochranu TDE z AKV, odešle dek každé databáze s podporou TDE do trezoru klíčů pro šifrování. Trezor klíčů vrátí šifrovaný soubor DEK, který je pak uložen v databázi uživatelů.

V případě potřeby server odešle chráněný soubor DEK do trezoru klíčů k dešifrování.

Auditoři můžou pomocí Azure Monitoru zkontrolovat protokoly auditu událostí trezoru klíčů, pokud je protokolování povoleno.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Požadavky na konfiguraci TDE spravované zákazníkem

### <a name="requirements-for-configuring-akv"></a>Požadavky na konfiguraci AKV

- Trezor klíčů a instance SQL Database/managed musí patřit stejnému tenantovi služby Azure Active Directory. Meziklientské trezory klíčů a interakce serveru nejsou podporovány. Chcete-li přesunout prostředky později, TDE s AKV bude muset být překonfigurován. Další informace o [přesouvání zdrojů](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- [Funkce obnovitelného odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musí být povolena v trezoru klíčů, aby došlo k odstranění náhodného klíče (nebo trezoru klíčů) před ztrátou dat. Zdroje s modloslužebnou jsou uchovávány po dobu 90 dnů, pokud je zákazník v mezidobí neobnoví nebo neodstraní. Akce *obnovení* a *vymazání* mají vlastní oprávnění přidružená k zásadám přístupu k trezoru klíčů. Funkce obnovitelného odstranění je ve výchozím nastavení vypnutá a lze ji povolit prostřednictvím [prostředí PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) nebo [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Nelze povolit prostřednictvím portálu Azure.  

- Udělte serveru SQL Database nebo spravované instanci přístup k trezoru klíčů (get, wrapKey, unwrapKey) pomocí jeho identity služby Azure Active Directory. Při použití portálu Azure se identita Azure AD automaticky vytvoří. Při použití Prostředí PowerShell nebo CLI musí být identita Azure AD explicitně vytvořena a dokončení by mělo být ověřeno. Podrobné podrobné pokyny při použití prostředí PowerShell najdete v [tématu Konfigurace TDE pomocí rozhraní BYOK](transparent-data-encryption-byok-azure-sql-configure.md) a [Konfigurace tde pomocí příkazu BYOK pro spravovanou instanci.](https://aka.ms/sqlmibyoktdepowershell)

- Při použití brány firewall s AKV je nutné povolit možnost *Povolit, aby důvěryhodné služby společnosti Microsoft obcély bránu firewall*.

### <a name="requirements-for-configuring-tde-protector"></a>Požadavky na konfiguraci ochrany TDE

- Ochrana TDE může být pouze asymetrická, RSA 2048 nebo RSA HSM 2048 klíč.

- Datum aktivace klíče (pokud je nastaveno) musí být datum a čas v minulosti. Datum vypršení platnosti (pokud je nastaveno) musí být budoucí datum a čas.

- Klíč musí být ve stavu *Povoleno.*

- Pokud importujete existující klíč do trezoru klíčů, nezapomeňte jej zadat v podporovaných formátech souborů (.pfx, .byok nebo .backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Doporučení při konfiguraci TDE spravované zákazníkem

### <a name="recommendations-when-configuring-akv"></a>Doporučení při konfiguraci AKV

- Přidružte maximálně 500 databází pro obecné účely nebo 200 důležitých podnikových databází celkem pomocí trezoru klíčů v jednom předplatném, abyste zajistili vysokou dostupnost při přístupu serveru k ochraně TDE v trezoru klíčů. Tyto údaje jsou založeny na zkušenostech a jsou zdokumentovány v [limitech služeb trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits). Záměrem je zabránit problémům po převzetí služeb při selhání serveru, protože se aktivuje tolik klíčových operací proti trezoru, jak jsou databáze v tomto serveru.

- Nastavte zámek prostředků v trezoru klíčů, abyste mohli řídit, kdo může odstranit tento důležitý prostředek a zabránit náhodnému nebo neoprávněnému odstranění. Další informace o [uzamčení prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Povolit auditování a vytváření sestav na všech šifrovacích klíčích: Trezor klíčů poskytuje protokoly, které lze snadno vložit do dalších nástrojů pro správu informací o zabezpečení a událostí. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) je jedním z příkladů služby, která je již integrována.

- Propojte každý server se dvěma trezory klíčů, které se nacházejí v různých oblastech a mají stejný materiál klíče, abyste zajistili vysokou dostupnost šifrovaných databází. Označte pouze klíč z trezoru klíčů ve stejné oblasti jako ochrana TDE. Systém se automaticky přepne do trezoru klíčů ve vzdálené oblasti, pokud dojde k výpadku ovlivňujícímu trezor klíčů ve stejné oblasti.

### <a name="recommendations-when-configuring-tde-protector"></a>Doporučení při konfiguraci ochrany TDE
- Uchovávejte kopii ochrany TDE na bezpečném místě nebo ji uložte do úschovy.

- Pokud je klíč generován v trezoru klíčů, vytvořte zálohu klíčů před prvním použitím klíče v AKV. Zálohování lze obnovit pouze do trezoru klíčů Azure. Další informace o příkazu [Backup-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Vytvořte novou zálohu vždy, když jsou v klíči provedeny změny (např. klíčové atributy, značky, alokace).

- Při otáčení klíčů **ponechte předchozí verze** klíče v trezoru klíčů, aby bylo možné obnovit starší zálohy databáze. Při změně ochrany TDE pro databázi, staré zálohy databáze **nejsou aktualizovány** použít nejnovější ochrany TDE. V době obnovení každá záloha potřebuje ochranu TDE, se kterou byla v době vytvoření zašifrována. Otočení klíčů lze provést podle pokynů na [adrese Rotate the Transparent Data Encryption Protector using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Uchovávejte všechny dříve používané klíče v AKV i po přepnutí na klíče spravované službou. Zajišťuje zálohování databáze lze obnovit pomocí chráničů TDE uložených v AKV.  Ochrana TDE vytvořená pomocí azure trezoru klíčů musí být udržována, dokud nebudou vytvořeny všechny zbývající uložené zálohy pomocí klíčů spravovaných službou. Vytvořte obnovitelné záložní kopie těchto klíčů pomocí [programu Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Chcete-li odebrat potenciálně ohrožený klíč během incidentu zabezpečení bez rizika ztráty dat, postupujte podle kroků z [odebrat potenciálně ohrožený klíč](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Nepřístupný chránič TDE

Pokud je transparentní šifrování dat nakonfigurováno tak, aby používalo klíč spravovaný zákazníkem, je vyžadován nepřetržitý přístup k ochraně TDE, aby databáze zůstala online. Pokud server ztratí přístup k ochraně TDE spravované zákazníkem v AKV, do 10 minut databáze začne odepřít všechna připojení s odpovídající chybovou zprávou a změnit jeho stav na *Nepřístupný*. Jedinou akcí povolenou v databázi ve stavu Nepřístupný je jeho odstranění.

> [!NOTE]
> Pokud je databáze nepřístupná z důvodu přerušovaného výpadku sítě, není nutná žádná akce a databáze se automaticky vrátí do režimu online.

Po obnovení přístupu ke klíči vyžaduje přepnutí databáze zpět do režimu online další čas a kroky, které se mohou lišit v závislosti na době, která uplynula bez přístupu ke klíči a velikosti dat v databázi:

- Pokud je přístup ke klíči obnoven do 8 hodin, databáze se automaticky uzdraví během příští hodiny.

- Pokud je přístup ke klíči obnoven po více než 8 hodinách, automatické uzdravování není možné a vrácení databáze vyžaduje další kroky na portálu a může trvat značné množství času v závislosti na velikosti databáze. Jakmile je databáze opět online, dříve nakonfigurovaná nastavení na úrovni serveru, jako je konfigurace [skupiny s podporou převzetí služeb při selhání,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) historie obnovení v čase a značky budou **ztraceny**. Proto se doporučuje implementovat oznamovací systém, který vám umožní identifikovat a řešit základní problémy s přístupem klíče do 8 hodin.

### <a name="accidental-tde-protector-access-revocation"></a>Náhodné zrušení přístupu k ochraně TDE

Může se stát, že uživatel s dostatečnými přístupovými právy k trezoru klíčů omylem zakáže přístup k klíči serverem:

- vyvolání získání trezoru klíčů *,* *wrapKey*, *rozbalení* oprávnění klíče ze serveru

- odstranění klíče

- odstranění trezoru klíčů

- změna pravidel brány firewall trezoru klíčů

- odstranění spravované identity serveru ve službě Azure Active Directory

Další informace o [běžných příčinách databáze, které mají být nepřístupné](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitoring TDE řízeného zákazníkem

Chcete-li sledovat stav databáze a povolit výstrahy pro ztrátu přístupu k ochraně TDE, nakonfigurujte následující funkce Azure:
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Nepřístupná databáze, která ztratila přístup k ochraně TDE, se zobrazí jako "Nedostupná" po odepření prvního připojení k databázi.
- [Protokol aktivit při](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) selhání přístupu k ochraně TDE v trezoru klíčů spravovaném zákazníkem jsou do protokolu aktivit přidány položky.  Vytváření výstrah pro tyto události vám umožní obnovit přístup co nejdříve.
- [Skupiny akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) mohou být definovány tak, aby vám posílaly oznámení a upozornění na základě vašich preferencí, například E-mail/SMS/Push/Voice, Logická aplikace, Webhook, ITSM nebo Automatizační runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Zálohování a obnovení databáze pomocí TDE spravovaného zákazníkem

Jakmile je databáze zašifrována pomocí TDE pomocí klíče z trezoru klíčů, všechny nově generované zálohy jsou také šifrovány pomocí stejné ochrany TDE. Při změně ochrany TDE, staré zálohy databáze **nejsou aktualizovány** použít nejnovější ochrany TDE.

Chcete-li obnovit zálohu zašifrovanou pomocí ochrany TDE z trezoru klíčů, ujistěte se, že materiál klíče je k dispozici pro cílový server. Proto doporučujeme zachovat všechny staré verze ochrany TDE v trezoru klíčů, takže zálohy databáze lze obnovit.

> [!IMPORTANT]
> V každém okamžiku nemůže být více než jeden Chránič TDE nastavit pro server. Je to klíč označený "Make klíč výchozí ochrana TDE" v okně portálu Azure. Více dalších klíčů však lze propojit se serverem bez jejich označení jako ochrana TDE. Tyto klíče se nepoužívají k ochraně DEK, ale mohou být použity při obnovení ze zálohy, pokud je záložní soubor zašifrován klíčem s odpovídajícím kryptografickým otiskem.

Pokud klíč, který je potřeba pro obnovení zálohy již není k dispozici pro cílový server, následující `<Servername>` chybová zpráva je vrácena na \<obnovení zkuste: \<"Cílový server nemá přístup ke všem AKV UR vytvořené mezi časové razítko #1> a časové razítko #2>. Opakujte operaci po obnovení všech identifikátorů URI AKV."

Chcete-li ji zmírnit, spusťte rutinu [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) pro cílový logický server databáze SQL nebo [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) pro cílovou spravovanou instanci, abyste vrátili seznam dostupných klíčů a identifikovali chybějící. Chcete-li zajistit obnovení všech záloh, ujistěte se, že cílový server pro obnovení má přístup ke všem potřebným klíčům. Tyto klíče nemusí být označeny jako ochrana TDE.

Další informace o obnovení zálohování pro databázi SQL najdete [v tématu Obnovení databáze Azure SQL](sql-database-recovery-using-backups.md). Další informace o obnovení zálohování pro fond SQL, naleznete [v tématu Obnovení fondu SQL](../synapse-analytics/sql-data-warehouse/backup-and-restore.md). Nativní zálohování/obnovení serveru SQL Server se spravovanou instancí naleznete v [tématu Úvodní příručka: Obnovení databáze do spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

Další pozornost pro soubory protokolu: Zálohované soubory protokolu zůstávají zašifrovány pomocí původní ochrany TDE, i když byla otočena a databáze nyní používá novou ochranu TDE.  V době obnovení budou k obnovení databáze zapotřebí oba klíče.  Pokud soubor protokolu používá ochranu TDE uloženou v úložišti klíčů Azure, bude tento klíč potřeba v době obnovení, i když byla databáze mezitím změněna tak, aby používala tde spravované službou.

## <a name="high-availability-with-customer-managed-tde"></a>Vysoká dostupnost s TDE spravovaným zákazníkem

I v případech, kdy neexistuje žádná nakonfigurovaná geografická redundance pro server, je důrazně doporučeno nakonfigurovat server tak, aby používal dva různé trezory klíčů ve dvou různých oblastech se stejným materiálem klíče. To lze provést vytvořením ochrany TDE pomocí trezoru primárního klíče, který se nachází ve stejné oblasti jako server, a klonováním klíče do trezoru klíčů v jiné oblasti Azure, takže server má přístup k druhému trezoru klíčů, pokud by byl trezor primárního klíče dojít k výpadku, když je databáze v provozu.

Pomocí rutiny Backup-AzKeyVaultKey načtěte klíč v šifrovaném formátu z trezoru primárního klíče a potom použijte rutinu Obnovení azKeyVaultKey a zadejte trezor klíčů v druhé oblasti pro klonování klíče. Případně můžete pomocí portálu Azure zálohovat a obnovovat klíč. Klíč v trezoru sekundárníklíč v se jiné oblasti by neměla být označena jako ochrana TDE a není ani povoleno.

 Pokud dojde k výpadku ovlivňujícímu trezor primárního klíče a pouze potom se systém automaticky přepne na druhý propojený klíč se stejným kryptografickým otiskem v trezoru sekundárních klíčů, pokud existuje. Všimněte si však, že přepínač se nestane, pokud TDE ochrana je nepřístupná z důvodu odvolaných přístupových práv, nebo protože klíč nebo trezor klíčů je odstraněn, protože to může znamenat, že zákazník záměrně chtěl omezit server v přístupu ke klíči.

![Jednoserver HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR a zákazníkem spravované TDE

Ve scénářích [aktivní geografické replikace](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) i [skupin s podporou převzetí služeb při selhání](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) vyžaduje každý server samostatný trezor klíčů, který musí být umístěn společně se serverem ve stejné oblasti Azure. Zákazník je zodpovědný za udržování materiálu klíče v trezorech klíčů konzistentní, takže geograficky sekundární je synchronizován a může převzít pomocí stejného klíče z místního trezoru klíčů, pokud primární stane nepřístupné z důvodu výpadku v oblasti a převzetí služeb při selhání se aktivuje . Lze nakonfigurovat až čtyři sekundární moduly a řetězení (sekundární sekundární prostředí) není podporováno.

Chcete-li se vyhnout problémům při vytváření nebo během geografické replikace z důvodu neúplného materiálu klíče, je důležité dodržovat tato pravidla při konfiguraci TDE spravované zákazníkem:

- Všechny trezory klíčů musí mít stejné vlastnosti a stejná přístupová práva pro příslušné servery.

- Všechny trezory klíčů musí obsahovat stejný materiál klíče. Vztahuje se nejen na aktuální ochranu TDE, ale na všechny předchozí chrániče TDE, které mohou být použity v záložních souborech.

- Počáteční nastavení a otočení ochrany TDE musí být provedeno nejprve na sekundární maješku a potom na primární.

![Skupiny převzetí služeb při selhání a geo-dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Chcete-li otestovat převzetí služeb při selhání, postupujte podle pokynů v [aktivní masce geografické replikace](sql-database-geo-replication-overview.md). Mělo by být provedeno v pravidelných intervalech potvrdit přístupová oprávnění pro SQL do obou trezorů klíčů byly zachovány.

## <a name="next-steps"></a>Další kroky

Můžete také zkontrolovat následující ukázkové skripty prostředí PowerShell pro běžné operace s TDE spravovanou zákazníkem:

- [Otočení ochrany pro šifrování transparentních dat pro databázi SQL pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Odebrání ochrany pro transparentní šifrování dat (TDE) pro databázi SQL pomocí prostředí PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Správa transparentního šifrování dat ve spravované instanci pomocí vlastního klíče pomocí PowerShellu](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
