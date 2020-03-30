---
title: Šifrování dat pomocí klíče spravovaného zákazníkem – Azure Database for PostgreSQL – jeden server
description: Šifrování dat Azure Database for PostgreSQL Single server pomocí klíče spravovaného zákazníkem umožňuje přenést vlastní klíč (BYOK) pro ochranu dat v klidovém stavu. To také umožňuje organizacím provádět oddělení povinností při správě klíčů a dat.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297018"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database for PostgreSQL Jednoserverové šifrování dat pomocí klíče spravovaného zákazníkem

> [!NOTE]
> V tomto okamžiku je nutné požádat o přístup k použití této funkce. Chcete-li tak AskAzureDBforPostgreSQL@service.microsoft.comučinit, obraťte se na .

Šifrování dat pomocí klíčů spravovaných zákazníkem pro server Azure Database for PostgreSQL Single umožňuje přinést si vlastní klíč (BYOK) pro ochranu dat v klidovém stavu. To také umožňuje organizacím provádět oddělení povinností při správě klíčů a dat. Díky šifrování spravovanému zákazníkem nesete odpovědnost za životní cyklus klíče, oprávnění k používání klíčů a auditování operací s klíči a máte nad ním plnou kontrolu.

Šifrování dat pomocí klíčů spravovaných zákazníkem pro Azure Database for PostgreSQL Single server je nastavené na úrovni serveru. Pro daný server se k šifrování šifrovacího klíče (DEK) používaného službou používá klíč spravovaný zákazníkem, nazývaný kšifrovací klíč (KEK). KEK je asymetrický klíč uložený v instanci [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) vlastněné zákazníkem a zákazníkem. Šifrovací klíč (KEK) a datový šifrovací klíč (DEK) je popsán podrobněji dále v tomto článku.

Key Vault je cloudový externí systém pro správu klíčů. Je vysoce dostupný a poskytuje škálovatelné a zabezpečené úložiště pro kryptografické klíče RSA, volitelně podporované moduly zabezpečení hardwarového zabezpečení (HSM) standardizovaného standardu FIPS 140-2 Level 2. Neumožňuje přímý přístup k uloženému klíči, ale poskytuje služby šifrování a dešifrování autorizovaným entitám. Trezor klíčů může klíč vygenerovat, importovat nebo [jej nechat přenést z místního zařízení s hesm](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL Single server podporuje cenové úrovně "Obecné účely" a "Optimalizované pro paměť".

## <a name="benefits"></a>Výhody

Šifrování dat pro Azure Database pro server PostgreSQL Single poskytuje následující výhody:

* Přístup k datům je plně řízen vámi schopností odstranit klíč a znepřístupnit databázi 
*    Plná kontrola nad životním cyklem klíčů, včetně střídání klíče tak, aby byl v souladu s podnikovými zásadami
*    Centrální správa a organizace klíčů v azure key vaultu
*    Schopnost provádět oddělení povinností mezi bezpečnostními úředníky a DBA a správci systému

## <a name="terminology-and-description"></a>Terminologie a popis

**Šifrovací klíč (DEK)**: Symetrický klíč AES256 používaný k šifrování oddílu nebo bloku dat. Šifrování každého bloku dat pomocí jiného klíče ztěžuje útoky kryptoanalýzy. Přístup k DEKs je potřeba poskytovatele prostředků nebo instance aplikace, která je šifrování a dešifrování konkrétní blok. Když nahradíte soubor DEK novým klíčem, musí být znovu zašifrována novým klíčem pouze data v přidruženém bloku.

**Šifrovací klíč (KEK):** Šifrovací klíč používaný k šifrování deks. KEK, který nikdy neopustí Key Vault umožňuje DEKs sami být šifrována a kontrolovány. Entita, která má přístup k KEK může být jiný než entita, která vyžaduje DEK. Vzhledem k tomu, KEK je nutné dešifrovat DEKs, KEK je skutečně jediný bod, kterým DEKs lze účinně odstranit odstraněním KEK.

Deks, šifrované s KEKs, jsou uloženy samostatně. Pouze entita s přístupem k KEK může dešifrovat tyto DEKs. Další informace naleznete [v tématu Zabezpečení v šifrování v klidovém stavu](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Jak funguje šifrování dat pomocí klíče spravovaného zákazníkem

![Diagram, který znázorňuje přehled přineste si vlastní klíč](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

Aby server PostgreSQL používal klíče spravované zákazníkem uložené v trezoru klíčů k šifrování sady DEK, poskytuje správce trezoru klíčů serveru následující přístupová práva:

* **get**: Pro načtení veřejné části a vlastností klíče v trezoru klíčů.
* **wrapKey**: Chcete-li být schopni šifrovat DEK.
* **rozbalit klíč**: Chcete-li být schopni dešifrovat DEK.

Správce trezoru klíčů může také [povolit protokolování událostí auditu trezoru klíčů](../azure-monitor/insights/azure-key-vault.md), takže je lze auditovat později.

Pokud je server nakonfigurován tak, aby používal klíč spravovaný zákazníkem uložený v trezoru klíčů, odešle dek do trezoru klíčů pro šifrování. Trezor klíčů vrátí šifrovaný soubor DEK, který je uložen v databázi uživatelů. Podobně v případě potřeby server odešle chráněný DEK do trezoru klíčů pro dešifrování. Auditoři můžou pomocí Azure Monitoru zkontrolovat protokoly událostí auditu trezoru klíčů, pokud je protokolování povoleno.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Požadavky na konfiguraci šifrování dat pro Azure Database pro server PostgreSQL Single

Níže jsou uvedeny požadavky na konfiguraci trezoru klíčů:

* Trezor klíčů a Azure Database for PostgreSQL Single server musí patřit do stejného klienta Azure Active Directory (Azure AD). Meziklientský trezor klíčů a interakce serveru nejsou podporovány. Přesunutí prostředků poté vyžaduje překonfigurovat šifrování dat.
* Je nutné povolit funkci obnovitelného odstranění v trezoru klíčů, abyste byli chráněni před ztrátou dat, pokud dojde k náhodnému odstranění klíče (nebo trezoru klíčů). Obnovitelné prostředky jsou uchovávány po dobu 90 dnů, pokud je uživatel mezitím neobnoví nebo neočistí. Akce obnovení a vymazání mají vlastní oprávnění přidružená k zásadám přístupu trezoru klíčů. Funkce obnovitelného odstranění je ve výchozím nastavení vypnutá, ale můžete ji povolit prostřednictvím Prostředí PowerShell nebo Azure CLI (všimněte si, že ji nemůžete povolit prostřednictvím portálu Azure).
* Udělte databázi Azure pro postgreSQL jeden server přístup k trezoru klíčů s get, wrapKey a unwrapKey oprávnění pomocí jeho jedinečnou spravovanou identitu. Na webu Azure Portal se jedinečná identita automaticky vytvoří, když je na serveru PostgreSQL Single povoleno šifrování dat. Podrobné podrobné pokyny při používání webu Azure Portal najdete v článku [Šifrování dat pro server Azure Database for PostgreSQL Single pomocí portálu Azure Portal.](howto-data-encryption-portal.md)

* Používáte-li bránu firewall s trezorem klíčů, musíte povolit možnost **Povolit důvěryhodným službám společnosti Microsoft obejít bránu firewall**.

Níže jsou uvedeny požadavky na konfiguraci klíče spravovaného zákazníkem:

* Klíč spravovaný zákazníkem, který má být použit pro šifrování DEK, může být pouze asymetrický, RSA 2028.
* Datum aktivace klíče (pokud je nastaveno) musí být datum a čas v minulosti. Datum vypršení platnosti (pokud je nastaveno) musí být budoucí datum a čas.
* Klíč musí být ve stavu *Povoleno.*
* Pokud importujete existující klíč do trezoru klíčů, nezapomeňte jej zadat v`.pfx` `.byok`podporovaných formátech souborů ( , , `.backup`).

## <a name="recommendations"></a>Doporučení

Pokud používáte šifrování dat pomocí klíče spravovaného zákazníkem, tady jsou doporučení pro konfiguraci trezoru klíčů:

* Nastavte zámek prostředků v trezoru klíčů, abyste mohli řídit, kdo může odstranit tento důležitý prostředek a zabránit náhodnému nebo neoprávněnému odstranění.
* Povolte auditování a vytváření sestav na všech šifrovacích klíčích. Trezor klíčů poskytuje protokoly, které lze snadno vložit do dalších nástrojů pro správu informací o zabezpečení a událostí. Azure Monitor Log Analytics je jedním z příkladů služby, která je už integrovaná.

* Ujistěte se, že trezor klíčů a databáze Azure pro server PostgreSQL Single jsou umístěny ve stejné oblasti, abyste zajistili rychlejší přístup pro operace dek obtékání a rozbalování.

Zde jsou doporučení pro konfiguraci klíče spravovaného zákazníkem:

* Uchovávejte kopii klíče spravovaného zákazníkem na bezpečném místě nebo ji uložte do úschovy.

* Pokud trezor klíčů generuje klíč, vytvořte zálohu klíčů před prvním použitím klíče. Zálohu lze obnovit pouze do trezoru klíčů. Další informace o příkazu zálohování naleznete v tématu [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Nepřístupná klíčová podmínka spravovaná zákazníkem

Při konfiguraci šifrování dat pomocí klienta spravovaného klíče v trezoru klíčů je vyžadován nepřetržitý přístup k tomuto klíči, aby server zůstal online. Pokud server ztratí přístup ke klíči spravovanému zákazníkem v trezoru klíčů, server začne odepřít všechna připojení do 10 minut. Server vydá odpovídající chybovou zprávu a změní stav serveru na *Nepřístupný*. Jedinou akcí povolenou v databázi v tomto stavu je jeho odstranění.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Náhodné odvolání přístupu klíče z trezoru klíčů

Může se stát, že někdo s dostatečnými přístupovými právy k trezoru klíčů omylem zakáže přístup k klíči serverem:

* Zrušení trezoru klíčů get, wrapKey a unwrapKey oprávnění ze serveru.
* Odstranění klíče.
* Odstranění trezoru klíčů.
* Změna pravidel brány firewall trezoru klíčů.

* Odstranění spravované identity serveru ve službě Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Sledování klíče spravovaného zákazníkem v trezoru klíčů

Chcete-li sledovat stav databáze a povolit výstrahy před ztrátou přístupu k ochraně šifrování transparentního datového šifrování, nakonfigurujte následující funkce Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): Nepřístupná databáze, která ztratila přístup ke klíči zákazníka, se po odepření prvního připojení k databázi zobrazí jako "Nepřístupný".
* [Protokol aktivit](../service-health/alerts-activity-log-service-notifications.md): Při selhání přístupu ke klíči zákazníka v trezoru klíčů spravovaném zákazníkem jsou do protokolu aktivit přidány položky. Pokud vytvoříte výstrahy pro tyto události, můžete přístup obnovit co nejdříve.

* [Skupiny akcí](../azure-monitor/platform/action-groups.md): Definujte je tak, aby vám zasílala oznámení a upozornění na základě vašich preferencí.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Obnovení a replikace pomocí spravovaného klíče zákazníka v trezoru klíčů

Po Azure Database for PostgreSQL Single server je šifrována s klientem spravovaný klíč uložený v trezoru klíčů, všechny nově vytvořené kopie serveru je také šifrována. Tuto novou kopii můžete vytvořit buď prostřednictvím místní nebo geografické operace obnovení nebo prostřednictvím replik y pro čtení. Kopii však lze změnit tak, aby odrážela spravovaný klíč nového zákazníka pro šifrování. Při změně klíče spravovaného zákazníkem začnou staré zálohy serveru používat nejnovější klíč.

Chcete-li se vyhnout problémům při nastavování šifrování dat spravovaného zákazníkem během obnovení nebo čtení repliky, je důležité postupovat podle následujících kroků na hlavních a obnovených/replikových serverech:

* Spusťte proces vytváření obnovy nebo čtení replik z hlavní databáze Azure pro server PostgreSQL Single.
* Uchovávejte nově vytvořený server (obnovený/replika) v nepřístupném stavu, protože jeho jedinečná identita ještě nebyla udělena oprávnění k trezoru klíčů.
* Na obnoveném/replikovaném serveru znovu ověřte klíč spravovaný zákazníkem v nastavení šifrování dat. Tím je zajištěno, že nově vytvořený server je uveden obtékání a rozbalit oprávnění ke klíči uloženému v trezoru klíčů.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [nastavit šifrování dat pomocí klíče spravovaného zákazníkem pro databázi Azure pro server PostgreSQL Single pomocí portálu Azure](howto-data-encryption-portal.md).

