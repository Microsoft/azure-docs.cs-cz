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
ms.openlocfilehash: 8137a292045377c5dccb69c21a8118d0dc17874e
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069936"
---
# <a name="internet-of-things-security-from-the-ground-up"></a>Zabezpečení Internetu věcí od počátku

Internet of Things (IoT) představuje jedinečné výzvy zabezpečení, ochrany osobních údajů a dodržování předpisů pro firmy po celém světě. Na rozdíl od tradiční kybernetických technologie, kde tyto problémy točí kolem software a jak je implementován IoT se týká, co se stane, když kybernetických a fyzických světů sloučit. Ochrana řešení IoT vyžaduje zajištění zabezpečeného zřizování zařízení, zabezpečené připojení mezi tato zařízení a cloud a ochranu zabezpečení dat v cloudu při zpracování a ukládání. Práce proti takové funkce, ale zařízení s omezenými zdroji, geografické rozptýlení nasazení a velký počet zařízení v rámci řešení.

Tento článek popisuje, jak poskytnout zabezpečený a privátní cloudové řešení Internet of Things akcelerátory řešení IoT. Akcelerátory řešení poskytnout kompletní řešení začátku do konce, se zabezpečením, které jsou integrované do každé fáze od základů. V Microsoftu, vývoj softwaru pro zabezpečení je součástí softwarového inženýrství postupem je integrován od Microsoftu desítky dlouho zkušenosti s vývojem zabezpečeného softwaru. Aby to Security Development Lifecycle (SDL) je základní vývojové metodologie, spolu s celou řadu služeb zabezpečení na úrovni infrastruktury, jako je Operational Security Assurance (OSA) a oddělení Microsoft Digital Crimes Unit, Microsoft Security Response Center a Microsoft Malware Protection Center.

Akcelerátory řešení poskytují jedinečné funkce tohoto zpřístupnění zřizování, připojení k a ukládání dat ze zařízení IoT jednoduché a transparentní a nejbezpečnější. Tento článek zkoumá funkcí zabezpečení akcelerátory řešení Azure IoT, jsou strategie nasazení k zajištění zabezpečení, ochrany osobních údajů a dodržování předpisů problémy řešit.

## <a name="introduction"></a>Úvod

Internet of Things (IoT) je wave budoucnost, nabízí firmám okamžité a skutečná příležitostí snížit náklady, zvyšovat výnosy a transformujte svoje podnikání. Řada podniků, ale váhají nasazení IoT v jejich organizace z důvodu obavy o zabezpečení, ochrany osobních údajů a dodržování předpisů. Hlavní bod zájmu pochází z jedinečnost infrastrukturu IoT, která sloučí kybernetických a fyzických světů společně, rozkladu slov jednotlivá rizik vyplývajících z těchto dvou světů. Zabezpečení IoT se vztahuje k zajištění integrity kódu, které jsou spuštěné na zařízeních, poskytuje ověřování zařízení a uživatelů, definování vymazat vlastnictví zařízení (stejně jako data generovaná z těchto zařízení) a je odolná proti selháním kybernetických a fyzických útoků.

Pak je problém o ochraně osobních údajů. Společnosti byste transparentnosti týkající se shromažďování dat, jako v co se shromažďují a proč, kteří je můžou zobrazit, který řídí přístup a tak dále. Dostupné jsou i obecné bezpečnostní problémy zařízení společně s lidí je provoz a problematiky oborové standardy pro dodržování předpisů.

Vzhledem k zabezpečení, ochrany osobních údajů, transparentnosti a dodržování předpisů se týká, Volba správného zprostředkovatele řešení IoT zůstávají náročné. Spojením jednotlivé IoT software a služby poskytované různých dodavatelů, která představuje mezery v zabezpečení, ochrany osobních údajů, transparentnosti a dodržování předpisů, který může být obtížné zjistit, natož opravit. Volba správné IoT software a služby poskytovatele podle hledání poskytovatelé, kteří mají rozsáhlé praxe s provozem služeb, které pracovat nad více vertikální vazby a geografické oblasti, ale jsou také škálovat zabezpečené a transparentním způsobem. Podobně platí pomáhá pro vybraného zprostředkovatele lze několik desetiletí zkušeností s vývojem zabezpečeného softwaru, která běží na miliardách počítačů po celém světě a mít možnost si cení toho světě hrozeb k hlasování o tomto novém světě orientovaném na Internet of Things.

## <a name="secure-infrastructure-from-the-ground-up"></a>Zabezpečené infrastruktury od počátku

[Microsoft Cloud](https://azure.microsoft.com) infrastruktura podporuje více než jednu miliardu zákazníků ve 127 zemích. Kreslení v Microsoftu pro několik desetiletí získávaným zkušenostem vytvářením podnikového softwaru a provozováním některých z největších online služeb na světě, Microsoft Cloud poskytuje vyšší úroveň nabízí vyšší zabezpečení, ochrany osobních údajů, dodržování předpisů a před internetovými útoky postupy omezení rizik než většina zákazníků může dosáhnout na své vlastní.

[Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) poskytuje závazného pořádaného microsoftem procesu vývoje softwaru, který začleňuje požadavky na zabezpečení celého životního cyklu softwaru. K tomu, že provozní činnosti stejnou úroveň postupy zabezpečení, používá SDL striktní bezpečnostní pokyny podle procesu Operational Security Assurance (OSA) od Microsoftu. Microsoft rovněž spolupracuje s třetích stran auditorskými probíhající ověření, že splňuje své závazky dodržování předpisů, a angažovat Microsoft v široké zabezpečení, a to prostřednictvím vytváření sad centra pro vzdělávání profesionálů v oblasti, včetně oddělení Microsoft Digital Crimes Unit, Středisko Microsoft Security Response Center a Microsoft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – zabezpečené infrastruktury IoT pro vaši firmu

Microsoft Azure nabízí kompletní cloudové řešení, ten, který kombinuje neustále rostoucí kolekce integrovaných cloudových služeb – analýza, strojové učení, úložiště, zabezpečení, sítě a webových – bez závazku špičkové ochrany a soukromí vašich dat. Společnosti Microsoft [předpokládej chybu zabezpečení](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) strategie používá vyhrazené *červený tým* specialistů na zabezpečení softwaru, kteří simulace útoku, testování schopnost Azure detekovat, chránit před stále novými hrozbami a obnovení před porušením. Společnosti Microsoft [globální reakce na incidenty](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) týmu funguje neustále a zobrazuje na důsledky útoků a škodlivé aktivity. Tým následuje stanovené postupy pro správu incidentů, komunikace a obnovení a používá rozhraní zjistitelné a předvídatelné s interní a externí partnery.

Systémy od Microsoftu poskytují zjišťování neoprávněných vniknutí průběžné a ochrany před únikem informací, ochrana před útoky na služby, testování průniku a forenzních nástrojů, které pomáhají zjišťovat a zmírňovat hrozby. [Ověřování službou Multi-Factor Authentication](../articles/active-directory/authentication/multi-factor-authentication.md) poskytuje vyšší úroveň zabezpečení pro koncové uživatele při přístupu k síti. A pro aplikace a Hostitel poskytovatele, společnost Microsoft nabízí řízení přístupu, monitorování, anti-malware, zjišťování ohrožení zabezpečení, oprav a správu konfigurace.

Akcelerátory řešení využít výhod zabezpečení a ochrany osobních údajů integrované do platformy Azure spolu s procesu SDL a OSA pro zabezpečený vývoj a provoz veškerého softwaru společnosti Microsoft. Tyto postupy obsahují ochrana infrastruktury, sítě ochrana a Správa identit a funkce, které jsou zásadní pro zabezpečení řešení.

[Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) v rámci [akcelerátory řešení IoT](../articles/iot-fundamentals/iot-introduction.md) nabízí plně spravovanou službu, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi zařízeními IoT a služby Azure, jako [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) a [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) pomocí pověření zabezpečení jednotlivých zařízení a řízení přístupu.

Ke komunikaci nejlépe zabezpečení a ochrany osobních údajů funkce součástí akcelerátory řešení Azure IoT, tento článek boří sady do tří oblastí primární zabezpečení.

![Akcelerátory řešení Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Zabezpečené zřizování zařízení a ověřování

Akcelerátory řešení zabezpečení zařízení, pokud nejsou navýšení kapacity v poli zadáním klíče jedinečné identity pro každé zařízení, která umožňuje infrastruktura IoT komunikovat se zařízením, i když je v provozu. Proces je rychlé a snadné nastavení. Vygenerovaný klíč s ID zařízení uživatel vybral tento balíček je základem token používaných pro veškerou komunikaci mezi zařízením a Azure IoT Hub.

ID zařízení může být přiřazený k zařízení během výrobního (který je, rozsvítilo v modulu hardwarového zabezpečení) nebo použít existující oprava identity jako proxy server (například sériová čísla procesoru). Protože změny tohoto identifikační informace v zařízení není jednoduché, je potřeba zavést ID logického zařízení v případě, že základní změny hardwaru zařízení, ale logické zařízení zůstává stejná. V některých případech se může stát přidružení identitu zařízení v době nasazení zařízení (například ověřený pole programátor fyzicky nakonfiguruje nové zařízení při komunikaci s back-endem řešení). [Registru identit služby Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) poskytuje zabezpečené úložiště identit zařízení a zabezpečení klíčů pro řešení. Osoby nebo skupiny identit zařízení můžete přidat na seznam povolených nebo blokovaných webů, umožňuje úplnou kontrolu přístupu zařízení.

Zásady řízení přístupu Azure IoT Hub v cloudu Povolit aktivaci a zakazuje všechny identity zařízení, poskytuje způsob, jak zrušit přidružení zařízení z nasazení IoT v případě potřeby. Toto přidružení a zrušení přidružení zařízení je založen na každou identitu zařízení.

Funkce zabezpečení další zařízení:

* Zařízení nepřijímají nevyžádané síťové připojení. Navázání všechna připojení a trasy podporuje pouze odchozí. Aby zařízení mohlo přijmout příkaz z back-endu musí inicializovat připojení a kontrolovat případné čekající příkazy ke zpracování. Jakmile mezi zařízením a centrem IoT se vytvoří zabezpečené připojení, zasílání zpráv z cloudu do zařízení a zařízení do cloudu je možné posílat transparentně.

* Zařízení pouze připojení k nebo vytvořit trasy a dobře známé služby, se kterými mají partnerský, jako jsou služby Azure IoT Hub.

* Autorizaci na úrovni systému a ověřování používat identifikaci jednotlivých zařízení, aby přístupové údaje a povolení téměř – okamžitě odvolatelný.

### <a name="secure-connectivity"></a>Zabezpečené připojení

Odolnost zasílání zpráv je důležité funkce všech řešení IoT. Je nutné trvale příkazy poskytovat nebo přijímat data ze zařízení podtržené fakt, že zařízení IoT připojeni přes Internet nebo další podobné sítě, které nemusí nespolehlivé. Azure IoT Hub nabízí odolnost zasílání zpráv mezi cloudem a zařízení prostřednictvím systému potvrzení v reakci na zprávy. Trvanlivost dat pro zasílání zpráv se dosahuje prostřednictvím ukládání do mezipaměti zpráv ve službě IoT Hub po dobu až sedmi dní telemetrie a dva dny pro příkazy.

Efektivita je důležité zajistit ochranu prostředků a operace v prostředí s omezenými zdroji. HTTPS (HTTP zabezpečení), standardní bezpečné verze protokolu http Oblíbené, je podporované ve službě Azure IoT Hub, umožňující efektivní komunikaci. Rozšířeného protokolu řízení front zpráv (AMQP) a zprávy služby Řízení front MQTT (Telemetry Transport), podporované ve službě Azure IoT Hub, jsou určeny pouze pro efektivitu z hlediska využití prostředků, ale také spolehlivé doručování zpráv. 

Škálovatelnost vyžaduje možnost bezpečně spolupracovat s širokou škálu zařízení. Azure IoT hub umožňuje pomocí zabezpečeného připojení k IP povolené i IP nepodporujícím zařízení. Jsou povolené IP zařízení může přímo připojit a komunikovat s centrem IoT přes zabezpečené připojení. Zařízení bez podporou IP jsou s omezenými zdroji a připojit pouze přes krátkou vzdálenost komunikační protokoly, jako je například Zwave ZigBee a Bluetooth. Brána pole se používá k agregaci těchto zařízení a provádí překlad protokolu umožňuje zabezpečenou obousměrnou komunikaci s cloudem.

Další připojení funkce zabezpečení zahrnují:

* Komunikační trasa mezi zařízením a Azure IoT Hub, nebo mezi brány a Azure IoT Hub je zabezpečený pomocí standardních zabezpečení TLS (Transport Layer) s Azure IoT Hub ověřování pomocí protokolu X.509.

* Aby bylo možné chránit zařízení před nevyžádaná příchozí připojení, Azure IoT Hub nelze otevřít žádné připojení k zařízení. Zařízení zahájí všechna připojení.

* Azure IoT Hub trvale ukládá zprávy pro zařízení a čeká na zařízení připojovat. Tyto příkazy jsou uloženy po dobu dvou dnů, povolení zařízení připojení nedojde, z důvodu nebo připojením obavy, přijímá tyto příkazy. Azure IoT Hub udržuje fronty podle zařízení pro každé zařízení.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Zabezpečení zpracování a ukládání v cloudu

Šifrovaná komunikace pro zpracování dat v cloudu akcelerátory řešení pomůžou zabezpečit data. Poskytuje možnost implementovat další šifrování a správy klíčů zabezpečení.

Pro ověřování a autorizace uživatelů pomocí Azure Active Directory (AAD), poskytnete akcelerátory řešení Azure IoT modelu na základě zásad autorizace pro data v cloudu a umožní spravovat snadný přístup, které je možné auditovat a zkontrolovat. Tento model také umožňuje téměř okamžité zrušení přístupu k datům v cloudu a zařízení připojených k akcelerátorům řešení Azure IoT.

Jakmile budou data v cloudu, může být zpracována a uložené v libovolné pracovní postup definovaný uživatelem. Přístup k každá část dat je řízen pomocí Azure Active Directory, v závislosti na službu úložiště použít.

Všechny klíče používané IoT infrastruktury jsou uložené v cloudu v zabezpečeném úložišti, s možností přejít v případě, že klíče musí být znovu zajištěny. Data mohou být uložena v [služby Azure Cosmos DB](../articles/cosmos-db/introduction.md) nebo v [databází SQL](../articles/sql-database/sql-database-faq.md), definice úroveň zabezpečení požadované povolení. Azure navíc poskytuje způsob, jak sledovat a auditovat veškerý přístup k vašim datům vás upozorní na jakékoli napadení nebo neoprávněným přístupem.

## <a name="conclusion"></a>Závěr

Internet of Things začíná u vašich věcí – věcí, které jsou nejdůležitější pro podniky. IoT může poskytovat úžasné hodnotu pro podniky snižuje náklady, zvýšení výnosů a transformace firmy. Úspěch této transformace do značné míry závisí na výběr správného zprostředkovatele software a služby IoT. To znamená, že hledání poskytovatele, který nejen catalyzes tuto transformaci Díky porozumění obchodním potřebám a požadavkům, ale přináší také služeb a softwaru vytvořeného pomocí zabezpečení, ochrany osobních údajů, transparentnosti a dodržování předpisů jako aspekty návrhu hlavní. Microsoft má rozsáhlé zkušenosti s vývojem a nasazením zabezpečeného softwaru a služeb a dál za lídra v tomto novou éru Internet of Things.

Akcelerátory řešení sestavit v bezpečnostních opatření záměrné, povolení zabezpečené sledování prostředků pro zlepšení efektivity, provozní výkon disku a začněte s inovací a využívat pokročilé analýzy dat. k transformaci firem. S jeho vrstveného přístupu na zabezpečení, více funkcí zabezpečení a vzory návrhu akcelerátory řešení pomáhající s nasazením infrastruktura, která může být důvěryhodný pro transformaci jakoukoli firmu.

## <a name="additional-information"></a>Další informace

Každý akcelerátor řešení vytvoří instance služeb Azure, jako například:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): brána, která se připojuje cloudu do zařízení. Je možné škálovat na miliony připojení na rozbočovače a zpracování velkých objemů dat s podporou ověřování podle zařízení pomáhá vám zabezpečit vaše řešení.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): škálovatelná, plně indexované databázovou službu pro částečně strukturovaná data, která spravuje metadat pro zařízení si zřídíte, jako jsou atributy, konfigurace a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné a vysoce propustné zpracování, schématu nezávislé indexování dat a také bohaté rozhraní příkazů jazyka SQL.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): v reálném čase v cloudu, která umožňuje rychlý vývoj a nasazení nízkonákladového analytického řešení pro odhalení informací v reálném čase ze zařízení, senzorů, infrastruktury a aplikací pro zpracování datových proudů . Data z této plně spravované služby můžete škálovat pro jakýkoli svazek, zatímco stále dosahuje vysoké propustnosti, nízké latence a odolnost proti chybám.

* [**Služba Azure App Services**](https://azure.microsoft.com/services/app-service/): cloudovou platformou můžete tvořit výkonné webové a mobilní aplikace, které se připojují k datům bez ohledu na; v cloudu nebo místně. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integrate váš Software jako služba (SaaS) a podnikové aplikace s out-of-the-box připojení k desítkám cloudových služeb a podnikových aplikací. Kód v oblíbeném jazyce a integrovaném vývojovém prostředí – .NET, Node.js, PHP, Python nebo Java – k vytváření webových aplikací a rozhraní API rychleji než kdy dřív.

* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): funkci Logic Apps služby Azure App Service pomáhá vašemu řešení IoT umožní vaše stávající systémy z podnikové integrace a automatizace pracovních postupů. Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají spouštěčem událostí a provádějí sérii kroků, pravidla a akce, které používají výkonné konektory pro integraci s obchodními procesy. Logic Apps nabízí out-of-the-box připojení k rozsáhlému ekosystému aplikací SaaS, cloudové a místní aplikace.

* [**Azure Blob storage**](https://azure.microsoft.com/services/storage/): spolehlivé, úsporné cloudové úložiště pro data, která vaše zařízení odesílají do cloudu.