---
title: Azure Database for PostgreSQL šifrování dat s jedním serverem pomocí klíče spravovaného zákazníkem
description: Azure Database for PostgreSQL šifrování dat s jedním serverem pomocí klíče spravovaného zákazníkem vám umožní Bring Your Own Key (BYOK) pro ochranu dat v klidovém umístění a umožňuje organizacím implementovat oddělení úloh v rámci správy klíčů a dat.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: f9e60b2f1685e03a9daa7a4801f43799a21eb411
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940557"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>Azure Database for PostgreSQL šifrování dat s jedním serverem pomocí klíče spravovaného zákazníkem

> [!NOTE]
> V tuto chvíli musíte požádat o přístup k používání této možnosti. Pokud to chcete udělat, kontaktujte prosím AskAzureDBforPostgreSQL@service.microsoft.com.

Azure Database for PostgreSQL šifrování dat s jedním serverem pomocí klíče spravovaného zákazníkem vám umožní Bring Your Own Key (BYOK) pro ochranu dat v klidovém umístění a umožňuje organizacím implementovat oddělení úloh v rámci správy klíčů a dat. Pomocí šifrování spravovaného zákazníkem zodpovídáte za úplnou kontrolu životního cyklu klíče (vytváření, nahrávání, rotace, odstraňování), oprávnění k použití klíčů a auditování operací s klíči.

U Azure Database for PostgreSQLho jednoho serveru se šifrování dat nastaví na úrovni serveru. Pomocí této formy šifrování dat se klíč používá k šifrování šifrovacího klíče databáze (klíč DEK), což je asymetrický klíč spravovaný zákazníkem, který je uložený ve službě Customer Customer a Customer Customer-based [Azure Key Vault (integrace)](../key-vault/key-Vault-secure-your-key-Vault.md), což je cloudový externí systém správy klíčů. INTEGRACE je vysoce dostupný a poskytuje škálovatelné zabezpečené úložiště pro kryptografické klíče RSA, volitelně zajištěné moduly zabezpečení FIPS 140-2 Level 2 (HSM). Neumožňuje přímý přístup k uloženému klíči, ale poskytuje služby šifrování a dešifrování pomocí klíče u autorizovaných entit. Klíč je možné vygenerovat Key Vault, importovat nebo [přenést do Key Vault ze zařízení HSM Prem](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL jeden server podporuje Pro obecné účely a paměťově optimalizované cenové úrovně.

## <a name="benefits"></a>Výhody

Šifrování dat pro Azure Database for PostgreSQL jeden server přináší následující výhody:

* Zvýšení transparentnosti a podrobné řízení a správy šifrovacího klíče 
* Centrální správa a organizace klíčů jejich hostováním v Azure Key Vault. 
* Možnost implementovat oddělení povinností při správě klíčů a dat v rámci organizace
* Oddělte správu klíčů od správy dat v rámci organizace, aby Key Vault správce mohl odvolat přístupová oprávnění k klíčům k tomu, aby se zašifrovaná databáze nepřístupná. 
* Větší důvěra od koncových zákazníků, protože Azure Key Vault je navržený tak, aby Microsoft nemohl zobrazit ani extrahovat šifrovací klíče

## <a name="terminology-and-description"></a>Terminologie a popis

**Šifrovací klíč dat (klíč DEK)** – symetrický AES256 klíč, který slouží k šifrování oddílu nebo bloku dat. Šifrování každého bloku dat jiným klíčem usnadňuje útokům na kryptografickou analýzu. Poskytovatel prostředků nebo instance aplikace, která šifruje a šifruje konkrétní blok, vyžaduje přístup k DEKs. Když je klíč DEK nahrazen novým klíčem, musí být znovu zašifrována pouze data v příslušném bloku s novým klíčem.

Klíč **šifrovacího klíče (KEK)** – šifrovací klíč používaný k šifrování šifrovacích klíčů dat. Použití klíčového šifrovacího klíče, který nikdy neopouští Key Vault, umožňuje šifrování a řízení šifrovacích klíčů samotných dat. Entita, která má přístup k KEK, může být jiná než entita, která vyžaduje klíč dek. Vzhledem k tomu, že KEK je vyžadován k dešifrování DEKs, je KEK v podstatě jediným bodem, pomocí kterého je DEKs možné efektivně odstranit odstraněním KEK.

Šifrovací klíče dat šifrované pomocí klíčového šifrovacího klíče se ukládají samostatně a k dešifrování těchto šifrovacích klíčů může použít jenom entita s přístupem ke klíčovým šifrovacím klíčem. Další informace najdete v tématu [zabezpečení v šifrování v klidovém umístění](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Jak funguje šifrování dat pomocí klíče spravovaného zákazníkem

![Přehled vlastního klíče](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Aby mohl server PostgreSQL používat pro šifrování klíč DEK klíče, které jsou uložené v integrace, musí správce Key Vault k serveru přidělit tato přístupová práva pomocí jeho jedinečné identity:

* **Get** – pro načtení veřejné části a vlastností klíče v Key Vault
* **wrapKey** – aby bylo možné chránit (ŠIFROVAT) klíč DEK
* **unwrapKey** – aby bylo možné zrušit ochranu (dešifrovat) klíč DEK

Správce Key Vault taky může [Povolit protokolování událostí auditu Key Vault](../azure-monitor/insights/azure-key-vault.md), aby se mohly auditovat později.

Pokud je server nakonfigurovaný tak, aby používal klíč spravovaný zákazníkem, který je uložený v Key Vault, server pošle klíč DEK do Key Vault pro šifrování. Key Vault vrátí šifrovaný klíč DEK, který je uložený v uživatelské databázi. Podobně pokud je to potřeba, server pošle chráněný klíč DEK do Key Vault k dešifrování. Auditoři můžou použít Azure Monitor ke kontrole protokolů Key Vault AuditEvent, pokud je povolené protokolování.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Požadavky na konfiguraci šifrování dat pro Azure Database for PostgreSQL jeden server

### <a name="requirements-for-configuring-akv"></a>Požadavky na konfiguraci integrace

* Key Vault a Azure Database for PostgreSQL jeden server musí patřit do stejného tenanta Azure Active Directory (AAD). Interakce mezi Key Vault klienty a servery nejsou podporovány. Přesunutí prostředků poté vyžaduje překonfigurování šifrování dat. Přečtěte si další informace o přesouvání prostředků.
* Funkce obnovitelného odstranění musí být na Key Vault povolena, aby bylo možné chránit před náhodným nebo Key Vaultm únikem informací. Obnovitelné odstraněné prostředky se uchovávají po dobu 90 dnů, pokud je zákazník neobnovil nebo nevymazal. Akce obnovit a odstranit mají vlastní oprávnění přidružená v zásadách přístupu Key Vault. Funkce obnovitelného odstranění je ve výchozím nastavení vypnutá a dá se povolit prostřednictvím PowerShellu nebo rozhraní příkazového řádku. Nelze ji povolit prostřednictvím Azure Portal.
* Udělte jednomu serveru Azure Database for PostgreSQL přístup k Key Vault pomocí oprávnění **Get, wrapKey a unwrapKey** s použitím jeho jedinečné spravované identity. Při použití Azure Portal se jedinečné identifikace automaticky vytvoří, když je na jednom serveru PostgreSQL povolené šifrování dat. Podrobné pokyny pro použití Azure Portal najdete v tématu [Konfigurace šifrování dat pro jediný server PostgreSQL](howto-data-encryption-portal.md) .

* Pokud používáte bránu firewall s integrace, musíte povolit možnost povolit *důvěryhodným službám Microsoftu obejít bránu firewall*.

### <a name="requirements-for-configuring-customer-managed-key"></a>Požadavky na konfiguraci klíče spravovaného zákazníkem

* Klíč spravovaný zákazníkem, který se má použít k šifrování klíč DEK, může být jenom asymetrická, RSA 2028.
* Datum aktivace klíče (Pokud je nastaveno) musí být datum a čas v minulosti. Datum vypršení platnosti (Pokud je nastaveno) musí být budoucí datum a čas.
* Klíč musí být v *povoleném* stavu.
* Pokud importujete existující klíč do Key Vault, nezapomeňte ho zadat v podporovaných formátech souborů (`.pfx`, `.byok`, `.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Doporučení při používání šifrování dat pomocí klíče spravovaného zákazníkem

### <a name="recommendation-for-configuring-akv"></a>Doporučení pro konfiguraci integrace

* Nastavte zámek prostředků na Key Vault, abyste měli kontrolu nad tím, kdo může tento kritický prostředek odstranit a zabránit náhodnému nebo neoprávněnému odstranění. Přečtěte si další informace o zámkech prostředků.
* Povolit auditování a vytváření sestav u všech šifrovacích klíčů: Key Vault poskytuje protokoly, které se dají snadno vložit do dalších nástrojů pro správu informací a událostí zabezpečení. Azure Monitor Log Analytics je jedním z příkladů služby, která je již integrovaná.

* Zajistěte, aby se Key Vault a Azure Database for PostgreSQL jeden server nacházely ve stejné oblasti, abyste zajistili rychlejší přístup k operacím zabalení a rozbalení klíč dek.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Doporučení pro konfiguraci klíče spravovaného zákazníkem

* Uchovávejte kopii klíče spravovaného zákazníkem (KEK) na bezpečném místě nebo ho v úschově do služby v úschově.

* Pokud se klíč vygeneruje v Key Vault, před prvním použitím klíče v integrace vytvořit zálohu klíčů. Zálohování lze obnovit pouze do Azure Key Vault. Přečtěte si další informace o příkazu [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) .

## <a name="inaccessible-customer-managed-key-condition"></a>Nepřístupná podmínka pro klíč spravovaný zákazníkem

Když je v Azure Key Vault (integrace) nakonfigurované šifrování dat s klíčem spravovaným zákazníkem, je potřeba nepřetržitý přístup k tomuto klíči, aby server zůstal online. Pokud server ztratí přístup k klíči spravovanému zákazníkem v integrace během 10 minut, server zahájí odepření všech připojení k příslušné chybové zprávě a změní stav serveru na **nepřístupný**. Jediná akce povolená u databáze v nepřístupovém stavu ji odstraňuje.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Odvolání přístupu k náhodnému klíči z Azure Key Vault (integrace)

Může dojít k tomu, že někdo, který má dostatečná přístupová práva k Key Vault náhodně zakáže přístup serveru k klíči pomocí:

* odvolání Key Vault oprávnění Get, wrapKey a unwrapKey ze serveru
* klíč se odstraňuje.
* odstranění Key Vault
* Změna pravidel brány firewall Key Vault

* Odstraňuje se spravovaná identita serveru v Azure Active Directory.

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Monitorování klíče spravovaného zákazníkem v Key Vault

Pokud chcete monitorovat stav databáze a povolit upozorňování na ztrátu přístupu TDE Protector, nakonfigurujte následující funkce Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md) – nepřístupná databáze, která ztratila přístup k klíči zákazníka, se po odepření prvního připojení k databázi zobrazí jako nedostupná.
* [Protokol aktivit](../service-health/alerts-activity-log-service-notifications.md) – když se přístup k klíči zákazníka v Key Vault spravovaném zákazníkem nezdařil, přidají se do protokolu aktivit položky. Vytváření výstrah pro tyto události vám umožní co nejdříve obnovit přístup.

* [Skupiny akcí](../azure-monitor/platform/action-groups.md) můžete definovat tak, aby vám odesílaly oznámení a výstrahy na základě vašich preferencí, například e-mailu, SMS/Push/Voice, Logic Apps, Webhook, ITSM nebo Automation Runbook.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Obnovení a replikování pomocí spravovaného klíče zákazníka v Key Vault

Jakmile je jeden server Azure Database for PostgreSQL zašifrovaný pomocí spravovaného klíče zákazníka uloženého v Key Vault, bude jakákoli nově vytvořená kopie serveru (buď i v případě operace místního nebo geografického obnovení nebo prostřednictvím replik čtení) zašifrovaná taky stejným spravovaný klíč zákazníka. Můžou se ale změnit tak, aby odrážely spravovaný klíč nového zákazníka pro šifrování. Když se změní klíč spravovaný zákazníkem, starší zálohy serveru začnou používat nejnovější klíč.

Aby nedocházelo k problémům při nastavování šifrování dat spravovaného zákazníkem během obnovování nebo čtení repliky, je důležité postupovat podle těchto kroků na serveru hlavní a obnovení nebo repliky:

* Zahajte proces vytváření repliky obnovení nebo čtení z hlavního Azure Database for PostgreSQL jednoho serveru.
* Nově vytvořený server (Obnovený/replika) je ponechán jako nedostupný, protože jeho jedinečná identita ještě neudělila oprávnění Azure Key Vault (integrace).
* Na obnoveném nebo replikovém serveru znovu ověřte klíč spravovaný zákazníkem v nastavení šifrování dat, abyste zajistili, že nově vytvořenému serveru budou udělena oprávnění k zabalení nebo odbalení klíče uloženého v integrace.

* Oba výše uvedené kroky je nutné provést, aby bylo zajištěno, že šifrování dat bude zachováno v hlavní databázi i obnovený server repliky.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [pomocí Azure Portal nastavit šifrování dat pomocí klíče spravovaného zákazníkem pro jeden server Azure Database for PostgreSQL](howto-data-encryption-portal.md).
