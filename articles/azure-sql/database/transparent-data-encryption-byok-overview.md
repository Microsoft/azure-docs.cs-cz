---
title: Transparentní šifrování dat spravované zákazníkem (TDE)
description: Podpora Bring Your Own Key (BYOK) pro transparentní šifrování dat (TDE) s Azure Key Vault pro SQL Database a Azure synapse Analytics. TDE s BYOK přehledem, výhodami, jak funguje, požadavky a doporučení.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 5cfd76d6b2f6bb9429a7605ac05adb23d87a80d3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790878"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Transparentní šifrování dat Azure SQL s využitím klíče spravovaného zákazníkem
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL [transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) s klíčem spravovaným zákazníkem povoluje scénář BRING Your Own Key (BYOK) pro ochranu dat v klidovém umístění a umožňuje organizacím implementovat oddělení úloh v rámci správy klíčů a dat. U transparentního šifrování dat spravovaného zákazníkem je zákazník zodpovědný za a úplnou kontrolu nad správou životního cyklu klíčů (vytváření klíčů, nahrávání, rotace, odstraňování), oprávnění použití klíčů a auditování operací s klíči.

V tomto scénáři je klíč, který se používá k šifrování šifrovacího klíče databáze (klíč DEK), označovaný jako TDE ochrana, asymetrický klíč spravovaný zákazníkem, který je uložený v cloudu a zákazníkovi spravovaném [Azure Key Vault (integrace)](../../key-vault/general/secure-your-key-vault.md), což je cloudový externí systém správy klíčů. Key Vault je vysoce dostupné a škálovatelné zabezpečené úložiště pro kryptografické klíče RSA, volitelně zajištěné moduly zabezpečení FIPS 140-2 Level 2 (HSM). Neumožňuje přímý přístup k uloženému klíči, ale poskytuje služby šifrování a dešifrování pomocí klíče u autorizovaných entit. Klíč může být vygenerován trezorem klíčů, importován nebo [přenesen do trezoru klíčů ze zařízení Prem HSM](../../key-vault/keys/hsm-protected-keys.md).

Pro Azure SQL Database a Azure synapse Analytics se ochrana TDE nastaví na úrovni serveru a děděna všemi šifrovanými databázemi přidruženými k tomuto serveru. V případě spravované instance Azure SQL je ochrana TDE nastavena na úrovni instance a zděděna všemi šifrovanými databázemi v této instanci. Pojem *Server* odkazuje na server v SQL Database a v Azure synapse a na spravovanou instanci v rámci SQL Managed instance v tomto dokumentu, pokud není uvedeno jinak.

> [!IMPORTANT]
> Pro ty, kteří používají TDE spravované službou, kteří chtějí začít používat TDE spravované zákazníkem, zůstanou data během přepínání přešifrovaná a nedochází k výpadkům a opětovnému šifrování souborů databáze. Přepnutí z klíče spravovaného službou na klíč spravovaný zákazníkem vyžaduje jenom opětovné šifrování klíč DEK, což je rychlá a online operace.

> [!NOTE]
> <a id="doubleencryption"></a> Pokud chcete zákazníkům Azure SQL poskytovat dvě úrovně šifrování neaktivních dat, zavádějí se do provozu šifrování infrastruktury (pomocí šifrovacího algoritmu AES-256) se spravovanými klíči platformy. To poskytuje dodatečnou vrstvu šifrování v klidovém formátu spolu s TDE s klíči spravovanými zákazníkem, který je už dostupný. Pro Azure SQL Database a spravovanou instanci budou při zapnutém šifrování infrastruktury zašifrované všechny databáze včetně hlavní databáze a dalších systémových databází. V tuto chvíli musí zákazníci požádat o přístup k této funkci. Pokud vás zajímá Tato možnost, obraťte se na AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Výhody TDE spravovaného zákazníkem

TDE spravovaný zákazníkem poskytuje zákazníkům tyto výhody:

- Plnou a podrobnou kontrolu nad využitím a správou ochrany TDE;

- Transparentnost využití ochrany TDE;

- Možnost implementovat oddělení povinností při správě klíčů a dat v rámci organizace;

- Správce Key Vault může odvolat přístupová oprávnění k klíčům k zajištění nedostupnosti šifrované databáze.

- Centrální Správa klíčů v integrace.

- Větší důvěra od koncových zákazníků, protože integrace je navržený tak, aby Microsoft nemohl zobrazit ani extrahovat šifrovací klíče;

## <a name="how-customer-managed-tde-works"></a>Způsob fungování TDE spravovaného zákazníkem

![Nastavení a fungování TDE spravovaného zákazníkem](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Aby mohl server využívat ochranu TDE, která je uložená v integrace pro šifrování klíč DEK, musí správce trezoru klíčů k serveru přidělit tato přístupová práva pomocí jedinečné identity Azure Active Directory (Azure AD):

- **Get** – pro načtení veřejné části a vlastností klíče v Key Vault

- **wrapKey** – aby bylo možné chránit (ŠIFROVAT) klíč DEK

- **unwrapKey** – aby bylo možné zrušit ochranu (dešifrovat) klíč DEK

Správce trezoru klíčů může taky [Povolit protokolování událostí auditu trezoru klíčů](../../azure-monitor/insights/key-vault-insights-overview.md), aby se mohly auditovat později.

Když je server nakonfigurovaný tak, aby používal ochranu TDE z integrace, server pošle klíč DEK každé databázi s povoleným TDEm do trezoru klíčů pro šifrování. Trezor klíčů vrátí šifrovaný klíč DEK, který je pak uložený v uživatelské databázi.

V případě potřeby pošle Server chráněnou klíč DEK do trezoru klíčů pro dešifrování.

Auditoři můžou pomocí Azure Monitor zkontrolovat protokoly AuditEvent trezoru klíčů, pokud je povolené protokolování.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Požadavky na konfiguraci TDE spravovaného zákazníkem

### <a name="requirements-for-configuring-akv"></a>Požadavky na konfiguraci integrace

- Trezor klíčů a SQL Database/spravovaná instance musí patřit ke stejnému Azure Active Directory tenanta. Trezor klíčů mezi klienty a interakce serveru nejsou podporovány. Pokud chcete přesunout prostředky, TDE s integrace se musí překonfigurovat. Přečtěte si další informace o [přesouvání prostředků](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

- V trezoru klíčů musí být povolená funkce [obnovitelného odstranění](../../key-vault/general/soft-delete-overview.md) . k ochraně před náhodným odstraněním klíče (nebo trezoru klíčů) dojde k odstranění. Obnovitelné odstraněné prostředky se uchovávají po dobu 90 dnů, pokud je zákazník neobnovil nebo nevymazal. Akce *obnovit* a *Odstranit* mají svá vlastní oprávnění přidružená do zásad přístupu trezoru klíčů. Funkce obnovitelného odstranění je ve výchozím nastavení vypnutá a je možné ji povolit prostřednictvím [PowerShellu](../../key-vault/general/soft-delete-powershell.md#enabling-soft-delete) nebo rozhraní příkazového [řádku](../../key-vault/general/soft-delete-cli.md#enabling-soft-delete). Nelze ji povolit prostřednictvím Azure Portal.  

- Udělte serveru nebo spravované instanci přístup k trezoru klíčů (Get, wrapKey, unwrapKey) pomocí jeho Azure Active Directory identity. Při použití Azure Portal se identita Azure AD automaticky vytvoří. Při použití PowerShellu nebo rozhraní příkazového řádku musí být identita Azure AD explicitně vytvořená a měla by se ověřit její dokončení. Podrobné pokyny při používání PowerShellu najdete v tématu [Konfigurace TDE s BYOK](transparent-data-encryption-byok-configure.md) a [Konfigurace TDE s BYOK pro spravovanou instanci SQL](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) .

- Pokud používáte bránu firewall s integrace, musíte povolit možnost povolit *důvěryhodným službám Microsoftu obejít bránu firewall* .

### <a name="requirements-for-configuring-tde-protector"></a>Požadavky na konfiguraci ochrany TDE

- Ochrana TDE může být jedině asymetrická, RSA nebo RSA HSM Key. Podporované délky klíčů jsou 2048 a 3072 bajtů.

- Datum aktivace klíče (Pokud je nastaveno) musí být datum a čas v minulosti. Datum vypršení platnosti (Pokud je nastaveno) musí být budoucí datum a čas.

- Klíč musí být v *povoleném* stavu.

- Pokud importujete existující klíč do trezoru klíčů, nezapomeňte ho zadat v podporovaných formátech souborů (. pfx,. BYOK nebo. Backup).

> [!NOTE]
> Azure SQL teď podporuje použití klíče RSA uloženého ve spravovaném HSM jako ochrany TDE. Tato funkce je ve **verzi Public Preview** . Azure Key Vault spravovaný modul HSM je plně spravovaná cloudová služba s vysokou dostupností, která vyhovuje standardům, která vám umožní chránit kryptografické klíče pro vaše cloudové aplikace, a to pomocí ověřené HSM úrovně 3 ve standardu FIPS 140-2. Přečtěte si další informace o [spravovaných HSM](../../key-vault/managed-hsm/index.yml).


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Doporučení při konfiguraci TDE spravovaného zákazníkem

### <a name="recommendations-when-configuring-akv"></a>Doporučení při konfiguraci integrace

- Přidružte k nejvyšší 500 Pro obecné účely nebo 200 databází Pro důležité obchodní informace v rámci jednoho předplatného jako Trezor klíčů, abyste zajistili vysokou dostupnost, když server přistupuje k ochraně TDE v trezoru klíčů. Tyto údaje vycházejí ze zkušeností a popsaných v [omezeních služby trezoru klíčů](../../key-vault/general/service-limits.md). Cílem je, aby se zabránilo problémům po převzetí služeb při selhání serveru, protože se aktivuje tolik operací s klíčem na trezoru, protože jsou databáze na tomto serveru.

- Nastavte zámek prostředků v trezoru klíčů, abyste mohli řídit, kdo může odstranit tento důležitý prostředek a zabránit náhodnému nebo neoprávněnému odstranění. Přečtěte si další informace o [zámkech prostředků](../../azure-resource-manager/management/lock-resources.md).

- Povolit auditování a vytváření sestav na všech šifrovacích klíčích: Trezor klíčů poskytuje protokoly, které se dají snadno vložit do dalších nástrojů pro správu informací a událostí zabezpečení. Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) je jedním z příkladů služby, která je už integrovaná.

- Propojte každý server se dvěma trezory klíčů, které jsou umístěné v různých oblastech, a podržte stejný klíčový materiál, abyste zajistili vysokou dostupnost šifrovaných databází. Označte jenom klíč z trezoru klíčů ve stejné oblasti jako ochranu TDE. Pokud dojde k výpadku ovlivněného trezoru klíčů ve stejné oblasti, systém se automaticky přepne do trezoru klíčů ve vzdálené oblasti.

### <a name="recommendations-when-configuring-tde-protector"></a>Doporučení při konfiguraci ochrany TDE

- Uchovávejte kopii ochrany TDE na bezpečném místě nebo ji v úschově do služby v úschově.

- Pokud se klíč vygeneruje v trezoru klíčů, před prvním použitím klíče v integrace vytvořte zálohu klíčů. Zálohování lze obnovit pouze do Azure Key Vault. Přečtěte si další informace o příkazu [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey) .

- Vytvořte novou zálohu při každé změně klíče (např. klíčové atributy, značky, seznamy ACL).

- Při střídání klíčů **ponechte předchozí verze** klíče v trezoru klíčů, aby bylo možné obnovit starší zálohy databáze. Když se ochrana TDE u databáze změní, starší zálohy databáze **se neaktualizují** , aby používaly nejnovější ochranu TDE. V době obnovení potřebuje každá záloha TDE ochranu, která byla zašifrována v době vytváření. Rotace klíčů je možné provést podle pokynů v tématu [otočení transparentní šifrování dat ochrany pomocí PowerShellu](transparent-data-encryption-byok-key-rotation.md).

- Všechny dříve použité klíče v integrace ponechte i po přepnutí na klíče spravované službou. Zajišťuje, aby zálohy databáze byly obnoveny pomocí TDE ochrany uložených v integrace.  TDE ochrany vytvořené pomocí Azure Key Vault musí být zachovány, dokud se všechny zbývající uložené zálohy nevytvořily pomocí klíčů spravovaných službou. Pomocí [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey)udělejte obnovitelné záložní kopie těchto klíčů.

- Chcete-li odebrat potenciálně ohrožený klíč během incidentu zabezpečení bez rizika ztráty dat, postupujte podle kroků v části [Odebrání potenciálně ohroženého klíče](transparent-data-encryption-byok-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Nedostupné ochrany TDE

Pokud je transparentní šifrování dat nakonfigurované tak, aby používalo klíč spravovaný zákazníkem, vyžaduje se nepřetržitý přístup k ochraně TDE, aby databáze zůstala online. Pokud server ztratí přístup k ochraně TDE spravovaného zákazníkem v integrace, během až 10 minut databáze spustí odepření všech připojení k příslušné chybové zprávě a změní její stav na *nepřístupný* . Jediná akce povolená u databáze v nepřístupovém stavu ji odstraňuje.

> [!NOTE]
> Pokud je databáze nepřístupná z důvodu přerušovaného výpadku sítě, není nutná žádná akce a databáze se vrátí zpět online automaticky.

Po obnovení přístupu k tomuto klíči bude zálohování databáze zpět online vyžadovat další čas a kroky, které se mohou lišit v závislosti na době uplynulé bez přístupu ke klíči a velikosti dat v databázi:

- Pokud se přístup k klíči obnoví do 8 hodin, bude databáze automaticky zacelená během příští hodiny.

- Pokud se přístup ke klíči obnoví za více než 8 hodin, automatická oprava není možná a vrácení databáze vyžaduje provedení dalších kroků na portálu a v závislosti na velikosti databáze může trvat poměrně dlouhou dobu. Jakmile je databáze znovu online, dříve konfigurovaná nastavení na úrovni serveru, jako je například konfigurace [skupiny převzetí služeb při selhání](auto-failover-group-overview.md) , historie obnovení bodu v čase a značky, **bude ztracena** . Proto se doporučuje implementovat systém oznámení, který vám umožní identifikovat a vyřešit problémy s přístupem k základnímu klíči během 8 hodin.

Níže je uveden seznam dalších kroků požadovaných na portálu, které nepřístupné databáze vrátí zpět do online režimu.

![Nepřístupná databáze TDE BYOK](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Odvolání přístupu k nechtěně TDE ochraně

Může dojít k tomu, že někdo s dostatečným oprávněním pro přístup k trezoru klíčů omylem zakáže přístup k tomuto klíči pomocí:

- odvolávání oprávnění *Get* , *wrapKey* a *unwrapKey* trezoru klíčů ze serveru

- klíč se odstraňuje.

- Odstraňuje se Trezor klíčů.

- mění se pravidla brány firewall trezoru klíčů.

- Odstraňuje se spravovaná identita serveru v Azure Active Directory.

Přečtěte si další informace o [běžných příčinách, proč databáze nebude přístupná](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitorování TDE spravovaného zákazníkem

Pokud chcete monitorovat stav databáze a povolit upozorňování na ztrátu přístupu TDE Protector, nakonfigurujte následující funkce Azure:

- [Azure Resource Health](../../service-health/resource-health-overview.md). Nepřístupná databáze, která ztratila přístup k ochraně TDE, se po odepření prvního připojení k databázi zobrazí jako nedostupná.
- [Protokol aktivit](../../service-health/alerts-activity-log-service-notifications-portal.md) , když se přístup k ochraně TDE v trezoru klíčů spravovaný zákazníkem nezdařil, přidají se do protokolu aktivit položky.  Vytváření výstrah pro tyto události vám umožní co nejdříve obnovit přístup.
- [Skupiny akcí](../../azure-monitor/platform/action-groups.md) můžou být definované tak, aby vám poslaly oznámení a výstrahy na základě vašich požadavků, třeba E-mail/SMS/Push/Voice, Logic Apps, Webhook, ITSM nebo Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Zálohování a obnovení databáze se TDE spravovanými zákazníky

Jakmile je databáze zašifrovaná pomocí TDE s použitím klíče z Key Vault, všechny nově vygenerované zálohy se taky šifrují pomocí stejné ochrany TDE. Po změně ochrany TDE se starší zálohy databáze **neaktualizují** , aby používaly nejnovější ochranu TDE.

Pokud chcete obnovit zálohu zašifrovanou pomocí ochrany TDE ochrany před Key Vault, ujistěte se, že je k dispozici klíčový materiál pro cílový server. Proto doporučujeme, abyste zachovali všechny staré verze ochrany TDE v trezoru klíčů, aby bylo možné obnovit zálohy databáze.

> [!IMPORTANT]
> V každém okamžiku může být pro server nedostupná více než jedna sada ochrany TDE. Je to klíč označený jako "nastavit klíč jako výchozí TDE ochrana" v okně Azure Portal. Na server ale můžete propojit víc dalších klíčů, aniž byste je museli označit jako ochranu pomocí TDE. Tyto klíče se nepoužívají k ochraně klíč DEK, ale během obnovování se dají použít při obnovení ze zálohy, pokud je záložní soubor zašifrovaný s klíčem s odpovídajícím kryptografickým otiskem.

Pokud klíč potřebný k obnovení zálohy již není cílovým serverem k dispozici, vrátí se při obnovení následující chybová zpráva: "cílový server nemá `<Servername>` přístup ke všem identifikátorům URI integrace vytvořeným mezi \<Timestamp #1> a \<Timestamp #2> . Opakujte prosím operaci po obnovení všech identifikátorů URI integrace. "

Pokud ho chcete zmírnit, spusťte rutinu [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) pro cílový server nebo rutinu [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) pro cílovou spravovanou instanci, která vrátí seznam dostupných klíčů a určí chybějící. Aby bylo možné obnovit všechny zálohy, ujistěte se, že cílový server pro obnovení má přístup ke všem potřebným klíčům. Tyto klíče není nutné označit jako ochranu pomocí TDE.

Další informace o obnovení záloh pro SQL Database najdete v tématu [obnovení databáze v SQL Database](recovery-using-backups.md). Další informace o obnovení zálohy pro fond SQL najdete v tématu [obnovení fondu SQL](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md). Nativní zálohování a obnovování SQL Server pomocí spravované instance SQL najdete v tématu [rychlý Start: obnovení databáze do spravované instance SQL.](../managed-instance/restore-sample-database-quickstart.md)

Další aspekty pro soubory protokolu: zálohované soubory protokolu zůstávají šifrované s původní ochranu TDE, i když byla otočená a databáze teď používá nové ochrany TDE.  V době obnovení budou pro obnovení databáze nutné oba klíče.  Pokud soubor protokolu používá ochranu TDE, která je uložená v Azure Key Vault, bude tento klíč potřeba v době obnovení, a to i v případě, že se databáze změnila tak, aby mezitím používala TDE spravovanou službou.

## <a name="high-availability-with-customer-managed-tde"></a>Vysoká dostupnost pomocí TDE spravovaných zákazníky

I v případě, že není k dispozici žádná nakonfigurovaná geografická redundance pro server, důrazně doporučujeme nakonfigurovat server tak, aby používal dvě různé trezory klíčů ve dvou různých oblastech se stejným materiálem klíče. Dá se dosáhnout vytvořením ochrany TDE pomocí primárního trezoru klíčů umístěného ve stejné oblasti jako server a klonování klíče do trezoru klíčů v jiné oblasti Azure, aby měl server přístup k druhému trezoru klíčů, aby při práci s databází probíhal výpadek primárního trezoru klíčů.

Pomocí rutiny Backup-AzKeyVaultKey načtěte klíč v šifrovaném formátu z primárního trezoru klíčů a potom pomocí rutiny Restore-AzKeyVaultKey zadejte do druhé oblasti Trezor klíčů, ve kterém se klíč naklonuje. Případně můžete k zálohování a obnovení klíče použít Azure Portal. Klíč v trezoru sekundárního klíče v jiné oblasti by neměl být označený jako TDE Protector a není ani povolený.

Pokud dojde k výpadku, který má vliv na primární Trezor klíčů, systém se automaticky přepne na druhý propojený klíč se stejným kryptografickým otiskem v sekundárním trezoru klíčů, pokud existuje. Poznámka: Tento přepínač se nestane, pokud je ochrana TDE nepřístupná z důvodu odvolaných přístupových práv, nebo protože se odstranil klíč nebo Trezor klíčů, protože by mohl zákazník záměrně chtít, aby k tomuto klíči omezil přístup serveru.

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geografická DR a TDE spravovaná zákazníky

V rámci scénářů [aktivní geografické replikace](active-geo-replication-overview.md) a [skupiny převzetí služeb při selhání](auto-failover-group-overview.md) vyžaduje každý server samostatný Trezor klíčů, který musí být umístěný společně se serverem ve stejné oblasti Azure. Zákazník zodpovídá za to, že se klíčový materiál v trezorech klíčů udržuje konzistentně, takže geografická sekundární synchronizace je synchronizovaná a může přebírat stejný klíč z místního trezoru klíčů, pokud je primární z nich nedostupný kvůli výpadku v oblasti a aktivuje se převzetí služeb při selhání. Je možné nakonfigurovat až čtyři sekundární a řetězení (nesekundárních) není podporováno.

Aby nedocházelo k problémům při zřizování nebo během geografické replikace kvůli nekompletnímu klíčovému materiálu, je důležité při konfiguraci TDE spravovaného zákazníkem dodržovat tato pravidla:

- Všechny příslušné trezory klíčů musí mít stejné vlastnosti a stejná přístupová práva pro příslušné servery.

- Všechny příslušné trezory klíčů musí obsahovat identický klíčový materiál. Vztahuje se nejen na aktuální ochranu TDE, ale na všechny předchozí ochrany TDE, které se můžou použít v záložních souborech.

- Počáteční nastavení i rotace ochrany TDE se musí provést na sekundárním počítači a pak na primárním počítači.

![Skupiny převzetí služeb při selhání a geografická Dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Chcete-li otestovat převzetí služeb při selhání, postupujte podle kroků v [přehledu aktivní geografické replikace](active-geo-replication-overview.md). Testování převzetí služeb při selhání by se mělo provádět pravidelně, aby se ověřilo, že SQL Database zachovává přístupová oprávnění ke trezorům klíčů.

## <a name="next-steps"></a>Další kroky

U běžných operací se TDE spravovanými zákazníky možná budete chtít taky ověřit následující ukázkové skripty PowerShellu:

- [Otočení ochrany transparentní šifrování dat pro SQL Database pomocí PowerShellu](transparent-data-encryption-byok-key-rotation.md)

- [Odebrání ochrany transparentní šifrování dat (TDE) pro SQL Database pomocí prostředí PowerShell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Správa transparentní šifrování dat ve spravované instanci SQL pomocí vlastního klíče pomocí PowerShellu](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)