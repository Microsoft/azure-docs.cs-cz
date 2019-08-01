---
title: Azure Key Vault ve službě Azure Austrálie
description: Pokyny týkající se konfigurace a používání Azure Key Vault pro správu klíčů v rámci australských oblastí pro splnění konkrétních požadavků na zásady australské vlády, předpisy a právní předpisy.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602124"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault ve službě Azure Austrálie

Zabezpečené úložiště kryptografických klíčů a správy životního cyklu kryptografického klíče jsou kritické prvky v rámci kryptografických systémů.  Služba, která poskytuje tuto funkci v Azure, je Azure Key Vault. Key Vault byl IRAP k zabezpečení a ACSC byl certifikován pro chráněné.  Tento článek popisuje klíčové důležité informace, které se používají Key Vault k zajištění dodržování bezpečnostních mechanismů (ISM) v programu pro [řízení informačních](https://acsc.gov.au/infosec/ism/) signálů (ISM).

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy. Vzhledem k tomu, že jsou tato data citlivá a důležitá pro podnikání, Key Vault umožňuje zabezpečený přístup k trezorům klíčů a povoluje jenom autorizované uživatele a aplikace. Existují tři hlavní artefakty spravované a řízené Key Vault:

- klíče
- tajné kódy
- certificates

Tento článek se zaměřuje na správu klíčů pomocí Key Vault.

![Azure Key Vault](media/azure-key-vault-overview.png)

*Diagram 1 – Azure Key Vault*

## <a name="key-design-considerations"></a>Klíčové faktory návrhu

### <a name="deployment-options"></a>Možnosti nasazení

Existují dvě možnosti vytváření trezorů klíčů Azure. Obě možnosti používají Thales hardwarového nShield, které využívají moduly hardwarového zabezpečení (HSM), jsou ověřené standardem FIPS (Federal Information Processing Standards) a jsou schváleny k ukládání klíčů v CHRÁNĚNých prostředích. Dostupné možnosti:

- **Trezory chráněné softwarem:** Byla ověřena FIPS 140-2 úrovně 1. Klíče uložené v modulu HARDWAROVÉho zabezpečení Operace šifrování a dešifrování se provádějí ve výpočetních prostředcích v Azure.
- **Trezory chráněné HSM:** Ověřeno FIPS 140-2 úrovně 2. Klíče uložené v modulu HARDWAROVÉho zabezpečení V modulu HARDWAROVÉho zabezpečení se provádí operace šifrování a dešifrování.

Key Vault podporuje klíče Rivest-Shamir-Adleman (RSA) a ECC (Curve Cryptography). Výchozím nastavením jsou klíče RSA 2048-bit, ale existuje pokročilá možnost pro klíče RSA 3072-bit, RSA 4096-bit a ECC klíče.  Všechny klíče vyhovují ovládacím prvkům ISM, ale jsou upřednostňovány klávesy eliptické křivky.

### <a name="resource-operations"></a>Operace prostředků

Azure Key Vault se účastní několik osoby:

- **Správce Key Vault:** Spravuje životní cyklus trezoru.
- **Správce klíčů:** Spravuje životní cyklus klíčů v trezoru.
- **Vývojář nebo operátor:** Integrace klíčů z trezoru do aplikací a služeb
- **Auditor** Monitorovat použití klíče a přístup
- **Vyrovnání** Použití klíčů k zabezpečení informací

Azure Key Vault je zabezpečená pomocí dvou oddělených rozhraní:

- **Rovina správy:** Tato rovina se zabývá správou trezoru a je zabezpečená pomocí RBAC.
- **Rovina dat:** Tato rovina se zabývá správou a přístupem k artefaktům v trezoru.  Zabezpečeno pomocí zásad přístupu Key Vault.

Jak je vyžaduje ISM, před volajícím (uživatelem nebo aplikací) se vyžaduje správné ověřování a autorizace, aby mohli získat přístup k trezoru klíčů buď pomocí roviny.

Azure RBAC má jednu vestavěnou roli pro Key Vault, [Key Vault přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor)k řízení správy trezorů klíčů. Doporučuje se vytvořit vlastní role zarovnané na podrobnější role pro správu trezorů klíčů.

>[!WARNING]
>Když je přístup k klíčům povolen prostřednictvím zásad Key Vault přístupu, uživatel nebo aplikace má přístup ke všem klíčům v trezoru klíčů (například pokud má uživatel přístup "odstranit"), který může odstranit všechny klíče).  Proto by se měly nasadit několik trezorů klíčů, aby se zarovnaly doménám a hranicím zabezpečení.

### <a name="networking"></a>Sítě

Můžete nakonfigurovat Key Vault brány firewall a virtuální sítě pro řízení přístupu k rovině dat.  Můžete povolit přístup uživatelům nebo aplikacím v zadaných sítích a přitom odepřít přístup uživatelům nebo aplikacím ve všech ostatních sítích. [Důvěryhodné služby](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) jsou výjimkou tohoto ovládacího prvku, pokud je povolená možnost Povolit důvěryhodné služby.  Řízení virtuálních sítí se nevztahuje na rovinu správy.

Přístup k trezorům klíčů by měl být explicitně omezen na minimální sadu sítí, které mají uživatele nebo aplikace vyžadující přístup k klíčům.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault podporuje BYOK.  BYOK umožňuje uživatelům importovat klíče ze svých stávajících klíčových infrastruktur.  Thales poskytuje [australské sady nástrojů](https://www.microsoft.com/download/details.aspx?id=45345) pro podporu zabezpečeného přenosu a importu klíčů z externího modulu HSM (například klíče vygenerované v offline pracovní stanici) do Key Vault.

### <a name="key-vault-auditing-and-logging"></a>Key Vault auditování a protokolování

ACSC vyžaduje, aby entity Společenství používaly příslušné služby Azure ke sledování a vytváření sestav o svých úlohách Azure v reálném čase.

Protokolování je povoleno povolením nastavení diagnostiky **_"AuditEvent"_** u klíčových hodnot.  Události auditu se zaprotokolují do zadaného účtu úložiště.  Doba **_"RetentionInDays"_** by měla být nastavená podle zásad uchovávání dat.  [Operace](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) na rovině správy i rovině dat jsou auditovány a protokolovány. [Řešení Azure Key Vault v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) lze použít ke kontrole Key Vault protokolů AuditEvent.  Ke zpracování a distribuci Key Vault AuditEvents se dá použít řada dalších služeb Azure.

### <a name="key-rotation"></a>Obměna klíčů

Ukládání klíčů v Key Vault poskytlo jeden bod pro udržování klíčů mimo aplikace, které umožňují aktualizovat klíče, aniž by to mělo vliv na chování aplikací. Ukládání klíčů v Azure Key Vault umožňuje různé strategie pro podporu rotace klíčů:

- Ručně
- Prostřednictvím kódu programu prostřednictvím rozhraní API
- Skripty pro automatizaci (například pomocí PowerShellu a Azure Automation)

Tyto možnosti umožňují střídání klíčů v pravidelných intervalech, aby splňovaly požadavky na dodržování předpisů nebo ad hoc, pokud se vyskytnou obavy, že klíče byly ohroženy.

#### <a name="key-rotation-strategies"></a>Strategie pro rotaci klíčů

Je důležité vyvinout vhodnou strategii střídání klíčů pro klíče, které jsou uložené v trezoru klíčů.  Použití špatného klíče povede k nesprávnému dešifrování informací a ztrátě klíčů může vést k úplné ztrátě přístupu k informacím.  Příklady strategií střídání klíčů pro různé scénáře zahrnují:

- **Data o letu:** mezi 2 stranami se přenáší informace o nestálosti.  Když je klíč otočený, musí mít obě strany mechanismus pro synchronní načtení aktualizovaných klíčů z trezoru klíčů.
- **Data jako REST:** Strana ukládá šifrovaná data a dešifruje je v budoucnu, aby ji bylo možné použít.  Když se klíč otočí, musí se data dešifrovat pomocí starého klíče a pak se zašifrovat pomocí nového, otočeného klíče.  Existují přístupy k minimalizaci dopadu procesu dešifrování/šifrování pomocí klíčů šifrování klíče (viz příklad).  Společnost Microsoft spravuje většinu procesu souvisejícího s rotací klíčů pro Azure Storage (viz...)
- **Přístupové klíče:** řada služeb Azure má přístupové klíče, které mohou být uloženy v Key Vault (například CosmosDB).  Služby Azure mají primární a sekundární přístupový klíč.  Je důležité, aby se oba klíče Nestřídat současně.  Proto by měl být jeden klíč otočen po tečkě a operace klíče byla ověřena, druhý klíč lze otočit.

### <a name="high-availability"></a>Vysoká dostupnost

ISM má několik ovládacích prvků, které se vztahují k kontinuitě podnikových aplikací.
Azure Key Vault má více vrstev redundance s obsahem replikovaným v rámci oblasti a sekundárním, [spárovaným regionem](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Pokud je Trezor klíčů ve stavu převzetí služeb při selhání, je v režimu jen pro čtení a vrátí se do režimu čtení i zápisu primární služba se obnoví.

ISM má několik ovládacích prvků vztahujících se k zálohování.  Je důležité vyvíjet a spouštět vhodné plány zálohování a obnovení pro trezory a jejich klíče.

## <a name="key-lifecycle"></a>Životní cyklus klíčů

### <a name="key-operations"></a>Klíčové operace

Key Vault podporují následující operace s klíčem:

- **vytvořeny** Umožňuje klientovi vytvořit klíč v Key Vault. Hodnota klíče je vygenerována Key Vault a uložená a není klientovi uvolněna. V Key Vault lze vytvořit asymetrické klíče.
- **importovat** Umožňuje klientovi importovat existující klíč do Key Vault. Asymetrické klíče mohou být importovány do Key Vault pomocí řady různých metod balení v rámci konstrukce JWK.
- **Update** Umožňuje klientovi s dostatečnými oprávněními upravovat metadata (atributy klíčů) přidružená k klíči, který byl dřív uložený v Key Vault.
- **dstranit** Povolí klientovi s dostatečnými oprávněními k odstranění klíče z Key Vault.
- **seznamu** Umožňuje klientovi zobrazit seznam všech klíčů v daném Key Vault.
- **verze seznamu:** Umožňuje klientovi zobrazit seznam všech verzí daného klíče v daném Key Vault.
- **Čtěte** Umožňuje klientovi načíst veřejné části daného klíče v Key Vault.
- **zálohovat** Exportuje klíč do chráněného formuláře.
- **obnovil** Importuje dříve zálohovaný klíč.

K dispozici je odpovídající sada oprávnění, která může být udělena uživatelům, instančním objektům nebo aplikacím pomocí Key Vaultch položek řízení přístupu, aby jim umožnila provádět klíčové operace.

Key Vault má funkci obnovitelného odstranění, která umožňuje obnovení odstraněných trezorů a klíčů. Ve výchozím nastavení není **_"obnovitelné odstranění"_** povoleno, ale po jeho povolení jsou objekty uchovávány po dobu 90 dnů (doba uchování) a zároveň se zobrazuje pro odstranění.  Dodatečné oprávnění **_"Vyprázdnit"_** umožňuje trvalé odstranění klíčů, pokud je možnost vyprázdnit **_ochranu_** zakázaná.

Vytvoření nebo Import existujícího klíče vytvoří novou verzi klíče.

### <a name="cryptographic-operations"></a>Kryptografické operace

Key Vault taky podporuje kryptografické operace pomocí klíčů:

- **podepsat a ověřit:** Tato operace je "Sign hash" nebo "verify hash". Key Vault nepodporuje Hashování obsahu v rámci vytváření podpisu.
- **šifrování a zabalení klíče:** Tato operace slouží k ochraně jiného klíče.
- **šifrování a dešifrování:** uložený klíč se používá k šifrování nebo dešifrování jednoho bloku dat.

K dispozici je odpovídající sada oprávnění, která je možné udělit uživatelům, instančním objektům nebo aplikacím pomocí Key Vaultch položek řízení přístupu, aby mohly provádět kryptografické operace.

Existují tři klíčové atributy, které mohou určovat, zda je klíč povolen a funguje kryptografické operace:

- **umožněn**
- **NBF:** před zadaným datem není povolené.
- **EXP:** datum vypršení platnosti

## <a name="storage-and-keys"></a>Úložiště a klíče

Klíče spravované zákazníkem jsou pružnější a umožňují posoudit a spravovat klíče, které mají být kontrolovány. Také umožňují auditování šifrovacích klíčů používaných k ochraně dat.
Existují tři aspekty ukládání a klíčů uložených v Key Vault:

- Key Vault klíče spravovaného účtu úložiště
- Šifrování služby Azure Storage (SSE) pro neaktivní neaktivní data
- Spravované disky a Azure Disk Encryption

Správa klíčů účtu služby Key Vault Azure Storage je rozšíření pro Key Vault klíčovou službu, která podporuje synchronizaci a regeneraci klíčů účtů úložiště (rotace).  [Azure Storage integrace s Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (verze Preview) se doporučuje, když je vydaná, protože poskytuje nejvyšší zabezpečení a snadné použití.
SSE používá ke správě Šifrování neaktivních dat dva klíče:

- Klíče šifrování klíčů (KEK)
- Šifrovací klíče dat (klíč DEK)

I když Microsoft spravuje DEKs, SSE má možnost použít zákaznickou KEK spravovanou, kterou je možné uložit v Key Vault. To umožňuje rotaci klíčů v Azure Key Vault podle odpovídajících zásad dodržování předpisů. Po otočení klíčů Azure Storage znovu zašifruje šifrovací klíč účtu pro tento účet úložiště. Výsledkem není opětovné šifrování všech dat a nevyžaduje se žádná další akce.

SSE se používá pro spravované disky, ale klíče spravované zákazníkem se nepodporují.  Šifrování spravovaných disků je možné provádět pomocí Azure Disk Encryption s KEK klíči spravovanými zákazníky v Key Vault.

## <a name="next-steps"></a>Další kroky

Přečtěte si článek o [federaci identit](identity-federation.md) .

Přečtěte si další dokumentaci Azure Key Vault a kurzy v [referenční knihovně](reference-library.md)
