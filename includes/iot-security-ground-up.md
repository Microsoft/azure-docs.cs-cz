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
ms.openlocfilehash: 128a2e8b200f1323b88aad635f27c1b686ecbed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72789729"
---
Internet věcí (IoT) představuje pro podniky po celém světě jedinečné výzvy v oblasti zabezpečení, ochrany osobních údajů a dodržování předpisů. Na rozdíl od tradičních kybernetických technologií, kde se tyto problémy točí kolem softwaru a způsobu jeho implementace, ioT se týká toho, co se stane, když se kybernetické a fyzické světy sbíhají. Ochrana řešení IoT vyžaduje zajištění bezpečného zřizování zařízení, zabezpečené připojení mezi těmito zařízeními a cloudem a zabezpečení ochrany dat v cloudu během zpracování a ukládání. Práce proti takové funkce, ale jsou prostředky omezené zařízení, geografické rozložení nasazení a velký počet zařízení v rámci řešení.

Tento článek popisuje, jak akcelerátory řešení IoT poskytují zabezpečené a privátní cloudové řešení Internetu věcí. Akcelerátory řešení poskytují kompletní komplexní řešení se zabezpečením zabudovaným do každé fáze od základů. Vývoj zabezpečeného softwaru ve společnosti Microsoft je součástí softwarového inženýrství, která vychází z desetiletí zkušeností společnosti Microsoft s vývojem zabezpečeného softwaru. Za tímto účelem je životní cyklus vývoje zabezpečení (SDL) základní metodikou vývoje ve spojení s řadou služeb zabezpečení na úrovni infrastruktury, jako je Operační zabezpečení (OSA) a oddělení microsoft digital crimes, Microsoft Security Response Center a Microsoft Malware Protection Center.

Akcelerátory řešení nabízejí jedinečné funkce, díky nimž je zřizování, připojování a ukládání dat ze zařízení IoT snadné a transparentní a především bezpečné. Tento článek zkoumá akcelerátory řešení Azure IoT funkce zabezpečení a strategie nasazení k zajištění zabezpečení, ochrany osobních údajů a dodržování předpisů problémy jsou vyřešeny.

## <a name="introduction"></a>Úvod

Internet věcí (IoT) je vlnou budoucnosti a nabízí podnikům okamžité příležitosti a příležitosti v reálném světě ke snížení nákladů, zvýšení příjmů a transformaci jejich podnikání. Mnoho firem však váhá s nasazením IoT ve svých organizacích kvůli obavám o zabezpečení, ochranu osobních údajů a dodržování předpisů. Hlavní problém pramení z jedinečnosti infrastruktury IoT, která spojuje kybernetický a fyzický svět dohromady a zhoršuje jednotlivá rizika spojená s těmito dvěma světy. Zabezpečení IoT se vztahuje k zajištění integrity kódu běžícího na zařízeních, poskytování ověřování zařízení a uživatelů, definování jasného vlastnictví zařízení (stejně jako dat generovaných těmito zařízeními) a odolnosti vůči kybernetickým a fyzickým útokům.

Pak je tu otázka soukromí. Společnosti chtějí transparentnost, pokud jde o shromažďování údajů, jako v tom, co se shromažďuje a proč, kdo je může vidět, kdo kontroluje přístup a tak dále. A konečně, existují obecné bezpečnostní otázky zařízení spolu s lidmi, kteří je provozují, a otázky zachování průmyslových standardů dodržování předpisů.

Vzhledem k obavám o zabezpečení, ochranu osobních údajů, transparentnost a dodržování předpisů zůstává výběr správného poskytovatele řešení IoT výzvou. Sešívání jednotlivých částí softwaru a služeb IoT poskytovaných různými dodavateli přináší mezery v zabezpečení, soukromí, transparentnosti a dodržování předpisů, což může být obtížné odhalit, natož opravit. Výběr správného softwaru a služeb IoT je založen na hledání poskytovatelů, kteří mají rozsáhlé zkušenosti s provozem služeb, které se rozprostírají napříč vertikálami a zeměpisnými oblastmi, ale jsou také schopny škálovat bezpečným a transparentním způsobem. Podobně pomáhá vybranému poskytovateli mít desítky let zkušeností s vývojem zabezpečeného softwaru běžícího na miliardách strojů po celém světě a má schopnost ocenit hrozbu, kterou tento nový svět internetu věcí představuje.

## <a name="secure-infrastructure-from-the-ground-up"></a>Bezpečná infrastruktura od základu

Infrastruktura [Microsoft Cloud](https://azure.microsoft.com) podporuje více než jednu miliardu zákazníků ve 127 zemích nebo oblastech. Na základě desetiletí trvajících zkušeností společnosti Microsoft s budováním podnikového softwaru a s pouštění jedněch z největších online služeb na světě poskytuje microsoft cloud vyšší úroveň vylepšeného zabezpečení, ochrany osobních údajů, dodržování předpisů a postupů pro zmírňování hrozeb. než by většina zákazníků mohla dosáhnout sama.

[Životní cyklus vývoje zabezpečení (SDL)](https://www.microsoft.com/sdl/) poskytuje povinný celopodnikový proces vývoje, který začleňuje požadavky na zabezpečení do celého životního cyklu softwaru. Aby bylo zajištěno, že provozní činnosti budou dodržovat stejnou úroveň postupů zabezpečení, používá Společnost SDL přísné pokyny pro zabezpečení stanovené v procesu Microsoft Operational Security Assurance (OSA). Společnost Microsoft také spolupracuje s auditorskými firmami třetích stran na průběžném ověřování, že splňuje své povinnosti v oblasti dodržování předpisů, a společnost Microsoft se zabývá rozsáhlým bezpečnostním úsilím prostřednictvím vytváření center excelence, včetně oddělení microsoft digital crimes, Microsoft Security Response Center a Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – zabezpečená infrastruktura IoT pro vaši firmu

Microsoft Azure nabízí kompletní cloudové řešení, které kombinuje neustále rostoucí kolekci integrovaných cloudových služeb – analýzy, strojové učení, úložiště, zabezpečení, sítě a web – s předním závazkem v oblasti ochrany a soukromí vašich údajů. Microsoft [předpokládá porušení](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategie používá specializované *červený tým* odborníků na zabezpečení softwaru, kteří simulují útoky, testování schopnosti Azure odhalit, chránit před vznikajícími hrozbami a zotavit se z porušení. Globální tým společnosti Microsoft [pro reakci na incidenty](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) pracuje nepřetržitě, aby zmírnil účinky útoků a škodlivých aktivit. Tým dodržuje zavedené postupy pro správu incidentů, komunikaci a obnovení a používá zjistitelná a předvídatelná rozhraní s interními a externími partnery.

Systémy společnosti Microsoft poskytují nepřetržitou detekci a prevenci vniknutí, prevenci útoků služeb, pravidelné testování průniku a forenzní nástroje, které pomáhají identifikovat a zmírnit hrozby. [Vícefaktorové ověřování](../articles/active-directory/authentication/multi-factor-authentication.md) poskytuje další vrstvu zabezpečení pro koncové uživatele pro přístup k síti. A pro aplikaci a poskytovatele hostitele nabízí společnost Microsoft řízení přístupu, monitorování, anti-malware, skenování zranitelnosti, opravy a správu konfigurace.

Akcelerátory řešení využívají zabezpečení a ochranu osobních údajů integrované v platformě Azure spolu s procesy SDL a OSA pro bezpečný vývoj a provoz veškerého softwaru společnosti Microsoft. Tyto postupy poskytují ochranu infrastruktury, ochranu sítě a funkce identity a správy, které jsou zásadní pro zabezpečení jakéhokoli řešení.

[Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) v rámci [akcelerátorů řešení IoT](../articles/iot-fundamentals/iot-introduction.md) nabízí plně spravovanou službu, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi zařízeními IoT a službami Azure, jako je [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) a Azure [Stream Analytics,](../articles/stream-analytics/stream-analytics-introduction.md) pomocí přihlašovacích údajů zabezpečení pro jedno zařízení a řízení přístupu.

Aby bylo možno co nejlépe komunikovat funkce zabezpečení a ochrany osobních údajů integrované do akcelerátorů řešení Azure IoT, tento článek rozdělí sadu do tří primárních oblastí zabezpečení.

![Akcelerátory řešení Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Zřizování a ověřování zabezpečeného zařízení

Akcelerátory řešení zabezpečují zařízení v terénu tím, že poskytují jedinečný klíč identity pro každé zařízení, který může infrastruktura IoT použít ke komunikaci se zařízením během jeho provozu. Tento proces je rychlý a snadno nastavit. Generovaný klíč s ID zařízení vybrané uživatelem tvoří základ tokenu používaného ve veškeré komunikaci mezi zařízením a službou Azure IoT Hub.

ID zařízení mohou být přidružena k zařízení během výroby (tj. blikají v modulu hardwarového vztahu) nebo mohou jako proxy server použít existující pevnou identitu (například sériová čísla procesoru). Vzhledem k tomu, že změna těchto identifikačních informací v zařízení není jednoduchá, je důležité zavést ID logického zařízení v případě, že se změní základní hardware zařízení, ale logické zařízení zůstane stejné. V některých případech může dojít k přidružení identity zařízení v době nasazení zařízení (například ověřený terénní inženýr fyzicky konfiguruje nové zařízení při komunikaci s back-endem řešení). [Registr identit služby Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) poskytuje zabezpečené úložiště identit zařízení a klíčů zabezpečení pro řešení. Jednotlivé nebo skupiny identit zařízení lze přidat do seznamu povolených položek nebo seznamu blokování, což umožňuje úplnou kontrolu nad přístupem k zařízení.

Zásady řízení přístupu azure ioT hubv cloudu umožňují aktivaci a zakázání jakékoli identity zařízení a poskytují způsob, jak v případě potřeby zrušit přidružení zařízení k nasazení IoT. Toto přidružení a oddělení zařízení je založeno na každé identitě zařízení.

Mezi další funkce zabezpečení zařízení patří:

* Zařízení nepřijímají nevyžádaná síťová připojení. Navazují všechna spojení a trasy pouze odchozím způsobem. Aby zařízení přijalo příkaz z back-endu, musí zařízení zahájit připojení, aby zkontrolovalo všechny čekající příkazy, které mají být zpracovány. Jakmile je připojení mezi zařízením a službou IoT Hub bezpečně navázáno, lze zprávy z cloudu do zařízení a zařízení do cloudu odesílat transparentně.

* Zařízení se připojují jenom nebo vytvářejí trasy ke známým službám, se kterými jsou partnerská, jako je například Azure IoT Hub.

* Autorizace a ověřování na úrovni systému používají identity pro zařízení, takže přístupová pověření a oprávnění jsou téměř okamžitě odvolatelná.

### <a name="secure-connectivity"></a>Zabezpečené připojení

Odolnost zasílání zpráv je důležitou vlastností každého řešení IoT. Potřeba trvale dodávat příkazy a/nebo přijímat data ze zařízení je podtržena skutečností, že zařízení IoT jsou připojena přes Internet nebo jiné podobné sítě, které mohou být nespolehlivé. Azure IoT Hub nabízí odolnost zasílání zpráv mezi cloudem a zařízeními prostřednictvím systému potvrzení v reakci na zprávy. Další odolnost pro zasílání zpráv je dosaženo ukládání zpráv do mezipaměti v centru IoT Hub po dobu až sedmi dnů pro telemetrii a dva dny pro příkazy.

Účinnost je důležitá pro zajištění zachování zdrojů a provozu v prostředí s omezenými zdroji. PROTOKOL HTTPS (HTTP Secure), standardní zabezpečená verze oblíbeného protokolu HTTP, je podporovaná službou Azure IoT Hub, která umožňuje efektivní komunikaci. Protokol YMQP (Advanced Message Queuing Protocol) a Přenos telemetrie služby Řízení front zpráv (MQTT), podporovaný službou Azure IoT Hub, jsou navrženy nejen z hlediska efektivity z hlediska využití prostředků, ale také spolehlivého doručování zpráv. 

Škálovatelnost vyžaduje možnost bezpečného vzájemného působení s širokou škálou zařízení. Azure IoT hub umožňuje zabezpečené připojení k zařízením s podporou IP i zařízení s podporou IP. Zařízení s podporou PROTOKOLU IP se mohou přímo připojit a komunikovat s službou IoT Hub prostředně prostředně prostředně zabezpečenépřipojení. Zařízení bez ip adres jsou omezena na prostředky a připojují se pouze přes komunikační protokoly na krátké vzdálenosti, jako jsou Zwave, ZigBee a Bluetooth. Brána pole se používá k agregaci těchto zařízení a provádí překlad protokolu, aby umožnila zabezpečenou obousměrnou komunikaci s cloudem.

Mezi další funkce zabezpečení připojení patří:

* Komunikační cesta mezi zařízeními a službou Azure IoT Hub nebo mezi bránami a službou Azure IoT Hub je zabezpečená pomocí standardního zabezpečení transportní vrstvy (TLS) pomocí služby Azure IoT Hub ověřená pomocí protokolu X.509.

* Z důvodu ochrany zařízení před nevyžádanými příchozími připojeními azure iot hub neotevře žádné připojení k zařízení. Zařízení inicializuje všechna připojení.

* Azure IoT Hub trvale ukládá zprávy pro zařízení a čeká na připojení zařízení. Tyto příkazy jsou uloženy po dobu dvou dnů, což umožňuje zařízení připojení sporadicky, z důvodu napájení nebo připojení obavy, přijímat tyto příkazy. Azure IoT Hub udržuje frontu pro každé zařízení pro každé zařízení.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Bezpečné zpracování a ukládání v cloudu

Akcelerátory řešení pomáhají zabezpečit data od šifrované komunikace až po zpracování dat v cloudu. Poskytuje flexibilitu při implementaci dalšího šifrování a správy klíčů zabezpečení.

Pomocí Azure Active Directory (AAD) pro ověřování uživatelů a autorizaci, akcelerátory řešení Azure IoT můžete poskytnout model autorizace založené na zásadách pro data v cloudu, což umožňuje snadnou správu přístupu, který lze auditovat a kontrolovat. Tento model také umožňuje téměř okamžité odvolání přístupu k datům v cloudu a zařízení připojených k akcelerátorům řešení Azure IoT.

Jakmile jsou data v cloudu, mohou být zpracována a uložena v libovolném uživatelském pracovním postupu. Přístup ke každé části dat je řízen pomocí služby Azure Active Directory v závislosti na použité službě úložiště.

Všechny klíče používané infrastrukturou IoT jsou uloženy v cloudu v zabezpečeném úložišti, s možností převrácení v případě, že klíče je potřeba znovu zřídit. Data mohou být uložena v [Azure Cosmos DB](../articles/cosmos-db/introduction.md) nebo v [databázích SQL](../articles/sql-database/sql-database-faq.md), což umožňuje definici požadované úrovně zabezpečení. Azure navíc poskytuje způsob, jak monitorovat a auditovat veškerý přístup k vašim datům, aby vás upozornil na jakékoli narušení nebo neoprávněný přístup.

## <a name="conclusion"></a>Závěr

Internet věcí začíná vašimi věcmi – věcmi, které jsou pro firmy nejdůležitější. IoT může přinést firmě úžasnou hodnotu tím, že sníží náklady, zvýší příjmy a transformuje podnikání. Úspěch této transformace do značné míry závisí na výběru správného softwaru ioT a poskytovatele služeb. To znamená najít poskytovatele, který nejen katalyzuje tuto transformaci pochopením obchodních potřeb a požadavků, ale také poskytuje služby a software vytvořený se zabezpečením, ochranou osobních údajů, transparentností a dodržováním předpisů jako hlavní aspekty návrhu. Společnost Microsoft má rozsáhlé zkušenosti s vývojem a nasazováním zabezpečeného softwaru a služeb a je i nadále lídrem v této nové době internetu věcí.

Akcelerátory řešení vytvářejí bezpečnostní opatření již od návrhu, což umožňuje bezpečné monitorování aktiv za účelem zvýšení efektivity, zvýšení provozní výkonnosti, umožnění inovací, a využití pokročilých analýz dat k transformaci podniků. Díky vrstveným přístupům k zabezpečení, více funkcím zabezpečení a návrhovým vzorům pomáhají akcelerátory řešení nasadit infrastrukturu, které lze důvěřovat při transformaci jakékoli firmy.

## <a name="additional-information"></a>Další informace

Každý akcelerátor řešení vytváří instance služeb Azure, jako jsou:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): Vaše brána, která propojuje cloud se zařízeními. Můžete škálovat na miliony připojení na rozbočovač a zpracovávat obrovské objemy dat pomocí podpory ověřování na zařízení, která vám pomůže zabezpečit vaše řešení.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Škálovatelná, plně indexovaná databázová služba pro částečně strukturovaná data, která spravují metadata pro zařízení, která zřídíte, jako jsou atributy, konfigurace a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné a vysoce propustné zpracování, indexování dat bez schématu a bohaté rozhraní dotazu SQL.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Zpracování streamů v reálném čase v cloudu, které vám umožní rychle vyvinout a nasadit nízkonákladové analytické řešení pro odhalení přehledů v reálném čase ze zařízení, senzorů, infrastruktury a aplikací. Data z této plně spravované služby můžete škálovat na libovolný svazek a zároveň dosáhnout vysoké propustnosti, nízké latence a odolnosti proti chybám.

* [**Azure App Services:**](https://azure.microsoft.com/services/app-service/)Cloudová platforma pro vytváření výkonných webových a mobilních aplikací, které se připojují k datům kdekoli; v cloudu nebo v místním prostředí. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integrujte se svým softwarem jako službou (SaaS) a podnikovými aplikacemi s out-of-the-box připojení k desítkám cloudových služeb a podnikových aplikací. Kódujte ve svém oblíbeném jazyce a ide – .NET, Node.js, PHP, Python nebo Java – a vytvářejte webové aplikace a rozhraní API rychleji než kdy předtím.

* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): Funkce Logic Apps služby Azure App Service pomáhá integrovat vaše řešení IoT do vašich stávajících obchodních systémů a automatizovat procesy pracovního postupu. Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají od aktivační události a pak provést řadu kroků – pravidla a akce, které používají výkonné konektory pro integraci s obchodními procesy. Logic Apps nabízí out-of-the-box připojení k rozsáhlé ekosystému SaaS, cloudové a místní aplikace.

* [**Úložiště objektů blob Azure:**](https://azure.microsoft.com/services/storage/)Spolehlivé a hospodárné cloudové úložiště pro data, která vaše zařízení odesílají do cloudu.