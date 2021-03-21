---
title: Rozhraní zabezpečení – Azure IoT Edge | Microsoft Docs
description: Přečtěte si o zabezpečení, ověřování a ověřovacích standardech, které se použily k vývoji Azure IoT Edge a měli byste je brát v úvahu při návrhu řešení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ff154eee6c6174a8f1a3aa7bea37ef62273bb1a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489498"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpečení pro Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge řeší rizika spojená s přesunem dat a analýzou na inteligentní hranici. IoT Edge standardy zabezpečení vyrovnává flexibilitu u různých scénářů nasazení s ochranou, kterou očekáváte ze všech služeb Azure.

IoT Edge běží na různých modelech a modelech hardwaru, podporuje několik operačních systémů a platí pro různé scénáře nasazení. Místo toho, aby nabízel konkrétní řešení pro konkrétní scénáře, IoT Edge je rozšiřitelné rozhraní zabezpečení založené na dobře dodaných principech, které jsou navržené pro škálování. Riziko scénáře nasazení závisí na mnoha faktorech, včetně:

* Vlastnictví řešení
* Geografické nasazení
* Citlivost dat
* Ochrana osobních údajů
* Aplikace svisle
* Zákonné požadavky

Tento článek poskytuje přehled rozhraní IoT Edge Security Framework. Další informace najdete v tématu [zabezpečení inteligentního Edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy podporují usnadnění kontroly a snadné implementace, z nichž obě jsou hallmarksy zabezpečení. Bezpečnostní řešení by mělo být samo pro kontrolu v rámci vyhodnocení na vytvoření vztahu důvěryhodnosti sestavení a nemělo by se jednat o mezní hodnotu pro nasazení. Návrh architektury pro zabezpečení Azure IoT Edge je založený na čase testovaném a osvědčeným protokolům zabezpečení pro zajištění znalostí a opakovaného použití.

## <a name="authentication"></a>Authentication

Když nasadíte řešení IoT, musíte mít jistotu, že k vašemu řešení mají přístup jenom důvěryhodní aktéri, zařízení a moduly. Ověřování založené na certifikátech je primární mechanismus ověřování pro Azure IoT Edge platformu. Tento mechanismus je odvozený ze sady standardů, kterými se řídí infrastruktura veřejných klíčů (PKiX) společností IETF (Internet Engineering Task Force).

Všechna zařízení, moduly a objekty actor, které komunikují se zařízením Azure IoT Edge, by měly mít jedinečné identity certifikátů. Tento návod se vztahuje na to, jestli jsou tyto interakce fyzické nebo přes síťové připojení. Ne každý scénář nebo součást mohou poskytovat ověřování na základě certifikátu, takže rozšiřitelnost architektury zabezpečení nabízí zabezpečené alternativy.

Další informace najdete v tématu [Azure IoT Edge využití certifikátu](iot-edge-certs.md).

## <a name="authorization"></a>Autorizace

Principem nejnižší úrovně oprávnění je, že uživatelé a součásti systému by měli mít přístup jenom k minimální sadě prostředků a datům potřebným k provádění jejich rolí. Zařízení, moduly a aktéri by měly přistupovat pouze k prostředkům a datům v rámci jejich oboru oprávnění a pouze v případě, že je možné je architektonické. Některá oprávnění se dají konfigurovat s dostatečnými oprávněními a jiné jsou vynutily architekturu. Některé moduly můžou být například autorizované pro připojení k Azure IoT Hub. Neexistuje proto žádný důvod, proč by měl modul v jednom IoT Edge zařízení přistupovat k podmnožině modulu v jiném zařízení IoT Edge.

Mezi další autorizační schémata patří oprávnění k podepisování certifikátů a řízení přístupu na základě role (RBAC).

## <a name="attestation"></a>Attestation

Ověřování identity zajišťuje integritu softwarových bitů, což je důležité pro detekci a zabránění malwaru. Rozhraní Azure IoT Edge Security Framework klasifikuje ověřování v rámci tří hlavních kategorií:

* Statické ověření identity
* Ověření identity za běhu
* Ověření identity softwaru

### <a name="static-attestation"></a>Statické ověření identity

Statické ověření identity ověřuje integritu veškerého softwaru v zařízení během napájení, včetně operačního systému, všech modulů runtime a informací o konfiguraci. Vzhledem k tomu, že během napájení probíhá statické ověření, často se označuje jako zabezpečené spouštění. Architektura zabezpečení pro IoT Edge zařízení se rozšiřuje na výrobce a zahrnuje zabezpečené hardwarové možnosti, které zajišťují statické procesy ověření identity. Tyto procesy zahrnují upgrade zabezpečeného spouštění a zabezpečeného firmwaru. Při práci v těsné spolupráci s dodavateli silikonu eliminují nadbytečné vrstvy firmwaru. tím se minimalizuje hladina hrozeb.

### <a name="runtime-attestation"></a>Ověření identity za běhu

Jakmile systém dokončí proces zabezpečeného spouštění, dobře navržené systémy by měly detekovat pokusy o vložení malwaru a přijmout vhodná opatření. Útoky na malware můžou cílit na porty a rozhraní systému. Pokud mají škodlivých aktérů fyzický přístup k zařízení, můžou se zabývat samotným zařízením nebo využívat útoky na straně kanálu k získání přístupu. Takové malcontent, jestli se změny malwaru nebo neoprávněné konfigurace nedají zjistit pomocí statického ověření identity, protože se vloží po spouštěcím procesu. Protiopatření, která nabízí nebo vynutila hardwarová ochrana zařízení předá tyto hrozby dál. Rozhraní zabezpečení pro IoT Edge explicitně volá pro rozšíření, která bojovat proti hrozbám za běhu.  

### <a name="software-attestation"></a>Ověření identity softwaru

Všechny zdravé systémy, včetně inteligentních hraničních systémů, vyžadují opravy a upgrady. Zabezpečení je důležité pro procesy aktualizace, jinak můžou být potenciální vektory hrozeb. Rozhraní zabezpečení pro IoT Edge volá aktualizace přes měřené a podepsané balíčky, aby se zajistila integrita a ověření zdroje balíčků. Tento standard se vztahuje na všechny operační systémy a bity softwaru aplikací.

## <a name="hardware-root-of-trust"></a>Kořen hardwarového vztahu důvěryhodnosti

Pro mnoho inteligentních hraničních zařízení, zejména zařízení, která můžou být fyzicky dostupná pro potenciálně škodlivé objekty actor, je zabezpečení hardwaru poslední ochranou pro ochranu. Pro taková nasazení je rozhodující zásadní neoprávněný hardware. Azure IoT Edge podporuje v případě různých profilů rizik a scénářů nasazení různé typy hardwarových kořenových certifikátů, které jsou součástí důvěryhodnosti hardwaru. Vztah důvěryhodnosti hardwaru může pocházet z běžných standardů protokolu zabezpečení, jako je například čip Trusted Platform Module (ISO/IEC 11889) a modul složení identifikátoru zařízení (kost) důvěryhodné výpočetní skupiny. Zabezpečte také enklávy technologie, jako je TrustZones a rozšíření software Guard (SGX).

## <a name="certification"></a>Certifikace

Aby zákazníci mohli dělat kvalifikovaná rozhodnutí při nastavování Azure IoT Edge zařízení pro své nasazení, IoT Edge rámec zahrnuje požadavky na certifikaci. Základem těchto požadavků jsou certifikace týkající se deklarací zabezpečení a certifikace souvisejících s ověřováním implementace zabezpečení. Například certifikace deklarací zabezpečení znamená, že zařízení IoT Edge používá zabezpečený hardware známý k napadení spouštění. Certifikace ověřování znamená, že zabezpečený hardware byl správně implementován, aby nabídl tuto hodnotu v zařízení. V souladu s principem jednoduchosti se systém pokusí udržet zatížení, které je minimální.

## <a name="extensibility"></a>Rozšiřitelnost

Díky technologii IoT s různými typy obchodních transformací by se zabezpečení mělo vyvíjet paralelně a řešit nově vznikající scénáře. Rozhraní Azure IoT Edge Security Framework začíná systémem Solid Foundation, na kterém sestavuje rozšíření do různých dimenzí, které mají být zahrnuty:

* Služby zabezpečení první strany, jako je služba Device Provisioning pro Azure IoT Hub.
* Služby třetích stran, jako jsou spravované služby zabezpečení pro různé vertikální aplikace (jako je průmysl nebo zdravotnictví) nebo zaměření na technologie (například monitorování zabezpečení v sítích sítí nebo Silicon služby ověření identity hardwaru) prostřednictvím rozsáhlé sítě partnerů.
* Starší systémy, které zahrnují retrofitting s alternativními strategiemi zabezpečení, jako je použití zabezpečené technologie jiné než certifikátů pro ověřování a správu identit.
* Zabezpečte hardware pro přijímání vznikajících zabezpečených hardwarových technologií a příspěvků na Silicon partnerům.

V konečném případě zabezpečení inteligentního Edge vyžaduje spolupráci od otevřené komunity, která je na základě společného zájmu v zabezpečení IoT. Tyto příspěvky můžou být ve formě zabezpečených technologií nebo služeb. Rozhraní Azure IoT Edge Security Framework nabízí pevný základ pro zabezpečení, který je rozšiřitelný pro maximální pokrytí, aby nabízelo stejnou úroveň důvěryhodnosti a integrity na inteligentních hraničních zařízeních jako u cloudu Azure.  

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure IoT Edge [zabezpečení inteligentního Edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
