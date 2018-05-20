---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c1ba6cb5b01c86c64fd6134c592829050bbb226d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="internet-of-things-security-from-the-ground-up"></a>Zabezpečení Internetu věcí od základů

Internet věcí (IoT) představuje jedinečné výzvy zabezpečení, ochrany osobních údajů a dodržování předpisů pro firmy po celém světě. Na rozdíl od tradičních internetový technologie, kde tyto problémy základem softwaru a o tom, jak je implementována IoT se vztahuje na co se stane, když internetový a fyzické světů sloučit. Ochrana řešení IoT vyžaduje zajištění zabezpečené zřizování zařízení, zabezpečené připojení mezi tato zařízení a cloudu a ochranu zabezpečení dat v cloudu při zpracování a úložiště. Pracovat se tyto funkce, ale jsou zařízení s omezenými zdroji, geografické rozptýlení nasazení a velký počet zařízení v rámci řešení.

Tento článek popisuje, jak akcelerátorů řešení IoT poskytují zabezpečení a privátní cloudového řešení Internetu věcí. Řešení akcelerátorů poskytovat úplného začátku do konce řešení, se zabezpečení, které jsou součástí každé fáze od základů. Ve společnosti Microsoft, vývoj softwaru zabezpečení je součástí softwaru inženýrství postupem je integrován do společnosti Microsoft desetiletí dlouho prostředí vývoje zabezpečení softwaru. Aby to životního cyklu SDL (Security Development) je základní vývoj metodika, spolu s hostitelem služby zabezpečení na úrovni infrastruktury, například provozní zajištění zabezpečení (OSA) a Microsoft jejichž šíření tým Dcu, Microsoft Security Response Center a Microsoft Malware Protection Center.

Akcelerátorů řešení nabízejí jedinečné funkce, že zkontrolujte zřizování, připojení k a ukládání dat ze zařízení IoT snadný a transparentní a nejvyšší všech, zabezpečení. Tento článek prozkoumá funkce zabezpečení akcelerátorů řešení Azure IoT a strategie nasazení k zajištění zabezpečení, ochrany osobních údajů a dodržování předpisů problémy řešeny.

## <a name="introduction"></a>Úvod

Internet věcí (IoT) je wave budoucí, nabízí firmám okamžité a reálného příležitostí ke snížení nákladů, zvýšit výnosy a transformaci své firmy. Mnoho firem jsou však odhodlání k nasazení IoT v jejich organizace z důvodu obavy o zabezpečení, ochrany osobních údajů a dodržování předpisů. Hlavní bodu zájmu pochází z jedinečnost IoT infrastrukturu, která sloučí internetový a fyzické světů společně vícesložkovém jednotlivých rizik vyplývajících z těchto dvou světů. Zabezpečení IoT se vztahuje k zajištění integrity kódu běžící na zařízení, poskytuje ověřování zařízení a uživatelů, definování zrušte vlastnictví zařízení (stejně jako data generována tato zařízení) a použití odolné vůči internetový a fyzickým útokům.

Pak je problém ochrany osobních údajů. Společnosti mají být průhledná týkající se shromažďování dat, jako v co jsou shromažďována a proč, kteří mohou vidět ho, který určuje přístup a tak dále. Nakonec existují obecné bezpečnostní problémy zařízení společně s osoby je operační a problematiky uchovávání oborových standardů dodržování předpisů.

Vzhledem k zabezpečení, ochrany osobních údajů, průhlednost a aspekty dodržování předpisů, výběr správné poskytovatele řešení IoT zůstává výzvu. Ve hřbetu společně jednotlivé IoT softwaru a služeb od různých dodavatelů zavádí mezery v zabezpečení, ochrany osobních údajů, průhlednost a dodržování předpisů, který může být obtížné zjistit, let alone opravit. Volba správné IoT software a služby zprostředkovatele je založena na hledání zprostředkovatelé, kteří mají rozsáhlých zkušeností s spuštění služeb, které jsou rozmístěny napříč pocházejícími a zeměpisných oblastí, ale jsou taky škálovat zabezpečené a transparentním způsobem. Podobně je užitečné pro vybraného zprostředkovatele mít desetiletí prostředí s vývojem zabezpečené software spuštěný na až miliardy počítačů po celém světě, a mít možnost oceníte povahu hrozeb, které představuje tento nový world Internet věcí.

## <a name="secure-infrastructure-from-the-ground-up"></a>Zabezpečení infrastruktury od základů

[Microsoft Cloud](https://www.microsoft.com/enterprise/microsoftcloud/default.aspx#fbid=WzBsRQi6aGk) infrastruktura podporuje více než jednu miliardu zákazníků v 127 zemích. Kreslení na desetiletí dlouho zkušeností společnosti Microsoft vytváření podnikového softwaru a spuštění některých z online služby největší na světě, Microsoft Cloud poskytuje vyšší úrovně lepší zabezpečení, ochrany osobních údajů, dodržování předpisů a hrozeb zmírnění postupy než většina zákazníků může dosáhnout na své vlastní.

[Životního cyklu SDL (Security Development)](https://www.microsoft.com/sdl/) poskytuje povinné vývoj společnosti proces, který se vloží do softwaru celý životní cyklus požadavky na zabezpečení. Aby bylo zajištěno, že provozní aktivity podle stejnou úroveň postupy zabezpečení, používá SDL pokyny bezpečnost bez kompromisů nastíněny v procesu provozní zajištění zabezpečení (OSA) společnosti Microsoft. Microsoft také pracuje s podniky auditu třetích stran pro probíhající ověření, že splňuje jeho povinnosti dodržování předpisů a Microsoft zapojí v úsilí široký zabezpečení prostřednictvím vytváření Center vynikajících, včetně Microsoft jejichž šíření tým Dcu, Microsoft Security Response Center a Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - zabezpečené IoT infrastrukturu pro vaši organizaci

Microsoft Azure nabízí řešení kompletní cloud, který kombinuje neustále rostoucí kolekce integrovaných cloudové služby – analytics, machine learning, úložiště, zabezpečení, sítě a webové – s své špičkový snahy o ochranu a ochranu osobních údajů vaše data. Společnosti Microsoft [předpokládá porušení](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategie používá vyhrazená *red team* odborníků zabezpečení softwaru, kteří simulovat útoky, testování schopnost Azure rozpoznat, ochrana před vznikajícími hrozbami a obnovení z narušení. Společnosti Microsoft [globální reakcí na incidenty](https://www.microsoft.com/TrustCenter/Security/DesignOpSecurity) týmu funguje po celý den, aby se minimalizoval vliv útoků a škodlivé aktivity. Tým postupuje podle stanovené postupy pro správu incidentů, komunikaci a obnovení a používá rozhraní zjistitelný a předvídatelný s interní a externí partnery.

Systémy společnosti Microsoft zadejte zjišťování neoprávněných vniknutí průběžné a prevence, zabránění útoku služby, regulární průnikům testování a forenzní nástroje, které pomáhají identifikovat a zmírnit hrozby. [Služba Multi-Factor authentication](../articles/active-directory/authentication/multi-factor-authentication.md) poskytuje další úroveň zabezpečení pro koncové uživatele pro přístup k síti. A pro aplikace a poskytovateli hostitele, společnost Microsoft nabízí řízení přístupu, monitorování, proti malwaru, zjišťování ohrožení zabezpečení, opravy a konfigurace správy.

Řešení akcelerátorů využít výhod zabezpečení a o ochraně osobních údajů, které jsou součástí platformy Azure společně s procesy SDL a OSA pro zabezpečený vývoj a provoz veškerého softwaru společnosti Microsoft. Tyto postupy obsahují infrastrukturu ochrany, ochrany sítě a identita a Správa funkce, které jsou zásadní pro zabezpečení řešení.

[Azure IoT Hub](../articles/iot-hub/iot-hub-what-is-iot-hub.md) v rámci [akcelerátorů řešení IoT](../articles/iot-accelerators/iot-accelerators-what-is-azure-iot.md) nabízí plně spravovanou službu, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi zařízeními IoT a službám Azure, jako [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) a [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) pomocí přihlašovacích údajů na zařízení zabezpečení a řízení přístupu.

Pro komunikaci nejlépe zabezpečení a ochrany osobních údajů funkcí integrovaných do akcelerátorů řešení Azure IoT, v tomto článku dojde-li suite do tří oblastí primární zabezpečení.

![Akcelerátory řešení Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Zabezpečené zřizování zařízení a ověřování

Řešení akcelerátorů zabezpečit zařízení, když jsou se v poli tím, že poskytuje klíč jedinečnou identitu pro každé zařízení, který můžete použít IoT infrastruktura pro komunikaci se zařízením, když je v operaci. Proces je rychlý a snadný nastavit. Generovaný klíč s ID zařízení vybrané uživatelem je základem token používá v veškerou komunikaci mezi zařízením a Azure IoT Hub.

ID zařízení může být přidružen zařízení během výrobní (která je, nainstalovaný v modulu hardwarového vztahu důvěryhodnosti), nebo mohou využít stávající pevné identity jako proxy server (například procesoru sériová čísla). Vzhledem k tomu, že změna tento identifikační údaje v zařízení není jednoduchý, je potřeba zavést ID logického zařízení v případě, že základní změny hardwaru zařízení, ale logického zařízení zůstává stejná. V některých případech může dojít přidružení identitu zařízení v době nasazení zařízení (například technika ověřené pole fyzicky nakonfiguruje nové zařízení při komunikaci se serverem back-end řešení). [Registru identit Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) poskytuje zabezpečené úložiště identit zařízení a zabezpečení klíčů pro řešení. Jednotlivé nebo skupiny identit zařízení lze přidat na seznam povolených nebo blokovaných, povolení plnou kontrolu nad přístup k zařízení.

Zásady řízení přístupu Azure IoT Hub v cloudu povolit aktivace a zakázání všechny identity zařízení, poskytuje způsob, jak zrušit přidružení zařízení z nasazení služby IoT případě potřeby. Toto přidružení a zrušení přidružení zařízení je založena na každou identitu zařízení.

Funkce zabezpečení další zařízení:

* Zařízení nepřijímají nevyžádané síťová připojení. Vytvoří všechna připojení a trasy způsobem pouze odchozí. Aby zařízení mohlo přijmout příkaz z back-end zařízení musí inicializovat připojení a kontrolovat případné čekající příkazy ke zpracování. Jakmile je bezpečně navázat připojení mezi zařízením a IoT Hub, zasílání zpráv z cloudu do zařízení a zařízení do cloudu nelze odesílat transparentně.
* Zařízení pouze připojení k nebo vytvářet trasy k dobře známým službám, se kterými se kterými mají partnerský, jako je Azure IoT Hub.
* Úrovni systému autorizaci a ověřování používat identity podle zařízení, což přístupové údaje a povolení téměř-okamžitě odvolatelné.

### <a name="secure-connectivity"></a>Zabezpečené připojení

Odolnost zasílání zpráv je důležitou součást řešení IoT. Fakt, že zařízení IoT připojeni přes Internet nebo jiné podobné sítě, které může nespolehlivé jsou podtržené potřeba spolehlivě příkazy poskytovat nebo přijímat data ze zařízení. Azure IoT Hub nabízí odolnost zasílání zpráv mezi cloudu a zařízením prostřednictvím systému potvrzování v odpovědi na zprávy. Další odolnost pro zasílání zpráv se dosahuje ukládání do mezipaměti zpráv ve službě IoT Hub až sedm dní, telemetrie a dva dny pro příkazy.

Efektivita je důležité zajistit uchování zdrojů a operaci v prostředí s omezenými zdroji. HTTPS (HTTP zabezpečení), standardní zabezpečené verzi protokolu http oblíbených podporuje Azure IoT Hub, povolení efektivní komunikace. Pokročilé služby Řízení front zpráv (protokolu AMQP) a zpráv služby Řízení front Telemetrie přenosu (MQTT), podporované službou Azure IoT Hub, jsou navrženy nejen pro efektivitu z hlediska využití prostředků, ale také doručování zpráv spolehlivé. 

Škálovatelnost vyžaduje schopnost bezpečně spolupracovat s širokou škálu zařízení. Azure IoT hub umožňuje zabezpečené připojení k zařízení s povolenou adresou IP i nepodporujícím IP. Zařízení s podporou IP jsou může přímo připojit a komunikovat s centrem IoT prostřednictvím zabezpečeného připojení. Zapnutá non-IP adresa zařízení jsou omezené prostředků a připojit pouze přes krátkou vzdálenost komunikační protokoly, jako je například Zwave, ZigBee a Bluetooth. Brána pole se používá k agregaci těchto zařízení a provádí překlad protokolu pro zajištění zabezpečené obousměrná komunikace s cloudem.

Funkce zabezpečení další připojení:

* Komunikační trasa mezi zařízením a Azure IoT Hub, nebo mezi brány a Azure IoT Hub, je zabezpečena pomocí standardní zabezpečení TLS (Transport Layer) s Azure IoT Hub ověřování pomocí protokolu X.509.
* Chcete-li chránit zařízení před nevyžádaný příchozí připojení, Azure IoT Hub nelze otevřít žádné připojení k zařízení. Zařízení zahájí všechna připojení.
* Azure IoT Hub spolehlivě uloží zprávy pro zařízení a čeká zařízení pro připojení. Tyto příkazy se uchovávají po dobu dvou dní, povolení zařízení připojující se k, z důvodu napájením nebo připojením aspekty pro příjem těchto příkazů. Azure IoT Hub uchovává fronty za zařízení pro každé zařízení.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Zabezpečené zpracování a úložiště v cloudu

Z šifrované komunikace pro zpracování dat v cloudu akcelerátorů řešení pomohou zabezpečit data. Poskytuje flexibilitu při implementaci další šifrování a správy klíčů zabezpečení.

Pomocí Azure Active Directory (AAD) pro ověřování uživatelů a autorizaci, akcelerátorů řešení Azure IoT může poskytnout model na základě zásad autorizace pro data v cloudu, povolení správy snadný přístup, které je možné auditovat a zkontrolovat. Tento model taky umožňuje téměř rychlých odvolání přístupu k datům v cloudu a zařízení připojená k akcelerátorů řešení Azure IoT.

Jakmile jsou data v cloudu, můžete zpracovat a uložené v pracovním postupu žádné uživatelem definované. Přístup pro každou část dat je řízen s Azure Active Directory, v závislosti na služby úložiště používá.

Všechny klíče používané IoT infrastruktury jsou uložené v cloudu v zabezpečeném úložišti možnost mění v případě, že klíče musí být znovu zajištěny. Data se uloží v [Azure Cosmos DB](../articles/cosmos-db/introduction.md) nebo v [databází SQL](../articles/sql-database/sql-database-faq.md), povolení definice úroveň zabezpečení potřeby. Kromě toho Azure poskytuje způsob, jak sledovat a auditování veškerý přístup k datům vás upozorní na jakékoli narušení nebo neoprávněného přístupu.

## <a name="conclusion"></a>Závěr

Internet věcí začíná vaší věcí – věcí, které vás zajímají firmy. IoT může poskytnout úžasné hodnota obchodní nebo snížení nákladů, zvýšit výnosy a transformace firmy. Úspěch této transformace do značné míry závisí na výběr správné zprostředkovatele software a služby IoT. To znamená, hledání zprostředkovatele, který pouze catalyzes Tato transformace požadavky a pochopení obchodních potřeb, ale také poskytuje služeb a softwaru, které jsou vytvořené s nástroji zabezpečení, ochrany osobních údajů, průhlednost a dodržování předpisů jako hlavní faktorů. Společnost Microsoft rozsáhlých zkušeností s vývoj a nasazení zabezpečené softwaru a služeb a nadále vedoucí postavení v této nové stáří Internet věcí.

Řešení akcelerátorů sestavení v bezpečnostní opatření návrh povolení zabezpečeného sledování prostředků pro zlepšení efektivity, provozní výkon disku a povolit inovace a využít pokročilé datové analýzy k transformaci firmy. S jeho vrstveného přístupu k zabezpečení, více funkce zabezpečení a vzory návrhu akcelerátorů řešení pomůže nasadit infrastrukturu, která může být důvěryhodný transformace každého podnikání.

## <a name="additional-information"></a>Další informace

Každé řešení akcelerátoru vytváří instance služby Azure, například:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): bránu, která se připojuje cloudu do zařízení. Je možné škálovat na miliony připojení na rozbočovače a procesu ohromné objemy dat s podporou ověřování podle zařízení pomáhá vám zabezpečit vaše řešení.
* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): škálovatelné a plně indexované databázová služba pro částečně strukturovaných dat, který spravuje metadata pro zařízení, zřídíte, jako je například atributy, konfiguraci a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné a vysokou propustností zpracování, bez ohledu na schéma indexování dat a bohaté rozhraní SQL.
* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): zpracování v cloudu, která umožňuje rychle vyvíjet a nasadit řešení analytics nízkonákladové k odhalení přehledy v reálném čase ze zařízení, senzorů, infrastruktury a aplikace v reálném čase datového proudu. . Data z této plně spravovanou službu, můžete škálovat na jakýkoli svazek, zatímco stále dosahuje vysoké propustnosti, s nízkou latencí a odolnost proti chybám.
* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): Cloudová platforma vytvářet výkonné webové a mobilní aplikace, které se připojují k datům odkudkoli; v cloudu nebo místně. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integrate váš Software jako služba (SaaS) a podnikové aplikace s připojením se na pole k desítek cloudové služby a podnikové aplikace. Kód v váš oblíbený jazyk a IDE – rozhraní .NET, Node.js, PHP, Python nebo Java – k vytvoření webové aplikace a rozhraní API rychleji než kdy dřív.
* [**Služba Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): funkce The Logic Apps služby Azure App Service pomáhá integrovat řešení IoT tak, aby vaše stávající-obchodní systémy a automatizovat pracovní postupy. Služba Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají spouštěčem událostí a provádějí sérii kroků – pravidla a akce, které použít Výkonné konektory pro integraci s procesy vaší firmy. Služba Logic Apps nabízí připojení se na pole k velká ekosystém SaaS, cloudových a místních aplikací.
* [**Úložiště objektů blob Azure**](https://azure.microsoft.com/services/storage/): spolehlivé a ekonomické cloudové úložiště pro data, která vaše zařízení odesílají do cloudu.