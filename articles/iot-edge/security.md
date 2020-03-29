---
title: Rámec zabezpečení – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Seznamte se s bezpečnostními, ověřovacími a autorizačními standardy, které se používaly k vývoji Azure IoT Edge a měly by být považovány při navrhování řešení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760109"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpečení pro Azure IoT Edge

Azure IoT Edge řeší rizika, která jsou vlastní při přesunu dat a analýz na inteligentní okraj. Standardy zabezpečení IoT Edge vyvažují flexibilitu pro různé scénáře nasazení s ochranou, kterou očekáváte od všech služeb Azure.

IoT Edge běží na různých technologiích a modelech hardwaru, podporuje několik operačních systémů a vztahuje se na různé scénáře nasazení. Místo toho, aby nabízel konkrétní řešení pro konkrétní scénáře, je IoT Edge rozšiřitelný rámec zabezpečení založený na dobře zakotvených principech, které jsou navrženy pro škálování. Riziko scénáře nasazení závisí na mnoha faktorech, včetně:

* Vlastnictví řešení
* Geografie nasazení
* Citlivost dat
* Ochrana osobních údajů
* Aplikace vertikální
* Regulační požadavky

Tento článek obsahuje přehled architektury zabezpečení IoT Edge. Další informace naleznete v [tématu Zabezpečení inteligentní hrany](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy podporují snadnou kontrolu a snadnou implementaci, což jsou charakteristické znaky bezpečnosti. Bezpečnostní řešení by se mělo dát ke kontrole v rámci hodnocení, aby se vybudovala důvěra a neměla by být překážkou pro nasazení. Návrh architektury pro zabezpečení Azure IoT Edge je založen na časově prověřených a osvědčených protokolech zabezpečení pro znalost a opakované použití.

## <a name="authentication"></a>Ověřování

Při nasazení řešení IoT, musíte vědět, že pouze důvěryhodné objekty actor, zařízení a moduly mají přístup k vašemu řešení. Ověřování na základě certifikátu je primární mechanismus pro ověřování pro platformu Azure IoT Edge. Tento mechanismus je odvozen ze souboru standardů, kterými se řídí infrastruktura veřejných klíčů (PKiX) podle Internet Engineering Task Force (IETF).

Všechna zařízení, moduly a objekty actor, které interagují se zařízením Azure IoT Edge, by měly mít jedinečné identity certifikátů. Tento návod platí bez ohledu na to, zda jsou interakce fyzické nebo prostřednictvím síťového připojení. Ne každý scénář nebo součást může půjčovat sám na ověřování na základě certifikátu, takže rozšiřitelnost rozhraní zabezpečení nabízí zabezpečené alternativy.

Další informace najdete v [tématu využití certifikátu Azure IoT Edge](iot-edge-certs.md).

## <a name="authorization"></a>Autorizace

Princip nejnižšího oprávnění říká, že uživatelé a součásti systému by měli mít přístup pouze k minimální sadě prostředků a dat potřebných k plnění jejich rolí. Zařízení, moduly a objekty actor by měly přistupovat pouze k prostředkům a datům v rámci jejich oboru oprávnění a pouze v případě, že je architektonicky povolena. Některá oprávnění lze konfigurovat s dostatečnými oprávněními a jiná jsou architektonicky vynucena. Některé moduly mohou být například autorizované pro připojení k Azure IoT Hub. Neexistuje však žádný důvod, proč by modul v jednom zařízení IoT Edge měl přistupovat ke dvojčeti modulu v jiném zařízení IoT Edge.

Mezi další autorizační schémata patří podpisová práva k certifikátu a řízení přístupu na základě rolí (RBAC).

## <a name="attestation"></a>Osvědčení

Atestace zajišťuje integritu softwarových bitů, což je důležité pro detekci a prevenci malwaru. Rámec zabezpečení Azure IoT Edge klasifikuje atestace do tří hlavních kategorií:

* Statické osvědčení
* Atestace za běhu
* Atestace softwaru

### <a name="static-attestation"></a>Statické osvědčení

Statické ověření ověřuje integritu veškerého softwaru na zařízení během zapnutí, včetně operačního systému, všech runtimes a informací o konfiguraci. Vzhledem k tomu, že statické atestace dochází během zapnutí, je často označována jako bezpečné spuštění. Rámec zabezpečení pro zařízení IoT Edge se vztahuje na výrobce a zahrnuje zabezpečené hardwarové funkce, které zajišťují statické procesy atestace. Tyto procesy zahrnují zabezpečené spuštění a upgrade firmwaru. Úzká spolupráce s dodavateli křemíku eliminuje nadbytečné vrstvy firmwaru, takže minimalizuje plochu hrozby.

### <a name="runtime-attestation"></a>Atestace za běhu

Jakmile systém dokončí bezpečný zaváděcí proces, dobře navržené systémy by měly detekovat pokusy o injekci malwaru a přijmout správná protiopatření. Útoky malwaru se mohou zaměřit na porty a rozhraní systému. Pokud mají uživatelé se zlými úmysly fyzický přístup k zařízení, mohou manipulovat se samotným zařízením nebo k získání přístupu použít útoky na straně kanálu. Takový nespokojený, ať už malware nebo neoprávněné změny konfigurace, nelze zjistit statickým potvrzením, protože je injektován po spuštění procesu. Protiopatření nabízená nebo vynucená hardwarem zařízení pomáhají tyto hrozby odvrátit. Rámec zabezpečení pro IoT Edge explicitně volá rozšíření, která bojují proti hrozbám za běhu.  

### <a name="software-attestation"></a>Atestace softwaru

Všechny zdravé systémy, včetně inteligentních hraničních systémů, potřebují opravy a upgrady. Zabezpečení je důležité pro procesy aktualizace, jinak mohou být potenciálními vektory hrozeb. Rámec zabezpečení pro IoT Edge vyžaduje aktualizace prostřednictvím měřených a podepsaných balíčků, aby byla zajištěna integrita a ověření zdroje balíčků. Tato norma platí pro všechny operační systémy a bity aplikačního softwaru.

## <a name="hardware-root-of-trust"></a>Hardwarový kořen důvěryhodnosti

Pro mnoho inteligentních hraničních zařízení, zejména zařízení, která mohou být fyzicky přístupná potenciálními škodlivými aktéry, je zabezpečení hardwaru poslední obranou pro ochranu. Hardware odolný proti manipulaci je pro taková nasazení zásadní. Azure IoT Edge doporučuje dodavatelům zabezpečeného křemíkového hardwaru, aby nabízeli různé varianty hardwarového kořene důvěryhodnosti, aby vyhověli různým rizikovým profilům a scénářům nasazení. Důvěryhodnost hardwaru může pocházet ze společných standardů protokolu zabezpečení, jako je Trusted Platform Module (ISO/IEC 11889) a Trusted Computing Group Device Identifier Composition Engine (DICE). Zabezpečené enklávové technologie jako TrustZones a Rozšíření zabezpečení softwaru (SGX) také poskytují důvěryhodnost hardwaru.

## <a name="certification"></a>Certifikace

Aby zákazníci měli možnost činit informovaná rozhodnutí při obstarávání zařízení Azure IoT Edge pro jejich nasazení, zahrnuje architektura IoT Edge požadavky na certifikaci. Základem těchto požadavků jsou certifikace týkající se bezpečnostních tvrzení a certifikací týkajících se ověření implementace zabezpečení. Například certifikace deklarace zabezpečení znamená, že zařízení IoT Edge používá zabezpečený hardware, o kterém je známo, že odolává útokům při spuštění. Certifikace ověření znamená, že zabezpečený hardware byl správně implementován tak, aby tuto hodnotu v zařízení nabízel. V souladu se zásadou jednoduchosti se rámec snaží udržet zátěž certifikace minimální.

## <a name="extensibility"></a>Rozšiřitelnost

Díky technologiím IoT, které pohánějí různé typy obchodních transformací, by se zabezpečení mělo vyvíjet souběžně a řešit vznikající scénáře. Architektura zabezpečení Azure IoT Edge začíná pevným základem, na kterém se buduje v rozšiřitelnosti do různých dimenzí, aby zahrnovala:

* Služby zabezpečení první strany, jako je služba zřizování zařízení pro Azure IoT Hub.
* Služby třetích stran, jako jsou spravované služby zabezpečení pro různé vertikály aplikací (jako je průmyslová nebo zdravotní péče) nebo zaměření na technologie (jako je monitorování zabezpečení v sítích ok nebo služby atestace křemíku hardwaru) prostřednictvím bohaté sítě Partnery.
* Starší systémy zahrnují dovybavení alternativními strategiemi zabezpečení, jako je použití jiné zabezpečené technologie než certifikátů pro ověřování a správu identit.
* Bezpečný hardware pro přijetí nově vznikajících bezpečných hardwarových technologií a příspěvků křemíkových partnerů.

Zajištění inteligentního okraje nakonec vyžaduje příspěvky na spolupráci od otevřené komunity založené na společném zájmu na zabezpečení IoT. Tyto příspěvky mohou mít formu bezpečných technologií nebo služeb. Architektura zabezpečení Azure IoT Edge nabízí pevný základ pro zabezpečení, který je rozšiřitelný pro maximální pokrytí a nabízí stejnou úroveň důvěryhodnosti a integrity v inteligentním okraji jako v cloudu Azure.  

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure IoT Edge [zabezpečuje inteligentní výhodu](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
