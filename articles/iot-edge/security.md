---
title: Architektury zabezpečení – Azure IoT Edge | Dokumentace Microsoftu
description: Další informace o zabezpečení, ověřování a autorizace standardy, které byly použity k vývoji Azure IoT Edge a by měly být považovány za při návrhu řešení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760109"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpečení Azure IoT Edge

Azure IoT Edge řeší rizika spojená s přesunem dat a analýzou na inteligentní hranici. IoT Edge standardy zabezpečení vyrovnává flexibilitu u různých scénářů nasazení s ochranou, kterou očekáváte ze všech služeb Azure.

IoT Edge běží na různých modelech a modelech hardwaru, podporuje několik operačních systémů a platí pro různé scénáře nasazení. Místo toho, aby nabízel konkrétní řešení pro konkrétní scénáře, IoT Edge je rozšiřitelné rozhraní zabezpečení založené na dobře dodaných principech, které jsou navržené pro škálování. Riziko scénáře nasazení závisí na mnoha faktorech, včetně:

* Vlastnictví řešení
* Geografické nasazení
* Citlivost dat
* Ochrana osobních údajů
* Aplikace svisle
* Zákonné požadavky

Tento článek poskytuje přehled rozhraní IoT Edge Security Framework. Další informace najdete v tématu [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy podporují usnadnění kontroly a snadné implementace, z nichž obě jsou hallmarksy zabezpečení. Bezpečnostní řešení by mělo být samo pro kontrolu v rámci vyhodnocení na vytvoření vztahu důvěryhodnosti sestavení a nemělo by se jednat o mezní hodnotu pro nasazení. Návrh architektury pro zabezpečení Azure IoT Edge je založený na čase testovaném a osvědčeným protokolům zabezpečení pro zajištění znalostí a opakovaného použití.

## <a name="authentication"></a>Ověření

Když nasadíte řešení IoT, musíte mít jistotu, že k vašemu řešení mají přístup jenom důvěryhodní aktéri, zařízení a moduly. Ověřování založené na certifikátech je primární mechanismus ověřování pro Azure IoT Edge platformu. Tento mechanismus je odvozený ze sady standardů, kterými se řídí infrastruktura veřejných klíčů (PKiX) společností IETF (Internet Engineering Task Force).

Všechna zařízení, moduly a objekty actor, které komunikují se zařízením Azure IoT Edge, by měly mít jedinečné identity certifikátů. Tento návod se vztahuje na to, jestli jsou tyto interakce fyzické nebo přes síťové připojení. Ne každý scénář nebo součást mohou poskytovat ověřování na základě certifikátu, takže rozšiřitelnost architektury zabezpečení nabízí zabezpečené alternativy.

Další informace najdete v tématu [Azure IoT Edge využití certifikátu](iot-edge-certs.md).

## <a name="authorization"></a>Autorizace

Principem nejnižší úrovně oprávnění je, že uživatelé a součásti systému by měli mít přístup jenom k minimální sadě prostředků a datům potřebným k provádění jejich rolí. Zařízení, moduly a aktéri by měly přistupovat pouze k prostředkům a datům v rámci jejich oboru oprávnění a pouze v případě, že je možné je architektonické. Některá oprávnění se dají konfigurovat s dostatečnými oprávněními a jiné jsou vynutily architekturu. Některé moduly můžou být například autorizované pro připojení k Azure IoT Hub. Neexistuje proto žádný důvod, proč by měl modul v jednom IoT Edge zařízení přistupovat k podmnožině modulu v jiném zařízení IoT Edge.

Mezi další autorizační schémata patří oprávnění k podepisování certifikátů a řízení přístupu na základě role (RBAC).

## <a name="attestation"></a>Osvědčení

Ověřování identity zajišťuje integritu softwarových bitů, což je důležité pro detekci a zabránění malwaru. Architektury zabezpečení Azure IoT Edge klasifikuje ověření v rámci tří hlavních kategorií:

* Statické ověření
* Ověření modulu runtime
* Ověření softwaru

### <a name="static-attestation"></a>Statické ověření

Statické ověření identity ověřuje integritu veškerého softwaru v zařízení během napájení, včetně operačního systému, všech modulů runtime a informací o konfiguraci. Vzhledem k tomu, že během napájení probíhá statické ověření, často se označuje jako zabezpečené spouštění. Architektura zabezpečení pro IoT Edge zařízení se rozšiřuje na výrobce a zahrnuje zabezpečené hardwarové možnosti, které zajišťují statické procesy ověření identity. Tyto procesy zahrnují upgrade zabezpečeného spouštění a zabezpečeného firmwaru. Při práci v těsné spolupráci s dodavateli silikonu eliminují nadbytečné vrstvy firmwaru. tím se minimalizuje hladina hrozeb.

### <a name="runtime-attestation"></a>Ověření modulu runtime

Jakmile systém dokončí proces zabezpečeného spouštění, dobře navržené systémy by měly detekovat pokusy o vložení malwaru a přijmout vhodná opatření. Útoky na malware můžou cílit na porty a rozhraní systému. Pokud mají škodlivých aktérů fyzický přístup k zařízení, můžou se zabývat samotným zařízením nebo využívat útoky na straně kanálu k získání přístupu. Takové malcontent, jestli se změny malwaru nebo neoprávněné konfigurace nedají zjistit pomocí statického ověření identity, protože se vloží po spouštěcím procesu. Protiopatření, která nabízí nebo vynutila hardwarová ochrana zařízení předá tyto hrozby dál. Rozhraní zabezpečení pro IoT Edge explicitně volá pro rozšíření, která bojovat proti hrozbám za běhu.  

### <a name="software-attestation"></a>Ověření softwaru

Všechny zdravé systémy, včetně inteligentních hraničních systémů, vyžadují opravy a upgrady. Zabezpečení je důležité pro procesy aktualizace, jinak můžou být potenciální vektory hrozeb. Rozhraní zabezpečení pro IoT Edge volá aktualizace přes měřené a podepsané balíčky, aby se zajistila integrita a ověření zdroje balíčků. Tento standard se vztahuje na všechny operační systémy a bity softwaru aplikací.

## <a name="hardware-root-of-trust"></a>Důvěryhodný kořenový certifikát hardwaru

Pro mnoho inteligentních hraničních zařízení, zejména zařízení, která můžou být fyzicky dostupná pro potenciálně škodlivé objekty actor, je zabezpečení hardwaru poslední ochranou pro ochranu. Pro taková nasazení je rozhodující zásadní neoprávněný hardware. Azure IoT Edge podporuje v případě různých profilů rizik a scénářů nasazení různé typy hardwarových kořenových certifikátů, které jsou součástí důvěryhodnosti hardwaru. Vztah důvěryhodnosti hardwaru může pocházet z běžných standardů protokolu zabezpečení, jako je například čip Trusted Platform Module (ISO/IEC 11889) a modul složení identifikátoru zařízení (kost) důvěryhodné výpočetní skupiny. Zabezpečte také enklávy technologie, jako je TrustZones a rozšíření software Guard (SGX).

## <a name="certification"></a>Certifikace

Aby zákazníci mohli dělat kvalifikovaná rozhodnutí při nastavování Azure IoT Edge zařízení pro své nasazení, IoT Edge rámec zahrnuje požadavky na certifikaci. Základní tyto požadavky jsou certifikace týkající se zabezpečení deklarace identity a certifikací vztahující se k ověření implementaci zabezpečení. Například certifikace deklarací zabezpečení znamená, že zařízení IoT Edge používá zabezpečený hardware známý k napadení spouštění. Certifikace ověřování znamená, že zabezpečený hardware byl správně implementován, aby nabídl tuto hodnotu v zařízení. V souladu s principem jednoduchosti se systém pokusí udržet zatížení, které je minimální.

## <a name="extensibility"></a>Rozšíření

Díky technologii IoT s různými typy obchodních transformací by se zabezpečení mělo vyvíjet paralelně a řešit nově vznikající scénáře. Architektury zabezpečení Azure IoT Edge začíná slušný základ, na kterém sestavení v rozšíření do různých dimenzí, které chcete zahrnout:

* Zabezpečení služeb, jako jsou služby Device Provisioning pro službu Azure IoT Hub.
* Služby třetích stran, jako jsou spravované služby zabezpečení pro různé vertikální aplikace (jako je průmysl nebo zdravotnictví) nebo zaměření na technologie (například monitorování zabezpečení v sítích sítí nebo Silicon služby ověření identity hardwaru) prostřednictvím rozsáhlé sítě její.
* Pomocí zabezpečeného technologie než certifikáty pro ověřování a správě identit, jako je starší verze systémů mají zahrnout zpětné přidání s alternativní bezpečnostní strategie.
* Zabezpečte hardware pro přijímání vznikajících zabezpečených hardwarových technologií a příspěvků na Silicon partnerům.

V konečném případě zabezpečení inteligentního Edge vyžaduje spolupráci od otevřené komunity, která je na základě společného zájmu v zabezpečení IoT. Tyto příspěvky mohou být ve tvaru zabezpečené technologií nebo služeb. Architektura zabezpečení Azure IoT Edge nabízí solidní základ pro zabezpečení, která je rozšiřitelná v zájmu maximálního pokrytí nabízí stejnou úroveň důvěryhodnosti a integrity v inteligentních hraničních zařízení jako pomocí cloudu Azure.  

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak Azure IoT Edge je [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
