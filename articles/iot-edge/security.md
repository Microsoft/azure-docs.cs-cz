---
title: Zabezpečení ve službě Azure IoT Edge | Dokumentace Microsoftu
description: Zabezpečení, ověřování a autorizace zařízení IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4292dc3abf775d48ee33cc7a48793d080156efb1
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568933"
---
# <a name="securing-azure-iot-edge"></a>Zabezpečení Azure IoT Edge

Zajištění inteligentních hraničních zařízení je potřeba udělit důvěru ve fungování kompletní řešení IoT. Azure IoT Edge je navržená pro zabezpečení, které je možné rozšířit do různých rizikových profilů, scénářích nasazení a nabízí stejnou úroveň ochrany, který očekáváte, že ze všech služeb Azure.

Azure IoT Edge běží na jiném hardwaru, podporuje Linux i Windows a vztahuje se na různých nasazení scénáře.  Posouzené riziko závisí na mnoha důležité informace, včetně řešení vlastnictví, zeměpisnou oblast nasazení, citlivosti dat, ochrany osobních údajů, svislé a zákonným požadavkům aplikace.  Místo nabízí konkrétní řešení pro konkrétní scénáře, je vhodné navrhovat rozhraní rozšiřitelných zabezpečení založené na základě dobře konkrétních zásady navržené pro škálování. 
 
Tento článek obsahuje přehled architektury zabezpečení. Další informace najdete v tématu [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy podporovat snadnost kontroly a snadné implementaci, které jsou hallmark zabezpečení.  Řešení dobře navrženou zabezpečení by měl řešení kontroly podle hodnocení k vytvoření vztahu důvěryhodnosti a neměly by být mezní k nasazení.  Návrh architektury pro zabezpečení Azure IoT Edge vychází z prověřené a ověřené zabezpečení protokoly využít důvěrně a opakované použití. 

## <a name="authentication"></a>Authentication

Bez nejistým vědět, jaké objekty actor, zařízení a komponenty se účastní doručování hodnoty pomocí kompletního řešení IoT je prvořadá při vytváření vztahu důvěryhodnosti.  Tyto znalosti nabízí zabezpečené zodpovědnost účastníkům povolení základ pro přijetí.  Azure IoT Edge dosáhne tyto znalosti prostřednictvím ověřování.  Hlavní mechanismus pro ověřování pro platformu Azure IoT Edge je ověřování prostřednictvím certifikátu.  Tento mechanismus je odvozena ze sady standardů pro řízení infrastruktury veřejných klíčů (PKiX) podle Engineering Task Force IETF (Internet).     

Volá framework zabezpečení Azure IoT Edge pro identity jedinečný certifikát pro zařízení, moduly (kontejnery, které provádí zapouzdření logiky v rámci zařízení) a objekty actor interakci s zařízení Azure IoT Edge, už to jsou fyzicky nebo prostřednictvím připojení k síti.  Každý scénář nebo součásti mohou řešení k ověřování na základě které extensibility framework zabezpečení nabízí zabezpečené cesty vedoucí pro umístění. 

## <a name="authorization"></a>Autorizace

Možnost delegování přístupu autorita a ovládací prvek je zásadní dosahování zabezpečení základní princip – principem minimálního oprávnění.  Zařízení, moduly a objekty actor mohou získat přístup jenom k prostředkům a datům v rámci oboru jejich oprávnění a pouze v případě, že je architektonicky povolená.  To znamená, že některá oprávnění se dají konfigurovat pomocí dostatečná oprávnění a další vynucená architektonicky.  Například když modul může oprávnění prostřednictvím privilegovaných konfigurace k navázání připojení ke službě Azure IoT Hub, neexistuje žádný důvod, proč by měl modul na jednom zařízení Azure IoT Edge přístup k dvojčeti modulu v jiném zařízení Azure IoT Edge.  Z tohoto důvodu ten by architektonicky vyloučen. 

Jiná schémata autorizace zahrnují práva na podpisový certifikát a řízení přístupu na základě role (RBAC).  Rozšiřitelnost rozhraní zabezpečení povoluje přijetí jiná schémata až po zralé autorizace. 

## <a name="attestation"></a>Osvědčení

Ověření identity zajišťuje integritu softwaru bitů.  Je důležité pro odhalování a prevenci malwaru.  Architektury zabezpečení Azure IoT Edge klasifikuje ověření v rámci tří hlavních kategorií:

* Statické ověření
* Ověření modulu runtime
* Ověření softwaru

### <a name="static-attestation"></a>Statické ověření

Statické ověření je ověřování integrity všech součástí instalace softwaru včetně operačních systémů, všechny moduly runtime, a zvyšte výkon konfiguračních informací během zařízení.  To se často označuje jako zabezpečeného spuštění.  Architektura zabezpečení pro zařízení Azure IoT Edge rozšiřuje silicon dodavatelům a zahrnuje funkce a bezpečný hardware ingrained, aby zajistil statické ověření procesy. Tyto procesy zahrnout zabezpečené spuštění a zabezpečené firmware upgradovat procesy.  V úzké spolupráci s dodavateli z křemíku eliminuje nadbytečné firmware vrstvy a tím minimalizovat plochu hrozeb. 

### <a name="runtime-attestation"></a>Ověření modulu runtime

Jakmile systém dokončil proces ověřené spouštění a je v provozu a bude zjišťovat spuštěné, dobře navržená zabezpečené systémy pokusí vložit škodlivého softwaru prostřednictvím systémy porty a rozhraní a provést řádné protiopatření.  Pro inteligentní hraniční zařízení ve fyzické vazbě útočníky je možné vložit malcontent prostředky jiné než rozhraní zařízení, jako jsou falšování a útoky na straně kanálu.   Takové malcontent, což může být ve formě malwaru nebo změny neoprávněné konfigurace, by obvykle není detekovat procesem zabezpečeného spuštění, protože k nim dojde po spuštění procesu.  Opatření nabízí nebo vynucuje výrazně hardwaru zařízení je pak možné warding vypnutí těchto hrozeb.  Architektura zabezpečení pro Azure IoT Edge se explicitně zdůrazňuje pro rozšíření pro combatting runtime hrozby.     

### <a name="software-attestation"></a>Ověření softwaru

Všechny systémy v pořádku, včetně systémů inteligentních hraničních zařízení musí být vydávání kompaktních opravy a upgrady.  Zabezpečení je důležité pro procesy, které aktualizace v opačném případě, že můžou být potenciální jinými vektory hrozeb.  Architektura zabezpečení pro Azure IoT Edge volání aktualizací prostřednictvím měří a podepsaných balíčků pro zajistit integritu a ověřování zdroje balíčků.  To se vztahuje na všechny operační systémy a aplikace softwaru bitů. 

## <a name="hardware-root-of-trust"></a>Důvěryhodný kořenový certifikát hardwaru

V mnoha nasazeních systému inteligentních hraničních zařízení, zejména těch, které jsou nasazeny v místech, kde potenciální útočníky mají fyzický přístup k zařízení, je zabezpečení nabízené hardwaru zařízení poslední defense pro ochranu.  Z tohoto důvodu je nejvíce zásadní pro taková nasazení kotvícího důvěru v odolné proti hardwaru.  Architektura zabezpečení pro Azure IoT Edge zahrnuje spolupráci dodavateli hardwaru zabezpečené silicon nabízí různými charakteristikami hardwaru kořenového vztahu důvěryhodnosti pro různé profily rizik a scénáře nasazení. Mezi ně patří použití zabezpečeného silicon týkajícími se běžných standardů zabezpečení protokolu, jako je Trusted Platform Module (ISO/IEC 11889) a Trusted Computing Group od zařízení identifikátor DICE (Composition Engine).  Patří sem také zabezpečené enklávy technologií, jako je TrustZones a Software Guard rozšíření (SGX). 

## <a name="certification"></a>Certifikace

Azure IoT Edge framework pomáhá zákazníkům umožňují přijímat informovaná rozhodnutí při opatřování zařízení Azure IoT Edge pro jejich nasazení, obsahuje požadavky na certifikaci.  Základní tyto požadavky jsou certifikace týkající se zabezpečení deklarace identity a certifikací vztahující se k ověření implementaci zabezpečení.  Certifikace zabezpečení deklarace identity by například informovat, že zařízení IoT Edge používá zabezpečený hardware známé odolat spuštění útoků. Ověření certifikace by informovat, že byl zabezpečený hardware správné implementaci nabízí tuto hodnotu v zařízení.  V souladu se zásadou jednoduchost rozhraní framework nabízí pro zpracování obrazu zachovat minimální režie certifikační.   

## <a name="extensibility"></a>Rozšiřitelnost

Rozšiřitelnost je prvotřídní občanem v rámci zabezpečení Azure IoT Edge.  S technologií IoT řízení různých typů transformace firmy stojí z důvodu, že zabezpečení by měla bez problémů umožněte vývoj za lockstep adresu nově vznikající scénáře.  Architektury zabezpečení Azure IoT Edge začíná slušný základ, na kterém sestavení v rozšíření do různých dimenzí, které chcete zahrnout: 

* Zabezpečení služeb, jako jsou služby Device Provisioning pro službu Azure IoT Hub.
* Třeba služby třetích stran, jako je zabezpečení spravované služby pro vertikálně jinou aplikaci (například průmyslové nebo zdravotní péče) nebo technologie fokus (jako je monitorování v zabezpečení sítě, sítě nebo službách ověření hardwaru silicon) prostřednictvím bohaté sítě partneři.
* Pomocí zabezpečeného technologie než certifikáty pro ověřování a správě identit, jako je starší verze systémů mají zahrnout zpětné přidání s alternativní bezpečnostní strategie.
* Zabezpečený hardware pro bezproblémové přijetí nově vznikající technologie a bezpečný hardware a silicon příspěvky partnera.

Jedná se o jen pár příkladů dimenzí pro rozšíření a architektury zabezpečení Azure IoT Edge je navržena jako zabezpečení od samotného začátku pro podporu tohoto rozšíření. 

Nakonec nejvyšší úspěch při zabezpečení inteligentních hraničních zařízení výsledkem spolupráci příspěvků v komunitě open využitím společný zájem o zabezpečení IoT.  Tyto příspěvky mohou být ve tvaru zabezpečené technologií nebo služeb.  Architektura zabezpečení Azure IoT Edge nabízí solidní základ pro zabezpečení, která je rozšiřitelná v zájmu maximálního pokrytí nabízí stejnou úroveň důvěryhodnosti a integrity v inteligentních hraničních zařízení jako pomocí cloudu Azure.  

## <a name="next-steps"></a>Další postup

Další informace o tom, jak Azure IoT Edge je [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
