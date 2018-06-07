---
title: Zabezpečení v Azure IoT Edge | Microsoft Docs
description: Zabezpečení, ověřování a autorizace IoT hraniční zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: effe33182435d2a4afbad3356c6840070ba96325
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631424"
---
# <a name="securing-azure-iot-edge---preview"></a>Zabezpečení Azure IoT okraj – náhled

Zabezpečení inteligentního okraj je nezbytné pro zakládá důvěru operaci koncová řešení IoT. Azure IoT okraj je určená pro zabezpečení, které lze rozšířit do různých riziko profilů, scénáře nasazení a nabízí stejnou úroveň ochrany, které uživatel očekával od všech služeb Azure.

Azure IoT Edge běží na jiný hardware, podporuje Linux a Windows a používá se pro scénáře nasazení různých.  Odhadní riziko závisí na několik rozhodnutí, včetně řešení vlastnictví, geography nasazení, data velkých a malých písmen, ochrany osobních údajů, svislé a zákonné požadavky aplikací.  Místo nabídky konkrétní řešení konkrétních scénářů, má smysl návrh představuje extensible zabezpečení rozhraní založené na dobře grounded zásadách, které jsou určené pro škálování. 
 
Tento článek obsahuje přehled zabezpečení systému. Další informace najdete v tématu [zabezpečení inteligentního edge][lnk-edge-blog].

>[!NOTE]
>Rozhraní zabezpečení popsané dole je nyní probíhá přidání do produktu a bude k dispozici v obecné dostupnosti verzi Azure IoT okraj. Produkt je aktuálně ve verzi public preview, verze určený umožňující vývoj a při vytváření prototypu edge řešení, nasazení v produkčním prostředí není úplná, které je třeba úplné zabezpečení systému.   

## <a name="standards"></a>Standardy

Standardy povýšit snadné kontroly a snadná implementace, které jsou hallmark zabezpečení.  Řešení dobře navrženou zabezpečení by měl jít o kontroly v rámci zkušební verze na vytvoření vztahu důvěryhodnosti a neměla by být mezní do nasazení.  Návrh rozhraní pro zabezpečení Azure IoT Edge vychází z prověřené a protokoly zabezpečení odvětví ověřené využít znalosti a opakované použití. 

## <a name="authentication"></a>Authentication

Znalost bez nejistých, jaké aktéři, zařízení a součásti se účastní doručení hodnotné koncová řešení IoT je prvořadá při vytváření vztahů důvěryhodnosti.  Tyto znalosti nabízí zabezpečené odpovědnosti za účastníků k povolení základ pro jejich příchodu.  Azure IoT Edge dosáhne replikace prostřednictvím ověřování.  Primární mechanismus pro ověřování pro platformu Azure IoT okraj je ověřování pomocí certifikátů.  Tento mechanismus je odvozena ze sady standardů pro řízení infrastruktury veřejných klíčů (PKiX) Internet Engineering Task Force (IETF).     

Zabezpečení rozhraní Azure IoT Edge volá pro identity jedinečný certifikát pro zařízení, moduly (kontejnery, které zapouzdřují logiky v rámci zařízení) a aktéři interakci s Azure IoT hraniční zařízení je jej fyzicky nebo prostřednictvím připojení k síti.  Ne každý scénář nebo součást může jít o ověřování pomocí certifikátů pro kterou rozšiřitelnost rozhraní Framework zabezpečení nabízí zabezpečené cesty pro umístění. 

## <a name="authorization"></a>Autorizace

Možnost delegování autority a řízení přístupu je zásadní k dosahování zásadu základní zabezpečení – Princip nejnižších nutných oprávnění.  Zařízení, moduly a aktéři může získat přístup pouze k prostředkům a datům v rámci oboru jejich oprávnění a pouze v případě, že je z pohledu architektury povolená.  To znamená, že některá oprávnění jsou konfigurovat dostatečná oprávnění a ostatní z pohledu architektury vynucené.  Například když modul může oprávnění prostřednictvím privilegované konfigurace k inicializaci připojení k Azure IoT Hub, neexistuje žádný důvod, proč by měl modul v jednom zařízení Azure IoT Edge přístup twin modulu v jiném zařízení Azure IoT okraj.  Z tohoto důvodu k tomu by z pohledu architektury vyloučen. 

Ostatní metody autorizace zahrnují práva podpisový certifikát a řízení přístupu na základě role (RBAC).  Rozšiřitelnost rozhraní Framework zabezpečení povoluje přijetí jiná schémata vyspělá autorizace. 

## <a name="attestation"></a>Osvědčení

Ověření zajistí integritu softwaru bits.  Je důležité pro odhalování a prevenci malwaru.  Zabezpečení rozhraní Azure IoT Edge klasifikuje ověření v rámci tří kategorií:

* Statické ověření
* Ověření modulu runtime
* Ověření softwaru

### <a name="static-attestation"></a>Statické ověření

Statické ověření se kontroluje integrita všech bitů softwaru, včetně operačních systémů, všechny moduly runtime, a informace o konfiguraci v zařízení zapnutí.  Ho se často označuje jako zabezpečené spouštění.  Zabezpečení rozhraní pro zařízení Azure IoT Edge rozšiřuje silicon dodavatelům a zahrnuje možnosti zabezpečený hardware ingrained, aby zajistil, statické ověření procesy. Tyto procesy zahrnout zabezpečené spouštění a zabezpečené firmware upgrade procesy.  V úzké spolupráci s dodavateli silicon eliminuje nadbytečné firmware vrstev a současně minimalizujete její prostor hrozeb. 

### <a name="runtime-attestation"></a>Ověření modulu runtime

Jakmile systém dokončil proces ověřené spouštění a je v provozu a spuštěný, dobře navrženým zabezpečených systémů by zjistit pokusí vložit škodlivého softwaru prostřednictvím systémy porty a rozhraní a provést řádné protiopatření.  Pro inteligentní hraniční zařízení ve fyzické vazbě nebezpečného actors je možné vložit malcontent způsoby než rozhraní zařízení, jako je falšování a útoky straně kanálu.   Takové malcontent, což může být ve tvaru malwaru nebo změny konfigurace neoprávněným, by obvykle není detekovat procesem zabezpečené spouštění, protože dějí po dokončení procesu spuštění.  Protiopatření nabízené nebo vynucená hardwarem zařízení výrazně přispívá ke warding mimo tyto hrozby.  Architektura zabezpečení pro Azure IoT Edge explicitně volá pro rozšíření pro combatting runtime hrozeb.     

### <a name="software-attestation"></a>Ověření softwaru

Všechny systémy v pořádku včetně inteligentního edge systémy musí být přenášejí opravy a upgrady.  Zabezpečení je důležité pro procesy aktualizace jinak, že může se jednat o potenciálních hrozeb způsobů.  Zabezpečení rozhraní pro volání Azure IoT Edge aktualizací prostřednictvím měří a podepsané balíčky zajistili integritu a ověřování zdroji balíčků.  To se vztahuje na všechny operační systémy a aplikace softwaru bitů. 

## <a name="hardware-root-of-trust"></a>Důvěryhodný kořenový certifikát hardwaru

V mnoha nasazeních systému inteligentního hraniční zařízení, především těch, které jsou nasazené v místech, kde mají potenciální nebezpečného actors fyzický přístup k zařízení, je zabezpečení nabízené hardwaru zařízení poslední obrany pro ochranu.  Z tohoto důvodu je nejvíce zásadní pro takovýchto nasazeních kotvícího vztah důvěryhodnosti v proti zfalšování hardwaru.  Zabezpečení rozhraní pro Azure IoT Edge zahrnuje spolupráce zabezpečené silicon dodavatelů hardwaru a nabídnout různými charakteristikami hardwaru kořenového vztahu důvěryhodnosti, aby dokázala pojmout různé profily riziko a scénáře nasazení. Mezi ně patří použití zabezpečeného silicon se běžné standardy protokolu zabezpečení jako Trusted Platform Module (ISO/IEC 11889) a Trusted Computing Group na zařízení identifikátor složení modulu (ROZČLENĚNÍ).  Patří sem také zabezpečené enclave technologie, jako je TrustZones a softwaru ochrana rozšíření (SGX). 

## <a name="certification"></a>Certifikace

Rozhraní Azure IoT Edge pomoc zákazníkům rozhodování při nákup Azure IoT hraniční zařízení pro jejich nasazení, zahrnuje požadavky na certifikaci.  Základní na tyto požadavky jsou certifikace týkající se zabezpečení deklarací identity a certifikace vztahující se k ověření implementace zabezpečení.  Například certifikační deklarace identity zabezpečení by informovat o tom, že IoT hraniční zařízení používá zabezpečený hardware známé odolat spouštěcí útoky. Ověření certifikační by informovat o tom, aby byl zabezpečený hardware implementovaná správně nabízet tuto hodnotu v zařízení.  V souladu se zásadou jednoduchost nabízí rozhraní vizi omezení zátěže certifikaci minimální.   

## <a name="extensibility"></a>Rozšiřitelnost

Rozšiřitelnost je první třídy občanem v rámci zabezpečení Azure IoT okraj.  Pomocí technologie IoT řídí různé typy obchodního transformace znamená důvodu, že by se měl v lockstep adresu rozvíjející scénáře bezproblémově vyvíjet zabezpečení.  Zabezpečení rozhraní Azure IoT Edge začíná sice solidní základ, na kterém sestavení v rozšíření do různých dimenze zahrnují: 

* První strany zabezpečení služby jako službu zřizování zařízení pro Azure IoT Hub.
* Služby třetích stran, jako služby spravovaných zabezpečení pro jinou aplikaci pocházejícími (třeba průmyslové nebo zdravotní péče) nebo technologie zaměřuje (jako jsou sítě nebo službách ověření hardwaru silicon OK sítě monitorování v zabezpečení) prostřednictvím bohaté síť partneři.
* Starší verze systémů zahrnout modernizace s alternativní bezpečnostní strategie, jako je pomocí technologie zabezpečení než certifikáty pro ověřování a identita správu.
* Zabezpečte hardwaru pro bezproblémové přijetí vznikajícími technologie zabezpečený hardware a silicon příspěvky partnera.

Jedná se jenom několik příkladů dimenze pro rozšiřitelnost a Azure IoT Edge zabezpečení je vytvořeno tak, aby bylo zabezpečené od základů pro podporu tohoto rozšíření. 

V části end nejvyšší úspěch v zabezpečení inteligentního edge výsledkem spolupráce příspěvky z otevřená komunita vycházejí z běžných zájem o zabezpečení IoT.  Tyto příspěvky může být ve formě bezpečné technologií nebo služeb.  Zabezpečení rozhraní Azure IoT Edge nabízí sice solidní základ pro zabezpečení, který je rozšiřitelný pro maximální pokrytí nabízet stejnou úroveň důvěryhodnosti a integrita v inteligentního hranici jako s cloudu Azure.  

## <a name="next-steps"></a>Další postup

Další informace o tom, jak je Azure IoT Edge [zabezpečení inteligentního edge][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 