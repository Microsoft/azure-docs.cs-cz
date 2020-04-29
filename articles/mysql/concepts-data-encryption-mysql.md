---
title: Šifrování dat s klíčem spravovaným zákazníkem – Azure Database for MySQL
description: Azure Database for MySQL šifrování dat pomocí klíče spravovaného zákazníkem vám umožní Bring Your Own Key (BYOK) pro ochranu dat v klidovém umístění. Umožňuje také organizacím implementovat oddělení povinností při správě klíčů a dat.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a97fee619858aa024ff208b72d3b2594c30d2fd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299120"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL šifrování dat pomocí klíče spravovaného zákazníkem

> [!NOTE]
> V tuto chvíli musíte požádat o přístup k používání této možnosti. Pokud to chcete udělat, AskAzureDBforMySQL@service.microsoft.comkontaktujte.

Šifrování dat pomocí klíčů spravovaných zákazníkem pro Azure Database for MySQL vám umožní přinést si vlastní klíč (BYOK) pro ochranu dat v klidovém prostředí. Umožňuje také organizacím implementovat oddělení povinností při správě klíčů a dat. Pomocí šifrování spravovaného zákazníkem zodpovídáte za vás a plně řídíte životní cyklus klíčů, oprávnění k použití klíče a auditování operací s klíči.

Šifrování dat pomocí klíčů spravovaných zákazníkem pro Azure Database for MySQL je nastaveno na úrovni serveru. Pro daný server se k zašifrování datového šifrovacího klíče (klíč DEK) používaného službou používá klíč spravovaný zákazníkem (KEK), který se nazývá klíč šifrovací klíč (). KEK je asymetrický klíč uložený v instanci [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) spravované zákazníkem a zákazníkem. Klíč šifrování klíče (KEK) a šifrovací klíč (klíč DEK) jsou podrobněji popsány dále v tomto článku.

Key Vault je cloudový externí systém pro správu klíčů. Je vysoce dostupná a poskytuje škálovatelné a zabezpečené úložiště pro kryptografické klíče RSA, volitelně zajištěné moduly zabezpečení FIPS 140-2 Level 2 (HSM). Neumožňuje přímý přístup k uloženému klíči, ale poskytuje služby šifrování a dešifrování autorizovaným entitám. Key Vault může klíč vygenerovat, naimportovat nebo [přenášet z místního zařízení HSM](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for MySQL podporuje cenové úrovně "Pro obecné účely" a "paměťově optimalizovaná".

## <a name="benefits"></a>Výhody

Šifrování dat pro Azure Database for MySQL přináší následující výhody:

* Přístup k datům je plně řízen možností odebrání klíče a zpřístupnění databáze. 
* Úplná kontrola nad životním cyklem klíčů, včetně rotace klíče, který se má zarovnat k podnikovým zásadám
* Centrální správa a organizace klíčů v Azure Key Vault
* Možnost implementovat oddělení povinností mezi bezpečnostními důstojníky a správci systému


## <a name="terminology-and-description"></a>Terminologie a popis

**Šifrovací klíč dat (klíč DEK)**: symetrický AES256 klíč, který slouží k šifrování oddílu nebo bloku dat. Šifrování každého bloku dat jiným klíčem usnadňuje útokům na kryptografickou analýzu. Poskytovatel prostředků nebo instance aplikace, která šifruje a šifruje konkrétní blok, vyžaduje přístup k DEKs. Při nahrazení klíč DEK novým klíčem musí být znovu zašifrována pouze data v jeho přidruženém bloku s novým klíčem.

Klíč **šifrovacího klíče (KEK)**: šifrovací klíč používaný k šifrování DEKs. KEK, který nikdy neopouští Key Vault, umožňuje, aby se DEKs sám zašifroval a řídil. Entita, která má přístup k KEK, může být jiná než entita, která vyžaduje klíč dek. Vzhledem k tomu, že KEK je vyžadován k dešifrování DEKs, je KEK v podstatě jediným bodem, pomocí kterého je DEKs možné efektivně odstranit odstraněním KEK.

DEKs šifrované pomocí KEK se ukládají samostatně. Pouze entita s přístupem k KEK může dešifrovat tyto DEKs. Další informace najdete v tématu [zabezpečení v šifrování v klidovém umístění](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Jak funguje šifrování dat pomocí klíče spravovaného zákazníkem

![Diagram, který zobrazuje přehled Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Aby mohl server MySQL používat pro šifrování klíč DEK klíče, které jsou uložené v Key Vault, správce Key Vault poskytuje následující přístupová práva k serveru:

* **Get**: pro načtení veřejné části a vlastností klíče v trezoru klíčů.
* **wrapKey**: aby bylo možné zašifrovat klíč dek.
* **unwrapKey**: aby bylo možné dešifrovat klíč dek.

Správce trezoru klíčů může také [Povolit protokolování událostí auditu Key Vault](../azure-monitor/insights/azure-key-vault.md), aby se mohly auditovat později.

Když je server nakonfigurovaný tak, aby používal klíč spravovaný zákazníkem, který je uložený v trezoru klíčů, server pošle klíč DEK do trezoru klíčů pro šifrování. Key Vault vrátí šifrovaný klíč DEK, který je uložený v uživatelské databázi. Obdobně platí, že pokud je to potřeba, server pošle chráněný klíč DEK do trezoru klíčů pro dešifrování. Auditoři můžou pomocí Azure Monitor kontrolovat protokoly událostí auditu Key Vault, pokud je povolené protokolování.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Požadavky na konfiguraci šifrování dat pro Azure Database for MySQL

Níže jsou uvedené požadavky na konfiguraci Key Vault:

* Key Vault a Azure Database for MySQL musí patřit do stejného tenanta Azure Active Directory (Azure AD). Interakce mezi Key Vault klienty a servery nejsou podporovány. Přesunutí prostředků poté vyžaduje překonfigurování šifrování dat.
* Pokud dojde k odstranění náhodného klíče (nebo Key Vault), je nutné povolit funkci obnovitelného odstranění v trezoru klíčů, aby se chránila před ztrátou dat. Obnovitelné odstraněné prostředky se uchovávají po dobu 90 dnů, pokud je uživatel neobnoví nebo neodstraní. Akce obnovit a odstranit mají vlastní oprávnění přidružená v zásadách přístupu Key Vault. Funkce obnovitelného odstranění je ve výchozím nastavení vypnutá, ale můžete ji povolit prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure CLI (Všimněte si, že ji nemůžete povolit prostřednictvím Azure Portal).
* Udělte Azure Database for MySQL přístup k trezoru klíčů pomocí oprávnění Get, wrapKey a unwrapKey pomocí jeho jedinečné spravované identity. V Azure Portal se jedinečná identita automaticky vytvoří, když je v MySQL povolené šifrování dat. Podrobné pokyny najdete v tématu [Konfigurace šifrování dat pro MySQL](howto-data-encryption-portal.md) , podrobné pokyny, pokud používáte Azure Portal.

* Pokud používáte bránu firewall s Key Vault, musíte povolit možnost **Povolit důvěryhodným službám Microsoftu obejít bránu firewall**.

Níže jsou uvedené požadavky na konfiguraci klíče spravovaného zákazníkem:

* Klíč spravovaný zákazníkem, který se má použít k šifrování klíč DEK, může být jenom asymetrická, RSA 2028.
* Datum aktivace klíče (Pokud je nastaveno) musí být datum a čas v minulosti. Datum vypršení platnosti (Pokud je nastaveno) musí být budoucí datum a čas.
* Klíč musí být v *povoleném* stavu.
* Pokud importujete existující klíč do trezoru klíčů, nezapomeňte ho zadat v podporovaných formátech souborů (`.pfx`, `.byok`, `.backup`).

## <a name="recommendations"></a>Doporučení

Pokud používáte šifrování dat pomocí klíče spravovaného zákazníkem, tady najdete doporučení pro konfiguraci Key Vault:

* Nastavte zámek prostředků na Key Vault, abyste měli kontrolu nad tím, kdo může tento kritický prostředek odstranit a zabránit náhodnému nebo neoprávněnému odstranění.
* Povolte auditování a vytváření sestav u všech šifrovacích klíčů. Key Vault poskytuje protokoly, které se dají snadno vložit do dalších nástrojů pro správu událostí a informací o zabezpečení. Azure Monitor Log Analytics je jedním z příkladů služby, která je už integrovaná.

* Ujistěte se, že Key Vault a Azure Database for MySQL nacházejí ve stejné oblasti, abyste zajistili rychlejší přístup k klíč DEK zabalení a rozbalení operací.

Tady jsou doporučení pro konfiguraci klíče spravovaného zákazníkem:

* Uchovávejte si kopii klíče spravovaného zákazníkem na bezpečném místě nebo ho v úschově do služby v úschově.

* Pokud Key Vault vygeneruje klíč, před prvním použitím klíče vytvořte zálohu klíčů. Zálohu lze obnovit pouze do Key Vault. Další informace o příkazu Backup najdete v tématu [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Nepřístupná podmínka pro klíč spravovaný zákazníkem

Když nakonfigurujete šifrování dat pomocí klíče spravovaného zákazníkem v Key Vault, musí server zůstat v online režimu nepřetržitý přístup k tomuto klíči. Pokud server ztratí přístup k klíči spravovanému zákazníkem v Key Vault, server zahájí odepření všech připojení během 10 minut. Server vydá odpovídající chybovou zprávu a změní stav serveru na *nepřístupný*. Jediná akce povolená u databáze v tomto stavu ji odstraňuje.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Odvolání přístupu k náhodnému klíči z Key Vault

Může dojít k tomu, že někdo, který má dostatečná přístupová práva k Key Vault náhodně zakáže přístup serveru k klíči pomocí:

* Odvolávání oprávnění Get, wrapKey a unwrapKey trezoru klíčů ze serveru.
* Klíč se odstraňuje.
* Odstraňuje se Trezor klíčů.
* Mění se pravidla brány firewall trezoru klíčů.

* Odstraňuje se spravovaná identita serveru v Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorovat klíč spravovaný zákazníkem v Key Vault

Pokud chcete monitorovat stav databáze a povolit upozorňování na ztrátu transparentního přístupu k ochraně šifrování dat, nakonfigurujte následující funkce Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): nepřístupná databáze, která ztratila přístup k klíči zákazníka, v případě zamítnutí prvního připojení k databázi zobrazuje "nepřístupný".
* [Protokol aktivit](../service-health/alerts-activity-log-service-notifications.md): když se přístup k klíči zákazníka v Key Vault spravovaném zákazníkem nezdařil, přidají se do protokolu aktivit položky. Pokud vytvoříte výstrahy pro tyto události, můžete co nejdřív obnovit přístup.

* [Skupiny akcí](../azure-monitor/platform/action-groups.md): Definujte tyto možnosti, abyste vám poslali oznámení a výstrahy na základě vašich předvoleb.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Obnovení a replikace pomocí spravovaného klíče zákazníka v Key Vault

Když je Azure Database for MySQL zašifrovaný pomocí spravovaného klíče zákazníka uloženého v Key Vault, všechny nově vytvořené kopie serveru se taky šifrují. Tuto novou kopii můžete vytvořit buď prostřednictvím operace místního nebo geografického obnovení, nebo prostřednictvím replik pro čtení. Kopii ale můžete změnit tak, aby odrážela spravovaný klíč nového zákazníka pro šifrování. Když se změní klíč spravovaný zákazníkem, staré zálohy serveru začnou používat nejnovější klíč.

Aby nedocházelo k problémům při nastavování šifrování dat spravovaného zákazníkem během obnovování nebo čtení repliky, je důležité postupovat podle těchto kroků na serverech hlavních a obnovených replik:

* Zahajte proces vytváření repliky obnovení nebo čtení z hlavního Azure Database for MySQL.
* Nechejte nově vytvořený server (Obnovený nebo repliku) v nepřístupovém stavu, protože jeho jedinečná identita ještě nemá udělená oprávnění Key Vault.
* Na serveru obnoveného nebo repliky znovu ověřte klíč spravovaný zákazníkem v nastavení šifrování dat. Tím se zajistí, že nově vytvořenému serveru budou udělena oprávnění k zalamování a odbalení klíče uloženého v Key Vault.

## <a name="next-steps"></a>Další kroky

Naučte se, jak [nastavit šifrování dat pomocí klíče spravovaného zákazníkem pro službu Azure Database for MySQL pomocí Azure Portal](howto-data-encryption-portal.md).
