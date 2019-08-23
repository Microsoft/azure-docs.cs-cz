---
title: Architektura úložiště OPC – Azure | Microsoft Docs
description: Architektura služby správy certifikátů úložiště OPC
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 151eb535e70bd485c978ac21539c784bbea0a532
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973230"
---
# <a name="opc-vault-architecture"></a>Architektura úložiště OPC

Tento článek poskytuje přehled o mikroslužbě **trezoru OPC** a **modulu OPC pro trezor IoT Edge**.

## <a name="overview"></a>Přehled

Aplikace OPC UA používají certifikáty instance aplikace k zajištění zabezpečení na úrovni aplikace. Zabezpečené připojení je vytvořeno pomocí asymetrické kryptografie, pro kterou certifikáty aplikací poskytují pár veřejného a privátního klíče. Certifikáty mohou být podepsané svým držitelem nebo mohou být podepsány certifikační autoritou (CA).

Aplikace OPC UA má seznam důvěryhodných certifikátů, které reprezentují aplikace, kterým důvěřují. Tyto certifikáty můžou být podepsané svým držitelem, podepsaný certifikační autoritou nebo může být kořenová CA nebo samostatná certifikační autorita. Pokud je důvěryhodný certifikát součástí většího řetězu certifikátů, aplikace považuje všechny certifikáty, které se řetězí k certifikátu v seznamu důvěryhodných certifikátů, pokud je možné ověřit úplný řetěz certifikátů.

Hlavním rozdílem mezi Důvěřujícími certifikáty podepsanými držitelem a důvěryhodným certifikátem certifikační autority je úsilí při instalaci potřebné k nasazení a údržbě důvěry a další úsilí při hostování certifikační autority specifické pro společnost. 

Aby bylo možné distribuovat důvěru certifikátů podepsaných svým držitelem pro n serverů s jedinou klientskou aplikací, musí být všechny certifikáty serverových aplikací nainstalovány v seznamu důvěryhodných klientských aplikací a certifikát klientské aplikace musí být nainstalován ve všech Seznamy důvěryhodných serverových aplikací. Toto administrativní úsilí je poměrně zatížené a přidává ještě víc, pokud je potřeba zvážit životnost certifikátů a obnovují se certifikáty.

Použití certifikační autority specifické pro společnost může značně zjednodušit správu důvěryhodnosti s více servery a klienty. V takovém případě správce vygeneruje certifikát instance aplikace podepsaný certifikační autoritou pro každého klienta a server, který se používá. Certifikát certifikační autority se navíc nainstaluje do všech seznamů důvěryhodných aplikací na všech serverech a klientech. Tento přístup vyžaduje, aby se obnovily jenom certifikáty s vypršenou platností a aby se pro ovlivněné aplikace nahradily.

Služba Azure průmyslový IoT OPC UA Certificate Management je naše řešení pro správu certifikační autority specifické pro společnost pro aplikace OPC UA, která je založená na mikroslužbě trezoru OPC.

OPC trezor poskytuje mikroslužby pro hostování podnikové certifikační autority v zabezpečeném cloudu, které jsou založené na zabezpečených službách Azure AD s Azure Key Vault s moduly hardwarového zabezpečení, Cosmos DB a volitelně také IoT Hub jako obchod s aplikacemi.

Mikroslužba trezoru OPC je navržená tak, aby podporovala pracovní postup založený na rolích, kde aplikace přihlásila certifikáty k aplikacím a v případě, že správci zabezpečení a schvalovatelé s přihlašovacími právy ve Azure Key Vault schvalují nebo odmítnou

Pro zajištění kompatibility s existujícími řešeními založenými na OPC UA GDS jsou součástí služby podpora pro modul back-based Edge pro OPC trezor, který implementuje *globální zjišťování serveru OPC UA a rozhraní správy certifikátů* k distribuci. certifikáty a seznamy důvěryhodných certifikátů podle části 12 specifikace. Nicméně od našeho vědomí se toto rozhraní serveru GDS ještě nepoužívá a zatím má omezené funkce (role čtenářů). [Na vyžádání si Vylepšete prostředí na žádost zákazníka (*)](#yet-unsupported-features).

## <a name="architecture"></a>Architektura

Architektura je založená na mikroslužbě trezoru OPC s modulem OPC trezoru IoT Edge pro síť továrny a webové ukázkové uživatelské prostředí pro řízení pracovního postupu:

![Architektura OPCVault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikroslužba trezoru OPC

Mikroslužba trezoru OPC se skládá z následujících rozhraní k implementaci pracovního postupu pro distribuci a správu certifikační autority specifické pro společnost pro aplikace OPC UA:

### <a name="application"></a>Aplikace 
- "OPC UA" může být server nebo klient aplikace nebo obojí. OPC trezor slouží jako registrační autorita aplikace v tomto případě. 
- Vedle základních operací registrace, aktualizace a zrušení registrace aplikací existují také rozhraní pro hledání a dotazování aplikací pomocí vyhledávacích výrazů. 
- Žádosti o certifikát musí odkazovat na platnou aplikaci, aby mohla zpracovat požadavek a vystavit podepsaný certifikát se všemi OPCmi rozšířeními, která jsou specifická pro UA. 
- Aplikační služba se buď zálohuje v databázi CosmosDB, nebo v [registru zařízení OpcTwin (*)](#yet-unsupported-features), v závislosti na konfiguraci zákazníka.

### <a name="certificate-group"></a>Skupina certifikátů
- Skupina certifikátů je entita, která ukládá kořenovou certifikační autoritu nebo certifikát podřízené certifikační autority, včetně privátního klíče pro podepisování certifikátů. 
- Délka klíče RSA, délka hash SHA-2 a životnosti se dají konfigurovat pro certifikační autoritu vystavitele i pro podepsané certifikáty aplikací. 
- V rámci jedné služby lze hostovat více skupin, aby bylo možné v budoucích rozšířeních se skupinami certifikátů https, User a ECC Algorithm [(*)](#yet-unsupported-features). 
- Certifikáty certifikační autority se ukládají v Azure Key Vault s využitím modulů hardwarového zabezpečení standardu FIPS 140-2 úrovně 2. Privátní klíč nikdy neopouští zabezpečené úložiště, protože se k podepisování používá AzureAD zabezpečená operace Key Vault. 
- Certifikáty certifikační autority se dají v průběhu času prodloužit a v důsledku Key Vault historie stále zůstat v bezpečném úložišti. 
- Seznam odvolaných certifikátů pro každý certifikát certifikační autority je uložený také v Key Vault jako tajný klíč. Jakmile je aplikace odregistrována, certifikát aplikace je také odvolán v seznamu CRL správcem.
- Je podporováno dávkové a jedno odvolání certifikátu.

### <a name="certificate-request"></a>Žádost o certifikát
Požadavek certifikátu implementuje pracovní postup, aby vygeneroval nový pár klíčů nebo podepsaný certifikát pomocí žádosti o podepsání certifikátu (CSR) pro aplikaci OPC UA. 
- Požadavek je uložen v databázi s doprovodnou informací, jako je předmět nebo CSR, a odkazem na aplikaci OPC UA. 
- Obchodní logika ve službě ověřuje požadavek proti informacím uloženým v aplikační databázi. Například identifikátor URI aplikace v databázi se musí shodovat s identifikátorem URI aplikace v CSR.
- Správce zabezpečení s právy pro podpis (schvalující role) žádost schválí nebo zamítne. Pokud je žádost schválená, vygenerují se nový pár klíčů nebo podepsaný certifikát. Nový privátní klíč je bezpečně uložený v trezoru klíčů v době, kdy se nový podepsaný veřejný certifikát uloží do databáze žádostí o certifikát.
- Žadatel se může dotazovat na stav žádosti, dokud není schválen nebo odvolán. Pokud byl požadavek schválen, privátní klíč a certifikát lze stáhnout a nainstalovat v úložišti certifikátů aplikace OPC UA.
- Žadatel teď může požadavek přijmout, aby odstranil nepotřebné informace z databáze požadavků. 

Po celou dobu životnosti podepsaného certifikátu může být aplikace smazána nebo může dojít k ohrožení zabezpečení klíče. V takovém případě může správce certifikační autority:
- Odstraní aplikaci, která zároveň odstraní všechny nedokončené a schválené žádosti o certifikát aplikace. 
- Další možností je odstranit pouze jednu žádost o certifikát, pokud je obnovena nebo ohrožena pouze jedna z nich.
- Nyní jsou ohrožené schválené a přijaté žádosti o certifikát označeny jako odstraněné.
- Správce může pravidelně provádět obnovování seznamu CRL certifikační autority vystavitele. V době obnovení jsou všechny odstraněné žádosti o certifikát odvolány a sériová čísla certifikátů jsou přidána do seznamu odvolaných certifikátů CRL. Odvolané žádosti o certifikát jsou označené jako odvolané.
- V naléhavých událostech je možné odvolat samostatné žádosti o certifikát.
- Nakonec jsou aktualizované seznamy odvolaných certifikátů k dispozici pro distribuci do zúčastněných klientů a serverů OPC UA.

Další informace o rozhraní API mikroslužeb OPC trezoru najdete v dokumentaci k Swagger.

## <a name="opc-vault-iot-edge-module"></a>Modul IoT Edge trezoru OPC
Aby bylo možné podporovat globální Server zjišťování sítě továrny, může být na hraničních zařízeních nasazen modul OPC trezor, který je spouštěn jako místní .Net Core aplikace nebo může být spuštěn v kontejneru Docker. Z důvodu nedostatku podpory ověřování auth2 v aktuálním zásobníku OPC UA .NET Standard je funkce modulu Edge pro OPC trezoru omezená na roli čtenáře, protože uživatele nejde zosobnit od modulu Edge k mikroslužbám pomocí standardu OPC UA GDS.  prostředí. V této fázi[(*)](#yet-unsupported-features)jsou povoleny pouze operace, které nevyžadují roli zapisovače, správce nebo schvalovatele. 

## <a name="yet-unsupported-features"></a>Zatím nepodporované funkce

**(*)** ještě není podporováno.
