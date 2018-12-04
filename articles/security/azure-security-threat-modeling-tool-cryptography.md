---
title: Šifrování – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 79803a749b6d08c94bcbf5f3ca66aac8b7294fa3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844647"
---
# <a name="security-frame-cryptography--mitigations"></a>Zabezpečení Frame: Šifrování | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Webové aplikace** | <ul><li>[Používat pouze schválený symetrický blokové šifry a délky klíčů](#cipher-length)</li><li>[Použití schválené režimy šifrování bloku a inicializační vektory pro symetrické šifrování](#vector-ciphers)</li><li>[Použití schválené asymetrické algoritmy, délky klíčů a odsazení](#padding)</li><li>[Použití schválené generátorů náhodných čísel](#numgen)</li><li>[Nepoužívejte stream symetrického šifrování](#stream-ciphers)</li><li>[Použití schválené MAC/HMAC/označenými hashovacích algoritmů](#mac-hash)</li><li>[Používat pouze funkce schválené kryptografické hodnoty hash](#hash-functions)</li></ul> |
| **Database** | <ul><li>[Používat silné šifrovací algoritmy k šifrování dat v databázi](#strong-db)</li><li>[Balíčků služby SSIS by měl být zašifrované a digitálně podepsané](#ssis-signed)</li><li>[Přidat digitální podpis zabezpečitelným prostředkům kritické databáze](#securables-db)</li><li>[Použít systém SQL server EKM chrání šifrovací klíče](#ekm-keys)</li><li>[Pokud šifrovací klíče by neměl být odhalena k databázovému stroji AlwaysEncrypted funkci používat](#keys-engine)</li></ul> |
| **Zařízení IoT** | <ul><li>[Bezpečně Store kryptografických klíčů v zařízení IoT](#keys-iot)</li></ul> | 
| **Brána IoT cloudu** | <ul><li>[Generování náhodného symetrického klíče dostatečně dlouhé pro ověření do služby IoT Hub](#random-hub)</li></ul> | 
| **Mobilní klient Dynamics CRM** | <ul><li>[Ujistěte se, že zásady správy zařízení je v místě, ke kterému potřebuje použít PIN kód a umožňuje vzdálené vymazání](#pin-remote)</li></ul> | 
| **Klient Dynamics CRM Outlook** | <ul><li>[Ujistěte se, že zásady správy zařízení je v místě, ke kterému vyžaduje kód PIN a hesla/automatické zamykání a šifruje všechna data (např. Bitlocker)](#bitlocker)</li></ul> | 
| **Serveru identit** | <ul><li>[Ujistěte se, že podpisové klíče jsou jednotlivě při použití serveru identit](#rolled-server)</li><li>[Ujistěte se, že ID přidělení kryptograficky silného klienta, tajného klíče klienta jsou použity na serveru identit](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Používat pouze schválený symetrický blokové šifry a délky klíčů

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat pouze symetrický blokové šifry a přidružené délky klíčů, které byly explicitně schváleny společností Advisor Crypto ve vaší organizaci. Schválené symetrické algoritmy v Microsoftu patří následující blokového šifrování:</p><ul><li>Pro nový kód jsou přijatelné AES-128, AES-192 a AES-256</li><li>Kvůli zpětné kompatibilitě se existující kód je přijatelné tři klíče 3DES</li><li>Pro produkty pomocí symetrických blokového šifrování:<ul><li>Rozšířené šifrování AES (Standard) se vyžaduje pro nový kód</li><li>Tři klíče triple Data Encryption Standard (3DES) je povolený v existujícím kódu z důvodu zpětné kompatibility</li><li>Všechny ostatní blokové šifry, včetně RC2, DES, 2 klíče 3DES, DESX a pruhovaného, se dá použít jenom pro dešifrování se stará data a musí být nahrazen, pokud se používá pro šifrování</li></ul></li><li>U bloku symetrický šifrovací algoritmy se vyžaduje minimální délkou klíče 128 bitů. Pouze algoritmus šifrování bloku doporučuje pro nový kód je AES (AES-128, AES-192 a AES-256 jsou všechny přijatelné)</li><li>V existujícím kódu; je aktuálně přijatelné, pokud je v tři klíče 3DES Přechod k AES se doporučuje. DES, DESX, RC2 a pruhovaného jsou již považovány za bezpečné. Tyto algoritmy dá použít jenom pro dešifrování existující data z důvodu zpětné kompatibility, a data měla znovu šifrovat pomocí doporučené blokových šifrách</li></ul><p>Mějte prosím na paměti, že všechny symetrický blokové šifry musí být použit s režim schválené šifrování, která vyžaduje použití odpovídající inicializační vektor (IV). Odpovídající inicializačního Vektoru, je obvykle náhodné číslo a nikdy konstantní hodnotu</p><p>Po přehledu panel Crypto vaší organizace, může být povoleno použití starších nebo jinak neschválených kryptografické algoritmy a délky menší klíčů pro čtení existující data (na rozdíl od zápisem nových dat). Musíte však souboru pro výjimku pro tento požadavek. Kromě toho v podnikových nasazeních produkty při měli zvážit upozornění správcům slabé šifrování se použije ke čtení dat. Tato upozornění by měl být vysvětlující a užitečné. V některých případech může být vhodné mít zásad skupiny řídit použití slabé šifrování</p><p>Povolené .NET algoritmy pro spravované kryptografických flexibility (v upřednostňovaném pořadí)</p><ul><li>AesCng (kompatibilní se standardem FIPS)</li><li>AuthenticatedAesCng (kompatibilní se standardem FIPS)</li><li>AESCryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>AESManaged (bez kompatibilní se standardem FIPS)</li></ul><p>Mějte prosím na paměti, že žádná z těchto algoritmy lze upravit pomocí `SymmetricAlgorithm.Create` nebo `CryptoConfig.CreateFromName` metody bez provedení změn souboru machine.config. Všimněte si také, že je s názvem AES ve verzích .NET před .NET 3.5 `RijndaelManaged`, a `AesCng` a `AuthenticatedAesCng` jsou > dostupná prostřednictvím webu CodePlex a vyžadují CNG v podkladovému operačnímu systému</p>

## <a id="vector-ciphers"></a>Použití schválené režimy šifrování bloku a inicializační vektory pro symetrické šifrování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Všechny symetrický blokové šifry, je nutné použít s režimu schválené symetrického šifrování. Pouze schválené režimy jsou CBC a CTS. Zejména mělo by se vyhnout provozní režim electronic code book (ECB); použití sady ECB vyžaduje kontrolu Crypto panel vaší organizace. Veškeré využití OFB, CFB, PEV.cenu, CCM, a GCM nebo jiných režim šifrování musí být přezkoumána pomocí nástěnky Crypto vaší organizace. Opětovné použití stejného inicializačního vektoru (IV) s variant blokového šifrování v "streamování šifry režimy" například PEV.cenu, může způsobit, že šifrovaná data, aby se odhalilo. Všechny symetrický blokové šifry, musí být rovněž použita s odpovídající inicializační vektor (IV). Odpovídající inicializačního Vektoru je přidělení kryptograficky silného, náhodné číslo a nikdy konstantní hodnotu. |

## <a id="padding"></a>Použití schválené asymetrické algoritmy, délky klíčů a odsazení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Použití zakázané kryptografických algoritmů přináší významné riziko zabezpečení produktu a musí se jim vyhnout. Produkty musí používat pouze kryptografické algoritmy a přidružené délky klíčů a odsazení explicitně schválené pomocí nástěnky Crypto vaší organizace.</p><ul><li>**RSA -** můžou sloužit ke signatura, výměny klíčů a šifrování. Šifrování RSA musí používat pouze režimy výplně OAEP nebo RSA KEM. Existující kód může použít odsazení režimu pouze z důvodů kompatibility v1.5 PKCS č. 1. Použití null odsazení je explicitně zakázané. Klíče > = 2 048 bitů je vyžadován pro nový kód. Existující kód může podporovat klíče < 2 048 bitů pouze pro zpětnou kompatibilitu po přezkoumání pomocí nástěnky Crypto vaší organizace. Klíče < 1 024 bitů se dá použít jenom pro dešifrování/ověření stará data, a musí být nahrazen if použito pro šifrování nebo operace podepisování</li><li>**ECDSA -** můžou sloužit ke signatura pouze. ECDSA s > = 256 bitů se vyžaduje pro nový kód. Podpisy založené na ECDSA musí používat některý z tři křivky NIST schválení (p-256, p-384 nebo P521). Křivky, které se důkladně analyzovat se dá použít jenom po přezkoumání pomocí nástěnky Crypto vaší organizace.</li><li>**ECDH -** slouží pro výměnu klíčů pouze. ECDH s > = 256 bitů se vyžaduje pro nový kód. Na základě ECDH výměny klíčů musí používat některý z tři křivky NIST schválení (p-256, p-384 nebo P521). Křivky, které se důkladně analyzovat se dá použít jenom po přezkoumání pomocí nástěnky Crypto vaší organizace.</li><li>**DSA -** může být přijatelný po kontrole a schválení z panelu Crypto vaší organizace. Obraťte se na vaše zabezpečení advisor naplánování kontroly Crypto panel vaší organizace. Pokud se vaše užívání DSA schválí, mějte na paměti, že budete muset zakázat použití klíčů menší než 2 048 bitů. CNG podporuje 2048 bitů a větší délky klíčů od verze Windows 8.</li><li>**Skupina Diffie-Hellman -** slouží pro relace správy klíčů pouze. Délka klíče > = 2 048 bitů je vyžadován pro nový kód. Existující kód může podporovat délky klíčů < 2 048 bitů pouze pro zpětnou kompatibilitu po přezkoumání pomocí nástěnky Crypto vaší organizace. Klíče nelze použít < 1 024 bitů.</li><ul>

## <a id="numgen"></a>Použití schválené generátorů náhodných čísel

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat schválené generátorů náhodných čísel. Pseudonáhodná funkce jako rand funkce modulu runtime jazyka C, rozhraní .NET Framework třída System.Random nebo systémové funkce, jako je například GetTickCount musí proto nikdy používat ve takového kódu. Použít duální eliptické křivky náhodných čísel generátor (DUAL_EC_DRBG) je zakázaná.</p><ul><li>**CNG -** BCryptGenRandom (pomocí příznaku BCRYPT_USE_SYSTEM_PREFERRED_RNG nedoporučuje, pokud volající může spustit na libovolné úrovni IRQL větší než 0 [PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nové implementace používejte BCryptGenRandom nebo CryptGenRandom) * rand_s – * SystemPrng (pro režim jádra)</li><li>**. NET -** RNGCryptoServiceProvider nebo RNGCng</li><li>**Windows Store Apps -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom nebo. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef náhodně výpočtu size_t, uint8_t \*bajtů)</li><li>**Apple OS X (< 10.7)-** použijte/dev/náhodné k načtení náhodná čísla</li><li>**Java(Including Google Android Java Code) -** java.security.SecureRandom třídy. Mějte na paměti, že pro Android verze 4.3 položku (Jelly Bean), vývojáři musí postupovat podle Android doporučená alternativní řešení a aktualizovat svoje aplikace explicitně inicializovat PRNG s entropie /dev/urandom nebo /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Nepoužívejte stream symetrického šifrování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Symetrický stream šifry, jako je například RC4, nesmí se používat. Místo šifry symetrický datového proudu používejte produkty bloková šifra, konkrétně AES s délkou klíče nejméně 128 bitů. |

## <a id="mac-hash"></a>Použití schválené MAC/HMAC/označenými hashovacích algoritmů

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat jenom schválené ověřovací kód zprávy (MAC) nebo HMAC ověřování kódu (HMAC) algoritmy.</p><p>Ověřovací kód zprávy (MAC) je určitý údaj připojen ke zprávě, která umožňuje její příjemce k ověření pravosti odesílatel i integritu zprávy pomocí tajného klíče. Použijte buď základě algoritmu hash MAC ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) nebo [cipher založené na blocích MAC](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) je přípustné jako dlouho jako všechny základní hodnoty hash nebo symetrický šifrovací algoritmy také schválená k používání; aktuálně jedná se o Funkce HMAC SHA2 (HMAC SHA256 a HMAC SHA384 HMAC SHA512.) a CMAC/OMAC1 a OMAC2 blokovat na základě šifer počítače Mac (jsou založeny na standardu AES).</p><p>Může být použití HMAC SHA1 přípustné, aby kompatibilita platformy, ale nebudete muset souboru výjimky tohoto postupu a projít kryptografické kontroly vaší organizace. Zkrácení HMAC na míň než 128 bitů se nepovoluje. Pomocí metody pro hodnoty hash klíče a data není schválený a panel Crypto vaší organizace, musí projít si projít před použití zákazníka.</p>|

## <a id="hash-functions"></a>Používat pouze funkce schválené kryptografické hodnoty hash

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Produkty musí používat řady SHA-2 hashovacích algoritmů (SHA256, SHA384 a SHA512). V případě potřeby kratší hodnotu hash, jako je délka výstupní 128bitové aby bylo možné přizpůsobit datová struktura s kratší hodnotu hash MD5 na paměti, může zkrátit produktových týmů, jednu z hodnot hash SHA2 (obvykle SHA256). Všimněte si, že je SHA384 zkrácená verze SHA512. Zkrácení kryptografické hodnoty hash z bezpečnostních důvodů na míň než 128 bitů se nepovoluje. Nový kód nesmí používat MD2 MD4, MD5, SHA-0, SHA-1 nebo RIPEMD hashovacích algoritmů. Kolize hodnot hash jsou výpočetně vhodná pro tyto algoritmy, které efektivně přeruší.</p><p>.NET hashovacích algoritmů pro spravované kryptografických flexibilitu povolené (v upřednostňovaném pořadí):</p><ul><li>SHA512Cng (kompatibilní se standardem FIPS)</li><li>SHA384Cng (kompatibilní se standardem FIPS)</li><li>SHA256Cng (kompatibilní se standardem FIPS)</li><li>SHA512Managed (bez kompatibilní se standardem FIPS) (použití SHA512 jako název algoritmu ve voláních HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA384Managed (bez kompatibilní se standardem FIPS) (použití SHA384 jako algoritmu název ve voláních HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA256Managed (bez kompatibilní se standardem FIPS) (použití SHA256 jako název algoritmu ve voláních HashAlgorithm.Create nebo CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>SHA256CryptoServiceProvider (kompatibilní se standardem FIPS)</li><li>SHA384CryptoServiceProvider (kompatibilní se standardem FIPS)</li></ul>| 

## <a id="strong-db"></a>Používat silné šifrovací algoritmy k šifrování dat v databázi

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Výběr algoritmu šifrování](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Kroky** | Algoritmy šifrování definovat transformace dat, které nelze snadno vrátit neoprávnění uživatelé. SQL Server umožňuje správcům a vývojářům vybrat z několika algoritmů, včetně DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bit RC4, DESX, 128bitový standard AES, 192 bitů AES a AES 256 bitů |

## <a id="ssis-signed"></a>Balíčků služby SSIS by měl být zašifrované a digitálně podepsané

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Identifikujte zdroj balíčků s digitálními podpisy](https://msdn.microsoft.com/library/ms141174.aspx), [hrozby a ohrožení zabezpečení omezení rizik (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Kroky** | Zdroj balíčku je osobu nebo organizaci, který vytvoří balíček. Spuštění balíčku z neznámého nebo nedůvěryhodného zdroje může být riskantní. Chcete-li zabránit neoprávněné manipulaci s balíčky služby SSIS, je třeba použít digitální podpisy. Navíc k zajištění důvěrnosti balíčků během úložiště/kopírování, balíčků služby SSIS musela být šifrovaná |

## <a id="securables-db"></a>Přidat digitální podpis zabezpečitelným prostředkům kritické databáze

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Přidejte podpis (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Kroky** | V případech, kde integrity zabezpečitelných kritické databáze musí být ověřena by měla sloužit digitální podpisy. Databáze zabezpečitelné prostředky, jako jsou uložené procedury, funkce, sestavení nebo aktivační událost může být digitálně podepsané. Níže je příklad, když to může být užitečné: dejte nám říct, ISV (nezávislý výrobce softwaru) poskytuje podporu pro software doručit do jedné z jejich zákazníků. Ještě před poskytnutím podpory, ISV vhodné zajistit, že databáze zabezpečeného softwaru nebylo manipulováno omylem nebo při pokusu škodlivé. Pokud zabezpečený je digitálně podepsané, výrobci můžete ověřit digitální podpis a ověřit jeho integritu.| 

## <a id="ekm-keys"></a>Použít systém SQL server EKM chrání šifrovací klíče

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [SQL Server rozšiřitelná Správa klíčů (EKM)](https://msdn.microsoft.com/library/bb895340), [rozšiřitelná Správa klíčů s využitím Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Kroky** | SQL Server Extensible Key Management umožňuje šifrovací klíče, které chrání soubory databáze k uložení do zařízení s mimo pole, například pomocí čipové karty, USB zařízení nebo modulu EKM/HSM. Také to umožňuje ochranu dat před správci databáze (s výjimkou členové skupiny sysadmin). Data lze zašifrovat pomocí šifrovacích klíčů, pouze uživatel databáze má přístup k externím modulu EKM/HSM. |

## <a id="keys-engine"></a>Pokud šifrovací klíče by neměl být odhalena k databázovému stroji AlwaysEncrypted funkci používat

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | SQL Azure, OnPrem |
| **Atributy**              | Verze - V12, MsSQL2016 SQL |
| **Odkazy**              | [Funkce Always Encrypted (databázový stroj)](https://msdn.microsoft.com/library/mt163865) |
| **Kroky** | Funkce Always Encrypted je funkce, navržená k ochraně citlivých dat, třeba čísla platebních karet nebo národní identifikační čísla (například USA čísla sociálního pojištění), uložené v databázích Azure SQL Database nebo SQL Server. Funkce Always Encrypted umožňuje klientům šifrovat citlivá data v klientských aplikacích a nikdy odhalit šifrovací klíče pro databázový stroj (SQL Database nebo SQL Server). V důsledku toho funkce Always Encrypted umožňuje oddělit mezi těmi, kdo data vlastní (a mohou je zobrazovat) a těmi, kdo spravovat data (ale nemají žádný přístup) |

## <a id="keys-iot"></a>Bezpečně Store kryptografických klíčů v zařízení IoT

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Operační systém zařízení – Windows IoT Core, připojení zařízení – sady SDK pro zařízení Azure IoT |
| **Odkazy**              | [TPM ve Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [nastavení TPM na Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [TPM sady SDK zařízení IoT Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroky** | Chráněné Symmetric nebo certifikátu privátního klíče bezpečně v hardwaru úložiště, jako je čipy TPM nebo čipové karty. Windows 10 IoT Core podporuje uživatele čip TPM a nebudou již existovat několik kompatibilní čipy TPM, které je možné: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Doporučujeme použít Firmwarovém nebo diskrétní TPM. Čip TPM softwaru by měla sloužit pouze pro účely vývoje a testování. Jakmile je k dispozici TPM a klíče jsou zřízené v ní, by měly být kód, který generuje token zapsány bez pevného kódování žádné citlivé údaje v něm. | 

### <a name="example"></a>Příklad:
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak je vidět, primární klíč zařízení není k dispozici v kódu. Místo toho je uložená v čipu TPM na pozici 0. Zařízení TPM generuje krátkodobé a jednorázové token SAS, který se pak použije k připojení ke službě IoT Hub. 

## <a id="random-hub"></a>Generování náhodného symetrického klíče dostatečně dlouhé pro ověření do služby IoT Hub

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Výběr brány - Azure IoT Hub |
| **Odkazy**              | neuvedeno  |
| **Kroky** | IoT Hub obsahuje registr identit zařízení a při zřizování zařízení, automaticky generuje náhodné symetrický klíč. Doporučujeme použít tuto funkci registru identit služby IoT Azure se vygenerovat klíč používaný k ověřování. IoT Hub umožňuje také klíče zadat při vytváření zařízení. Pokud klíč generován mimo službu IoT Hub během zřizování zařízení, se doporučuje vytvořit náhodný symetrický klíč nebo nejméně 256 bitů. |

## <a id="pin-remote"></a>Ujistěte se, že zásady správy zařízení je v místě, ke kterému potřebuje použít PIN kód a umožňuje vzdálené vymazání

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient Dynamics CRM | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že zásady správy zařízení je v místě, ke kterému potřebuje použít PIN kód a umožňuje vzdálené vymazání |

## <a id="bitlocker"></a>Ujistěte se, že zásady správy zařízení je v místě, ke kterému vyžaduje kód PIN a hesla/automatické zamykání a šifruje všechna data (např. Bitlocker)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Klient Dynamics CRM Outlook | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že zásady správy zařízení je v místě, ke kterému vyžaduje kód PIN a hesla/automatické zamykání a šifruje všechna data (např. Bitlocker) |

## <a id="rolled-server"></a>Ujistěte se, že podpisové klíče jsou jednotlivě při použití serveru identit

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Identita serveru – klíče, podpisů a šifrování ](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroky** | Ujistěte se, že podpisové klíče jsou jednotlivě při použití serveru identit. Odkaz v části odkazy vysvětluje, jak to plánování byste měli využít aniž by to způsobilo výpadky aplikací spoléhá na serveru identit. |

## <a id="client-server"></a>Ujistěte se, že ID přidělení kryptograficky silného klienta, tajného klíče klienta jsou použity na serveru identit

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Ujistěte se, že ID přidělení kryptograficky silného klienta, tajného klíče klienta jsou použity na serveru identit. Následující pokyny by měl být použit při generování ID klienta a tajný klíč:</p><ul><li>Generovat náhodné identifikátor GUID jako ID klienta</li><li>Generování generovaných kryptograficky náhodných klíče 256 bitů jako tajný klíč</li></ul>|
