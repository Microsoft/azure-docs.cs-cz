---
title: zahrnout soubor
description: zahrnout soubor
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 234407e6973657ba3ad0d78189e7cb1d363c15e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95553927"
---
Internet věcí (IoT) představuje jedinečné problémy se zabezpečením, ochranou osobních údajů a dodržováním předpisů pro firmy po celém světě. Na rozdíl od tradičních počítačové technologie, kde se tyto problémy dotýkají softwaru a způsobu jeho implementace, se v IoT projeví, co se stane, když se světů na internetu a fyzickou akci sblížení. Ochrana řešení IoT vyžaduje zajištění zabezpečeného zřizování zařízení, zabezpečení připojení mezi těmito zařízeními a cloudem a zabezpečení ochrany dat v cloudu během zpracování a ukládání. Práce s těmito funkcemi je však omezená na prostředky, geografickou distribuci nasazení a velký počet zařízení v rámci řešení.

Tento článek popisuje, jak akcelerátory řešení IoT poskytují zabezpečené a privátní cloudové řešení Internet věcí. Akcelerátory řešení poskytují kompletní ucelené řešení, které je součástí zabezpečení v každé fázi od základu. V Microsoftu je vývoj zabezpečeného softwaru součástí postupů softwarového inženýrství, který je v souladu s vývojem zabezpečeného softwaru od Microsoftu po dobu dekády. Aby se zajistilo, je životní cyklus zabezpečení SDL (Security Development Lifecycle) základem metodologie vývoje, společně s hostitelem služeb zabezpečení na úrovni infrastruktury, jako je třeba Operational Security Assurance (OSA) a jednotka digitálních činů společnosti Microsoft, Microsoft Security Response Center a Microsoft Malware Protection Center.

Akcelerátory řešení nabízejí jedinečné funkce, které usnadňují a transparentují zřizování, připojování a ukládání dat ze zařízení IoT, a to nejvíce ze všech zabezpečených. Tento článek pojednává o funkcích zabezpečení akcelerátorů řešení Azure IoT a strategiích nasazení, aby se zajistilo řešení problémů se zabezpečením, ochranou osobních údajů a dodržováním předpisů.

## <a name="introduction"></a>Úvod

Internet věcí (IoT) je vlna v budoucnosti a nabízí podnikům bezprostřední a reálné příležitosti pro snížení nákladů, zvýšení výnosů a transformaci jejich podnikání. Mnoho podniků ale ale váhají nasazení IoT ve svých organizacích z důvodu obav o zabezpečení, ochranu osobních údajů a dodržování předpisů. Zásadním bodem, který se týká, je jedinečnost infrastruktury IoT, která spojuje počítačové a fyzické světů společně s jednotlivými riziky, která jsou v těchto dvou světů. Zabezpečení IoT se vztahuje na zajištění integrity kódu běžícího na zařízeních, poskytování ověřování zařízení a uživatelů, definování jasného vlastnictví zařízení (a také dat vygenerovaných těmito zařízeními) a odolného vůči internetovým a fyzickým útokům.

Pak dojde k problému s ochranou osobních údajů. Společnosti chtějí transparentnost týkající se shromažďování dat, stejně jako v případě, že se shromažďují a proč se jim může zobrazit, kdo řízení přístupu a tak dále. Nakonec existují obecné bezpečnostní problémy v rámci tohoto zařízení spolu s lidmi, kteří je používají, a problémy s údržbou oborových standardů dodržování předpisů.

Vzhledem k problémům zabezpečení, ochrany osobních údajů, transparentnosti a dodržování předpisů a výběru správného poskytovatele řešení IoT zůstane výzva. Spojování jednotlivých částí softwaru a služeb IoT poskytovaných různými dodavateli přináší nedostatky v zabezpečení, ochraně osobních údajů, transparentnosti a dodržování předpisů, které mohou být obtížné detekovat, ať jde o samostatnou opravu. Volba správného softwaru IoT a poskytovatele služeb je založena na poskytovatelích, kteří mají rozsáhlou zkušenost s provozem služeb, které jsou rozloženy napříč vertikálně a geografickými oblastmi, ale také je možné škálovat zabezpečeným a transparentním způsobem. Podobně pomáhá vybraným poskytovateli mít v desítkových zkušenostech s vývojem zabezpečeného softwaru běžícího na miliardách počítačů po celém světě a mají možnost vyhodnotit hrozby na základě tohoto nového světa Internet věcí.

## <a name="secure-infrastructure-from-the-ground-up"></a>Zabezpečená infrastruktura od základu

Infrastruktura [Microsoft Cloud](https://azure.microsoft.com) podporuje více než 1 000 000 000 zákazníků ve 127 zemích nebo oblastech. Při sestavování podnikového softwaru a používání některých největších online službyů na světě je možné využít Microsoft Cloud vyšší úrovně zvýšeného zabezpečení, ochrany osobních údajů, dodržování předpisů a způsobů zmírnění hrozeb, než je většina zákazníků.

Rozhraní [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/) poskytuje povinný proces vývoje v rámci společnosti, který do celého životního cyklu softwaru vloží požadavky na zabezpečení. K zajištění toho, aby provozní činnosti dodržovaly stejnou úroveň postupů zabezpečení, používá SDL přísné bezpečnostní pokyny, které jsou uvedené v procesu OSA (Operational Security Assurance) od Microsoftu. Microsoft také spolupracuje s auditory třetích stran pro průběžné ověřování, že splňuje podmínky dodržování předpisů, a Microsoft se zavazuje k nejrůznějším bezpečnostním centrům, a to díky tvorbě špičkových Center, včetně jednotek digitálních zločinů Microsoftu, centra Microsoft Security Response Center a Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – Zabezpečte infrastrukturu IoT pro vaši firmu.

Microsoft Azure nabízí kompletní cloudové řešení, které spojuje nepřetržitě rostoucí kolekci integrovaných cloudových služeb – analýzy, strojové učení, úložiště, zabezpečení, sítě a web – díky špičkovému závazku k ochraně a ochraně soukromí vašich dat. Strategie [nedodržení](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) společnosti Microsoft používá vyhrazený *červený tým* odborníků na zabezpečení softwaru, kteří simulují útoky a testují schopnost Azure detekovat, chránit před vznikajícími hrozbami a obnovovat před porušením. Globální tým [reakce na incidenty](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) od Microsoftu funguje kolem hodin, aby se zmírnily důsledky útoků a škodlivá aktivita. Tým řídí postupy pro správu incidentů, komunikaci a obnovení a používá zjistitelná a předvídatelná rozhraní s interními a externími partnery.

Systémy Microsoftu poskytují nepřetržitou detekci vniknutí a prevenci, ochranu před útoky, pravidelné testování průniku a forenzní nástroje, které pomáhají identifikovat a zmírnit hrozby. [Multi-Factor Authentication](../articles/active-directory/authentication/concept-mfa-howitworks.md) poskytuje další úroveň zabezpečení pro koncové uživatele, kteří budou mít přístup k síti. A pro aplikace a poskytovatele hostitele Microsoft nabízí řízení přístupu, monitorování, ochranu proti malwaru, kontrolu ohrožení zabezpečení, opravy chyb a správu konfigurace.

Akcelerátory řešení využívají zabezpečení a ochranu osobních údajů integrované v platformě Azure společně s procesy SDL a OSA pro bezpečný vývoj a provoz veškerého softwaru Microsoftu. Tyto postupy poskytují ochranu infrastruktury, ochranu sítě a funkce identit a správy zásadní pro zabezpečení jakéhokoli řešení.

[Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) v rámci [akcelerátorů řešení IoT](../articles/iot-fundamentals/iot-introduction.md) nabízí plně spravovanou službu, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi zařízeními IoT a službami Azure, jako jsou [Azure Machine Learning](../articles/machine-learning/classic/index.yml) a [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) , pomocí zabezpečovacích přihlašovacích údajů pro zařízení a řízení přístupu.

K tomu, aby se co nejlépe komunikovalo s funkcemi zabezpečení a ochrany osobních údajů, které jsou integrované v akcelerátorech řešení Azure IoT, se v tomto článku rozdělí sada do tří hlavních oblastí zabezpečení.

![Akcelerátory řešení Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Zabezpečené zřizování a ověřování zařízení

Akcelerátory řešení zabezpečují zabezpečená zařízení, když jsou v terénu, zadáním jedinečného klíče identity pro každé zařízení, které může infrastruktura IoT použít ke komunikaci se zařízením v době, kdy je v provozu. Nastavení procesu je rychlé a snadné. Vygenerovaný klíč s uživatelem vybraným ID zařízení tvoří základ tokenu, který se používá při veškeré komunikaci mezi zařízením a IoT Hub Azure.

ID zařízení je možné přidružit k zařízení během výroby (tj. v modulu důvěryhodnosti hardwaru) nebo můžou používat existující pevnou identitu jako proxy (například sériová čísla procesoru). Vzhledem k tomu, že změna těchto identifikačních informací v zařízení není jednoduchá, je důležité zavést ID logických zařízení v případě, že se změní základní hardware zařízení, ale logické zařízení zůstane stejné. V některých případech se přidružení identity zařízení může vyskytnout v době nasazování zařízení (například ověřený technický technik fyzicky nakonfiguruje nové zařízení při komunikaci s back-endu řešení). [Registr identit v Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) poskytuje zabezpečené úložiště identit zařízení a klíčů zabezpečení pro řešení. Do seznamu povolených nebo blokovaných identit zařízení můžete přidat jednotlivé identity nebo skupiny a povolit tak úplnou kontrolu nad přístupem k zařízení.

Zásady řízení přístupu pro Azure IoT Hub v cloudu umožňují aktivaci a zakázání libovolné identity zařízení a umožňují tak odpojení zařízení od nasazení IoT v případě potřeby. Toto přidružení a zrušení přidružení zařízení je založené na každé identitě zařízení.

Mezi další funkce zabezpečení zařízení patří:

* Zařízení neakceptují nevyžádaná síťová připojení. Navážou všechna připojení a trasy pouze odchozím způsobem. Aby zařízení přijímalo příkaz z back-endu, musí iniciovat připojení, aby kontrolovalo všechny nevyřízené příkazy, které se mají zpracovat. Jakmile je připojení mezi zařízením a IoT Hub bezpečně navázáno, může být zasílání zpráv z cloudu do zařízení a zařízení do cloudu transparentní.

* Zařízení se připojují k dobře známým službám, se kterými jsou partnerské vztahy, jako je například Azure IoT Hub, se připojují k prostředkům nebo navázaly trasy.

* Ověřování na úrovni systému a ověřování používají identity vázané na zařízení, které přistupují k přihlašovacím údajům a oprávněním téměř okamžitě odvolatelné.

### <a name="secure-connectivity"></a>Zabezpečené připojení

Odolnost proti zasílání zpráv je důležitou funkcí řešení IoT. Nutnost trvale doručovat příkazy nebo přijímat data ze zařízení je podtržená skutečností, že zařízení IoT jsou připojená přes Internet nebo jiné podobné sítě, které můžou být nespolehlivé. Azure IoT Hub nabízí odolnost proti zasílání zpráv mezi cloudem a zařízeními prostřednictvím systému potvrzení v reakci na zprávy. Další odolnost pro zasílání zpráv se dosahuje ukládáním zpráv v IoT Hub po dobu až sedmi dnů pro telemetrie a dvou dnů pro příkazy.

Efektivita je důležitá pro zajištění ochrany prostředků a operací v prostředí s omezenými prostředky. Služba Azure IoT Hub podporuje protokol HTTPS (zabezpečený protokol HTTP), standardní zabezpečenou verzi protokolu HTTP, která umožňuje efektivní komunikaci. Rozšířený protokol řízení front zpráv (AMQP) (AMQP) a přenos telemetrie služby Řízení front zpráv (MQTT), které podporuje Azure IoT Hub, se navrhují nejen z důvodu efektivity používání prostředků, ale také spolehlivého doručování zpráv. 

Škálovatelnost vyžaduje možnost bezpečného fungování v široké škále zařízení. Azure IoT Hub umožňuje zabezpečené připojení k zařízením s povoleným protokolem IP a jiným zařízením než IP. Zařízení s podporou IP adres se můžou přímo připojit a komunikovat s IoT Hub přes zabezpečené připojení. Zařízení s nepovolenou IP adresou jsou omezená na prostředky a připojují se jenom přes krátké komunikační protokoly, jako jsou Zwave, ZigBee a Bluetooth. K agregaci těchto zařízení a provádění překladu protokolu pro zajištění zabezpečené obousměrné komunikace s cloudem slouží pole brány.

Mezi další funkce zabezpečení připojení patří:

* Komunikační cesta mezi zařízeními a Azure IoT Hub nebo mezi bránami a Azure IoT Hub je zabezpečená pomocí standardu TLS (Transport Layer Security) s Azure IoT Hub ověřený pomocí protokolu X. 509.

* Kvůli ochraně zařízení před nevyžádanými příchozími připojení neotevře Azure IoT Hub žádné připojení k tomuto zařízení. Zařízení zahájí všechna připojení.

* Azure IoT Hub trvale ukládá zprávy pro zařízení a čeká na připojení zařízení. Tyto příkazy jsou uložené po dobu dvou dnů, aby se zařízení připojovala zřídka, kvůli problémům s napájením nebo připojením, aby se tyto příkazy přijímaly. Azure IoT Hub udržuje pro každé zařízení frontu pro jednotlivá zařízení.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Zabezpečené zpracování a ukládání v cloudu

Z šifrované komunikace na zpracování dat v cloudu, akcelerátory řešení pomůžou zajistit zabezpečení dat. Poskytuje flexibilitu pro implementaci dalšího šifrování a správy bezpečnostních klíčů.

Pomocí Azure Active Directory (AAD) pro ověřování a autorizaci uživatelů můžou akcelerátory řešení Azure IoT poskytovat model autorizace založený na zásadách pro data v cloudu a umožnit tak správu a kontrolu snadné správy přístupu. Tento model také umožňuje prakticky okamžité odvolání přístupu k datům v cloudu a zařízení připojená k akcelerátorům řešení Azure IoT.

Jakmile jsou data v cloudu, můžete je zpracovat a uložit v jakémkoli uživatelsky definovaném pracovním postupu. Přístup ke každé části dat se řídí Azure Active Directory v závislosti na použité službě úložiště.

Všechny klíče používané infrastrukturou IoT jsou uložené v cloudu v zabezpečeném úložišti, a díky tomu je možné převádět i v případě, že klíče je potřeba znovu zřídit. Data je možné uložit v [Azure Cosmos DB](../articles/cosmos-db/introduction.md) nebo v [SQL Database](../articles/azure-sql/database/sql-database-paas-overview.md)a povolit definici požadované úrovně zabezpečení. Kromě toho Azure poskytuje způsob, jak monitorovat a auditovat veškerý přístup k vašim datům, abyste mohli upozorňovat na případné vniknutí nebo neoprávněný přístup.

## <a name="conclusion"></a>Závěr

Internet věcí začíná vašimi akcemi – to jsou věci, které jsou pro firmy nejvíc důležité. IoT může firmám poskytovat úžasné hodnoty, které snižují náklady, zvyšují výnosy a transformují firmy. Úspěch této transformace je převážně závislý na výběru správného softwaru IoT a poskytovatele služeb. To znamená, že hledají poskytovatele, který nejen catalyzes tuto transformaci, porozumění potřebám a požadavkům vaší firmy, ale také poskytuje služby a software sestavený s zabezpečením, ochranou osobních údajů, transparentností a dodržováním předpisů při návrhu. Microsoft má rozsáhlé zkušenosti s vývojem a nasazením zabezpečeného softwaru a služeb a v tomto novém stáří Internet věcí nadále vedoucím.

Akcelerátory řešení sestavují bezpečnostní opatření podle návrhu, umožňují bezpečné monitorování prostředků, aby se zlepšila efektivita, provozování provozu a poskytování inovací a využívání pokročilých datových analýz k transformaci firem. S využitím vrstveného přístupu k zabezpečení, více funkcí zabezpečení a vzorců návrhu usnadňuje akcelerátory řešení nasazení infrastruktury, která může být důvěryhodná pro transformaci všech firem.

## <a name="additional-information"></a>Další informace

Každý akcelerátor řešení vytváří instance služeb Azure, například:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): brána, která připojuje Cloud k zařízením. Můžete škálovat na miliony připojení na jeden hub a zpracovat obrovské objemy dat pomocí podpory ověřování pro jednotlivé zařízení, které vám pomůžou zabezpečit vaše řešení.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): škálovatelná, plně indexovaná databázová služba pro částečně strukturovaná data, která spravují metadata pro zařízení, která zřizujete, například atributy, konfiguraci a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné zpracování a vysokou propustnost, schéma – nezávislá indexování dat a bohatou rozhraní SQL dotazů.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): zpracování datových proudů v reálném čase v cloudu, které vám umožní rychle vyvíjet a nasazovat řešení s nízkými náklady, abyste mohli získat přehled o zařízeních, senzorech, infrastruktuře a aplikacích v reálném čase. Data z této plně spravované služby se můžou škálovat na libovolný svazek a přitom pořád dosahovat vysoké propustnosti, nízké latence a odolnosti.

* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): cloudová platforma pro vytváření výkonných webových a mobilních aplikací, které se připojují k datům kdekoli; v cloudu nebo v místním prostředí. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integraci s vaším softwarem jako služby (SaaS) a podnikovými aplikacemi s okamžitým připojením k desítkám cloudových služeb a podnikových aplikací. Kód ve vašem oblíbeném jazyce a integrovaném vývojovém prostředí (IDE) – .NET, Node.js, PHP, Python nebo Java – k vytváření webových aplikací a rozhraní API rychleji než kdy dřív.

* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): funkce Logic Apps Azure App Service pomáhá integrovat vaše řešení IoT do stávajících obchodních systémů a automatizovat pracovní postupy. Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají triggerem, a pak provádět řadu kroků – pravidla a akce, které používají výkonné konektory pro integraci s vašimi podnikovými procesy. Logic Apps nabízí předem dostupné možnosti připojení k rozsáhlému ekosystému SaaS, cloudových a místních aplikací.

* [**Azure Blob Storage**](https://azure.microsoft.com/services/storage/): spolehlivé a ekonomické úložiště v cloudu pro data, která vaše zařízení odesílají do cloudu.