---
title: Spojené království – oficiální třívrstvá webové aplikace automatizace – přehled
description: Spojené království – oficiální třívrstvá webové aplikace automatizace – přehled
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 787f504dea6e98911d6ba4716e88aff322c70c5b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151374"
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Přehled National zásady zabezpečení Kybernetického zabezpečení Centre cloudu


> [!NOTE]
> Tyto zásady zabezpečení jsou definovány pomocí Spojené království National Kybernetického zabezpečení Centre (NCSC). Odkazovat na [NCSC dokumentace](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) informace o testování postupy a pokyny pro každou zásadu zabezpečení.



## <a name="ncsc-cloud-security-principle-1"></a>Zásadu zabezpečení cloudu NCSC 1
### <a name="data-in-transit-protection"></a>Data v přenosu ochrany
Uživatelská data tranzitní sítě by měl být ochrany proti manipulaci a odposlouchávání.

To by mělo být dosaženo pomocí kombinace:

- NAP - odepření vaší útočník možnost nezachycuje data
- šifrování - odepření vaší útočník možnost číst data


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého se nakonfiguruje prostředky pro komunikaci pomocí protokolů pouze zabezpečené. Komponenta aplikační bránu firewall webových aplikací je nakonfigurován přijmout osob, které informují z externí používá přes HTTPS/TLS a komunikovat s fondu typu back end jenom přes protokol HTTPS/TLS. Služba Vzdálená plocha jsou nakonfigurovány pro použití zabezpečeného připojení. Síť VPN se používá k zabezpečení přenosů webových mezi AppGateway a Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure používá standardní protokol zabezpečení TLS (Transport Layer) 1.2 s šifrovací klíče RSA/SHA256 2048 bitů, dle doporučení CESG/NCSC, k šifrování komunikace mezi zákazníkem a cloudem i interně mezi Azure systémy a datových centrech. Například když správci použít portál Microsoft Azure ke správě služby ve své organizaci, nebudou se data přenášená mezi portálem a Správce zařízení odesílá přes šifrovaný kanál TLS. Když se e-mailem uživatel připojí k Outlook.com pomocí standardní webového prohlížeče, připojení HTTPS poskytuje zabezpečený kanál pro příjem a odesílání e-mailu.<br /> <br /> Azure nabízí svým zákazníkům celou řadu možností pro zabezpečení svých vlastních dat a provozu. Funkce správy certifikát, který je integrovaný do Azure poskytuje správcům flexibilitu při konfiguraci certifikátů a šifrovací klíče pro systémy správy, jednotlivé služby, relací zabezpečeného shell (SSH), připojení virtuální privátní sítě (VPN) připojení ke vzdálené ploše (RDP) a dalších funkcí. <br /><br /> Vývojáři použít pro přístup Advanced Encryption (Standard AES) algoritmů, společně s funkcí Secure Hash Algorithm (SHA-2) pro zpracování úlohy, jako jsou ověřování digitální zprostředkovatelů kryptografických služeb (CSP), integrované do rozhraní Microsoft .NET Framework podpisy. Azure Key Vault pomáhá zákazníkům zabezpečit kryptografické klíče a tajné klíče jejich uložením v modulech hardwarového zabezpečení (HSM). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Zásadu zabezpečení cloudu NCSC 2
### <a name="asset-protection-and-resilience"></a>Asset ochrany a odolnosti
Uživatelská data a prostředky, ukládání nebo zpracování, je třeba chránit proti fyzické manipulaci, ztrátě, poškození nebo převzetí role.

Aspekty ke zvážení jsou:

1. Fyzické umístění a právním řízení
2. Zabezpečení datového centra
3. Data na Rest ochrany
4. Čištění dat
5. Vyřazení zařízení
6. Fyzické odolnosti a dostupnosti


 ## <a name="ncsc-cloud-security-principle-21"></a>Zásadu zabezpečení cloudu NCSC 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>Fyzické umístění a právním řízení
Chcete-li pochopit právní podmínek, za kterých vaše data mohou mít přístup bez vašeho souhlasu musíte určit umístění, ve kterém je uložený, zpracování a spravované.
Budete také se vynucují potřeba pochopit, jak práci s daty ovládací prvky v rámci služby, relativně k UK předpisy. Nevhodný ochrany dat uživatele může způsobit právních i regulačních schvalovat nebo reputational poškození.


**Odpovědnosti:**`Customer`

> [!NOTE]
> Regionální nasazených služeb Azure a zákazníci mohou nakonfigurovat určité služby Azure k ukládání dat zákazníka pouze v jedné oblasti. Microsoft Azure poskytuje seznam globálně dostupnou datových center, aby bylo možné zajistit dostupnost a spolehlivost v globálním měřítku. Na ISO/IEC 27001: 2013 certifikovány všech datových centrech Azure. Spojené království geograficky se skládá z 2 oblasti: Spojené království – jih a Spojené království – západ.

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého vyzve správce, pro které oblasti prostředků Azure k nasazení. Doporučené oblasti pro nasazení jsou Spojené království – jih nebo Spojené království – západ. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Netýká se |


 ## <a name="ncsc-cloud-security-principle-22"></a>Zásadu zabezpečení cloudu NCSC 2.2
### <a name="datacenter-security"></a>Zabezpečení datového centra
Umístění použitý k poskytnutí cloudové služby potřebovat fyzické ochranu před neoprávněným přístupem, manipulaci, krádeže nebo překonfigurování systémů. Nedostatečné ochrany může vést k úniku, změnou nebo ke ztrátě dat.


**Odpovědnosti:**`Microsoft Azure`


|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure; Datacenter zabezpečení ochranných opatření jsou implementovat a spravovat přes Microsoft Azure. Tento princip je zděděn z Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje této zásady potřeby jejich zákazníků. Microsoft Azure je spuštěná v geograficky distribuované zařízeních společnosti Microsoft, místo a nástroje pro sdílení s jinými službami Microsoft online. Každé zařízení je určená ke spuštění 24 x 7 x 365 a využívá různé standardní opatření k ochraně operace z výpadku napájení, fyzického narušení a výpadky sítě. Těchto datových centrech v souladu s oborové standardy (například ISO 27001) pro fyzické zabezpečení a dostupnosti. Jsou spravovaná, sledovat a spravovat operace pracovníky společnosti Microsoft. <br /> <br /> Azure zákazníků, může být jisti, že kontrolních mechanismů fyzického zabezpečení jsou splněné datových centrech vůbec Azure z důvodu Azure, která uchovává certifikátů na všech datových center standard ISO/IEC 27001: 2013. Spojené království geograficky se skládá ze dvou oblastí: Spojené království – jih a Spojené království – západ. |


 ## <a name="ncsc-cloud-security-principle-23"></a>Zásadu zabezpečení cloudu NCSC 2.3
### <a name="data-at-rest-protection"></a>Data na Rest ochrany
K zajištění, že data nejsou k dispozici neoprávněným osobám s fyzický přístup k infrastruktuře, je třeba chránit uživatele data ukládaná v rámci služby bez ohledu na úložná média, na kterém se nachází. Bez odpovídající opatření na místě mohou být data nechtěně odhalena na zrušených, ztracené nebo odcizené médiu.


**Odpovědnosti:**`Shared`

> [!NOTE]
> Všechna řešení šifrování, které Microsoft Azure nabízí svým zákazníkům snadno integrovat s Azure Key Vault, která umožňuje snadnou správu šifrovacích klíčů.

|||
|---|---|
| **Zákazníka** | Důvěrnost a integritu všechny úložiště objektů blob nasazené řešením pro tento plán, podle kterého je chráněný pomocí Azure pro úložiště služby šifrování (SSE). SSE chrání data umístěná v účtech Azure storage pomocí šifrování AES 256 bitů. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure nabízí celou řadu funkcí šifrování, udělíte zákazníkům flexibilitu zvolit si řešení, který bude nejlépe vyhovovat svých potřeb. Azure Key Vault pomáhá zákazníkům snadno a náklady na efektivně zachovat kontrolu nad klíče používané cloudovými aplikacemi a službami šifrovat data. Azure Disk Encryption umožňuje zákazníkům šifrování virtuálních počítačů. Azure šifrování služby úložiště umožňuje šifrování všech dat do účtu úložiště zákazníka. |


 ## <a name="ncsc-cloud-security-principle-24"></a>Zásadu zabezpečení cloudu NCSC 2.4
### <a name="data-sanitization"></a>Čištění dat
Proces zřizování, migrace a poté zřizování prostředků by se neměly zobrazit neoprávněný přístup k uživatelským datům.

Nedostatečné čištění dat může mít za následek:

- Uživatelská data po neomezenou dobu uchovávané poskytovatelem služeb
- Uživatelská data je přístupný ostatním uživatelům služby, jako jsou opakovaně prostředky
- Nezabraňuje ztrátě nebo budou mít přístup na médiu zrušených, ztracené nebo odcizené uživatelská data.


**Odpovědnosti:**`Microsoft Azure`


|||
|---|---|
| **Zákazníka** | Microsoft Azure poskytuje smluvními ujištění, že odstranění trvalých dat v Azure. Tento princip jako takový je zděděn z Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure drží procesu uvolnění NIST SP800-88r1 s klasifikace dat zarovnán FIPS 199 střední. NIST poskytuje způsob zabezpečení vymazat (prostřednictvím firmware pevný disk) pro jednotky, které ji podporují. Pro pevné disky, které nelze vymazat Microsoft zničí a vykreslí obnovení informací možné (například rozpadnout skartovat, pulverize nebo spalování). Typ prostředku je dáno vhodné prostředky uvolnění. Záznamy o likvidaci zůstanou zachovány. Všechny služby Microsoft Azure využívat služby správy úložiště a uvolnění schválené média. <br />  <br /> Po vypršení platnosti nebo ukončení předplatné služby, může zákazník kontaktovat Microsoft a sdělte jim ohledně: <br /><br /> (1) zakázat účtu zákazníka a pak odstraňte data zákazníků; nebo <br /> (2) zachovat data uložená v online služby v účtu omezené funkce nejméně po dobu 90 dnů po vypršení platnosti nebo ukončení předplatného zákazníka ("uchovávání doba"), takže tohoto zákazníka může extrahovat data. Po uplynutí doby uchování bude společnost Microsoft zakázat účtu zákazníka a odstranit všechna data. Do mezipaměti nebo záložní kopie se vyprázdní do 30 dní od konce dobu uchování. |


 ## <a name="ncsc-cloud-security-principle-25"></a>Zásadu zabezpečení cloudu NCSC 2.5
### <a name="equipment-disposal"></a>Vyřazení zařízení
Jakmile zařízení slouží k poskytování služby dosáhne konce své životnosti, ho by měla být uvolněna z způsobem, který není k ohrožení zabezpečení služby nebo uživatelská data uložená ve službě.


**Odpovědnosti:**`Microsoft Azure`


|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure; postupy vyřazení zařízení jsou implementována a spravovat přes Microsoft Azure. Tento princip je zděděn z Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje této zásady potřeby jejich zákazníků. Při systému end životnosti provozní personál podle přísných data zpracování postupy a procesy uvolnění hardwaru, abyste zajistili, žádný hardware, který může obsahovat data zákazníků společnosti Microsoft je k dispozici nedůvěryhodní. Microsoft Azure drží procesu uvolnění NIST SP800-88r1 s klasifikace dat zarovnán FIPS 199 střední. NIST poskytuje způsob zabezpečení vymazat (prostřednictvím firmware pevný disk) pro jednotky, které ji podporují. Pro pevné disky, které nelze vymazat Microsoft zničí a vykreslí obnovení informací možné (například rozpadnout skartovat, pulverize nebo spalování). Typ prostředku je dáno vhodné prostředky uvolnění. Záznamy o likvidaci zůstanou zachovány. Všechny služby Microsoft Azure využívat služby správy úložiště a uvolnění schválené média. |


 ## <a name="ncsc-cloud-security-principle-26"></a>Zásadu zabezpečení cloudu NCSC 2.6
### <a name="physical-resilience-and-availability"></a>Fyzické odolnosti a dostupnosti
Služby mají různé úrovně odolnosti, které bude mít vliv na jejich schopnost pracovat normálně v případě selhání, incidenty nebo útokům. Službu bez záruky dostupnosti mohly být nedostupné, potenciálně na delší dobu, bez ohledu na to dopad na vaší firmy.


**Odpovědnosti:**`Shared`

> [!NOTE]
> Pokud zákazník nakonfiguruje Microsoft Azure správně povolením Azure Site Recovery a alternativní úložiště dat na jiný geo graficky nachází datacenter, Microsoft Azure může podporovat nepřetržitý provoz zákazníka nasazené prostředky.

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za vytvoření stránku alternativní úložiště. Implementace smlouvy zákazníka ovládacího prvku by měla adresa zákazníka schopnost pracovat v případě incidentu. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure má UK National Kybernetického zabezpečení Centre (NCSC) schválení datových center v různých geografických umístěních (Spojené království – jih a Spojené království – západ), chcete-li poskytovat odolnosti a dostupnosti. Je zodpovědností zákazníka tak, aby vyhradil kapacity oblast alternativní pomocí služby Azure Site Recovery. Jakmile budou mít nakonfigurovaný Azure Site Recovery, bude Azure spustit a zastavit zákaznické v snadný přechod na web alternativní zpracování. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Zásadu zabezpečení cloudu NCSC 3
### <a name="separation-between-users"></a>Oddělení mezi uživateli
Škodlivý nebo napadeného uživatele služby by neměl moci mít vliv na služby nebo data jiného.

Faktory ovlivňující uživatele oddělení patří:

- kde jsou implementované ovládací prvky oddělení – to je výraznou ovlivněno model služby (např. IaaS, PaaS, SaaS)
- kdo sdílíte službu s – to je závisí na modelu nasazení (například veřejné, privátní nebo komunity cloud)
- úroveň záruky, které jsou k dispozici v implementaci oddělení ovládacích prvků

> [!NOTE]
> V službě IaaS byste měli zvážit oddělení poskytované výpočty, úložiště a síťové součásti. Také mohou SaaS a PaaS služby založené na IaaS dědit některé vlastnosti oddělení základní infrastruktury IaaS.

**Odpovědnosti:**`Microsoft Azure`


|||
|---|---|
| **Zákazníka** | Microsoft Azure zajišťuje izolaci pro každého uživatele, aby jeden uživatel škodlivý nebo ohrožených neohrozilo služby nebo data jiného. Tento princip jako takový je zděděn z Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Z důvodu se virtuální servery cloudové zákazníka zlepší fyzické oddělení už neplatí. Microsoft Azure byla navržená tak, aby pomáhají identifikovat a čítač rizik vyplývajících ve víceklientském prostředí. Úložiště dat a zpracování je logicky rozdělen mezi uživateli Azure pomocí služby Active Directory a není přístupný pomocí jiného funkce vytvořených speciálně pro víceklientské služby, jehož cílem je zajistit, že jsou uživatelské údaje uložené ve sdílených datových centrech Azure organizace. <br /> <br /> Základní žádnému architektura sdíleného cloudu je izolace zadaná pro každého uživatele, aby jeden uživatel škodlivý nebo ohrožených neohrozilo služby nebo data jiného. <br /> <br /> Další informace týkající se klienta oddělení společnosti Microsoft najdete v tématu úplný popis v [zabezpečení Azure a dodržování předpisů plán, podle kterého - zásady zabezpečení cloudu NCSC - zákazníka odpovědnosti matice](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Zásadu zabezpečení cloudu NCSC 4
### <a name="governance-framework"></a>Zásady správného řízení Framework
Zprostředkovatel služby by měl mít framework zásad správného řízení zabezpečení, který koordinuje a přesměruje jeho správu služby a informace v něm. Technické ovládací prvky nasadit mimo toto rozhraní se zásadně narušena.
Máte framework efektivní zásad správného řízení zajistí tohoto postupu pracovníky, fyzické a technické ovládací prvky nadále fungovat prostřednictvím životnost služby. Také by měl odpovídat na změny ve službě, technologiím a vzhled nové hrozby.


**Odpovědnosti:**`Microsoft Azure`


|||
|---|---|
| **Zákazníka** | Microsoft Azure udržuje zdokumentovaných zabezpečení rozhraní zásad správného řízení pro služby Azure. Tento princip jako takový je zděděn z Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Rozhraní Microsoft framework dodržování předpisů zahrnuje standardní metodika pro definování domén dodržování předpisů, určení, které cíle vztahovat na daného týmu nebo prostředek a zachycení, jak jsou cíle řízení domény řešit dostatečně podrobně, protože se vztahují na dané sadě, oborových standardů a podnikových požadavků. Rozhraní framework mapuje ovládací prvky na více regulačních standardy, které umožňuje Microsoftu návrh a vytváření služeb pomocí společnou sadu ovládacích prvků, a tím zjednodušení dodržování předpisů pro určitou oblast předpisy dnes a vývoj v budoucnu. <br /> <br /> Procesy dodržování předpisů Microsoft také bylo snazší pro zákazníky a zajištění dodržování předpisů ve více službách efektivně podle jejich potřeb změny. Společně procesy efektivní dodržování předpisů a zabezpečení zlepšení technologie povolit Microsoft k zajištění údržby a rozbalte širokou škálu certifikáty třetích stran. Tyto certifikáty pomáhají uživatelům ke svým zákazníkům, auditoři a regulačních orgánů ukazují připravenosti dodržování předpisů. <br /> <br />  Azure v souladu s širokou škálu mezinárodní a dodržování předpisů místních a průmyslové standardy, například ISO 27001, FedRAMP, SOC 1 a SOC 2. Pomocí přísných audity třetích stran, která ukazují služby Azure pracovat a splňovat špičkových oborových standardů, certifikátů, atestace podle a povolení ověření dodržování prvkům přísných bezpečnostních obsažené v těchto standardů. <br /> <br /> Azure je navržen s strategie dodržování předpisů, která pomáhá zákazníkům adresu obchodních cílů a také oborových standardů a nařízení. Rozhraní framework dodržování předpisů zabezpečení zahrnuje test a audit fáze, analýza zabezpečení, osvědčené postupy správy riziko a analýzu srovnávacího testu zabezpečení k dosažení certifikáty a atestace podle. <br /> <br /> Další informace o dodržování shody architektury společnosti Microsoft najdete v tématu s úplným popisem v [zabezpečení Azure a dodržování předpisů plán, podle kterého - zásady zabezpečení cloudu NCSC - zákazníka odpovědnosti matice](https://aka.ms/blueprintuk-gcrm).|


 ## <a name="ncsc-cloud-security-principle-5"></a>Zásadu zabezpečení cloudu NCSC 5
### <a name="operational-security"></a>Provozního zabezpečení
Služba musí být provozovat a spravovat bezpečně za účelem bránit, zjišťovat nebo zabránit útokům. Dobrý provozního zabezpečení by neměly vyžadovat komplexní, byrokratickou, časově náročné nebo nákladné procesy.

Existují čtyři elementy vzít v úvahu:

- Konfigurace a správa změn – měli byste zajistit, aby byly změny v systému správně testovány a oprávnění. Změny nesmí být změněna neočekávaně vlastnosti zabezpečení
- Ohrožení zabezpečení správy - by měl zjišťovat a zmírňovat problémy se zabezpečením v základní součásti
- Ochranné monitorování - míry měli umístit do místní zjištění útoku a neoprávněnou aktivitu na službu
- Správa incidentů - zkontrolujte může reagovat na incidenty a obnovení služby zabezpečení, k dispozici




 ## <a name="ncsc-cloud-security-principle-51"></a>Zásadu zabezpečení cloudu NCSC 5.1
### <a name="configuration-and-change-management"></a>Konfigurace a správa změn
Měli byste mít přesný obrázek o prostředky, které tvoří služby, spolu s jejich konfigurace a závislosti.
Změny, které by mohly ohrozit zabezpečení služby by měl být identifikovat a spravovat. Neoprávněné změny by měl být zjištěna.
Kde změnit nespravuje efektivně, může být ohrožení zabezpečení bezděčně zaveden ke službě. A ani v případě povědomí o chybu, nemusí omezeny plně.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Šablony Azure Resource Manager a doprovodné prostředky, které tvoří tento plán, podle kterého představují směrný plán "konfigurace jako kód" nasazené architektuře. Řešení je k dispozici, když Githubu, které lze použít pro řízení konfigurace. <br /> <br /> Azure Active Directory účet, který oprávnění jsou implementované pomocí řízení přístupu na základě rolí přiřazování uživatelů do rolí poskytuje striktní určit, přes které uživatelé mohou zobrazit a řízení nasazené prostředky. Oprávnění k účtu Active Directory jsou implementovány pomocí řízení přístupu na základě rolí přiřazování uživatelů do skupiny zabezpečení. Tyto skupiny zabezpečení řídit akce, které mohou uživatelé provádět s ohledem na konfigurace operačního systému. Tato schémata na základě rolí lze rozšířit zákazník zvláště potřebám. <br /> <br /> Chcete-li být v souladu s touto zásadou, další konfigurace je vyžadováno zákazníkem pro použití v produkčním prostředí. Tyto konfigurace se jako takový bude muset být součástí procesu správy změn zákazníka. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure kontroluje a aktualizuje nastavení konfigurace a standardních hodnot konfigurace hardwaru, softwaru a síťových zařízení ročně. Změny vyvinuté, otestovány a schváleny před zadáním produkčního prostředí z vývoj nebo testovací prostředí. <br /> <br />Microsoft Azure platí standardní hodnoty konfigurace pomocí procesu změn a verze pro Microsoft Azure softwarové součásti (například operačního systému, prostředků infrastruktury, RDFE, XStore atd.) a proces bootstrap konfigurace hardwaru a síťové součásti zařízení zadávání Microsoft Azure produkčního prostředí jak je uvedeno níže. <br /> <br /> Standardní hodnoty konfigurace požadované pro služeb založených na Azure jsou kontrolovány tým zabezpečení Azure a dodržování předpisů a týmy služby v rámci testování před nasazením služby jejich produkční. |


 ## <a name="ncsc-cloud-security-principle-52"></a>Zásadu zabezpečení cloudu NCSC 5.2
### <a name="vulnerability-management"></a>Ohrožení zabezpečení správy
Poskytovatelé služeb by měl mít procesů správy k identifikaci, rychlou kontrolu a zmírnit ohrožení zabezpečení. Služby, které není, bude rychle zranitelný vůči útokům pomocí veřejně známé metod a nástroje.


**Odpovědnosti:**`Customer`


|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za ohrožení zabezpečení kontrolu zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). Implementace smlouvy zákazníka je potřeba vyřešit nástroje používá k provádění prověřování ohrožení zabezpečení. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Správa aktualizací zabezpečení pomáhá chránit systémy ze známých slabých míst. Systémy integrované nasazení Azure používá ke správě distribuce a instalaci aktualizací zabezpečení softwaru společnosti Microsoft. Azure je také možné využívat zdroje z Microsoft Security Response Center (MSRC), která identifikuje, monitoruje, odpoví a přeloží bezpečnostní incidenty v oblasti a cloudu ohrožení zabezpečení hodin každý den v roce. |


 ## <a name="ncsc-cloud-security-principle-53"></a>Zásadu zabezpečení cloudu NCSC 5.3
### <a name="protective-monitoring"></a>Ochranné monitorování
Službu, která efektivně nesleduje pro útok, zneužití a selhání, bude pravděpodobně k detekci útoky (úspěšné i neúspěšné). V důsledku toho je možné rychle reagovat na potenciální ohrožení prostředí a data.


**Odpovědnosti:**`Customer`


|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). Příkaz zákazníka řízení implementace je potřeba vyřešit mechanismy pro monitorování, zjišťování a reakce na útoky, zneužití a selhání. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Požadavky pro aktivní monitorování má definováno zabezpečení Microsoft Azure. Služba týmy nakonfigurovat active nástroje monitorování v souladu s těmito požadavky. Aktivní monitorování nástroje patří sledování agenta (MA) a System Center Operations Manager (SCOM), které jsou nakonfigurovány, aby zadejte čas výstrahy zabezpečení Microsoft Azure osobám v situacích, které vyžadují okamžitou akci. |


 ## <a name="ncsc-cloud-security-principle-54"></a>Zásadu zabezpečení cloudu NCSC 5.4
### <a name="incident-management"></a>Správa incidentů
Pokud pečlivě předem plánované správy incidentů procesy jsou na místě, budou pravděpodobně provést při výskytu incidentů, potenciálně ke zhoršení celkový vliv na uživatele nízký rozhodnutí.
Tyto procesy nemusí být složité nebo vyžadovat velké množství popis, ale dobrý správy incidentů bude minimalizaci vlivu uživatelům zabezpečení, spolehlivost a problémy v okolí s služby.


**Odpovědnosti:**`Shared`

> [!NOTE]
> V případech, kdy incidenty zabezpečení zákazník může mít vliv na stav zabezpečení ve službě Microsoft Azure zákazník zodpovídá za upozornění Microsoft Azure.

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za vytvoření o proces správy incidentů pro zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). Implementace smlouvy zákazníka je potřeba vyřešit reporting incidenty a výstrahy, podpora včasné odpovědi incidentu a předává informace PGA a jiných organizací HMG podle potřeby. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Společnost Microsoft implementovala proces správy incidentů zabezpečení pro usnadnění koordinovanou reakci na incidenty, musí dojít k některým. <br /> <br /> Zjistí-li Microsoft žádné neautorizovaného přístupu do všech datech zákazníků, který je uložen v jeho zařízení nebo v jeho zařízení nebo neoprávněného přístupu k těchto zařízení nebo zařízení, což vede ke ztrátě, zpřístupnění nebo změnou dat zákazníka, Microsoft uvedl, že proběhne následující: <br /> <br />   -Okamžitě oznámí zákazník incidentu zabezpečení; <br /> -Neprodleně prozkoumat incidentu zabezpečení a zákazník poskytnout podrobné informace o incidentu zabezpečení; a <br /> -Proveďte kroky přiměřené a výzva k zmírněna rizika a minimalizovat škod vzniklých v důsledku incidentu zabezpečení.  <br />  <br /> Představuje rozhraní správy incidentů bylo vytvořeno s definovány role a odpovědnosti přidělené. Tým služby Windows Azure Security Incident správy (WASIM) zodpovídá za správu incidenty zabezpečení, včetně Eskalace a zajištění zapojení specialisty týmy, pokud je to nezbytné. Azure vedoucí provozu, kteří jsou zodpovědní za dozor nad šetření a řešení incidentů zabezpečení a ochrana osobních údajů a s podporou dalších funkcí. <br /> <br /> Další informace týkající se procesy reakcí na incidenty společnosti Microsoft najdete v tématu úplný popis v [zabezpečení Azure a dodržování předpisů plán, podle kterého - zásady zabezpečení cloudu NCSC - zákazníka odpovědnosti matice](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Zásadu zabezpečení cloudu NCSC 6
### <a name="personnel-security"></a>Zabezpečení pracovníků
Kde zprostředkovatele pracovníky služby mají přístup k datům a systémů musíte vysokým zabezpečením v jejich důvěryhodnost. Podporované důkladné blokování podle odpovídající školení, snižuje pravděpodobnost náhodnému nebo škodlivý ohrožení pracovníky služby zprostředkovatele.
Poskytovatel služeb by měl předmětem pracovníky zabezpečení blokování a pravidelných bezpečnostních školení. Pracovníků v těchto rolí musí porozumět jejich zodpovědnosti. Zprostředkovatelé měli vymazat, jak obrazovky a spravovat osoby v rámci privilegované role.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za blokování jednotlivce a poskytování přístupu k prostředkům nasazené zákazníka pravidelných bezpečnostních školení pro jednotlivce. Implementace smlouvy zákazníka je potřeba vyřešit blokování kritéria pro role a četnost bezpečnostního školení. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure pracovníky, kteří provoz služby Azure a poskytování podpory zákazníků (nebo Microsoft dodavatelů, kteří pomoc s operace platformy, řešení potíží a technické podpory) podstoupit Microsoft zkontrolujte standardní pozadí (nebo podobnou) vyhodnocení education zaměstnanců, jejich zaměstnání a trestního historie. Pozadí kontroly široce odpovídají požadavkům UK Government BPSS/BS7858. Kontrola formální identity konkrétně nezahrnují.  <br /> <br /> Microsoft zahrnuje v kontraktech jeho zaměstnanců a subdodavatele zřizuje o utajení. Všechny příslušné zaměstnance společnosti Microsoft a subdodavatelů účasti v programu Microsoft Azure sponzorovaný bezpečnostního školení, který informuje pracovníci jejich zodpovědnosti pro zabezpečení informací. <br /> <br /> Zaměstnanci služby Microsoft Azure nebo subdodavatelů podezření, že potvrzování narušení zabezpečení nebo bychom při tom porušili zásady zabezpečení informací se vztahují procesu šetření a příslušné disciplinární než a včetně ukončení. Pokud to vyžadují okolnosti, Microsoft může věc k jednání vynucení agenturou zákona. <br /> <br /> Doplníte tento systém kontroly a education zabezpečení, Microsoft nasadí kombinace preventivní, Obranným a reaktivní ovládacích prvků k ochraně proti neoprávněnému vývojáře nebo správce aktivity, včetně následujících mechanismů: <br />  <br /> -Ovládací prvky úzkou přístup na citlivá data, včetně požadavek na dvojúrovňové ověřování na základě čipových karet k provádění citlivých operací. <br /> -Kombinace ovládacích prvků, které zlepšují nezávislé odhalování škodlivých aktivit. <br /> -Více úrovní sledování, protokolování a vytváření sestav. |


 ## <a name="ncsc-cloud-security-principle-7"></a>Zásadu zabezpečení cloudu NCSC 7
### <a name="secure-development"></a>Zabezpečený vývoj
Služby by měl být navržená tak a vyvinuté tak, aby zjišťovat a zmírňovat ohrožení jejich zabezpečení. Ty, které nejsou může být zranitelný vůči problémy zabezpečení, které by mohly ohrozit vaše data, způsobí ztrátu služby nebo povolit další škodlivé aktivity.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Systém Windows poskytuje ověření integrity souborů v reálném čase, ochrany a obnovení souborů jádra systému, které jsou nainstalovány v rámci systému Windows nebo autorizovaný aktualizací systému Windows prostřednictvím funkce ochrany WRP prostředků Windows (.), která umožňuje v reálném čase Probíhá kontrola integrity. <br /> <br /> Windows má zavedené brání spuštění kódu v umístění s omezeným přístupem paměti ochrana: Ne Execute (NX), místo rozložení náhodného přeskupování (technologie ASLR) adresu a zabránění spuštění dat (DEP). <br /> <br /> Tento plán, podle kterého se nasadí na hostiteli antimalwarové ochrany pro všechny nasazené virtuální počítače s Windows implementovaná pomocí programu Microsoft Windows Defender. Program Windows Defender je nakonfigurován pro automatické aktualizace obou antimalwarový stroj a ochrany podpisů jako verzi k dispozici. <br /> <br /> Chcete-li být v souladu s touto zásadou, další konfigurace je vyžadováno zákazníkem pro použití v produkčním prostředí. Tyto konfigurace se jako takový bude muset být součástí procesu zabezpečený vývoj zákazníka. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft životního cyklu SDL (Security Development) poskytuje efektivní procesu modelování hrozeb k identifikaci hrozby a ohrožení zabezpečení v softwaru a služeb. Modelování hrozeb team úkol, zahrnující nástroje operations manager, vedoucí programu nebo projektů, vývojáři a testerů a představuje provedení úlohy analýzy zabezpečení klíčů návrh řešení. Členové týmu pomocí nástroje modelování hrozeb SDL modelu všechny projekty a služby při sestavení i když jsou aktualizovány pomocí nové funkce a funkce. Modely Threat zahrnují všechny kódu, které jsou zveřejněné na prostor pro útok a všechny kód zapsaných správcem nebo licencí od třetích stran a vezměte v úvahu všechny hranice vztahů důvěryhodnosti. Systém STRIDE (falšování, Nepovolená manipulace, Odvolatelnost, zpřístupnění informací, odepření služby a zvýšení úrovně oprávnění) se používá k identifikaci a řešení bezpečnostní hrozby již v rané fázi v procesu návrhu předtím, než mohou ovlivnit zákazníků. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Zásadu zabezpečení cloudu NCSC 8
### <a name="supply-chain-security"></a>Zadejte řetězec zabezpečení
Zprostředkovatel služby by měl zkontrolujte, jestli jeho zásobovací řetězec uspokojivému podporuje všechny zásad zabezpečení, které deklarace identity služby k implementaci.
Cloudové služby často závisí produkty a služby třetích stran. V důsledku toho, pokud není implementována tato zásada, zadejte řetězec ohrožení zabezpečení můžete ohrozit zabezpečení služby a mít vliv na provádění jiných Principy zabezpečení.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za poskytování zabezpečeného zadejte řetězec dokumentace pro jakékoli třetí strany získali softwaru a operačních systémů, v rámci svého předplatného Azure. Implementace smlouvy zákazníka je potřeba vyřešit výjimky postupovat podle procesů se identifikovanou pomocí této dokumentace řetězu napájení. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Skupiny Microsoft Cloud zadejte řetězec (MCSC) se skládá z šesti jedinečný týmy každý přispívání do Azure ochrana před hrozbami do řetězu zadat.  <br />  <br /> -Nákup <br /> -Operace zákazníka <br /> -Nasazení kvality <br /> -Dodavatele relace správy <br /> -K výměně za chodu <br />  <br /> Další informace o skupině MCSC společnosti Microsoft najdete v tématu s úplným popisem v [zabezpečení Azure a dodržování předpisů plán, podle kterého - zásady zabezpečení cloudu NCSC - zákazníka odpovědnosti matice](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Zásadu zabezpečení cloudu NCSC 9
### <a name="secure-user-management"></a>Zabezpečení správy uživatelů
Poskytovatel by měla zpřístupnit nástroje můžete bezpečně spravujte používání své služby. Rozhraní pro správu a postupy jsou sice podstatná součást bezpečnostní bariéru, brání neoprávněným přístupem a změnou prostředky, aplikace a data.

Aspekty ke zvážení jsou:

- Ověřování uživatelů k rozhraní pro správu a kanály podpory
- Oddělení a řízení přístupu v rámci rozhraní pro správu



 ## <a name="ncsc-cloud-security-principle-91"></a>Zásadu zabezpečení cloudu NCSC 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Ověření uživatele, aby rozhraní pro správu a v rámci kanály podpory
Aby byla zachována zabezpečené služba, uživatelé potřeba správně ověřit dříve, než mohou provádět správu aktivity, sestavy chyb nebo žádostí o změny služby.
Tyto aktivity může být proveden prostřednictvím webového portálu služby správy, nebo jinými způsoby, například telefon nebo e-mail. Jsou pravděpodobně patří funkce, jako je zřizování nových elementů služby, Správa uživatelských účtů a správě dat uživatele.
Poskytovatelé služeb je potřeba zajistit, aby všech požadavků na správu, které by mohly mít dopad zabezpečení jsou přes zabezpečené a ověřené kanály. Pokud uživatelé nejsou silného ověřování pak podvodníka možné úspěšně provedení privilegovaných akcí ohrozit zabezpečení služby nebo data.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Pokud správci přístup k portálu Microsoft Azure ke správě prostředků Azure pro jejich organizaci, data přenášená mezi portálem a Správce zařízení se odesílají přes šifrovaný kanál zabezpečení TLS (Transport Layer) pomocí RSA 2048 bitů / SHA256 šifrovací klíče, dle doporučení CESG/NCSC.  <br /> <br /> Tento plán, podle kterého se používá ověřování systému Windows, vzdálené plochy a nástroj BitLocker. Tyto součásti lze nakonfigurovat moct spolehnout na FIPS 140 ověřit kryptografických modulů. <br /> <br /> Tento plán, podle kterého vynucuje autorizací logického přístupu pomocí řízení přístupu na základě rolí vynucuje přiřadit uživatele k role, služby Active Directory přiřadit uživatele do skupiny zabezpečení Azure Active Directory a ovládací prvky Windows úrovni operačního systému. Azure Active Directory role přiřazené k uživatelům nebo skupinám řídit logického přístupu k prostředkům v rámci Azure na úrovni prostředku, skupiny nebo předplatné. Skupiny zabezpečení Active Directory řídit logického přístupu k prostředkům na úrovni operačního systému a funkcí. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Zákazníci spravovat svoje prostředky Azure prostřednictvím portálu Azure, který poskytuje přístup k virtuálním počítačům, databází, cloudové služby, a dalším prostředkům nakonfigurovaný pro účtu zákazníka. Webový přístup k portálu Azure, je zabezpečena pomocí standardní zabezpečení TLS (Transport Layer) 1.2 připojení pomocí 2048 bitů RSA/SHA256 šifrovacích klíčů, jako doporučenou podle CESG/NCSC. Řízení přístupu na základě rolí jsou uvedeny které zákazníkům umožňují zadat omezený přístup k prostředkům Azure správy pro konkrétní uživatele a skupiny. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Zásadu zabezpečení cloudu NCSC 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>Oddělení a řízení přístupu v rámci rozhraní pro správu
Mnoho cloudové služby jsou spravované prostřednictvím rozhraní API nebo webové aplikace. Tato rozhraní jsou klíčovou součástí služby zabezpečení. Pokud uživatelé nejsou adekvátní oddělené v rámci rozhraní pro správu, může být jeden uživatel moci mít vliv na službu nebo upravit data jiného.
Privilegované účty pro správu pravděpodobně mít přístup k velkých objemů dat. Omezíte oprávnění jednotlivých uživatelů, kteří mají tyto nezbytně nutné vám může pomoct omezit škod způsobených uživatelé se zlými úmysly, ohroženého zařízení nebo zneužití přihlašovacích údajů.
Řízení přístupu na základě rolí poskytuje mechanismus k dosažení tohoto cíle a může být obzvláště důležité funkce pro uživatele, kteří spravují větší nasazení.
Vystavení rozhraní pro správu k méně přístupný sítím (například komunity spíše než veřejných sítích) je obtížnější pro útočníky k dosažení a útokům, jako by nutné nejprve získat přístup k jedné z těchto sítí. Pokyny k posuzování rizika odhalení rozhraní, které se různé typy sítě jsou poskytovány na základě Princip 11.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí služby Application Gateway, nástroj pro vyrovnávání zatížení a nakonfiguruje pravidla skupiny zabezpečení sítě k řízení commutations na externí hranice a mezi interní podsítě. Funkce uživatele je oddělená od funkce systému správy prostřednictvím vynucení ovládacích prvků logického přístupu a architektura systému. Rozhraní pro funkci správy systému jsou oddělené od uživatelského rozhraní. Všechny možnosti připojení správy je přes zabezpečené bastionu hostitele (jumpbox) umístěný v podsíti správy pomocí pravidel skupiny zabezpečení sítě pro omezení přístupu k prostředkům produkční podle potřeby. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Jak je uvedeno v oddělení mezi uživatele oddělení je integrovaná do Azure jádro. Azure Active Directory (Azure AD nebo AAD) slouží k poskytování každý uživatel, který má přístup jenom na prostředky, která mohou zobrazit a spravovat se ověřuje na portálu Azure. V důsledku toho jsou od sebe navzájem při správě prostřednictvím portálu Azure běžné výhradně oddělené účty různých zákazníků. |


 ## <a name="ncsc-cloud-security-principle-10"></a>Zásadu zabezpečení cloudu NCSC 10
### <a name="identity-and-authentication"></a>Identita a ověřování
Veškerý přístup k rozhraní služeb by měl omezené ověřeným a autorizovaným jednotlivcům.
Slabé ověřování těchto rozhraní může povolit neoprávněný přístup k vašim systémů, což vede k odcizení nebo změny dat, změny služby, nebo odepření služby.
Je důležité musí dojít k ověření přes zabezpečené kanály. E-mailu, HTTP nebo telefonu se bude zranitelný vůči útokům zachycení a sociální inženýrství.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého využívá pro správu účtu služby Active Directory. Přístup k prostředkům nasadit pomocí tento plán, podle kterého je chráněn před útoky před zneužitím integrované funkce protokolu Kerberos Azure Active Directory, služby Active Directory a operačního systému Windows. Ověřovací klient zasílá v ověřování protokolem Kerberos, obsahuje další data, například šifrované seznam adres IP, časová razítka klienta a doba platnosti lístku. Pokud je přehrány paket, zkontroluje se časové razítko. Pokud je starší než časové razítko nebo stejný jako předchozí authenticator, paket byl odmítnut. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure poskytuje služby k usnadnění sledování identity a také integrovat do úložiště identit, které již mohou být používán. Azure AD je komplexní služba správy identit a přístupu pro cloud, která pomáhá zabezpečit přístup k datům v místním a cloudovým aplikacím. Azure AD také zjednodušuje správu uživatelů a skupin kombinací základní adresářové služby, pokročilé zásady správného řízení identity, zabezpečení a správu přístupu aplikace. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Zásadu zabezpečení cloudu NCSC 11
### <a name="external-interface-protection"></a>Externí rozhraní ochrany
Všechny externí nebo méně důvěryhodné rozhraní služby by měl být identifikovány a správně střežit.
Pokud jsou některé z rozhraní vystavených privátní (například rozhraní pro správu) může být větších dopad ohrožení zabezpečení.
Různé modely můžete připojit ke cloudovým službám, které budou vaše podnikové systémy různé úrovně rizika.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí prostředky v architekturu s podsíť samostatný webový, databáze podsíť, podsíť služby Active Directory a podsítě správy. Podsítě jsou logicky oddělených použít na jednotlivé podsítě omezit přenos dat mezi podsítě, které jenom to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě (například externích přenosů nelze získat přístup k databázi, správu, nebo podsítě služby Active Directory).  <br /> <br /> Služby Application Gateway je nasazena pro správu externí připojení k zákazníka nasazené webové aplikace. Externí připojení pro přístup ke správě jsou omezeny na jumpbox (bastionu hostitel) nasazené v podsíti správy s pravidla zabezpečení sítě použít k omezení externí připojení autorizovaných IP adresy. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft využívá metodu volá "Red síťových adaptérů" ke zlepšení ovládací prvky zabezpečení Azure a procesů prostřednictvím regulární průnikům testování. Týmem Red je skupina zaměstnance na plný úvazek v rámci společnosti Microsoft, která se zaměřuje na provádění cílové a trvalé útoky na Microsoft infrastruktury, platformy a aplikace, ale ne koncoví zákazníků aplikace nebo data. <br /> <br /> Další informace o seskupování síťových adaptérů Red společnosti Microsoft a také popis Blue síťových adaptérů úsilí, najdete v části s úplným popisem v [zabezpečení Azure a dodržování předpisů plán, podle kterého - zásady zabezpečení cloudu NCSC - zákazníka odpovědnosti matice ](https://aka.ms/blueprintuk-gcrm).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Zásadu zabezpečení cloudu NCSC 12
### <a name="secure-service-administration"></a>Zabezpečení služby správy
Používá pro správu cloudové služby systémů bude vysoce privilegovaný přístup k této službě. Jejich ohrožení zabezpečení by měla mít významný dopad, včetně způsob, jak obejít kontrolní mechanismy zabezpečení a ukrást nebo zpracování velkých objemů dat.
Návrhu, implementaci a správu systémů správy postupujte podnikové osvědčených postupů, a přitom rozpozná jejich vysoké hodnoty útočníci.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Zákazník je odpovědný za dodržování zabezpečení pracovních stanic pro správu svého předplatného Azure a zákazník nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). Implementace smlouvy zákazníka je potřeba vyřešit mechanismy používá pro zmírnění rizik využívání zdrojů nasazené zákazníka. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure operations pracovníky nutné použít Správce zabezpečení pracovních stanic (pily; také označovanou jako pracovní stanice privilegovaného přístupu nebo PAWs). Přístup pila je rozšířením zavedené doporučený postup pro správu pracovníky používat samostatný správce a uživatelské účty. Tento postup používá jednotlivě přiřazené administrativní účet, který je zcela oddělené od uživatele standardní uživatelský účet. VIDĚLI sestavení na tento účet oddělení postupů tím, že poskytuje trustworthy pracovní stanice pro tyto citlivé účty. |


 ## <a name="ncsc-cloud-security-principle-13"></a>Zásadu zabezpečení cloudu NCSC 13
### <a name="audit-information-for-users"></a>Informace o auditování pro uživatele
Musí být zadaný s auditem záznamy jsou potřebné pro sledování přístupu ke službě a data uchovávat v něm. Typ k dispozici informace o auditování může mít přímý vliv na schopnost zjistit a reagovat na nevhodný nebo škodlivý aktivitu v rámci přiměřené časové osy.


**Odpovědnosti:**`Shared`


|||
|---|---|
| **Zákazníka** | Události Audituje tento plán, podle kterého patří Audituje protokoly Azure aktivity pro nasazené prostředky, protokoly na úrovni operačního systému a protokoly služby Active Directory. Tyto protokoly událostí obsahovat informace o dostatečná k určení, kdy dojde k událostem, zdroj události, výsledek události a další podrobné informace, které podporuje šetření bezpečnostních incidentů. Zákazníci mohou vyberte další události, které mají být auditovány zvláště potřebám. Všechny prostředky Azure mít k dispozici na webu Azure portal protokoly auditu. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure Log Analytics shromažďuje záznamy událostí, ke kterým dochází v rámci organizace systémů a sítí, jakmile k nim dojde před nikým dokáže manipulovat s nimi a umožňuje různé typy analýzy pomocí souvztažnosti data mezi několik počítačů. Azure umožňuje zákazníkům provést generování událostí zabezpečení a kolekce z Azure IaaS a PaaS role do centrální úložiště ve svých předplatných. Tyto shromážděné události je možné exportovat do místní informace o zabezpečení a událostí systémy pro správu (SIEM) pro průběžné monitorování. Po data se přenáší do úložiště, celá řada možností zobrazíte diagnostická data. <br /> <br /> Azure diagnostics předdefinované může pomoct s laděním. Pro aplikace, které jsou nasazené v Azure jsou ve výchozím nastavení povolené sadu událostí zabezpečení operačního systému. Zákazníci můžete přidat, odebrat nebo změnit události ověřovat přizpůsobení zásady auditu operačního systému. <br /> <br /> Na vysoké úrovni je poměrně snadné a snadno zahájit shromažďování protokolů pomocí předávání událostí systému Windows (WEF) nebo čím více advanced Azure Diagnostics, když virtuální počítače na bázi Windows jsou nasazeny pomocí IaaS v Azure. Kromě toho Azure Diagnostics lze nakonfigurovat ke shromažďování protokolů a událostí z instancí rolí PaaS. Pokud používáte virtuální počítače na základě IaaS, zákazník jednoduše nakonfiguruje a umožňuje události požadované zabezpečení stejným způsobem jako umožňují serverů Windows na protokolu auditů v jejich místní datacentra. Pro webové aplikace je také možné povolit protokolování internetové informační služby, pokud je primární aplikace a nasazení v Azure. Zákazníci můžete vždy uložit data zabezpečení v účtech úložiště v podporovaných geografické umístění libovolný splnění suverenity dat. |


 ## <a name="ncsc-cloud-security-principle-14"></a>Zásadu zabezpečení cloudu NCSC 14
### <a name="secure-use-of-the-service"></a>Bezpečné používání služby
Zabezpečení cloudových služeb a data ukládaná v nich můžete narušena, pokud používáte službu špatně. V důsledku toho je nutné určité odpovědnosti při používání služby, aby vaše data adekvátní ochranu.
Rozsah vaší povinností budou lišit v závislosti na modely nasazení cloudové služby a scénář, ve které máte v úmyslu používat službu. Konkrétní funkce jednotlivých služeb také může mít vliv. Například jak síti pro doručování obsahu chrání privátní klíč, nebo jak poskytovatele cloudových platebních služeb zjistí podvodné transakce jsou důležité informace o zabezpečení nad rámec obecných aspektů předmětem zásad zabezpečení cloudu.
S IaaS a PaaS nabídky jste zodpovědní za důležité aspekty zabezpečení vašich dat a úloh. Například pokud jste si opatřete IaaS výpočetní instance, obvykle bude zodpovědná za instalace moderní operačního systému, bezpečně konfigurace operačního systému, bezpečně nasazení všechny aplikace a také údržbu tuto instanci prostřednictvím použití opravy nebo provádění údržby vyžaduje.


**Odpovědnosti:**`Customer`

> [!NOTE]
> Zákazník může použít Azure Security Center pomáhá zabránit, zjistit a reagovat na hrozby nabízí lepší přehled a kontrolu nad zabezpečení prostředků Azure. Azure Security Center poskytuje integrované bezpečnostní sledování a správu zásad ve předplatná Azure, pomáhá zjišťovat hrozby, které byste jinak nevšimli a spolupracuje s řadou řešení zabezpečení.

|||
|---|---|
| **Zákazníka** | Šablony Azure Resource Manager a doprovodné prostředky, které tvoří tento plán, podle kterého podle obrany zabezpečení přístupu k zabezpečení. Chcete-li být v souladu s touto zásadou, není nutná další konfigurace zákazníkem pro použití v produkčním prostředí (například databáze software pro správu, nasazení webových aplikací). |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Netýká se |

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.
 - Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
 - Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.