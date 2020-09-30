---
title: Šifrování dat s klíčem spravovaným zákazníkem – Azure Database for MySQL
description: Azure Database for MySQL šifrování dat pomocí klíče spravovaného zákazníkem vám umožní Bring Your Own Key (BYOK) pro ochranu dat v klidovém umístění. Umožňuje také organizacím implementovat oddělení povinností při správě klíčů a dat.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 30c60dbe74835cb67879f7e0cf9bf403dca17fd8
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531084"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL šifrování dat pomocí klíče spravovaného zákazníkem

Šifrování dat pomocí klíčů spravovaných zákazníkem pro službu Azure Database for MySQL umožňuje šifrovat neaktivní uložená data s použitím vlastního klíče. Umožňuje také organizacím implementovat oddělení povinností při správě klíčů a dat. V případě šifrování spravovaného zákazníkem máte úplnou kontrolu nad životním cyklem klíčů, oprávněními k používání klíčů a auditováním operací s klíči, za které také zodpovídáte.

Šifrování dat pomocí klíčů spravovaných zákazníkem pro Azure Database for MySQL je nastaveno na úrovni serveru. Pro daný server se k zašifrování datového šifrovacího klíče (klíč DEK) používaného službou používá klíč spravovaný zákazníkem (KEK), který se nazývá klíč šifrovací klíč (). KEK je asymetrický klíč uložený v instanci [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) spravované zákazníkem a zákazníkem. Klíč šifrování klíče (KEK) a šifrovací klíč (klíč DEK) jsou podrobněji popsány dále v tomto článku.

Key Vault je cloudový externí systém pro správu klíčů. Je vysoce dostupná a poskytuje škálovatelné a zabezpečené úložiště pro kryptografické klíče RSA, volitelně zajištěné moduly zabezpečení FIPS 140-2 Level 2 (HSM). Neumožňuje přímý přístup k uloženému klíči, ale poskytuje služby šifrování a dešifrování autorizovaným entitám. Key Vault může klíč vygenerovat, naimportovat nebo [přenášet z místního zařízení HSM](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for MySQL podporuje cenové úrovně "Pro obecné účely" a "paměťově optimalizovaná". Další omezení najdete v části [omezení](concepts-data-encryption-mysql.md#limitations) .

## <a name="benefits"></a>Výhody

Šifrování dat pomocí klíčů spravovaných zákazníkem pro Azure Database for MySQL přináší následující výhody:

* Přístup k datům je plně řízen možností odebrání klíče a zpřístupnění databáze. 
* Úplná kontrola nad životním cyklem klíčů, včetně rotace klíče, který se má zarovnat k podnikovým zásadám
* Centrální správa a organizace klíčů v Azure Key Vault
* Možnost implementovat oddělení povinností mezi bezpečnostními důstojníky a správci systému


## <a name="terminology-and-description"></a>Terminologie a popis

**Šifrovací klíč dat (klíč DEK)**: symetrický AES256 klíč, který slouží k šifrování oddílu nebo bloku dat. Šifrování každého bloku dat jiným klíčem usnadňuje útokům na kryptografickou analýzu. Poskytovatel prostředků nebo instance aplikace, která šifruje a šifruje konkrétní blok, vyžaduje přístup k DEKs. Při nahrazení klíč DEK novým klíčem musí být znovu zašifrována pouze data v jeho přidruženém bloku s novým klíčem.

Klíč **šifrovacího klíče (KEK)**: šifrovací klíč používaný k šifrování DEKs. KEK, který nikdy neopouští Key Vault, umožňuje, aby se DEKs sám zašifroval a řídil. Entita, která má přístup k KEK, může být jiná než entita, která vyžaduje klíč dek. Vzhledem k tomu, že KEK je vyžadován k dešifrování DEKs, je KEK v podstatě jediným bodem, pomocí kterého je DEKs možné efektivně odstranit odstraněním KEK.

DEKs šifrované pomocí KEK se ukládají samostatně. Pouze entita s přístupem k KEK může dešifrovat tyto DEKs. Další informace najdete v tématu [zabezpečení v šifrování v klidovém umístění](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Jak funguje šifrování dat pomocí klíče spravovaného zákazníkem

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Diagram, který zobrazuje přehled Bring Your Own Key":::

Aby mohl server MySQL používat pro šifrování klíč DEK klíče, které jsou uložené v Key Vault, správce Key Vault poskytuje následující přístupová práva k serveru:

* **Get**: pro načtení veřejné části a vlastností klíče v trezoru klíčů.
* **wrapKey**: aby bylo možné zašifrovat klíč dek. Šifrovaný klíč DEK je uložený v Azure Database for MySQL.
* **unwrapKey**: aby bylo možné dešifrovat klíč dek. Azure Database for MySQL potřebuje dešifrovací klíč DEK k šifrování nebo dešifrování dat.

Správce trezoru klíčů může také [Povolit protokolování událostí auditu Key Vault](../azure-monitor/insights/key-vault-insights-overview.md), aby se mohly auditovat později.

Když je server nakonfigurovaný tak, aby používal klíč spravovaný zákazníkem, který je uložený v trezoru klíčů, server pošle klíč DEK do trezoru klíčů pro šifrování. Key Vault vrátí šifrovaný klíč DEK, který je uložený v uživatelské databázi. Obdobně platí, že pokud je to potřeba, server pošle chráněný klíč DEK do trezoru klíčů pro dešifrování. Auditoři můžou pomocí Azure Monitor kontrolovat protokoly událostí auditu Key Vault, pokud je povolené protokolování.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Požadavky na konfiguraci šifrování dat pro Azure Database for MySQL

Níže jsou uvedené požadavky na konfiguraci Key Vault:

* Key Vault a Azure Database for MySQL musí patřit do stejného tenanta Azure Active Directory (Azure AD). Interakce mezi Key Vault klienty a servery nejsou podporovány. Přesunutí prostředku Key Vault vyžaduje překonfigurování šifrování dat.
* Povolí funkci obnovitelného odstranění v trezoru klíčů, aby se chránila před ztrátou dat v případě, že dojde k odstranění náhodného klíče (nebo Key Vault). Obnovitelné odstraněné prostředky se uchovávají po dobu 90 dnů, pokud je uživatel neobnoví nebo neodstraní. Akce obnovit a odstranit mají vlastní oprávnění přidružená v zásadách přístupu Key Vault. Funkce obnovitelného odstranění je ve výchozím nastavení vypnutá, ale můžete ji povolit prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure CLI (Všimněte si, že ji nemůžete povolit prostřednictvím Azure Portal).
* Udělte Azure Database for MySQL přístup k trezoru klíčů pomocí oprávnění Get, wrapKey a unwrapKey pomocí jeho jedinečné spravované identity. V Azure Portal se jedinečná identita služby automaticky vytvoří, když je v MySQL povolené šifrování dat. Podrobné pokyny najdete v tématu [Konfigurace šifrování dat pro MySQL](howto-data-encryption-portal.md) , podrobné pokyny, pokud používáte Azure Portal.

Níže jsou uvedené požadavky na konfiguraci klíče spravovaného zákazníkem:

* Klíč spravovaný zákazníkem, který se má použít k šifrování klíč DEK, může být jenom asymetrická, RSA 2048.
* Datum aktivace klíče (Pokud je nastaveno) musí být datum a čas v minulosti. Datum vypršení platnosti (Pokud je nastaveno) musí být budoucí datum a čas.
* Klíč musí být v *povoleném* stavu.
* Pokud [importujete existující klíč](https://docs.microsoft.com/rest/api/keyvault/ImportKey/ImportKey) do trezoru klíčů, nezapomeňte ho zadat v podporovaných formátech souborů ( `.pfx` , `.byok` , `.backup` ).

## <a name="recommendations"></a>Doporučení

Pokud používáte šifrování dat pomocí klíče spravovaného zákazníkem, tady najdete doporučení pro konfiguraci Key Vault:

* Nastavte zámek prostředků na Key Vault, abyste měli kontrolu nad tím, kdo může tento kritický prostředek odstranit a zabránit náhodnému nebo neoprávněnému odstranění.
* Povolte auditování a vytváření sestav u všech šifrovacích klíčů. Key Vault poskytuje protokoly, které se dají snadno vložit do dalších nástrojů pro správu událostí a informací o zabezpečení. Azure Monitor Log Analytics je jedním z příkladů služby, která je už integrovaná.
* Zajistěte, aby se Key Vault a Azure Database for MySQL nacházejí ve stejné oblasti, aby se zajistil rychlejší přístup k zabalení klíč DEK a rozbalení operací.
* Trezor klíčů Azure můžete zamknout jenom na **privátní koncový bod a vybrané sítě** a povolit zabezpečení prostředků jenom *důvěryhodným službám Microsoftu* .

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="Diagram, který zobrazuje přehled Bring Your Own Key":::

Tady jsou doporučení pro konfiguraci klíče spravovaného zákazníkem:

* Uchovávejte si kopii klíče spravovaného zákazníkem na bezpečném místě nebo ho v úschově do služby v úschově.

* Pokud Key Vault vygeneruje klíč, před prvním použitím klíče vytvořte zálohu klíčů. Zálohu lze obnovit pouze do Key Vault. Další informace o příkazu Backup najdete v tématu [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Nepřístupná podmínka pro klíč spravovaný zákazníkem

Když nakonfigurujete šifrování dat pomocí klíče spravovaného zákazníkem v Key Vault, musí server zůstat v online režimu nepřetržitý přístup k tomuto klíči. Pokud server ztratí přístup k klíči spravovanému zákazníkem v Key Vault, server zahájí odepření všech připojení během 10 minut. Server vydá odpovídající chybovou zprávu a změní stav serveru na *nepřístupný*. Některé z důvodů, proč Server může dosáhnout tohoto stavu:

* Když vytvoříme bod v čase obnovení serveru pro váš Azure Database for MySQL, u kterého je povolené šifrování dat, nově vytvořený server bude v *nedostupném* stavu. Tuto chybu můžete vyřešit pomocí [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) nebo rozhraní příkazového [řádku](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Pokud pro váš Azure Database for MySQL vytvoříme repliku pro čtení, která má povolené šifrování dat, server repliky bude v *nepřístupovém* stavu. Tuto chybu můžete vyřešit pomocí [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) nebo rozhraní příkazového [řádku](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Pokud odstraníte Trezor klíčů, Azure Database for MySQL nebude moci získat přístup k tomuto klíči a bude přesunut do *nedostupného* stavu. Obnovte [Key Vault](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) a znovu ověřte šifrování dat, aby byl server *dostupný*.
* Pokud klíč odstraníme z trezoru klíčů, Azure Database for MySQL nebude moct získat přístup k tomuto klíči a přesune se do *nedostupného* stavu. Obnovte [klíč](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) a znovu ověřte šifrování dat *pro zpřístupnění serveru.*
* Pokud klíč uložený ve službě Azure webrecovery vyprší, klíč se stane neplatným a Azure Database for MySQL přejde do *nedostupného* stavu. Rozšíříte datum vypršení platnosti klíče pomocí rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-set-attributes) a pak znovu ověříte šifrování dat, aby byl server *dostupný*.

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

* [Skupiny akcí](../azure-monitor/platform/action-groups.md): Definujte tyto skupiny, abyste vám poslali oznámení a výstrahy na základě vašich předvoleb.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Obnovení a replikace pomocí spravovaného klíče zákazníka v Key Vault

Když je Azure Database for MySQL zašifrovaný pomocí spravovaného klíče zákazníka uloženého v Key Vault, všechny nově vytvořené kopie serveru se taky šifrují. Tuto novou kopii můžete vytvořit buď prostřednictvím operace místního nebo geografického obnovení, nebo prostřednictvím replik pro čtení. Kopii ale můžete změnit tak, aby odrážela spravovaný klíč nového zákazníka pro šifrování. Když se změní klíč spravovaný zákazníkem, staré zálohy serveru začnou používat nejnovější klíč.

Aby nedocházelo k problémům při nastavování šifrování dat spravovaného zákazníkem během obnovy nebo vytváření repliky pro čtení, je důležité postupovat podle těchto kroků na serverech pro zdroj a obnovení/repliky:

* Zahajte proces vytváření repliky Restore nebo Read ze zdrojového Azure Database for MySQL.
* Nechejte nově vytvořený server (Obnovený nebo repliku) v nepřístupovém stavu, protože jeho jedinečná identita ještě nemá udělená oprávnění Key Vault.
* Na obnoveném serveru repliky znovu ověřte klíč spravovaný zákazníkem v nastavení šifrování dat, abyste zajistili, že nově vytvořenému serveru budou udělena oprávnění k zalamování a odbalení klíče uloženého v Key Vault.

## <a name="limitations"></a>Omezení

V případě Azure Database for MySQL podporuje šifrování neaktivních dat pomocí CMK (Customers Key) několik omezení –

* Podpora této funkce je omezená na **pro obecné účely** a **paměťově optimalizované** cenové úrovně.
* Tato funkce se podporuje jen v oblastech a na serverech, které podporují úložiště až do 16 TB. Seznam oblastí Azure, které podporují úložiště až do 16TB, najdete v části úložiště [v dokumentaci.](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Všechny nové servery MySQL vytvořené v oblastech uvedených výše jsou **k dispozici**podpora šifrování pomocí klíčů manažera zákazníka. Obnovený bod v čase (PITR) nebo replika čtení se neprojeví, i když je teoreticky "New".
    > - Pokud chcete ověřit, jestli zřízený Server podporuje až 16TB, můžete přejít na okno cenová úroveň na portálu a zobrazit maximální velikost úložiště podporovanou zřízeným serverem. Pokud můžete posuvník přesunout až na 4 TB, váš server možná nepodporuje šifrování se spravovanými klíči zákazníka. Data se ale šifrují pomocí klíčů spravovaných službou. AskAzureDBforMySQL@service.microsoft.comPokud máte nějaké dotazy, obraťte se na něj.

* Šifrování se podporuje jenom s kryptografickým klíčem RSA 2048.

## <a name="next-steps"></a>Další kroky

Naučte se, jak [nastavit šifrování dat pomocí klíče spravovaného zákazníkem pro službu Azure Database for MySQL pomocí Azure Portal](howto-data-encryption-portal.md).
