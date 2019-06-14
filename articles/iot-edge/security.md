---
title: Architektury zabezpečení – Azure IoT Edge | Dokumentace Microsoftu
description: Další informace o zabezpečení, ověřování a autorizace standardy, které byly použity k vývoji Azure IoT Edge a by měly být považovány za při návrhu řešení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8aadddbc9ae13a87f89db4d7e7189ea7aa8aeef5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60612031"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpečení Azure IoT Edge

Azure IoT Edge je navržená k řešení scénářů rizik, které jsou obsaženy při přesunu dat a analýz na inteligentních hraničních zařízení. Standardy zabezpečení IoT Edge poskytují flexibilitu pro scénáře nasazení a různých rizikových profilů současně stále nabízíme ochrany, který očekáváte, že ze všech služeb Azure. 

Azure IoT Edge běží na různých využívá hardware a modely, podporuje několik operačních systémů a platí pro scénáře odlišných nasazení. Hodnocení rizik scénář nasazení závisí na mnoha důležité informace, včetně řešení vlastnictví, zeměpisnou oblast nasazení, citlivosti dat, ochrany osobních údajů, svislé a zákonným požadavkům aplikace. Místo nabídku konkrétní řešení pro konkrétní scénáře IoT Edge je rozšiřovatelnou platformu pro extensible zabezpečení založené na základě dobře konkrétních zásady, které jsou navržené pro škálování. 
 
Tento článek obsahuje přehled architektury zabezpečení IoT Edge. Další informace najdete v tématu [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy podporovat snadnost kontroly a snadné implementaci, které jsou hallmarks zabezpečení. Řešení zabezpečení by měl řešení kontroly podle hodnocení k vytvoření vztahu důvěryhodnosti a neměly by být mezní k nasazení. Návrh architektury pro zabezpečení Azure IoT Edge je založená na prověřené a protokoly zabezpečení osvědčené znalosti a opakované použití. 

## <a name="authentication"></a>Authentication

Když nasadíte řešení IoT, je potřeba vědět, že pouze důvěryhodné objektů actor, zařízení a moduly mají přístup do vašeho řešení. Tyto znalosti nabízí zabezpečené zodpovědnost účastníků. Azure IoT Edge dosáhne tyto znalosti prostřednictvím ověřování. Ověřování pomocí certifikátů je primární mechanismus ověřování pro platformu Azure IoT Edge. Tento mechanismus je odvozena ze sady standardů pro řízení infrastruktury veřejných klíčů (PKiX) podle Engineering Task Force IETF (Internet).     

Všechna zařízení, moduly a objekty actor interakci s zařízení Azure IoT Edge, ať už fyzicky, nebo prostřednictvím připojení k síti, by měly mít jedinečný certifikát identity. Ale ne každý scénář nebo součásti mohou řešení k ověřování prostřednictvím certifikátu. V těchto případech extensibility framework zabezpečení nabízí zabezpečené alternativy. 

## <a name="authorization"></a>Autorizace

Princip nejnižších oprávnění říká, že uživatelé a součástí systému by měly mít přístup jen k minimální sadu prostředků a data potřebná k provedení jejich role. Zařízení, moduly a objekty actor by měl přístup k prostředkům a data v rámci oboru jejich oprávnění a jenom v případě, že je architektonicky povolená. Některá oprávnění se dají konfigurovat s dostatečnými oprávněními a ostatní jsou architektonicky vynucena.  Modul může oprávnění prostřednictvím privilegovaných konfigurace, například k navázání připojení ke službě Azure IoT Hub. Neexistuje však žádný důvod, proč by měl modul na jednom zařízení Azure IoT Edge přístup k dvojčeti modulu v jiném zařízení Azure IoT Edge.

Ostatní schémata autorizace obsahují práva podpisový certifikát a řízení přístupu na základě role (RBAC). 

## <a name="attestation"></a>Osvědčení

Ověření identity zajišťuje integritu softwaru bitů.  Je důležité pro odhalování a prevenci malwaru.  Architektury zabezpečení Azure IoT Edge klasifikuje ověření v rámci tří hlavních kategorií:

* Statické ověření
* Ověření modulu runtime
* Ověření softwaru

### <a name="static-attestation"></a>Statické ověření

Statické ověření ověří integritu veškerého softwaru na zařízení, včetně operačního systému, všechny moduly runtime a konfiguračních informací během Zvyšte výkon zařízení. To se často označuje jako zabezpečeného spuštění. Architektura zabezpečení pro zařízení Azure IoT Edge rozšiřuje na výrobci a zahrnuje zabezpečené hardwarových možnostech, které zajistil statické ověření procesy. Tyto procesy zahrnout zabezpečené spuštění a zabezpečené firmware upgradovat.  V úzké spolupráci s dodavateli, silicon eliminuje nadbytečné firmware vrstvy, takže minimalizuje povrchu před internetovými útoky. 

### <a name="runtime-attestation"></a>Ověření modulu runtime

Jakmile systém dokončil proces zabezpečeného spuštění a je spuštěná, dobře navržené systémy by odhalit pokusy o vložení malware a provést řádné protiopatření. Porty a rozhraní pro přístup k systému v systému, mohou být zaměřeny Malwarových útoků. Nebo pokud útočníky mají fyzický přístup k zařízení mohou manipulovat s samotné zařízení nebo útoků na straně kanálu použije k získání přístupu. Takové malcontent, což může být ve formě malwaru nebo změny konfigurace neoprávněným, nebyl nalezen ve statické ověření vzhledem k tomu, že se vloží po procesu spouštění. Opatření nabízí nebo vynucuje zařízení hardwaru pomáhají zabránit těchto hrozeb.  Pro rozšíření, která proti hrozbám runtime explicitně volá framework zabezpečení pro Azure IoT Edge.  

### <a name="software-attestation"></a>Ověření softwaru

Všechny systémy v pořádku, včetně systémů inteligentních hraničních zařízení, musí přijmout, opravy a upgrady.  Zabezpečení je důležité pro procesy aktualizace v opačném případě, že můžou být potenciální jinými vektory hrozeb.  Architektura zabezpečení pro Azure IoT Edge volání aktualizací prostřednictvím měří a podepsaných balíčků pro zajistit integritu a ověřování zdroje balíčků.  Tento standard platí pro všechny operační systémy a aplikace softwaru bitů. 

## <a name="hardware-root-of-trust"></a>Důvěryhodný kořenový certifikát hardwaru

Zabezpečení hardwarových zařízení pro řadu inteligentních hraničních zařízení zařízení, zejména v místech, kde potenciální útočníky mají fyzický přístup k zařízení, je poslední defense pro ochranu. Odolné proti hardwaru je zásadní pro taková nasazení. Azure IoT Edge podporuje zabezpečené silicon dodavateli hardwaru nabízí různými charakteristikami hardwaru kořenového vztahu důvěryhodnosti pro různé profily rizik a scénáře nasazení. Hardware důvěryhodnosti mohou pocházet z běžných standardů zabezpečení protokolu, jako je Trusted Platform Module (ISO/IEC 11889) a Trusted Computing Group od zařízení identifikátor DICE (Composition Engine). Zabezpečte enklávy technologie jako TrustZones a Software Guard rozšíření (SGX) také poskytují důvěryhodnosti hardwaru. 

## <a name="certification"></a>Certifikace

Azure IoT Edge framework pomáhá zákazníkům umožňují přijímat informovaná rozhodnutí při opatřování zařízení Azure IoT Edge pro jejich nasazení, obsahuje požadavky na certifikaci.  Základní tyto požadavky jsou certifikace týkající se zabezpečení deklarace identity a certifikací vztahující se k ověření implementaci zabezpečení.  Certifikace zabezpečení deklarace identity by například informovat, že zařízení IoT Edge používá zabezpečený hardware známé odolat spuštění útoků. Ověření certifikace by informovat, že byl zabezpečený hardware správné implementaci nabízí tuto hodnotu v zařízení.  V souladu se zásadou jednoduchost rozhraní pokusí zachovat minimální režie certifikace.   

## <a name="extensibility"></a>Rozšiřitelnost

S technologií IoT řízení různých typů transformace firmy stojí z důvodu, že by se měl zabezpečení vyvíjet paralelně na adresu nově vznikající scénáře.  Architektury zabezpečení Azure IoT Edge začíná slušný základ, na kterém sestavení v rozšíření do různých dimenzí, které chcete zahrnout: 

* Zabezpečení služeb, jako jsou služby Device Provisioning pro službu Azure IoT Hub.
* Třeba služby třetích stran, jako je zabezpečení spravované služby pro vertikálně jinou aplikaci (například průmyslové nebo zdravotní péče) nebo technologie fokus (jako je monitorování v zabezpečení sítě, sítě nebo službách ověření hardwaru silicon) prostřednictvím bohaté sítě partneři.
* Pomocí zabezpečeného technologie než certifikáty pro ověřování a správě identit, jako je starší verze systémů mají zahrnout zpětné přidání s alternativní bezpečnostní strategie.
* Zabezpečený hardware pro přijetí nově vznikající technologie a bezpečný hardware a silicon příspěvky partnera.

Nakonec nejvyšší úspěch při zabezpečení inteligentních hraničních zařízení výsledkem spolupráci příspěvků v komunitě open využitím společný zájem o zabezpečení IoT.  Tyto příspěvky mohou být ve tvaru zabezpečené technologií nebo služeb.  Architektura zabezpečení Azure IoT Edge nabízí solidní základ pro zabezpečení, která je rozšiřitelná v zájmu maximálního pokrytí nabízí stejnou úroveň důvěryhodnosti a integrity v inteligentních hraničních zařízení jako pomocí cloudu Azure.  

## <a name="next-steps"></a>Další postup

Další informace o tom, jak Azure IoT Edge je [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
