---
title: Koncepty zabezpečení Azure IoT Hub X. 509 | Microsoft Docs
description: Koncept – porozumění hodnotě X. 509 certifikáty certifikační autority ve výrobě zařízení IoT a ověřování.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 877200cbafbe68fa6161025572abfddad651e172
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490716"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Konceptuální porozumění certifikátům CA X. 509 v oboru IoT

Tento článek popisuje hodnotu použití certifikátů certifikační autority (CA) X. 509 ve výrobě zařízení IoT a ověřování pro IoT Hub. Obsahuje informace o nastaveních dodavatelských řetězců a výhodách zvýraznění.

Tento článek popisuje:

* Jaké jsou certifikáty certifikační autority X. 509 a jak se mají získat

* Jak zaregistrovat certifikát CA X. 509 pro IoT Hub

* Postup nastavení výrobního dodavatelského řetězce pro ověřování na základě CA X. 509

* Způsob, jakým se zařízení podepsaná pomocí certifikační autority X. 509 připojují k IoT Hub

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Přehled

Ověřování pomocí certifikační autority X. 509 (CA) představuje přístup k ověřování zařízení, která se IoT Hub pomocí metody, která výrazně zjednodušuje vytváření identit zařízení a správu životního cyklu v zásobovacím řetězci.

Rozlišující atribut ověřování CA X. 509 je vztah 1: n, který má certifikát certifikační autority se zařízeními pro příjem dat. Tento vztah umožňuje registraci libovolného počtu zařízení do IoT Hub tím, že jednou registruje certifikát CA X. 509. jinak musí být jedinečné certifikáty zařízení předem registrované pro každé zařízení předtím, než se zařízení může připojit. Tento vztah 1: n taky zjednodušuje operace správy životního cyklu certifikátů zařízení.

Dalším důležitým atributem ověřování CA X. 509 je zjednodušení logistiky dodavatelského řetězce. Zabezpečené ověřování zařízení vyžaduje, aby každé zařízení mělo v rámci vztahu důvěryhodnosti jedinečný tajný klíč jako klíč. V ověřování na základě certifikátů je tento tajný klíč soukromým klíčem. Typický výrobní tok zařízení zahrnuje několik kroků a starají. Zabezpečená správa privátních klíčů zařízení napříč několika starají a udržování důvěry je obtížná a nákladná. Pomocí certifikačních autorit se tento problém vyřeší tím, že se každý implicitní správce podepíše do kryptografického řetězce důvěry, ale nebudete ho pověřit pomocí privátních klíčů zařízení. Každý implicitní správce v nástroji zase podepisuje zařízení podle příslušného kroku výrobního toku. Celkový výsledek je optimální dodavatelský řetězec s integrovanou zodpovědností prostřednictvím použití kryptografického řetězu důvěryhodnosti. Je potřeba poznamenat, že tento proces má při ochraně svých jedinečných privátních klíčů největší zabezpečení. K tomuto účelu doporučujeme používat hardwarové moduly hardwarového zabezpečení (HSM) schopné interně generovat privátní klíče, které nikdy neuvidí den.

Tento článek nabízí ucelený pohled na použití ověřování CA X. 509, od nastavení dodavatelských řetězců až po připojení zařízení, přičemž při použití reálného světa se přesvědčit porozumění.

Skupiny registrací můžete použít taky v Azure IoT Hub Device Provisioning Service (DPS), abyste mohli zpracovávat zřizování zařízení do Center. Další informace o použití DPS ke zřízení zařízení s certifikátem X. 509 najdete v tématu [kurz: zřízení více zařízení x. 509 pomocí skupin](../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md)registrací.

## <a name="introduction"></a>Úvod

Certifikát CA X. 509 je digitální certifikát, jehož držitel může podepisovat jiné certifikáty. Tento digitální certifikát je X. 509, protože odpovídá standardu formátování certifikátu předepsanému standardem RFC 5280 sdružení IETF a je certifikační autoritou (CA), protože jeho držitel může podepisovat jiné certifikáty.

Použití certifikační autority X. 509 je nejlépe pochopitelné ve vztahu k konkrétnímu příkladu. Zvažte možnost společnost-X, což je tvůrce inteligentních pomůcek X určených pro profesionální instalaci. Od společnosti-X se vyrábí i z výroby a instalace. Továrna IT od výrobce – Y k výrobě technologie Smart-X-widgets a techniků pro poskytovatele služeb – Z k instalaci. Společnost-X si přeje, aby se inteligentní a uživatelsky widgety přímo dopravují z továrny Y do technika – Z pro instalaci a aby se po instalaci připojovaly přímo k instanci IoT Hub společnosti X, a to bez dalšího zásahu od společnosti-X. K tomu je potřeba, aby společnost-X dokončila několik jednorázových operací nastavení pro automatické připojení k základnímu inteligentnímu Pomůckě. S ohledem na celý scénář je zbývající část tohoto článku strukturována takto:

* Získání certifikátu certifikační autority X. 509

* Registrovat certifikát CA X. 509 pro IoT Hub

* Podepisování zařízení do řetězce certifikátů důvěryhodnosti

* Připojení zařízení

## <a name="acquire-the-x509-ca-certificate"></a>Získání certifikátu certifikační autority X. 509

Společnost-X má možnost koupit certifikát CA X. 509 od veřejné kořenové certifikační autority nebo ho vytvořit pomocí procesu podepsaného svým držitelem. Jedna možnost by byla v závislosti na scénáři aplikace optimální. Bez ohledu na možnost proces zahrnuje dva základní kroky, vygenerování páru veřejného a privátního klíče a podepsání veřejného klíče na certifikát.

![Tok pro generování certifikátů X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Podrobnosti o tom, jak tento postup provést, se liší od různých poskytovatelů služeb.

### <a name="purchasing-an-x509-ca-certificate"></a>Zakoupení certifikátu certifikační autority X. 509

Zakoupení certifikátu certifikační autority má výhodu, že by měla dobře známá kořenová certifikační autorita fungovat jako důvěryhodná třetí strana, která vám při připojování zařízení ručí za legitimitu zařízení IoT. Společnost – X zvolí tuto možnost, pokud chtějí s produkty nebo službami třetích stran komunikovat s produkty nebo službami jiných výrobců po počátečním připojení k IoT Hub.

Pokud chcete koupit certifikát CA X. 509, společnost-X zvolí poskytovatele kořenových certifikátů. Hledání v Internetu fráze "Kořenová certifikační autorita" poskytne dobrá zájem. Kořenová certifikační autorita povede společnost-X o tom, jak vytvořit pár veřejného a privátního klíče a jak vygenerovat žádost o podepsání certifikátu pro své služby. CSR je formální proces, který se používá pro certifikát od certifikační autority. Výsledek tohoto nákupu je certifikát, který se použije jako certifikát autority. Vzhledem k všudypřítomnosti výrazněí certifikátů X. 509 je pravděpodobně certifikát správně formátován na standard RFC 5280 specifikace IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Vytvoření certifikátu certifikační autority Self-Signed X. 509

Proces vytvoření Self-Signed certifikátu certifikační autority X. 509 se podobá nákupu s výjimkou, že se týká registrace třetí strany, jako je například kořenová certifikační autorita. V našem příkladu společnost-X podepíše svůj certifikát autority namísto kořenové certifikační autority. Společnost – X může zvolit tuto možnost testování, dokud nebudou připravené k nákupu certifikátu autority. Společnost-X může také v produkčním prostředí používat certifikát CA X. 509 podepsaný svým držitelem, pokud inteligentní-X-widget není určený k připojení k žádným službám třetích stran mimo IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Zaregistrujte certifikát X. 509, aby bylo možné IoT Hub

Společnost-X potřebuje zaregistrovat certifikační autoritu X. 509, aby IoT Hub, kde bude sloužit k ověřování inteligentních pomůcek (Smart-X-widgetů) při jejich připojení. Jedná se o jednorázový proces, který umožňuje ověřit a spravovat libovolný počet zařízení s Smart-X widgetem. Jedná se o jednorázový proces, protože vztah 1: n mezi certifikátem certifikační autority a certifikáty zařízení, které jsou podepsány certifikátem certifikační autority nebo zprostředkujícím certifikátem. Tento vztah představuje jednu z hlavních výhod použití metody ověřování CA X. 509. Alternativou je nahrání individuálních kryptografických otisků certifikátů pro každé a každé zařízení s inteligentními X a widgety, které přidávají provozním nákladům.

Registrace certifikátu certifikační autority X. 509 je proces se dvěma kroky, nahrání certifikátu a důkazem o vlastnictví certifikátu.

![Registrace certifikátu X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Nahrávání certifikátu certifikační autority X. 509

Proces nahrání certifikátu certifikační autority X. 509 je právě ten, nahrajte do IoT Hub certifikát certifikační autority. IoT Hub očekává certifikát v souboru. Společnost-X jednoduše nahraje soubor certifikátu. Soubor certifikátu nesmí za žádných okolností obsahovat žádné privátní klíče. Osvědčené postupy od standardů řídících pravidla infrastruktury veřejných klíčů (PKI), která v tomto případě jsou v privátních verzích společnosti-X, se nacházejí výhradně v rámci společnosti-X.

### <a name="proof-of-possession-of-the-certificate"></a>Ověření vlastnictví certifikátu

Certifikát CA X. 509, stejně jako jakýkoliv digitální certifikát, je veřejné informace, které jsou náchylné k odposlouchávání. V takovém případě může odposlouchávání zachytit certifikát a pokusit se ho nahrát jako svůj vlastní. V našem příkladu IoT Hub chtít, aby se zajistilo, že společnost-x odesílá certifikát certifikační autority, která ve skutečnosti patří do společnosti-X. Udělá to tak náročné, že společnost-X by prokázala, že by ve skutečnosti měla certifikát prostřednictvím [toku kontrolního programu (pop)](https://tools.ietf.org/html/rfc5280#section-3.1). Tok ověření příchodu má za následek IoT Hub vygenerování náhodného čísla podepsaného společností-X pomocí jeho privátního klíče. Pokud společnost-X sledovala osvědčené postupy infrastruktury veřejných klíčů a chránila jejich soukromý klíč, pak budou mít jenom na pozici, aby správně reagovala na výzvu k ověření. IoT Hub pokračuje v registraci certifikátu CA X. 509 po úspěšné odezvě na výzvu k ověření příznaku.

Úspěšná odpověď na výzvu k ověření příchodu z IoT Hub dokončila registraci CA X. 509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Podepisování zařízení do řetězce certifikátů důvěryhodnosti

IoT vyžaduje, aby každé zařízení měla jedinečnou identitu. Tyto identity jsou ve formě certifikátů pro schémata ověřování na základě certifikátů. V našem příkladu to znamená, že každá technologie Smart-X-widget musí mít jedinečný certifikát zařízení. Jak je nastavení společnosti-X pro tento dodavatelský řetězec?

Jedním ze způsobů, jak jít o tuto funkci, je předem vygenerovat certifikáty pro inteligentní a křížové pomůcky a pověřit znalosti odpovídajících jedinečných klíčů zařízení s partnery dodavatelských řetězců. V případě společnosti-X to znamená, že objekt factory-Y a technici – Z. I když je to platná metoda, je dodávána s výzvami, které je třeba překonat, aby bylo zajištěno, že důvěřujete následujícím způsobem:

1. Aby bylo možné sdílet soukromé klíče zařízení s partnerskými dodavatelskými partnery, kromě ignorování osvědčených postupů pro nikdy nesdílení privátních klíčů je vytváření vztahů důvěryhodnosti v dodavatelských řetězcích nenáročné. To znamená, že se jedná o kapitálové systémy, jako jsou zabezpečené místnosti pro zadávání privátních klíčů zařízení, a procesy, jako je třeba pravidelné audity zabezpečení. Přidejte náklady do dodavatelského řetězce.

2. Bezpečné monitorování účtů pro zařízení v dodavatelských řetězcích a jejich následné správě v nasazení se stávají úlohou 1:1 pro každou dvojici mezi jednotlivými zařízeními z hlediska jedinečného certifikátu zařízení (tedy privátní klíč) od generace po vyřazení zařízení. Tato operace brání správě skupin zařízení, pokud není koncept skupin explicitně integrován do procesu nějakým způsobem. Zabezpečená správa účtů a životního cyklu zařízení se proto stala velkým zatížením provozu. V našem příkladu by tato režie byla označena jako společnost-X.

Ověřování certifikátů certifikační autority X. 509 nabízí elegantní řešení, které afore uvedené problémy prostřednictvím použití řetězů certifikátů. Řetěz certifikátů vychází z certifikační autority, která podepisuje zprostředkující certifikační autoritu, která zase podepisuje jinou zprostředkující certifikační autoritu, a tak dále, dokud nepodepíše zařízení koncovou zprostředkující CA. V našem příkladu firma-X podepisuje značky Factory-Y, který zase podepisuje technici – Z, který nakonec podepisuje Smart-X-widget.

![Hierarchie řetězu certifikátů](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Nad sebou se certifikáty v řetězu prezentují jako logickou stranu autority. Řada dodavatelských řetězců řídí tuto logickou stranu, při které se každá zprostředkující certifikační autorita přihlašuje k řetězci při přijímání všech nadřazených certifikátů certifikační autority a poslední zprostředkující certifikační autorita nakonec podepíše každé zařízení a vloží všechny certifikáty autority z řetězce do zařízení. To je běžné v případě, že podniková výrobní zakázka s hierarchickou výrobou probere určitou továrnu k výrobě. I když hierarchie může obsahovat několik úrovní (například podle geografického/výrobního typu/výrobního řádku), bude se zařízením pracovat pouze továrna na konci, ale řetěz je udržován od horního okraje hierarchie.

Alternativní řetězy můžou mít v zařízení různou zprostředkující certifikační autoritu. v takovém případě certifikační autorita, která komunikuje se zařízením, vloží obsah řetězu certifikátů v daném okamžiku. Hybridní modely jsou taky možné v případě, že se zařízením používá fyzická interakce jenom z nějaké certifikační autority.

V našem příkladu interakce typu Factory-Y a technici-Z s použitím technologie Smart-X-widget. I když společnost-X vlastní inteligentní pomůcku-X-widget, nejedná se ve skutečnosti o fyzické interakce v celém řetězci dodávek. Řetěz certifikátů důvěryhodnosti pro inteligentní technologie X se proto skládá ze společnosti-X podepisování továrny-Y, která zase podepisuje technika – Z, která pak poskytne finální signaturu inteligentního křížového widgetu. Výroba a instalace inteligentního-X-widgetu skládající se Z továrny a Y a technika-Z pomocí příslušných zprostředkujících certifikátů CA k podepisování každého a všech pomůcek inteligentních na X. Konečný výsledek celého procesu je inteligentní technologie X – widgety s jedinečnými certifikáty zařízení a řetěz certifikátů, které se nacházejí až po certifikát certifikační autority ve společnosti X.

![Řetěz důvěryhodnosti od certifikátů jedné společnosti po certifikáty jiné společnosti](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

To je dobrý bod pro kontrolu hodnoty metody CA X. 509. Místo předběžného generování a ručního vypínání certifikátů pro každou inteligentní-křížovou pomůcku do dodavatelského řetězce musela společnost-X podepsat jenom tovární-Y jenom jednou. Místo aby bylo nutné sledovat každé zařízení v životním cyklu zařízení, společnost-X může nyní sledovat a spravovat zařízení prostřednictvím skupin, které přirozeně vycházejí z procesu dodavatelského řetězce, například zařízení, která jsou instalována technikou Z července z červenec v roce.

A ne alespoň, metoda ověřování CA vystavuje bezpečnostní zodpovědnost do dodavatelského řetězce výrobního prostředku. Z důvodu procesu řetězení certifikátů jsou akce každého člena v řetězci kryptograficky zaznamenávány a ověřovány.

Tento proces spoléhá na určité předpoklady, které musí být dokončené pro úplnost. Vyžaduje nezávislé vytváření jedinečného páru veřejného a privátního klíče zařízení a privátní klíč je chráněný v rámci zařízení. Naštěstí zabezpečené Silicon kamínky ve formě modulů hardwarového zabezpečení (HSM), které jsou schopné interně generovat klíče a chránit privátní klíče. Společnost-X stačí přidat jednu z takových čipy do vyúčtování materiálů součástí inteligentního a křížového widgetu.

## <a name="device-connection"></a>Připojení zařízení

Předchozí části se nastavily na připojení zařízení. Tím, že jednoduše zaregistrujete certifikát certifikační autority X. 509, aby se IoT Hub jednou, jak se potenciálně miliony zařízení připojují a ověřují od prvního okamžiku?  Pouh prostřednictvím stejného nahrávání certifikátu a předchozího toku, který jsme nahráli dříve, jsme narazili na registraci certifikátu certifikační autority X. 509.

Zařízení vyrobená pro ověřování CA X. 509 jsou vybavená jedinečnými certifikáty zařízení a řetězcem certifikátů z příslušného zpracovatelského dodavatelského řetězce. Připojení zařízení, dokonce i velmi poprvé, probíhá ve dvou krocích procesu: nahrání řetězu certifikátů a důkaz o vlastnictví.

Během nahrávání řetězu certifikátů zařízení nahraje jedinečný certifikát zařízení spolu s řetězem certifikátů, který je v něm nainstalovaný, aby IoT Hub. Pomocí předregistrovaného certifikátu certifikační autority X. 509 může IoT Hub kryptograficky ověřit několik věcí, že nahraný řetěz certifikátů je interně konzistentní a že řetěz pochází platným vlastníkem certifikátu certifikační autority X. 509. Právě jste používali proces registrace certifikační autority X. 509, IoT Hub by iniciovala proces reakce na výzvu, který ověří, jestli řetěz a tedy certifikát zařízení skutečně patří do zařízení, které ho nahrává. Vygeneruje náhodnou výzvu pro podepsání zařízení pomocí jeho privátního klíče pro ověření IoT Hub. Aktivační události Úspěšné odpovědi IoT Hub přijmout zařízení jako autentické a udělit připojení k němu.

V našem příkladu by jednotlivé inteligentní pomůcky (Smart-X-widget) nahrály jedinečný certifikát zařízení spolu s certifikáty Factory-Y a technici-Z X. 509 a pak reagují na výzvu k ověření přípravení z IoT Hub.

![Tok z jednoho certifikátu na jiný, výzva pop z centra](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Všimněte si, že základ důvěryhodnosti se zanechá v ochraně privátních klíčů, včetně privátních klíčů zařízení. Proto nemůžeme dostatečně zdůraznit důležitost bezpečných čipů silikonu ve formě hardwarového modulu hardwarového zabezpečení (HSM) pro ochranu privátních klíčů zařízení a v rámci obecného osvědčeného postupu, který nikdy nesdílí žádné privátní klíče, jako je jeden objekt, který se svým soukromým klíčem pověřuje jiným způsobem.
