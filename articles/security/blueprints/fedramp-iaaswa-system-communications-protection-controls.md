---
title: Ochrana komunikace a systému plán, podle kterého - FedRAMP webové aplikace automatizace – dodržování předpisů a zabezpečení Azure
description: FedRAMP webové aplikace automatizace – systém a komunikace ochrany
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 6749ad50cd1ea1cd4ec6ca2f86fef43a9f1515d9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206828"
---
# <a name="system-and-communications-protection-sc"></a>Systém a komunikace ochrany (SC)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 řízení SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Systém a zásady ochrany komunikaci a postupy

**SC-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] systému a komunikaci zásady ochrany, které řeší účel, oboru, role, odpovědnosti, správu závazků, spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění systému a zásady ochrany komunikaci a přidružené systému a ovládací prvky ochrany komunikace; kontroluje a aktualizuje aktuální zásady ochrany systému a komunikace [přiřazení: organizace definované frekvence]; a postupy ochrany systému a komunikace [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady ochrany systému a komunikaci úrovni celého podniku a postupy zákazníka může být dostatečná k vyřešení tohoto ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 řízení SC-2

#### <a name="application-partitioning"></a>Dělení na oddíly aplikací

**SC 2** informace systému odděluje od funkce správy systému informace funkce uživatele (včetně uživatelské rozhraní služby).

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého uživatel funkce odděluje od funkce systému správy prostřednictvím vynucení ovládacích prvků logického přístupu a architektura systému. Funkce uživatele je omezený na rozhraní zákazníka nasazené webové aplikace. Rozhraní pro funkci správy systému jsou oddělené od uživatelského rozhraní. Všechny možnosti připojení správy je přes zabezpečené bastionu hostitele (jumpbox) umístěný v podsíti správy pomocí pravidel skupiny zabezpečení sítě pro omezení přístupu k prostředkům produkční podle potřeby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 řízení SC-3

#### <a name="security-function-isolation"></a>Izolace funkce zabezpečení

**SC-3** informace systému izoluje funkcí zabezpečení z funkcí netýká se zabezpečení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Windows udržuje domén samostatné provádění jednotlivých spuštěných procesů přiřazením privátní virtuální adresní prostor jednotlivých procesů. Kromě toho řešení implementuje architektura a přístupu ovládacích prvků slouží k oddělení funkce zabezpečení potřeby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 řízení SC-4

#### <a name="information-in-shared-resources"></a>Informace v sdílených prostředků

**SC-4** informace brání neoprávněným a neúmyslné informace o přenosu prostřednictvím sdílených systémových prostředků.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Operační systém spravuje prostředky (například paměť, úložiště) tak, aby informace jsou přístupné pouze pro uživatele a role s příslušnými oprávněními. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 řízení SC-5

#### <a name="denial-of-service-protection"></a>Útok na dostupnost služby ochrany

**SC-5** informace systému chrání před nebo omezení důsledků tyto typy útoků DOS: [přiřazení: organizace definované typy odmítnutí služby nebo odkazy na zdroje pro tyto údaje] podle nasazení [přiřazení: ochrana definované organizace zabezpečení].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí aplikační bránu, která zahrnují brány firewall webových aplikací a možnosti vyrovnávání zatížení. Podpora webová vrstva, databázové vrstvy a služby Active Directory nasazených virtuálních počítačů nasazených v sadě dostupnosti škálovatelné. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 řízení SC-6

#### <a name="resource-availability"></a>Dostupnost prostředků

**SC-6** informace systému chrání dostupnost prostředků přidělí [přiřazení: organizace definované prostředky] podle [výběr (jeden nebo více) s prioritou; kvóty; [Přiřazení: ochrana definované organizace zabezpečení]].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Každý proces Windows poskytuje prostředky potřebné ke spuštění programu. Priorita prostředků spravuje operačního systému. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800-53 řízení SC-7a

#### <a name="boundary-protection"></a>Hranice ochrany

**SC 7a** systému informace o monitorování a řídí komunikaci v externí hranice systému a klíče interní hranice v rámci systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí služby Application Gateway, nástroj pro vyrovnávání zatížení a nakonfiguruje pravidla skupiny zabezpečení sítě k řízení commutations na externí hranice a mezi interní podsítě. Aplikační bránu, Vyrovnávání zatížení a událostí skupiny zabezpečení sítě a diagnostické protokoly jsou shromážděny prostřednictvím analýzy protokolů umožňuje monitorování zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 řízení SC-7.b

#### <a name="boundary-protection"></a>Hranice ochrany

**SC-7.b** systém informace implementuje podsítě pro součásti veřejně přístupná systému, které jsou [výběr: fyzicky; logicky] oddělené z interní sítě organizace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí prostředky v architekturu s podsíť samostatný webový, databáze podsíť, podsíť služby Active Directory a podsítě správy. Podsítě jsou logicky oddělených použít na jednotlivé podsítě omezit přenos dat mezi podsítě, které jenom to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě (například externích přenosů nelze získat přístup k databázi, správu, nebo podsítě služby Active Directory). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800-53 řízení SC-7.c

#### <a name="boundary-protection"></a>Hranice ochrany

**SC-7.c** informace systému se připojí k externí sítě nebo systémy informace pouze prostřednictvím spravovaná rozhraní, který se skládá z zařízení pro ochranu hranic uspořádané podle architekturu zabezpečení organizace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí aplikační brány pro správu externí připojení k zákazníka nasazené webové aplikace. Externí připojení pro přístup ke správě jsou omezené na bastionu hostitele nebo IP adresy oprávnění jumpbox nasazena v podsíti správy s pravidla zabezpečení sítě použít k omezení externích připojení ke službě. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800-53 řízení SC-7 (3)

#### <a name="boundary-protection--access-points"></a>Hranice ochrany | Přístupové body

**SC-7 (3)** organizace omezuje počet externí síťové připojení k systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí dvě veřejné IP adresy: jednu asociovaným s bránou aplikace; jeden přidružený k hostiteli bastionu správy / jumpbox. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-4a"></a>.A NIST 800-53 řízení SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Hranice ochrany | Externí Telecommunications služby

**SC-7 (4) .a** organizace implementuje spravovaného rozhraní pro každou službu externí telekomunikace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí dvě veřejné IP adresy: jednu asociovaným s bránou aplikace; jeden přidružený k hostiteli bastionu správy / jumpbox. Správa těchto rozhraní je povolená díky softwarově definované sítě. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-4b"></a>.B NIST 800-53 řízení SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Hranice ochrany | Externí Telecommunications služby

**SC-7 (4) .b** organizace vytvoří zásadu tok provozu pro každé spravované rozhraní.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí dvě veřejné IP adresy: jednu asociovaným s bránou aplikace; jeden přidružený k hostiteli bastionu správy / jumpbox. Správa těchto rozhraní je povolená díky softwarově definované sítě. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-4c"></a>.C NIST 800-53 řízení SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Hranice ochrany | Externí Telecommunications služby

**SC-7 (4) .c** organizace chrání utajení a celistvost informací přenášených po každé rozhraní.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Bránu webové aplikace nasadit pomocí tento plán, podle kterého je nakonfigurovaný s naslouchací proces protokolu HTTPS, následná důvěrnost a integritu relací komunikace. Připojení ke vzdálené ploše na jumpbox jsou šifrované zajištění důvěrnosti a integrity. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-4d"></a>.D NIST 800-53 řízení SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Hranice ochrany | Externí Telecommunications služby

**SC-7 (4) .d** dokumentů. každý výjimkou zásady tok provozu pomocí podpůrné zvláště/obchodních potřeb a dobu trvání kterém musí organizace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nejsou zodpovědná za operací datového centra (Chcete-li zahrnout telecommunications služby). Všechny služby telekomunikace jsou poskytovány a spravuje Microsoft Azure. Tento ovládací prvek je zděděn z Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-4e"></a>.E NIST 800-53 řízení SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Hranice ochrany | Externí Telecommunications služby

**SC-7 (4).e** Organizace zkontroluje výjimky pro zásady tok přenosů [přiřazení: organizace definované frekvence] a odebere výjimky, které již nejsou podporovány explicitní zvláště/obchodních potřeb.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nejsou zodpovědná za operací datového centra (Chcete-li zahrnout telecommunications služby). Všechny služby telekomunikace jsou poskytovány a spravuje Microsoft Azure. Tento ovládací prvek je zděděn z Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800-53 řízení SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Hranice ochrany | Odepřít ve výchozím nastavení / Povolit výjimkou

**SC-7 (5)** systému informace v spravovaná rozhraní odmítne síťový provoz komunikace ve výchozím nastavení a umožňuje síťový provoz komunikace výjimkou (tedy Zakázat vše, povolit výjimkou).

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Sady pravidel u nasazeného nástrojem tento plán, podle kterého skupin zabezpečení sítě jsou konfigurováni pomocí odepřít výchozí schéma. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800-53 řízení SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Hranice ochrany | Zakázat dělené tunelové propojení pro vzdálená zařízení

**SC-7 (7)** informace systému, ve spojení se vzdáleným zařízením, zabrání zařízení z současně navazování bez vzdáleného připojení pomocí systému a komunikaci přes jiné připojení k prostředkům v externím sítím.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady Konfigurace vzdáleného zařízení podnikové úrovni zákazníka může adres dělené tunelové propojení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800-53 řízení SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Hranice ochrany | Směrovat provoz na ověřené Proxy servery

**SC-7 (8)** systémové trasy informace [přiřazení: provoz organizace definované interních komunikací] na [přiřazení: organizace definované externí sítě] prostřednictvím ověření proxy servery na spravované rozhraní.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zákazník definovaný informace o směrování přes ověřeného proxy serveru k externí síti. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800-53 řízení SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Hranice ochrany | Zabránit neoprávněným Exfiltration

**SC-7 (10)** organizace brání neoprávněným exfiltration informací mezi spravovaná rozhraní.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za brání neoprávněným exfiltration informace napříč spravovaná rozhraní. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800-53 řízení SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Hranice ochrany | Ochranu založenou na hostiteli

**SC-7 (12)** organizace implementuje [přiřazení: mechanismy ochrany definované organizace založená na hostiteli hranic] na [přiřazení: komponent systému informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán, podle kterého jsou nakonfigurovány s povolena brána firewall založená na hostiteli. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800-53 řízení SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Hranice ochrany | Izolace nástroje zabezpečení nebo mechanismy / podpora součásti

**SC-7 (13)** izoluje organizace [přiřazení: nástroje zabezpečení informace definované organizace, mechanismy a součástí podpory] z dalších komponent systému interní informace implementací fyzicky jednotlivé podsítě s spravovaná rozhraní pro součásti systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí prostředky v architekturu s podsítí samostatné správy pro zákazníka nasazení nástroje zabezpečení informace a podpora součástí. Podsítě jsou logicky odděleny pravidel skupiny zabezpečení sítě. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800-53 řízení SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Hranice ochrany | Selhání zabezpečení

**SC-7 (18)** informace systému selže bezpečně provozní selhání zařízení ochrany hranic.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Nejsou žádné fyzické hranici ochranu zařízení v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nasadí geograficky oddělené a redundantní servery brány a SSL VPN. Při selhání systému brány, bezpečně selže a přístup je omezen na prostředí. Aby bylo možné navázat připojení k prostředí Microsoft Azure, musíte vytvořit uživateli samostatné připojení k je aktivní server brány spravuje Microsoft Azure. <br /> Kromě toho pokud Microsoft Azure síťových zařízení (včetně hraniční směrovače, směrovače přístup, nástroje pro vyrovnávání zatížení, agregace přepínače a MANDÁTU) selžou, ovlivněných okruh stane odpojen, a tím selhání bezpečně. Nelze vést k selhání síťového zařízení Microsoft Azure, nebo způsobit informace, které jsou mimo systém a zadejte zařízení, ani můžete povolit selhání neoprávněným informace o verzi. Integrované redundance umožňuje prostředky Microsoft Azure selhání bez dopadu na dostupnost. |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800-53 řízení SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Hranice ochrany | Dynamické izolace nebo oddělení

**SC-7 (20)** informace systém poskytuje schopnost dynamicky isolate/oddělit [přiřazení: komponent systému informace definované organizace] z dalších komponent systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zajištění, že systém má schopnost dynamicky izolovat zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800-53 řízení SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Hranice ochrany | Izolace informace o systémových součástí

**SC-7 (21)** organizace využívá mechanismy ochrany hranic k oddělení [přiřazení: komponent systému informace definované organizace] podpora [přiřazení: poslání definované organizace nebo podnikovým funkcím].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí prostředky v architekturu s podsíť samostatný webový, databáze podsíť, podsíť služby Active Directory a podsítě správy. Podsítě jsou logicky odděleny použít na jednotlivé podsítě omezit přenos dat mezi podsítě, které jenom to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 řízení SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Důvěrnost přenosu a integrita

**SC-8** chrání informace systému [výběr (jeden nebo více): důvěrnost; integrity] přenášená informací.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | ((1) implementace ma-8 splňuje tento požadavek ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800-53 řízení SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Důvěrnost přenosu a Integrity | Kryptografické nebo alternativní fyzické ochrany

**SC-8 (1)** systém informace implementuje kryptografických mechanismů pro [výběr (jeden nebo více): zabránit neoprávněným úniku informací; zjišťuje změny informací] během přenosu Pokud jinak chráněn [přiřazení: organizace definované alternativní fyzické ochrana].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého se nakonfiguruje prostředky pro komunikaci pomocí protokolů pouze zabezpečené. Komponenta aplikační bránu firewall webových aplikací je nakonfigurován přijmout osob, které informují z externí používá přes HTTPS/TLS a komunikovat s fondu typu back end jenom přes protokol HTTPS/TLS. SQL Server je nakonfigurován pro komunikaci pouze pomocí protokolu HTTPS/TLS. Služba Vzdálená plocha jsou nakonfigurovány pro použití zabezpečeného připojení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800-53 řízení SC-10

#### <a name="network-disconnect"></a>Odpojení síťového

**SC-10** informace systém ukončuje připojení sítě přidružené k relaci komunikace na konci relace nebo po [přiřazení: organizace definované časové období] nečinnosti.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Ověřování pro relace vzdálené plochy je spravovat pomocí služby Active Directory. Jakmile přístup je zakázán pro uživatele ve službě Active Directory, jsou okamžitě ukončena vzdálené relace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 řízení SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Kryptografické klíče zřízení a řízení

**SC-12** organizace vytváří a spravuje kryptografické klíče pro požadované kryptografie pracujících v rámci systému informace v souladu s [přiřazení: požadavky organizace definované pro generování klíčů, distribuci, úložiště, přístupu a odstraňování].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí Azure Key Vault. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Azure Key Vault můžete generovat klíče pomocí FIPS 140-2 úroveň 2 hardwaru zabezpečení (HSM) modul generování klíče schopnosti. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800-53 řízení SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Kryptografické klíče vytvoření a Správa | Dostupnost

**SC-12 (1)** organizace udržuje dostupnost informací v případě ztráty kryptografické klíče uživatelé.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Azure Key Vault se používá k uložení kryptografické klíče a tajné klíče používán tento plán. Key Vault zjednodušuje proces správy klíčů pro klíče, které k přístupu a šifrování dat. Následující ověřovací data jsou uložené v Key Vault: Azure heslo pro účet nasadit, heslo správce virtuálního počítače, heslo účtu služby SQL Server. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800-53 řízení SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Kryptografické klíče vytvoření a Správa | Symetrické klíče

**SC-12 (2)** organizace vytváří, ovládací prvky a distribuuje symetrické kryptografické klíče pomocí [výběr: NIST kompatibilní se standardem FIPS; Technologie NSA schválení] správy klíčů a procesy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Azure Key Vault se používá k vytvoření, řízení a distribuci kryptografických klíčů. Azure Key Vault můžete generovat klíče pomocí FIPS 140-2 úroveň 2 hardwaru zabezpečení (HSM) modul generování klíče schopnosti. Klíče jsou uložené a spravovat v rámci bezpečně šifrované kontejnerů v Azure Key Vault. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800-53 řízení SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Kryptografické klíče vytvoření a Správa | Asymetrické klíče

**SC-12 (3)** organizace vytváří, ovládací prvky a distribuuje asymetrické kryptografické klíče pomocí [výběr: technologie NSA schválení správy klíčů a procesy; schválené certifikáty infrastruktury veřejných KLÍČŮ třídy 3 nebo prepositioned klíčový materiál; schválené certifikáty infrastruktury veřejných KLÍČŮ třídě 3 nebo 4 – třída a tokeny zabezpečení hardwaru, které ochrana privátního klíče uživatele].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za vytvoření, řízení a distribuci asymetrické kryptografické klíče (Pokud se používají v rámci zákazníka nasazené prostředky). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800-53 řízení SC-13

#### <a name="cryptographic-protection"></a>Kryptografické ochrany

**SC-13** systém informace implementuje [přiřazení: organizace definované kryptografických používá a typ šifrování, které jsou potřebné pro každé použití] v souladu s platné federální zákony Executive objednávky direktivy, zásady, nařízení, a standardy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého jsou zaměstnaní ověřování systému Windows, vzdálené plochy a nástroj BitLocker. Tyto součásti lze nakonfigurovat moct spolehnout na FIPS 140 ověřit kryptografických modulů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800-53 řízení SC-15.a

#### <a name="collaborative-computing-devices"></a>Spolupráce Computing zařízení

**SC-15.a** informace systém neumožňuje Vzdálená aktivace spolupráce výpočetní zařízení s následujícími výjimkami: [přiřazení: organizace definované výjimky, kde je Vzdálená aktivace smí].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nejsou žádná spolupráce výpočetní zařízení, které jsou nasazeny jako součást tento plán. Poznámka: K dispozici jsou fyzické výpočetní zařízení spolupráce v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800-53 řízení SC-15.b

#### <a name="collaborative-computing-devices"></a>Spolupráce Computing zařízení

**SC-15.b** informace systému poskytuje explicitní informace o použití uživatelům fyzicky přítomen v zařízení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nejsou žádná spolupráce výpočetní zařízení, které jsou nasazeny jako součást tento plán. Poznámka: K dispozici jsou fyzické výpočetní zařízení spolupráce v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800-53 řízení SC-17

#### <a name="public-key-infrastructure-certificates"></a>Certifikáty infrastruktury veřejných klíčů

**SC-17** organizace vydává certifikáty s veřejným klíčem v části [přiřazení: zásady definované organizace certifikátu] nebo získá od poskytovatele služeb schválené certifikáty s veřejným klíčem.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na podnikové úrovni infrastruktury veřejných klíčů pro vystavování certifikátů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800-53 řízení SC-18.a

#### <a name="mobile-code"></a>Mobilního kódu

**SC-18.a** organizace definuje přijatelná a nepřijatelná mobilního kódu a technologie mobilního kódu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Systém podnikové úrovni zákazníka a postupy ochrany komunikace může definovat přijatelná a nepřijatelná mobilního kódu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800-53 řízení SC-18.b

#### <a name="mobile-code"></a>Mobilního kódu

**SC-18.b** organizace vytváří implementace ke přijatelné mobilního kódu a mobilního kódu technologie a omezení použití.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Systém podnikové úrovni zákazníka a postupy ochrany komunikace může vytvořit omezení pro používání mobilního kódu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800-53 řízení SC-18.c

#### <a name="mobile-code"></a>Mobilního kódu

**SC-18.c** organizace povolí, sleduje a řídí použití mobilního kódu v rámci systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na podnikové úrovni proces pro ověřování, monitorování a řízení použití mobilního kódu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800-53 řízení SC-19.a

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.a** organizace vytváří implementace ke hlasové přes Internet Protocol (VoIP) technologie, podle kterého hrozí riziko poškození systému informace, pokud se používá neoprávněnému a omezení použití.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Neexistují žádné hlasové přes internet protocol technologie nasazen jako součást tento plán. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800-53 řízení SC-19.b

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.b** organizace povolí, sleduje a řídí použití VoIP v rámci systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Neexistují žádné hlasové přes internet protocol technologie nasazen jako součást tento plán. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800-53 řízení SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Zabezpečení název / adres služba překladu (autoritativní zdroj)

**SC-20.a** informace systému poskytuje další data počátku ověřování a integrita ověření artefaktů spolu s daty autoritativního názvového řešení, systém se vrátí v odpovědi na dotazy pro překlad názvů externí názvu nebo adresy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Je zodpovědná za zabezpečené služba překladu názvu a adresy zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800-53 řízení SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Zabezpečení název / adres služba překladu (autoritativní zdroj)

**SC-20.b** systému informací nabízí způsob, jak označují stav zabezpečení podřízené zóny a (Pokud podřízená podporuje zabezpečené řešení služby) Chcete-li povolit ověřování řetězu certifikátů mezi nadřazenými a podřízenými domény, při fungování v rámci v distribuované hierarchické oboru názvů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Je zodpovědná za zabezpečené služba překladu názvu a adresy zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800-53 řízení SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Zabezpečení název / adres služba překladu (rekurzivní nebo ukládání do mezipaměti překladač)

**SC-21** systém informace požadavků a provádí ověřování původu dat a ověření integrity dat v systému obdrží z autoritativních zdrojů odpovědi rozlišení názvu nebo adresy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za konfiguraci zákazníka nasazené prostředky k vyžádání a provést ověření původu dat a ověření integrity dat v názvu nebo adresy řešení odpovědí přijatých z autoritativních zdrojů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-22"></a>Ovládací prvek SC 22 NIST 800-53

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architektura a zřizování pro název / služba překladu adres

**SC-22** informačními systémy, které společně poskytují služba překladu názvu nebo adresy v organizaci, jsou odolné proti chybám a implementaci oddělení rolí interní/externí.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zajištění, že systémy poskytuje adresu služby překladu zákazníka nasazené prostředky jsou odolné proti chybám a implementaci oddělení rolí interní/externí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800-53 řízení SC-23

#### <a name="session-authenticity"></a>Pravosti relace

**SC-23** informace systému chrání pravost komunikace relací.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Vzdálený přístup k prostředkům nasadit pomocí tento plán, včetně portálu Azure, připojení ke vzdálené ploše a webové aplikace brány, jsou zabezpečené pomocí protokolu TLS. Protokol TLS poskytuje pravosti pro komunikaci na úrovni relace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-23-1"></a>Ovládací prvek SC-23 NIST 800-53 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Relace pravosti | Zrušení platnosti identifikátory relace při odhlášení

**(1) SC-23** informace systému by způsobila neplatnost identifikátory relace při odhlášení uživatele nebo jiné ukončení relace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Vzdálený přístup k prostředkům nasadit pomocí tento plán, včetně portálu Azure, připojení ke vzdálené ploše a webové aplikace brány, jsou zabezpečené pomocí protokolu TLS. Portál Azure a relací vzdálené plochy zneplatnit identifikátory relace při odhlášení. Webové relace zneplatnění se vynucuje prostřednictvím Azure Application Gateway - pravidla brány Firewall firewall webových (webové aplikace aplikací). Firewall webových aplikací platí spřažení souboru cookie relace a provádí časový limit relace po 30 minutách nečinnosti z klienta (Konfigurovat post nasazení do konkrétních pravidel organizace). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 řízení SC – 24

#### <a name="fail-in-known-state"></a>Selhání v známé stavu

**SC – 24** informace systému nepodaří [přiřazení: organizace známé stavu na definovaný] pro [přiřazení: organizace definované typy chyb] zachování [přiřazení: informace o stavu systému definované organizace] selhání.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědný za dodržování selhání zákazníka nasazené prostředky ve stavu známé. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800-53 řízení SC-28

#### <a name="protection-of-information-at-rest"></a>Ochrana informací v klidovém stavu

**SC-28** chrání informace systému [výběr (jeden nebo více): důvěrnost; integrity] z [přiřazení: organizace definované informace, které v klidovém stavu].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | ((1) implementace SC-28 splňuje tento požadavek ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-sc-28-1"></a>Ovládací prvek SC-28 NIST 800-53 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Ochrana informací v klidovém stavu | Kryptografické ochrany

**SC-28 (1)** systém informace implementuje kryptografických mechanismů, aby se zabránilo neoprávněnému zpřístupnění a úpravu [přiřazení: informace definované organizace] na [přiřazení: informace definované organizace systému komponenty].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán, podle kterého implementovat šifrování disku k ochraně utajení a integrity informací v klidovém stavu. Azure disk encryption pro systém Windows je implementovaná pomocí funkce nástroje BitLocker systému Windows. SQL Server je nakonfigurován pro používání transparentní dat šifrování (TDE), který provádí v reálném čase šifrování a dešifrování dat a souborů k ochraně informací v klidovém stavu protokolu. Šifrování TDE poskytuje záruku, že data uložena nebyla neoprávněnému přístupu k. Zákazník může zvolit implementovat další kontroly úrovni aplikace, chránit integritu uložené informace. Důvěrnost a integritu všech objektů BLOB storage nasadit pomocí tento plán, podle kterého jsou chráněny prostřednictvím použití Azure pro úložiště služby šifrování (SSE). SSE chrání data umístěná v účtech Azure storage pomocí šifrování AES 256 bitů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800-53 řízení SC-39

#### <a name="process-isolation"></a>Proces izolace

**SC-39** informace systém uchovává samostatné spuštění domény jednotlivých spuštěných procesů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Windows udržuje domén samostatné provádění jednotlivých spuštěných procesů přiřazením privátní virtuální adresní prostor jednotlivých procesů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |
