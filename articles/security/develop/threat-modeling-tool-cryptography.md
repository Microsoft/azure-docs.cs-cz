---
title: Kryptografie-Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Přečtěte si o zmírňování kryptografických služeb pro hrozby vystavené v Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 6af4fba8ddf50b795d847a2c7b4e2fbc02fe593f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587175"
---
# <a name="security-frame-cryptography--mitigations"></a>Rámec zabezpečení: kryptografie | Hrozeb 

| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace** | <ul><li>[Používejte jenom schválené symetrické blokové šifry a délky klíčů.](#cipher-length)</li><li>[Použít schválené režimy kódování bloku a inicializační vektory pro symetrické šifry](#vector-ciphers)</li><li>[Použití schválených asymetrických algoritmů, délek klíčů a odsazení](#padding)</li><li>[Použít schválené generátory náhodných čísel](#numgen)</li><li>[Nepoužívejte šifry symetrického streamu.](#stream-ciphers)</li><li>[Použijte schválené algoritmy hash MAC/HMAC/klíčů.](#mac-hash)</li><li>[Používejte jenom schválené kryptografické funkce hash.](#hash-functions)</li></ul> |
| **Databáze** | <ul><li>[Použití algoritmu silného šifrování k šifrování dat v databázi](#strong-db)</li><li>[Balíčky SSIS by měly být šifrované a digitálně podepsané](#ssis-signed)</li><li>[Přidat digitální podpis do důležitých zabezpečených databází](#securables-db)</li><li>[Použití SQL serveru EKM k ochraně šifrovacích klíčů](#ekm-keys)</li><li>[Použít funkci AlwaysEncrypted, pokud by se šifrovací klíče neměly vyvažovat do databázového stroje](#keys-engine)</li></ul> |
| **Zařízení IoT** | <ul><li>[Bezpečné ukládání kryptografických klíčů na zařízení IoT](#keys-iot)</li></ul> | 
| **Cloudová brána IoT** | <ul><li>[Vygenerujte náhodný symetrický klíč dostatečné délky pro ověřování IoT Hub](#random-hub)</li></ul> | 
| **Mobilní klient Dynamics CRM** | <ul><li>[Ujistěte se, že zásady správy zařízení jsou zavedeny, které vyžadují použití PIN a umožňují vzdálené vymazání.](#pin-remote)</li></ul> | 
| **Klient Dynamics CRM pro Outlook** | <ul><li>[Zajistěte, aby byly nastavené zásady správy zařízení, které vyžadují PIN/heslo nebo automatické uzamčení a šifrují všechna data (např. BitLocker).](#bitlocker)</li></ul> | 
| **Server identit** | <ul><li>[Zajistěte, aby se při použití serveru identity převzaly podpisové klíče.](#rolled-server)</li><li>[Ujistěte se, že kryptograficky silné ID klienta se používá na serveru identity.](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Používejte jenom schválené symetrické blokové šifry a délky klíčů.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Produkty musí používat pouze ta symetrická bloková šifra a přidružené délky klíčů, které byly explicitně schváleny kryptografickým poradcem ve vaší organizaci. Mezi schválené symetrické algoritmy v Microsoftu patří následující blokové šifry:</p><ul><li>Pro nový kód AES-128, AES-192 a AES-256 jsou přijatelné.</li><li>Z důvodu zpětné kompatibility se stávajícím kódem je přijatelné tři klíče 3DES.</li><li>Pro produkty používající symetrické blokové šifry:<ul><li>Pro nový kód se vyžaduje standard AES (Advanced Encryption Standard) (AES).</li><li>Algoritmus 3DES (Triple Data Encryption Standard) se třemi klíči je přípustný v existujícím kódu pro zpětnou kompatibilitu.</li><li>Všechna ostatní bloková šifra, včetně RC2, DES, 2 klíče 3DES, DESX a SkipJack, se dají použít jenom k dešifrování starých dat a musí se nahradit, pokud se používá pro šifrování.</li></ul></li><li>Pro algoritmy šifrování symetrického bloku se vyžaduje minimální délka klíče 128 bitů. Jediný blokový šifrovací algoritmus doporučený pro nový kód je AES (AES-128, AES-192 a AES-256 jsou všechny přijatelné)</li><li>Pokud se už v existujícím kódu používá, je použit algoritmus 3DES se třemi klíči. Přechod na AES je doporučený. Šifrování DES, DESX, RC2 a SkipJack se již nepovažují za bezpečné. Tyto algoritmy se dají použít jenom k dešifrování stávajících dat z důvodu zpětné kompatibility a data by se měla znovu šifrovat pomocí Doporučené blokové šifry.</li></ul><p>Všimněte si, že všechny šifry symetrického bloku musí být použity s schváleným režimem šifry, který vyžaduje použití příslušného inicializačního vektoru (IV). Vhodným IV, je obvykle náhodné číslo a nikdy konstantní hodnota.</p><p>Použití starších nebo jiných neschválených šifrovacích algoritmů a kratších délek klíčů pro čtení stávajících dat (na rozdíl od psaní nových dat) může být po kontrole šifrovacích panelů vaší organizace povolené. Před tímto požadavkem však musíte soubor pro výjimku. V podnikových nasazeních by se navíc měly zvážit správci upozornění, když se ke čtení dat používá slabé šifrování. Taková upozornění by měla být vysvětlující a jednat. V některých případech může být vhodné, aby Zásady skupiny řídit použití slabého šifrování.</p><p>Povolené algoritmy .NET pro flexibilitu spravovaného šifrování (v upřednostňovaném pořadí)</p><ul><li>AesCng (kompatibilní se standardem FIPS)</li><li>AuthenticatedAesCng (kompatibilní se standardem FIPS)</li><li>AESCryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>AESManaged (není kompatibilní se standardem FIPS)</li></ul><p>Počítejte s tím, že žádný z těchto algoritmů nelze zadat `SymmetricAlgorithm.Create` prostřednictvím `CryptoConfig.CreateFromName` metod nebo bez provedení změn v souboru machine.config. Všimněte si také, že AES ve verzích .NET předcházejících .NET 3,5 se nazývá `RijndaelManaged` a `AesCng` a `AuthenticatedAesCng` jsou >k dispozici prostřednictvím CODEPLEX a vyžádání CNG v PODKLADovém operačním systému.</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Použít schválené režimy kódování bloku a inicializační vektory pro symetrické šifry

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Všechny šifry symetrického bloku musí být použity s schváleným režimem symetrické šifry. Jediným schváleným režimem jsou CBC a CTS. Zejména je třeba zabránit režimu operace elektronického kódu v knize (ECB); použití ECB vyžaduje kontrolu šifrovacích Board vaší organizace. Veškeré využití OFB, CFB, centra, CCM a GCM nebo jakéhokoli jiného režimu šifrování musí být zkontrolováno pomocí kryptografické desky vaší organizace. Opětovné použití stejného inicializačního vektoru (IV) s blokujícími šiframi v režimech šifry streamování, jako je například centrum, může způsobit zobrazení šifrovaných dat. Všechna symetrická bloková šifra musí být také použita s vhodným inicializačním vektorem (IV). Vhodným IV je kryptograficky silné, náhodné číslo a nikdy konstantní hodnota. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Použití schválených asymetrických algoritmů, délek klíčů a odsazení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Použití zakázaných kryptografických algoritmů přináší významné riziko pro zabezpečení produktu a je třeba se jim vyhnout. Produkty musí používat jenom ty kryptografické algoritmy a přidružené délky klíčů a jejich odsazení, které byly explicitně schválené kryptografickým panelem vaší organizace.</p><ul><li>**RSA –** může být používán k šifrování, výměně klíčů a podpisu. Šifrování RSA musí používat pouze režimy vyplňování výplně OAEP nebo RSA-KEM. Existující kód může používat pouze režim odsazení PKCS #1 v 1.5 pouze pro kompatibilitu. Použití odsazení null je explicitně zakázáno. Pro nový kód se vyžadují klíče >= 2048 bitů. Existující kód může podporovat klíče < 2048 bitů jenom pro zpětnou kompatibilitu po kontrole pomocí kryptografické karty vaší organizace. Klíče < 1024 bity se dají použít jenom k dešifrování/ověřování starých dat a musí se nahradit, pokud se použijí pro operace šifrování nebo podepisování.</li><li>**ECDSA –** lze použít pouze pro podpis. Pro nový kód se vyžaduje ECDSA s >= 256-bit klíčů. Signatury založené na ECDSA musí používat jednu ze tří NIST schválených křivek (P-256, P-384 nebo P521). Křivky, které byly důkladně analyzovány, mohou být použity pouze po kontrole pomocí kryptografické karty vaší organizace.</li><li>**ECDH** se dá použít jenom pro výměnu klíčů. Pro nový kód je vyžadován ECDH s >= 256-bit klíčů. Výměna klíčů na bázi ECDH musí používat jednu ze tří NIST schválených křivek (P-256, P-384 nebo P521). Křivky, které byly důkladně analyzovány, mohou být použity pouze po kontrole pomocí kryptografické karty vaší organizace.</li><li>**DSA –** může být přijatelné po kontrole a schvalování z kryptografické karty vaší organizace. Pokud chcete naplánovat kontrolu šifrovacích panelů vaší organizace, obraťte se na svého bezpečnostního poradce. Pokud je vaše používání služby DSA schváleno, je třeba zakázat použití klíčů kratších než 2048 bitů. CNG podporuje 2048 a větší délky klíčů pro systém Windows 8.</li><li>**Diffie-Hellman** se dá použít jenom pro správu klíčů relace. Pro nový kód se vyžaduje délka klíče >= 2048 bitů. Existující kód může podporovat délky klíčů < 2048 bitů jenom pro zpětnou kompatibilitu po kontrole pomocí kryptografické karty vaší organizace. Klíče < 1024 bity se nedají použít.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Použít schválené generátory náhodných čísel

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Produkty musí používat schválené generátory náhodných čísel. Pseudonáhodných funkce, jako je funkce modulu runtime jazyka C Rand, .NET Framework třídy System. Random nebo funkce systému, jako je například funkce GetTickCount, proto musí být v takovém kódu nikdy použity. Použití algoritmu Dual-eliptické křivky generátoru náhodných čísel (DUAL_EC_DRBG) je zakázané.</p><ul><li>**CNG –** BCryptGenRandom (použití příznaku BCRYPT_USE_SYSTEM_PREFERRED_RNG doporučuje se, pokud volající nebude možné spustit na jakékoli úrovni IRQL větší než 0 [to znamená, PASSIVE_LEVEL])</li><li>**CAPI –** cryptGenRandom</li><li>**Win32/64 –** RtlGenRandom (nové implementace by měly používat BCryptGenRandom nebo CryptGenRandom) * rand_s * SystemPrng (pro režim jádra)</li><li>**. NET-** RNGCryptoServiceProvider nebo RNGCng</li><li>**Aplikace pro Windows Store –** Windows. Security. Cryptography. CryptographicBuffer. GenerateRandom nebo. GenerateRandomNumber</li><li>**Apple OS X (10.7 +)/iOS. (2.0 +)-** int SecRandomCopyBytes (SecRandomRef náhodný size_t počet uint8_t \* bajtů)</li><li>**Apple OS X (<10,7)-** Načtení náhodných čísel pomocí/dev/random</li><li>**Java (včetně kódu Google Android Java) –** Třída Java. Security. SecureRandom Upozorňujeme, že pro Android 4,3 (želé Bob) musí vývojáři postupovat podle doporučeného alternativního řešení pro Android a aktualizovat své aplikace tak, aby explicitně inicializoval PRNG s entropií z/dev/urandom nebo/dev/random.</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Nepoužívejte šifry symetrického streamu.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Šifry symetrických datových proudů, jako je například RC4, nesmí být použity. Místo šifry symetrických streamů by produkty měly používat blokové šifry, konkrétně AES s délkou klíče minimálně 128 bitů. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Použijte schválené algoritmy hash MAC/HMAC/klíčů.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Produkty musí používat pouze schválené algoritmy MAC (Message Authentication Code) nebo HMAC (hash-based Message Authentication Code).</p><p>Ověřovací kód zprávy (MAC) je informační zpráva připojená ke zprávě, která umožňuje jejímu příjemci ověřit pravost odesílatele a integritu zprávy pomocí tajného klíče. Použití rozhraní MAC (HMAC) založeného na algoritmu hash ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) nebo systému [Mac založeného na bloku](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) šifrování je přípustné, pokud jsou také schváleny všechny základní algoritmy hash nebo symetrické šifrování. v současné době zahrnuje funkce HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 a HMAC-SHA512) a Mac/CMAC a OMAC1 Block-based šifry (jsou založené na standardu AES).</p><p>Pro kompatibilitu platforem může být povoleno použití algoritmu HMAC-SHA1, ale pro tento postup bude nutné zadat výjimku a podstoupit kontrolu šifrování vaší organizace. Zkrácení HMAC na méně než 128 bitů není povoleno. Použití zákaznických metod k vytvoření hodnoty hash klíče a dat není schváleno a před použitím se musí projít kontrolou šifrovacích Board vaší organizace.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Používejte jenom schválené kryptografické funkce hash.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Produkty musí používat rodinu SHA-2 algoritmů hash (SHA256, SHA384 a SHA512). Pokud je potřeba kratší hodnota hash, jako je například 128 délka výstupu, aby odpovídala datové struktuře, která je navržena kratší hodnotou hash MD5, může tým produktů zkrátit jednu z hodnot hash SHA2 (obvykle SHA256). Všimněte si, že SHA384 je zkrácená verze SHA512. Zkrácení kryptografických hodnot hash pro účely zabezpečení na méně než 128 bitů není povoleno. Nový kód nesmí používat algoritmy hash MD2, MD4, MD5, SHA-0, SHA-1 nebo RIPEMD. Kolize algoritmu hash jsou pro tyto algoritmy prakticky proveditelné, což je efektivně rozdělené.</p><p>Povolené algoritmy hash .NET pro flexibilitu spravovaného kryptografie (v pořadí podle priority):</p><ul><li>SHA512Cng (kompatibilní se standardem FIPS)</li><li>SHA384Cng (kompatibilní se standardem FIPS)</li><li>SHA256Cng (kompatibilní se standardem FIPS)</li><li>SHA512Managed (nekompatibilní se standardem FIPS) (jako název algoritmu použijte SHA512 při voláních HashAlgorithm. Create nebo objektu CryptoConfig. CreateFromName)</li><li>SHA384Managed (nekompatibilní se standardem FIPS) (jako název algoritmu použijte SHA384 při voláních HashAlgorithm. Create nebo objektu CryptoConfig. CreateFromName)</li><li>SHA256Managed (nekompatibilní se standardem FIPS) (jako název algoritmu použijte SHA256 při voláních HashAlgorithm. Create nebo objektu CryptoConfig. CreateFromName)</li><li>SHA512CryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>SHA256CryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>SHA384CryptoServiceProvider (kompatibilní se standardem FIPS)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Použití algoritmu silného šifrování k šifrování dat v databázi

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Výběr šifrovacího algoritmu](/sql/relational-databases/security/encryption/choose-an-encryption-algorithm) |
| **Kroky** | Algoritmy šifrování definují transformace dat, které nelze snadno vrátit neoprávněnými uživateli. SQL Server umožňuje správcům a vývojářům vybírat z několika algoritmů, včetně DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bit RC4, DESX, 128 AES, 192-bit AES 256 a 16bitového AES. |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>Balíčky SSIS by měly být šifrované a digitálně podepsané

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Identifikace zdroje balíčků s digitálními podpisy](/sql/integration-services/security/identify-the-source-of-packages-with-digital-signatures), [zmírnění hrozeb a ohrožení zabezpečení (integrační služby)](/sql/integration-services/security/security-overview-integration-services) |
| **Kroky** | Zdroj balíčku je jednotlivec nebo organizace, které balíček vytvořily. Spuštění balíčku z neznámého nebo nedůvěryhodného zdroje může být riskantní. Aby se zabránilo neoprávněné manipulaci s balíčky SSIS, měli byste použít digitální podpisy. Aby bylo zajištěno utajení balíčků během úložiště nebo přenosu, musí být balíčky SSIS zašifrované |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Přidat digitální podpis do důležitých zabezpečených databází

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [PŘIDAT podpis (Transact-SQL)](/sql/t-sql/statements/add-signature-transact-sql) |
| **Kroky** | V případech, kdy je nutné ověřit integritu důležité databáze, je třeba použít digitální podpisy. Zabezpečitelné databáze, jako je uložená procedura, funkce, sestavení nebo Trigger, mohou být digitálně podepsány. Níže je uveden příklad, kdy může být užitečný: řekněme, že nezávislý výrobce softwaru (ISV) poskytuje podporu softwaru poskytovaného jednomu ze svých zákazníků. Před poskytnutím podpory by ISV chtěla zajistit, že databáze zabezpečená v softwaru nebyla úmyslně poškozená ani omylem nebo škodlivým pokusem. Pokud je zabezpečitelný podpis digitálně podepsaný, ISV může ověřit jeho digitální podpis a ověřit jeho integritu.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Použití SQL serveru EKM k ochraně šifrovacích klíčů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [SQL Server Extensible Key Management (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm), [rozšiřitelná správa klíčů pomocí Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) |
| **Kroky** | SQL Server rozšiřitelná Správa klíčů umožňuje šifrovací klíče, které chrání soubory databáze, aby byly uložené v nechráněném zařízení, jako je čipová karta, zařízení USB nebo modul EKM/HSM. Tato možnost také umožňuje ochranu dat od správců databáze (s výjimkou členů skupiny sysadmin). Data je možné šifrovat pomocí šifrovacích klíčů, ke kterým má přístup jenom uživatel databáze, k externímu modulu EKM/HSM. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Použít funkci AlwaysEncrypted, pokud by se šifrovací klíče neměly vyvažovat do databázového stroje

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure, OnPrem |
| **Atributy**              | SQL verze – V12, MsSQL2016 |
| **Reference**              | [Always Encrypted (databázový stroj)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |
| **Kroky** | Always Encrypted je funkce navržená tak, aby chránila citlivá data, jako jsou čísla kreditních karet nebo národní identifikační čísla (např. čísla sociálního pojištění USA) uložená v databázích Azure SQL Database nebo SQL Server. Always Encrypted umožňuje klientům šifrovat citlivá data v klientských aplikacích a nikdy Neodhalovat šifrovací klíče databázovému stroji (SQL Database nebo SQL Server). V důsledku toho Always Encrypted poskytuje oddělení mezi osobami, které data vlastní (a můžou zobrazit), a osobami, které data spravují (ale nemají přístup). |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Bezpečné ukládání kryptografických klíčů na zařízení IoT

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Operační systém zařízení – Windows IoT Core, připojení zařízení – sady SDK pro zařízení Azure IoT |
| **Reference**              | [TPM ve Windows IoT Core](/windows/iot-core/secure-your-device/TPM), [Nastavení čipu TPM v systému Windows IoT Core](/windows/iot-core/secure-your-device/setuptpm), [čip TPM sady SDK pro zařízení Azure IoT](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroky** | Symetrický nebo soukromý klíč certifikátu je bezpečně v úložišti chráněném hardwarem, jako je čip TPM nebo čipy čipových karet. Windows 10 IoT Core podporuje uživatele TPM a existuje několik kompatibilních čipy TPM, které je možné použít: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm . Doporučuje se použít firmware nebo diskrétní čip TPM. ČIP TPM softwaru by se měl používat jenom pro účely vývoje a testování. Jakmile bude čip TPM k dispozici a klíče jsou v něm zřízeny, kód, který vygeneruje token, by měl být napsán bez pevného kódování jakýchkoli citlivých informací. | 

### <a name="example"></a>Příklad
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak vidíte, primární klíč zařízení není v kódu přítomen. Místo toho je uložen v čipu TPM na pozici 0. Zařízení TPM generuje krátkodobý token SAS, který se pak použije pro připojení k IoT Hub. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Vygenerujte náhodný symetrický klíč dostatečné délky pro ověřování IoT Hub

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Reference**              | –  |
| **Kroky** | IoT Hub obsahuje registr identit zařízení a při zřizování zařízení automaticky generuje náhodný symetrický klíč. K vygenerování klíče používaného pro ověřování se doporučuje použít tuto funkci registru Azure IoT Hub identity. IoT Hub také umožňuje zadat klíč při vytváření zařízení. Pokud se klíč generuje mimo IoT Hub během zřizování zařízení, doporučuje se vytvořit náhodný symetrický klíč nebo aspoň 256 bitů. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Ujistěte se, že zásady správy zařízení jsou zavedeny, které vyžadují použití PIN a umožňují vzdálené vymazání.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Ujistěte se, že zásady správy zařízení jsou zavedeny, které vyžadují použití PIN a umožňují vzdálené vymazání. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Zajistěte, aby byly nastavené zásady správy zařízení, které vyžadují PIN/heslo nebo automatické uzamčení a šifrují všechna data (např. BitLocker).

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Klient Dynamics CRM pro Outlook | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Zajistěte, aby byly nastavené zásady správy zařízení, které vyžadují PIN/heslo nebo automatické uzamčení a šifrují všechna data (např. BitLocker). |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Zajistěte, aby se při použití serveru identity převzaly podpisové klíče.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Server identity – klíče, podpisy a kryptografie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroky** | Zajistěte, aby se při použití serveru identity převzaly podpisové klíče. Odkaz v části odkazy vysvětluje, jak by se to mělo naplánovat, aniž by to mělo za následek výpadkům aplikací, které se spoléhají na server identit. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Ujistěte se, že kryptograficky silné ID klienta se používá na serveru identity.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Ujistěte se, že kryptograficky silné ID klienta se používá na serveru identit. Při generování ID klienta a tajného klíče byste měli použít následující pokyny:</p><ul><li>Vygenerovat náhodný identifikátor GUID jako ID klienta</li><li>Generovat kryptograficky náhodný 256 bitový klíč jako tajný kód</li></ul>|