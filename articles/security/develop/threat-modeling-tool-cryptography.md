---
title: Kryptografie – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
description: zmírnění hrozeb vystavených v nástroji pro modelování hrozeb
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
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728192"
---
# <a name="security-frame-cryptography--mitigations"></a>Bezpečnostní rámec: Kryptografie | Skutečnosti snižující závažnost rizika 

| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace** | <ul><li>[Používejte pouze schválené symetrické blokové šifry a délky klíčů](#cipher-length)</li><li>[Použití schválených režimů blokové šifry a inicializačních vektorů pro symetrické šifry](#vector-ciphers)</li><li>[Použití schválených asymetrických algoritmů, délek klíčů a odsazení](#padding)</li><li>[Použití schválených generátorů náhodných čísel](#numgen)</li><li>[Nepoužívejte šifry symetrického proudu](#stream-ciphers)</li><li>[Použití schválených algoritmů hash MAC/HMAC/keyed](#mac-hash)</li><li>[Používejte pouze schválené kryptografické funkce hash](#hash-functions)</li></ul> |
| **Databáze** | <ul><li>[Použití silných šifrovacích algoritmů k šifrování dat v databázi](#strong-db)</li><li>[Balíčky SSIS by měly být šifrovány a digitálně](#ssis-signed)</li><li>[Přidání digitálního podpisu do důležitých databázových seřizovatelných](#securables-db)</li><li>[Použití SQL server EKM k ochraně šifrovacích klíčů](#ekm-keys)</li><li>[Funkce AlwaysEncrypted použijte, pokud by šifrovací klíče neměly být zjevovány databázovému stroji.](#keys-engine)</li></ul> |
| **Zařízení IoT** | <ul><li>[Bezpečné ukládání kryptografických klíčů na zařízení IoT](#keys-iot)</li></ul> | 
| **Cloudová brána IoT** | <ul><li>[Generovat náhodný symetrický klíč dostatečné délky pro ověřování do služby IoT Hub](#random-hub)</li></ul> | 
| **Mobilní klient Aplikace Dynamics CRM** | <ul><li>[Ujistěte se, že jsou zavedeny zásady správy zařízení, které vyžadují použití kódu PIN a umožňují vzdálené vymazání](#pin-remote)</li></ul> | 
| **Klient aplikace Dynamics CRM Outlook** | <ul><li>[Ujistěte se, že jsou zavedeny zásady správy zařízení, které vyžadují zámek PIN/heslo/auto, a šifruje všechna data (např.](#bitlocker)</li></ul> | 
| **Server identit** | <ul><li>[Ujistěte se, že podpisové klíče jsou převráceny při použití serveru identity](#rolled-server)</li><li>[Ujistěte se, že kryptograficky silné ID klienta, tajný klíč klienta se používají v Identity Server](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Používejte pouze schválené symetrické blokové šifry a délky klíčů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Produkty musí používat pouze ty symetrické blokové šifry a související délky klíčů, které byly explicitně schváleny Crypto Advisorem ve vaší organizaci. Schválené symetrické algoritmy společnosti Microsoft zahrnují následující blokové šifry:</p><ul><li>Pro nový kód AES-128, AES-192 a AES-256 jsou přijatelné</li><li>Pro zpětnou kompatibilitu s existujícím kódem je</li><li>Pro produkty používající symetrické blokové šifry:<ul><li>Pro nový kód je vyžadován pokročilý standard šifrování (AES).</li><li>Tříklíčný trojitý standard šifrování dat (3DES) je přípustný ve stávajícím kódu pro zpětnou kompatibilitu</li><li>Všechny ostatní blokové šifry, včetně RC2, DES, 2 Key 3DES, DESX a Skipjack, mohou být použity pouze pro dešifrování starých dat a musí být nahrazeny, pokud jsou použity pro šifrování</li></ul></li><li>Pro symetrické blokové šifrovací algoritmy je vyžadována minimální délka klíče 128 bitů. Jediný blokový šifrovací algoritmus doporučený pro nový kód je AES (AES-128, AES-192 a AES-256 jsou přijatelné)</li><li>Tříklíčná 3DES je v současné době přijatelná, pokud je již používána ve stávajícím kódu; přechod na AES. DES, DESX, RC2 a Skipjack již nejsou považovány za bezpečné. Tyto algoritmy mohou být použity pouze pro dešifrování existujících dat z důvodu zpětné kompatibility a data by měla být znovu zašifrována pomocí doporučené blokové šifry.</li></ul><p>Vezměte prosím na vědomí, že všechny symetrické blokové šifry musí být použity se schváleným šifrovacím režimem, který vyžaduje použití vhodného inicializačního vektoru (IV). Vhodné IV, je obvykle náhodné číslo a nikdy konstantní hodnota</p><p>Použití starších nebo jinak neschválených kryptografických algoritmů a menších délek klíčů pro čtení existujících dat (na rozdíl od zápisu nových dat) může být povoleno po kontrole Crypto Board vaší organizace. Je však nutné soubor pro výjimku proti tomuto požadavku. Kromě toho v podnikovém nasazení by produkty měly zvážit upozornění správců při použití slabého šifrování ke čtení dat. Tato varování by měla být vysvětlující a žalovatelná. V některých případech může být vhodné, aby zásady skupiny kontrolovaly používání slabých</p><p>Povolené algoritmy .NET pro spravovanou kryptografickou agilitu (v pořadí podle priority)</p><ul><li>AesCng (kompatibilní s FIPS)</li><li>AuthenticatedAesCng (kompatibilní s FIPS)</li><li>AESCryptoServiceProvider (kompatibilní s FIPS)</li><li>AESManaged (nekompatibilní s FIPS)</li></ul><p>Vezměte prosím na vědomí, že žádný `SymmetricAlgorithm.Create` z `CryptoConfig.CreateFromName` těchto algoritmů nelze zadat pomocí nebo metody bez provedení změn v souboru machine.config. Všimněte si také, že AES ve verzích rozhraní .NET `AesCng` před `AuthenticatedAesCng` rozhraním .NET 3.5 je pojmenována `RijndaelManaged`a a jsou >k dispozici prostřednictvím CodePlex a vyžadují CNG v základním operačním systému</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Použití schválených režimů blokové šifry a inicializačních vektorů pro symetrické šifry

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Všechny symetrické blokové šifry musí být použity ve schváleném režimu symetrické šifry. Jedinými schválenými režimy jsou CBC a CTS. Zejména je třeba se vyhnout způsobu fungování elektronického kódového knihy (ECB); použití ECB vyžaduje kontrolu kryptorady vaší organizace. Veškeré použití OFB, CFB, CTR, CCM a GCM nebo jiného režimu šifrování musí být přezkoumáno crypto boardem vaší organizace. Opětovné použití stejného inicializačního vektoru (IV) s blokovými šiframi v režimech "streamování šifrovacích zařízení", jako je CTR, může způsobit odhalení šifrovaných dat. Všechny symetrické blokové šifry musí být také použity s vhodným inicializačním vektorem (IV). Vhodné IV je kryptograficky silné, náhodné číslo a nikdy konstantní hodnota. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Použití schválených asymetrických algoritmů, délek klíčů a odsazení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Používání zakázaných kryptografických algoritmů představuje významné riziko pro bezpečnost produktů a je třeba se mu vyhnout. Produkty musí používat pouze ty kryptografické algoritmy a přidružené délky klíčů a odsazení, které byly explicitně schváleny crypto boardem vaší organizace.</p><ul><li>**RSA-** mohou být použity pro šifrování, výměnu klíčů a podpis. Šifrování RSA musí používat pouze režimy odsazení OAEP nebo RSA-KEM. Existující kód může používat pkcs #1 v1.5 režim odsazení pouze pro kompatibilitu. Použití odsazení null je explicitně zakázáno. Klíče >= 2048 bitů je vyžadovánpro nový kód. Existující kód může podporovat klíče < 2048 bitů pouze pro zpětnou kompatibilitu po kontrole crypto board vaší organizace. Klíče < 1024 bitů lze použít pouze k dešifrování/ověření starých dat a musí být nahrazeny, pokud jsou použity pro operace šifrování nebo podepisování.</li><li>**ECDSA-** lze použít pouze k podpisu. ECDSA s >= 256bitové klíče je vyžadovánpro nový kód. Podpisy založené na ECDSA musí používat jednu ze tří křivek schválených NIST (P-256, P-384 nebo P521). Křivky, které byly důkladně analyzovány, mohou být použity pouze po kontrole s crypto boardem vaší organizace.</li><li>**ECDH-** lze použít pouze pro výměnu klíčů. ECDH s >= 256bitové klíče je vyžadováno pro nový kód. Výměna klíčů založená na ECDH musí používat jednu ze tří křivek schválených NIST (P-256, P-384 nebo P521). Křivky, které byly důkladně analyzovány, mohou být použity pouze po kontrole s crypto boardem vaší organizace.</li><li>**DSA-** může být přijatelné po přezkoumání a schválení od vaší organizace Crypto Board. Obraťte se na svého bezpečnostního poradce a naplánujte kontrolu kryptografické rady vaší organizace. Pokud je vaše používání DSA schváleno, vezměte na vědomí, že budete muset zakázat používání klíčů kratších než 2048 bitů. CNG podporuje 2048bitové a větší délky klíčů od Windows 8.</li><li>**Diffie-Hellman-** lze použít pouze pro správu klíčů relace. Délka klíče >= 2048 bitů je vyžadována pro nový kód. Existující kód může podporovat délky klíčů < 2048 bitů pouze pro zpětnou kompatibilitu po kontrole crypto board vaší organizace. Klíče < 1024 bitů nelze použít.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Použití schválených generátorů náhodných čísel

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Výrobky musí používat schválené generátory náhodných čísel. Pseudonáhodné funkce, jako je například rand funkce runtime Jazyka C, třída .NET Framework System.Random nebo systémové funkce, jako je Například GetTickCount, proto musí být nikdy použity v takovém kódu. Použití algoritmu generátoru náhodných čísel (DUAL_EC_DRBG) s dvojitou eliptickou křivkou je zakázáno</p><ul><li>**CNG-** BCryptGenRandom(použití příznaku BCRYPT_USE_SYSTEM_PREFERRED_RNG doporučenou, pokud volající může spustit na jakékoli IRQL větší než 0 [to je, PASSIVE_LEVEL])</li><li>**CAPI-** kryptaGenRandom</li><li>**Win32/64, 2.** RtlGenRandom (nové implementace by měly používat BCryptGenRandom nebo CryptGenRandom) * rand_s * SystemPrng (pro režim jádra)</li><li>**. NET-** RNGCryptoServiceProvider nebo RNGCng</li><li>**Aplikace pro Windows Store-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom nebo . GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS (2.0+)-** int SecRandomCopyBytes (SecRandomRef náhodné, size_t počet, uint8_t \*bajty)</li><li>**Apple OS X (<10.7)-** Použití /dev/random k načtení náhodných čísel</li><li>**Java (včetně google android java kód) -** java.security.SecureRandom třídy. Všimněte si, že pro Android 4.3 (Jelly Bean), vývojáři musí dodržovat android doporučené řešení a aktualizovat své aplikace explicitně inicializovat PRNG s entropie z /dev/urandom nebo /dev/random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Nepoužívejte šifry symetrického proudu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Šifry symetrického proudu, například RC4, se nesmí používat. Namísto šifer symetrického proudu by produkty měly používat blokovou šifru, konkrétně AES s délkou klíče nejméně 128 bitů. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Použití schválených algoritmů hash MAC/HMAC/keyed

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Produkty musí používat pouze schválené ověřovací kód zprávy (MAC) nebo algoritmy ověřování zpráv založených na algoritmu hash (HMAC).</p><p>Ověřovací kód zprávy (MAC) je informace připojená ke zprávě, která umožňuje příjemci ověřit pravost odesílatele i integritu zprávy pomocí tajného klíče. Použití MAC ( HMAC ) založeného na hash ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) nebo [MAC založeného na blokové šifrě](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) je přípustné, pokud jsou všechny základní algoritmy hash nebo symetrického šifrování také schváleny pro použití; v současné době to zahrnuje funkce HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 a HMAC-SHA512) a cmac/OMAC1 a blokové macs založené na blokové šifrované (ty jsou založeny na AES).</p><p>Použití HMAC-SHA1 může být přípustné pro kompatibilitu platformy, ale budete muset podat výjimku z tohoto postupu a podstoupit kontrolu kryptografických údajů vaší organizace. Zkrácení hcc na méně než 128 bitů není povoleno. Použití zákaznických metod k hash klíče a dat není schváleno a musí před použitím projít kontrolou Crypto Board vaší organizace.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Používejte pouze schválené kryptografické funkce hash

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Produkty musí používat sha-2 řady algoritmů hash (SHA256, SHA384 a SHA512). Pokud je potřeba kratší hash, jako je například 128bitová délka výstupu, aby se vešla do datové struktury navržené s ohledem na kratší hash MD5, mohou týmy produktů zkrátit jeden z hash SHA2 (obvykle SHA256). Všimněte si, že SHA384 je zkrácená verze SHA512. Zkrácení kryptografických hash z bezpečnostních důvodů na méně než 128 bitů není povoleno. Nový kód nesmí používat algoritmy hash MD2, MD4, MD5, SHA-0, SHA-1 nebo RIPEMD. Kolize hash jsou pro tyto algoritmy výpočtově proveditelné, což je účinně přeruší.</p><p>Povolené algoritmy hash rozhraní .NET pro spravovanou kryptografickou agilitu (v pořadí podle priority):</p><ul><li>SHA512Cng (kompatibilní s FIPS)</li><li>SHA384Cng (kompatibilní s FIPS)</li><li>SHA256Cng (kompatibilní s FIPS)</li><li>SHA512Managed (nekompatibilní s FIPS) (použijte SHA512 jako název algoritmu při volání HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA384Managed (nekompatibilní s FIPS) (použijte SHA384 jako název algoritmu při volání HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA256Managed (nekompatibilní s FIPS) (použijte SHA256 jako název algoritmu při volání HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (kompatibilní s FIPS)</li><li>SHA256CryptoServiceProvider (kompatibilní s FIPS)</li><li>SHA384CryptoServiceProvider (kompatibilní s FIPS)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Použití silných šifrovacích algoritmů k šifrování dat v databázi

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Výběr šifrovacího algoritmu](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Kroky** | Šifrovací algoritmy definují transformace dat, které nelze snadno vrátit neoprávněnými uživateli. SQL Server umožňuje správcům a vývojářům vybrat si z několika algoritmů, včetně DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bit RC4, DESX, 128-bit AES, 192-bit AES, a 256-bit AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>Balíčky SSIS by měly být šifrovány a digitálně

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Identifikace zdroje balíčků s digitálními podpisy](https://msdn.microsoft.com/library/ms141174.aspx), [ohrožení a zmírnění zranitelnosti (Integrační služby)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Kroky** | Zdrojem balíčku je jednotlivec nebo organizace, která balíček vytvořila. Spuštění balíčku z neznámého nebo nedůvěryhodného zdroje může být riskantní. Aby se zabránilo neoprávněné neoprávněné manipulaci s balíčky SSIS, měly by být použity digitální podpisy. Aby byla zajištěna důvěrnost balíků během skladování/přepravy, musí být balíky SSIS šifrovány |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Přidání digitálního podpisu do důležitých databázových seřizovatelných

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [PŘIDAT PODPIS (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Kroky** | V případech, kdy musí být ověřena integrita kritické databáze, by měly být použity digitální podpisy. Databáze securables, jako je například uložená procedura, funkce, sestavení nebo aktivační událost mohou být digitálně podepsány. Níže je uveden příklad, kdy to může být užitečné: Řekněme, že nezávislý dodavatel softwaru (ISV) poskytl podporu softwaru dodaného jednomu z jejich zákazníků. Před poskytnutím podpory by isv chtít zajistit, aby databáze sevyléčitelná v softwaru nebyla zfalšována omylem nebo škodlivým pokusem. Pokud je sekuritivitelný digitálně podepsán, může isv ověřit svůj digitální podpis a ověřit jeho integritu.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Použití SQL server EKM k ochraně šifrovacích klíčů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Rozšiřitelná správa klíčů SQL Serveru (EKM)](https://msdn.microsoft.com/library/bb895340), [rozšiřitelná správa klíčů pomocí trezoru klíčů Azure (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Kroky** | Rozšiřitelná správa klíčů sql serveru umožňuje ukládání šifrovacích klíčů, které chrání databázové soubory, v off-boxovém zařízení, jako je karta SmartCard, zařízení USB nebo modul EKM/HSM. To také umožňuje ochranu dat před správci databáze (s výjimkou členů skupiny sysadmin). Data lze šifrovat pomocí šifrovacích klíčů, ke kterým má přístup pouze uživatel databáze v externím modulu EKM/HSM. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Funkce AlwaysEncrypted použijte, pokud by šifrovací klíče neměly být zjevovány databázovému stroji.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure, OnPrem |
| **Atributy**              | VERZE SQL - V12, MsSQL2016 |
| **Odkazy**              | [Vždy šifrované (databázový stroj)](https://msdn.microsoft.com/library/mt163865) |
| **Kroky** | Vždy šifrované je funkce určená k ochraně citlivých dat, jako jsou čísla kreditních karet nebo národní identifikační čísla (např. čísla sociálního pojištění v USA), uložená v databázích Azure SQL Database nebo SQL Server. Vždy šifrované umožňuje klientům šifrovat citlivá data uvnitř klientských aplikací a nikdy odhalit šifrovací klíče databázového stroje (SQL Database nebo SQL Server). V důsledku toho vždy šifrované poskytuje oddělení mezi těmi, kteří vlastní data (a mohou je zobrazit) a těmi, kteří spravují data (ale neměli by mít přístup) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Bezpečné ukládání kryptografických klíčů na zařízení IoT

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Operační systém zařízení – jádro Windows IoT Core, připojení zařízení – sady SDK pro zařízení Azure IoT |
| **Odkazy**              | [Čip TPM v jádru Windows IoT](https://developer.microsoft.com/windows/iot/docs/tpm), [Nastavení čipu TPM na jádru Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [Čip TPM sady Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroky** | Symetrické klíče nebo soukromé klíče certifikátu bezpečně v hardwarově chráněném úložišti, jako jsou čipy TPM nebo Čipové karty. Windows 10 IoT Core podporuje uživatele čipu TPM a existuje několik kompatibilních tpms, které lze použít: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Doporučujeme používat firmware nebo diskrétní čip TPM. Čip TPM softwaru by měl být používán pouze pro účely vývoje a testování. Jakmile je čip TPM k dispozici a klíče jsou v něm zřízeny, kód, který generuje token by měl být zapsán bez pevného kódování žádné citlivé informace v něm. | 

### <a name="example"></a>Příklad
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak je vidět, primární klíč zařízení není k dispozici v kódu. Místo toho je uložen v čipu TPM na slotu 0. Zařízení TPM generuje krátkodobý token SAS, který se pak používá k připojení k centru IoT Hub. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Generovat náhodný symetrický klíč dostatečné délky pro ověřování do služby IoT Hub

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | IoT Hub obsahuje registr identit zařízení a při zřizování zařízení automaticky generuje náhodný symetrický klíč. Doporučujeme použít tuto funkci registru identit služby Azure IoT Hub ke generování klíče používaného k ověřování. IoT Hub také umožňuje zadat klíč při vytváření zařízení. Pokud je klíč generován mimo IoT Hub během zřizování zařízení, doporučuje se vytvořit náhodný symetrický klíč nebo alespoň 256 bitů. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Ujistěte se, že jsou zavedeny zásady správy zařízení, které vyžadují použití kódu PIN a umožňují vzdálené vymazání

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient Aplikace Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že jsou zavedeny zásady správy zařízení, které vyžadují použití kódu PIN a umožňují vzdálené vymazání |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Ujistěte se, že jsou zavedeny zásady správy zařízení, které vyžadují zámek PIN/heslo/auto, a šifruje všechna data (např.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Klient aplikace Dynamics CRM Outlook | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že jsou zavedeny zásady správy zařízení, které vyžadují zámek PIN/heslo/auto, a šifruje všechna data (např. |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Ujistěte se, že podpisové klíče jsou převráceny při použití serveru identity

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Identity Server - klíče, podpisy a kryptografie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroky** | Ujistěte se, že podpisové klíče jsou vráceny při použití serveru identity. Odkaz v části odkazy vysvětluje, jak by to mělo být plánováno, aniž by došlo k výpadkům aplikací mj. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Ujistěte se, že kryptograficky silné ID klienta, tajný klíč klienta se používají v Identity Server

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Ujistěte se, že kryptograficky silné ID klienta, tajný klíč klienta se používají v Identity Server. Při generování ID klienta a tajného klíče by měly být použity následující pokyny:</p><ul><li>Generovat náhodný identifikátor GUID jako ID klienta</li><li>Generovat kryptograficky náhodný 256bitový klíč jako tajný klíč</li></ul>|
