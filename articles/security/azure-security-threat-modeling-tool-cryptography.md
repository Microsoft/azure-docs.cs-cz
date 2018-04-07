---
title: Kryptografie – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: způsoby zmírnění hrozeb, které jsou zveřejněné v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 5e5d487c4c793a49ce1d4ac17f6fcd672e09bb90
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="security-frame-cryptography--mitigations"></a>Rámce zabezpečení: Kryptografie | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Webové aplikace** | <ul><li>[Používejte pouze šifry schválené symetrický bloku a délky klíčů](#cipher-length)</li><li>[Použití schváleno režimy šifrování bloku a inicializační vektory pro symetrické šifry](#vector-ciphers)</li><li>[Použít schválené asymetrických algoritmů, délek klíčů a odsazení](#padding)</li><li>[Použití schváleno generátory náhodných čísel](#numgen)</li><li>[Nepoužívejte šifry symetrický datového proudu](#stream-ciphers)</li><li>[Použití schváleno MAC, HMAC/keyed algoritmů hash](#mac-hash)</li><li>[Použít pouze funkce schválené kryptografické hodnoty hash](#hash-functions)</li></ul> |
| **Database** | <ul><li>[Používat silné šifrování algoritmy šifrování dat v databázi](#strong-db)</li><li>[Balíčky SSIS by měl být zašifrované a digitálně podepsané](#ssis-signed)</li><li>[Přidat digitální podpis na zabezpečitelné prostředky, kritické databáze](#securables-db)</li><li>[Použijte k ochraně šifrovací klíče SQL server EKM.](#ekm-keys)</li><li>[Funkci AlwaysEncrypted používat v případě, že šifrovací klíče nesmí zjištěny pro databázový stroj](#keys-engine)</li></ul> |
| **Zařízení IoT** | <ul><li>[Bezpečně uložit šifrovací klíče v zařízení IoT](#keys-iot)</li></ul> | 
| **Brána IoT cloudu** | <ul><li>[Generoval náhodný symetrický klíč dostatečně dlouhé pro ověřování do služby IoT Hub](#random-hub)</li></ul> | 
| **Dynamics CRM mobilního klienta** | <ul><li>[Ujistěte se, že k zásadě správy zařízení je na místě, který vyžaduje použití kódu PIN a umožňuje vzdálené vymazání](#pin-remote)</li></ul> | 
| **Klient aplikace Outlook Dynamics CRM** | <ul><li>[Zkontrolujte, zda že je k zásadě správy zařízení v místě, ke kterému se vyžaduje PIN kód nebo heslo nebo automatické uzamčení a šifruje všechna data (např. Bitlocker)](#bitlocker)</li></ul> | 
| **Serveru identit** | <ul><li>[Ujistěte se, že podpisové klíče jsou převracet při použití serveru identit](#rolled-server)</li><li>[Zkontrolujte, zda ID kryptograficky silnou klienta, sdílený tajný klíč klienta jsou používány serveru identit](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Používejte pouze šifry schválené symetrický bloku a délky klíčů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat jenom tyto šifry symetrický bloku a přidružené délky klíčů, které byly explicitně schváleny Advisor kryptografických ve vaší organizaci. Schválené symetrické algoritmy ve společnosti Microsoft zahrnují následující šifry bloku:</p><ul><li>Pro nový kód jsou přijatelné AES-128, AES 192 a AES 256</li><li>Pro zpětnou kompatibilitu s existující kód je přijatelné tři klíč 3DES.</li><li>Pro produkty pomocí šifry symetrický bloku:<ul><li>Advanced Encryption (Standard AES) je vyžadována pro nový kód</li><li>Tři klíč triple Data Encryption Standard (3DES) je povolený v existujícím kódu z důvodu zpětné kompatibility</li><li>Všechny ostatní bloku šiframi, včetně RC2, DES, 2 klíč 3DES, DESX a pruhovaného, lze použít pouze pro dešifrování stará data a pokud se používá pro šifrování, je nutné nahradit</li></ul></li><li>U bloku symetrický šifrovací algoritmy se vyžaduje minimální délku klíče 128 bitů. Pouze šifrovací algoritmus bloku doporučeno pro nový kód je AES (AES-128, AES 192 a AES 256 jsou všechny přijatelná)</li><li>V existujícím kódu; je aktuálně přijatelné, pokud je v 3DES tři klíč doporučuje se přechod AES. DES, DESX, RC2 a pruhovaného jsou už považované za bezpečné. Tyto algoritmy lze použít pouze pro dešifrování existujících dat z důvodu zpětné kompatibility a data by měl být znovu zašifrována pomocí šifru doporučené bloku</li></ul><p>Prosím poznamenat, že všechny šifry symetrický bloku musí použít s režimem schválené šifer, které vyžaduje použití odpovídající inicializační vektor (IV). Odpovídající IV, je obvykle náhodné číslo a nikdy konstantní hodnota</p><p>Po zkontrolujte panel kryptografických vaší organizace, může být povoleno použití starší verze nebo jinak neschválených kryptografických algoritmů a délek menší klíčů pro čtení existujících dat (na rozdíl od zápis nová data). Však nutné soubor pro výjimku pro tento požadavek. Kromě toho v podnikových nasazeních produkty měli zvážit správci upozornění při slabé kryptografické algoritmy slouží k načtení dat. Takové upozornění by měl být vysvětlující a možné použít. V některých případech může být vhodné, aby byla zásad skupiny řídit použití slabé kryptografické algoritmy</p><p>Povolené .NET algoritmy pro spravované kryptografických flexibility (v upřednostňovaném pořadí)</p><ul><li>AesCng (kompatibilní se standardem FIPS)</li><li>AuthenticatedAesCng (kompatibilní se standardem FIPS)</li><li>AESCryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>AESManaged (bez – kompatibilní se standardem FIPS)</li></ul><p>Upozorňujeme, že žádná z tyto algoritmy lze zadat pomocí `SymmetricAlgorithm.Create` nebo `CryptoConfig.CreateFromName` metody bez provedení změn v souboru machine.config. Všimněte si také, zda je s názvem AES ve verzích rozhraní .NET před rozhraní .NET 3.5 `RijndaelManaged`, a `AesCng` a `AuthenticatedAesCng` jsou > k dispozici prostřednictvím webu CodePlex a vyžadovat CNG v základního operačního systému</p>

## <a id="vector-ciphers"></a>Použití schváleno režimy šifrování bloku a inicializační vektory pro symetrické šifry

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Všechny šifry symetrický bloku musí použít s režim schválené symetrického šifrování. Režimů pouze schválené jsou CBC a CTS. Konkrétně provozní režim elektronické kód adresáře (ECB) je nutno; použití ECB vyžaduje kontrolu kryptografických Tabule vaší organizace. Všechny využití OFB, CFB, PEV.cenu, CCM, a GCM nebo jiných režim šifrování musí být zkontrolovány uživatelem Tabule kryptografických vaší organizace. Opětovné použití stejné inicializační vektor (IV) s šifry bloku v "streamování šifry režimy" například PEV.cenu, může způsobit, že šifrovaná data, aby se odhalilo. Všechny šifry symetrický bloku musí být rovněž použit s příslušnou inicializační vektor (IV). Odpovídající IV je kryptograficky silnou, náhodné číslo a nikdy konstantní hodnotu. |

## <a id="padding"></a>Použít schválené asymetrických algoritmů, délek klíčů a odsazení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Použití zakázaného kryptografické algoritmy představuje významné riziko pro zabezpečení produktu a je třeba se vyhnout. Produkty musí používat jenom tyto kryptografické algoritmy a přidružené délky klíčů a odsazení explicitně schválené Radou kryptografických vaší organizace.</p><ul><li>**RSA -** mohou být použity pro šifrování, podpisu a výměny klíčů. Šifrování RSA musí používat pouze režimy odsazení OAEP nebo RSA KEM. Existující kód mohou používat PKCS č. 1 v1.5 odsazení režimu pouze z důvodu kompatibility. Použijte hodnotu null odsazení je explicitně zakázané. Klíče > = 2 048 bitů pro nový kód je vyžadován. Existující kód může podporovat klíče < 2048 bitů pouze pro zpětné kompatibility po kontrolní Radou kryptografických vaší organizace. Klíče < 1 024 bitů lze použít pouze pro dešifrování ověření stará data, a pokud nahrazené použije pro šifrování nebo operace podepisování</li><li>**ECDSA -** mohou být použity pro pouze podpis. ECDSA s > = 256 bitů se vyžaduje pro nový kód. Na základě ECDSA podpisy musí používat jednu z tři křivek NIST schválení (p-256, 384 nebo P521). Křivek, které nebyly analyzovány důkladně může použít až po kontrolu s Tabule kryptografických vaší organizace.</li><li>**ECDH -** mohou být použity pro výměnu klíčů pouze. ECDH s > = 256 bitů se vyžaduje pro nový kód. Na základě ECDH výměny klíčů musí používat jednu z tři křivek NIST schválení (p-256, 384 nebo P521). Křivek, které nebyly analyzovány důkladně může použít až po kontrolu s Tabule kryptografických vaší organizace.</li><li>**DSA -** může být po kontrole a schválení od vaší organizace kryptografických Tabule přijatelný. Obraťte se na váš zabezpečení Poradce při plánování zkontrolujte panel kryptografických vaší organizace. Pokud je používání agenta DSA, Všimněte si, že budete muset zakázat použití klíčů menší než 2 048 bitů. CNG podporuje 2048 bitů a větší délky klíčů od verze Windows 8.</li><li>**Diffie-Hellman -** mohou být použity pro relace správy klíčů pouze. Délka klíče > = 2 048 bitů pro nový kód je vyžadován. Existující kód může podporovat délky klíčů < 2048 bitů pouze pro zpětné kompatibility po kontrolní Radou kryptografických vaší organizace. Klíče nesmí být použity < 1 024 bitů.</li><ul>

## <a id="numgen"></a>Použití schváleno generátory náhodných čísel

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat schválené generátory náhodných čísel. Pseudonáhodná funkce jako je například rand – C runtime funkce, třídu rozhraní .NET Framework System.Random nebo funkce systému, jako je například GetTickCount nesmí, proto být nikdy použit v takový kód. Zakazuje použití algoritmu duální eliptické křivky náhodné číslo generátor (DUAL_EC_DRBG)</p><ul><li>**CNG -** BCryptGenRandom (použijte příznak BCRYPT_USE_SYSTEM_PREFERRED_RNG nedoporučuje, pokud má volající by se mohly spustit na jakékoli OVĚŘILO větší než 0 [PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nové implementace by měl použít BCryptGenRandom nebo CryptGenRandom) * rand_s – * SystemPrng (pro režimu jádra)</li><li>**. NET -** RNGCryptoServiceProvider nebo RNGCng</li><li>**Aplikace Windows Store -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom nebo. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef náhodné, size_t – počet, uint8_t \*bajtů)</li><li>**Apple OS X (< 10.7)-** použít/dev/náhodných k načtení náhodná čísla</li><li>**Java(Including Google Android Java Code) -** java.security.SecureRandom třídy. Upozorňujeme, že pro Android 4.3 (želé položku Bean), vývojáři musí postupovat podle Android doporučená řešení a aktualizace aplikací explicitně inicializovat PRNG s entropie z /dev/urandom nebo /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Nepoužívejte šifry symetrický datového proudu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Šifry symetrický datového proudu, jako je například RC4, nesmí se používat. Místo šifry symetrický datového proudu měli používat produkty cipher block, konkrétně AES s délkou klíče nejméně 128 bitů. |

## <a id="mac-hash"></a>Použití schváleno MAC, HMAC/keyed algoritmů hash

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat jen schválení ověřovací kód zprávy (MAC) nebo algoritmy kódu (HMAC) ověřování na základě hodnoty hash zpráv.</p><p>Ověřovací kód zprávy (MAC) je část informace, které jsou připojené k zprávu, která umožňuje jeho příjemce ověření pravosti odesílatele i integritu zprávy pomocí tajný klíč. Použití buď MAC na základě hodnoty hash ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) nebo [na základě bloku šifrovací MAC](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) je přípustné jako předpokladu, že všechny základní hodnotu hash nebo symetrický šifrovací algoritmy také schválen k použití; aktuálně to zahrnuje Funkce SHA2 HMAC s klíčem (HMAC SHA256, HMAC SHA384 a HMAC SHA512) a CMAC/OMAC1 a OMAC2 blokovat na základě šifrovací MAC (jsou založeny na standardu AES).</p><p>Může být použití HMAC SHA1 povolenou pro platformu kompatibility, ale budete muset souboru výjimku tento postup a projít kontrolní kryptografický vaší organizace. Zkrácení HMAC na méně než 128 bitů není povoleno. Pomocí metody hash klíč a data není schválený a Tabule kryptografických vaší organizace, musí projít si projít před použití zákazníka.</p>|

## <a id="hash-functions"></a>Použít pouze funkce schválené kryptografické hodnoty hash

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat řady SHA-2 algoritmů hash (SHA256, SHA384 a SHA512). V případě potřeby kratší hodnotu hash, jako je délka výstupní 128-bit Chcete-li přizpůsobit datová struktura navrhovat s kratší hodnotu hash MD5 na paměti, může zkrátit produktovými týmy jednu z hodnot hash SHA2 (obvykle SHA256). Všimněte si, SHA384 je oříznuta verzi SHA512. Zkrácení kryptografické hodnoty hash pro účely zabezpečení na méně než 128 bitů není povoleno. Nový kód nesmí používat MD2, MD4, MD5, SHA-0, SHA-1 nebo RIPEMD algoritmů hash. Kolize hodnot hash jsou u vhodná pro tyto algoritmy, které efektivně dělí.</p><p>Povolená .NET algoritmů hash pro spravované kryptografických flexibility (v upřednostňovaném pořadí):</p><ul><li>SHA512Cng (kompatibilní se standardem FIPS)</li><li>SHA384Cng (kompatibilní se standardem FIPS)</li><li>SHA256Cng (kompatibilní se standardem FIPS)</li><li>SHA512Managed (bez – kompatibilní se standardem FIPS) (použití SHA512 jako název algoritmu ve voláních HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA384Managed (bez – kompatibilní se standardem FIPS) (použití SHA384 jako algoritmu název ve voláních HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA256Managed (bez – kompatibilní se standardem FIPS) (použití SHA256 jako název algoritmu ve voláních HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>SHA256CryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>SHA384CryptoServiceProvider (kompatibilní se standardem FIPS)</li></ul>| 

## <a id="strong-db"></a>Používat silné šifrování algoritmy šifrování dat v databázi

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Výběr šifrovacího algoritmu](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Kroky** | Algoritmy šifrování definovat transformace dat, které nelze snadno vrátit neoprávnění uživatelé. SQL Server umožňuje správcům a vývojářům možnost volby mezi několik algoritmy, včetně DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, RC4 128-bit, DESX, AES 128-bit, AES 192 bitů a 256 bitů AES |

## <a id="ssis-signed"></a>Balíčky SSIS by měl být zašifrované a digitálně podepsané

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Identifikovat zdroj balíčků s digitálními podpisy](https://msdn.microsoft.com/library/ms141174.aspx), [hrozby a ohrožení zabezpečení zmírnění (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Kroky** | Zdroj balíčku je jednotlivec nebo organizace, vytvoření balíčku. Spuštění balíčku z neznámý nebo nedůvěryhodný zdroje může být riskantní. Chcete-li zabránit neoprávněným manipulaci s balíčky SSIS, je třeba použít digitální podpisy. Navíc k zajištění důvěrnosti balíčků během úložiště nebo přenosu, balíčky SSIS musela být šifrovaná |

## <a id="securables-db"></a>Přidat digitální podpis na zabezpečitelné prostředky, kritické databáze

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Kroky** | V případech, kde integritu zabezpečitelné kritické databáze musí být ověřena je třeba použít digitální podpisy. Databáze zabezpečitelné prostředky, jako jsou uložené procedury, funkce, sestavení nebo aktivační událost může být digitálně podepsané. Dole je příklad, pokud to může být užitečné: dejte nám vyslovení nezávislý dodavatel softwaru (nezávislé dodavatele softwaru) poskytl podporu, aby softwaru doručit do jednoho z zákazníků. Před zajištění podpory, ISV chtít zajištění, že buď omylem nebo škodlivý pokus nebyl změněn databázi zabezpečitelné v softwaru. Pokud zabezpečitelné digitálně podepsané, ISV můžete ověřit digitální podpis a ověřit jeho integritu.| 

## <a id="ekm-keys"></a>Použijte k ochraně šifrovací klíče SQL server EKM.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [SQL Server Ekm (EKM)](https://msdn.microsoft.com/library/bb895340), [Extensible Key Management pomocí Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Kroky** | SQL Server Extensible Key Management umožňuje šifrovací klíče, které chrání soubory databáze se neukládají v zařízení s vypnout pole například čipová karta, zařízení USB nebo modulu EKM/HSM. To také umožňuje ochranu dat před správce databáze (s výjimkou členové skupiny sysadmin). Data mohou být šifrována pomocí šifrovacích klíčů, jenom na uživateli databáze má přístup k na externí modulu EKM/HSM. |

## <a id="keys-engine"></a>Funkci AlwaysEncrypted používat v případě, že šifrovací klíče nesmí zjištěny pro databázový stroj

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | SQL Azure, OnPrem |
| **Atributy**              | MsSQL2016 verzi - 12, SQL |
| **Odkazy**              | [Funkce Always Encrypted (databázový stroj)](https://msdn.microsoft.com/library/mt163865) |
| **Kroky** | Vždy šifrovaný je funkce určené k ochraně citlivých dat, třeba čísla platebních karet nebo national identifikačními čísly (například USA čísel sociálního pojištění), uloženy v databázi Azure SQL Database nebo SQL Server. Vždy šifrovaný umožňuje klientům šifrovat citlivá data uvnitř klientské aplikace a nikdy odhalit šifrovací klíče pro databázový stroj (SQL Database nebo SQL Server). V důsledku toho vždy šifrována zajišťuje oddělení mezi uživatelů, kteří vlastní data (a můžete ji zobrazit) a ty, kteří spravovat data (ale musí mít žádný přístup) |

## <a id="keys-iot"></a>Bezpečně uložit šifrovací klíče v zařízení IoT

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Zařízení OS - jádro IoT Windows, připojení zařízení - SDK pro zařízení Azure IoT |
| **Odkazy**              | [Čip TPM na jádro IoT Windows](https://developer.microsoft.com/windows/iot/docs/tpm), [nastavení TPM na jádro IoT Windows](https://developer.microsoft.com/windows/iot/win10/setuptpm), [TPM SDK zařízení IoT Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroky** | Chráněný Symmetric nebo certifikátu privátního klíče, bezpečně v hardwaru úložiště, jako je čipy TPM a čipové karty. Jádro IoT Windows 10 podporuje uživatele čipu TPM a existuje několik kompatibilní čipy TPM, které lze použít: https://developer.microsoft.com/windows/iot/win10/tpm. Doporučujeme použít firmwaru nebo diskrétní TPM. Čip TPM softwaru lze používat pouze pro účely vývoje a testování. Jakmile čip TPM je k dispozici a klíče jsou zřízené v něm, kód, který generuje token budou zasílány bez pevné kódování žádné citlivé informace, které se v něm. | 

### <a name="example"></a>Příklad:
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak je vidět, primární klíč zařízení se nenachází v kódu. Místo toho je uložená v čipu TPM na pozici 0. Čip TPM zařízení generuje krátkodobou token SAS, který se pak použije k připojení ke službě IoT Hub. 

## <a id="random-hub"></a>Generoval náhodný symetrický klíč dostatečně dlouhé pro ověřování do služby IoT Hub

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Volba brány - Azure IoT Hub |
| **Odkazy**              | neuvedeno  |
| **Kroky** | IoT Hub obsahuje registr identit zařízení a při zřizování zařízení, automaticky vygeneruje náhodné symetrického klíče. Doporučujeme použít tuto funkci registru identit Azure IoT Hub pro vygenerování klíče používaného pro ověřování. Centrum IoT také umožňuje klíč zadat při vytváření zařízení. Pokud klíč je generován mimo IoT Hub během zřizování zařízení, se doporučuje vytvořit náhodný symetrický klíč nebo nejméně 256 bitů. |

## <a id="pin-remote"></a>Ujistěte se, že k zásadě správy zařízení je na místě, který vyžaduje použití kódu PIN a umožňuje vzdálené vymazání

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM mobilního klienta | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že k zásadě správy zařízení je na místě, který vyžaduje použití kódu PIN a umožňuje vzdálené vymazání |

## <a id="bitlocker"></a>Zkontrolujte, zda že je k zásadě správy zařízení v místě, ke kterému se vyžaduje PIN kód nebo heslo nebo automatické uzamčení a šifruje všechna data (např. Bitlocker)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Klient aplikace Outlook Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zkontrolujte, zda že je k zásadě správy zařízení v místě, ke kterému se vyžaduje PIN kód nebo heslo nebo automatické uzamčení a šifruje všechna data (např. Bitlocker) |

## <a id="rolled-server"></a>Ujistěte se, že podpisové klíče jsou převracet při použití serveru identit

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Identita serveru – klíčů, podpisů a šifrování ](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroky** | Ujistěte se, že podpisové klíče jsou převracet při použití Identity serveru. V části odkazy na odkaz vysvětluje by měl být plánované jak aniž by to způsobilo výpadky na aplikace, které spoléhají na serveru Identity. |

## <a id="client-server"></a>Zkontrolujte, zda ID kryptograficky silnou klienta, sdílený tajný klíč klienta jsou používány serveru identit

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Zkontrolujte, zda ID kryptograficky silnou klienta, sdílený tajný klíč klienta jsou používány serveru identit. Následující pokyny by měl být použit při generování ID klienta a tajný klíč:</p><ul><li>Generuje náhodné GUID jako ID klienta</li><li>Generování generovaných kryptograficky náhodných klíče 256 bitů jako tajný klíč</li></ul>|
