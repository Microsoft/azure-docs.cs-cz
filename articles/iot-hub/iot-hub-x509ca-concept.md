---
title: Koncepty zabezpečení Azure IoT Hub X.509 | Dokumenty společnosti Microsoft
description: Koncept – pochopení hodnoty certifikátů certifikační autority X.509 při výrobě zařízení IoT a ověřování .
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61320241"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Koncepční znalost certifikátů X.509 CA v oboru IoT

Tento článek popisuje hodnotu použití certifikátů certifikační autority X.509 (CA) při výrobě a ověřování zařízení IoT pro službu IoT Hub. Obsahuje informace o nastavení dodavatelského řetězce a zvýrazňují výhody.

Tento článek popisuje:

* Co jsou certifikáty Certifikační autority X.509 a jak je získat

* Jak zaregistrovat certifikát Certifikační autority X.509 do služby IoT Hub

* Jak nastavit výrobní dodavatelský řetězec pro ověřování pomocí certifikační autority X.509

* Jak se zařízení podepsaná pomocí X.509 CA připojují k IoT Hubu

## <a name="overview"></a>Přehled

Ověřování certifikační autority (CA) x.509 je přístup k ověřování zařízení do služby IoT Hub pomocí metody, která výrazně zjednodušuje vytváření identit zařízení a správu životního cyklu v dodavatelském řetězci.

Rozlišovací atribut ověřování certifikační autority X.509 je vztah 1:N, který má certifikát certifikační autority se svými navazujícími zařízeními. Tento vztah umožňuje registraci libovolného počtu zařízení do služby IoT Hub registrací certifikátu Certifikační autority X.509 jednou, jinak musí být jedinečné certifikáty zařízení před připojením předem zaregistrovány pro každé zařízení. Tento vztah 1:N také zjednodušuje operace správy životního cyklu certifikátů zařízení.

Dalším důležitým atributem ověřování certifikační autority X.509 je zjednodušení logistiky dodavatelského řetězce. Zabezpečené ověřování zařízení vyžaduje, aby každé zařízení uchovává jedinečný tajný klíč, jako je klíč jako základ pro důvěryhodnost. V ověřování založeném na certifikátech je tento tajný klíč soukromým klíčem. Typický tok výroby zařízení zahrnuje několik kroků a uschovatelů. Bezpečná správa soukromých klíčů zařízení napříč několika správci a udržování důvěry je obtížné a nákladné. Použití certifikačních úřadů řeší tento problém podepsáním každého správce do kryptografického řetězce důvěryhodnosti, nikoli svěřením soukromých klíčů zařízení. Každý uschovatel zase podepisuje zařízení v příslušném procesním kroku výrobního toku. Celkovým výsledkem je optimální dodavatelský řetězec s vestavěnou odpovědností pomocí kryptografického řetězce důvěry. Stojí za zmínku, že tento proces poskytuje největší zabezpečení, když zařízení chrání své jedinečné soukromé klíče. Za tímto účelem vyzýváme k použití hardwarových zabezpečených modulů (HSM), které jsou schopny interně generovat soukromé klíče, které nikdy nespatří světlo světa.

Tento článek nabízí komplexní zobrazení použití ověřování certifikační autority X.509, od nastavení dodavatelského řetězce až po připojení zařízení, při využití příkladu reálného světa k upevnění porozumění.

## <a name="introduction"></a>Úvod

Certifikát certifikační autority X.509 je digitální certifikát, jehož držitel může podepsat další certifikáty. Tento digitální certifikát je X.509, protože odpovídá standardu formátování certifikátu předepsanému standardem RFC 5280 společnosti IETF a je certifikační autoritou (CA), protože jeho držitel může podepsat další certifikáty.

Použití x.509 CA je nejlépe chápat ve vztahu ke konkrétnímu příkladu. Vezměme si Společnost-X, výrobce Smart-X-Widgety určené pro profesionální instalaci. Společnost-X outsourcuje jak výrobu, tak instalaci. To smlouvy výrobce Factory-Y na výrobu Smart-X-Widgety, a poskytovatel služeb Technician-Z k instalaci. Společnost X si přeje, aby Smart-X-Widget přímo dodává z Factory-Y do Technician-Z pro instalaci a že se připojí přímo k company-X instanci IoT Hub po instalaci bez dalšího zásahu společnosti-X. Aby se tak stalo, společnost X musí dokončit několik jednorázových operací nastavení, aby připravila Inteligentní X-Widget pro automatické připojení. S ohledem na scénář end-to-end je zbytek tohoto článku strukturován takto:

* Získání certifikátu certifikační autority X.509

* Registrace certifikátu Certifikační autority X.509 do služby IoT Hub

* Přihlášení zařízení do řetězu důvěryhodnosti certifikátů

* Připojení zařízení

## <a name="acquire-the-x509-ca-certificate"></a>Získání certifikátu certifikační autority X.509

Společnost X má možnost zakoupit certifikát Certifikační autority X.509 od veřejné kořenové certifikační autority nebo vytvořit certifikát prostřednictvím procesu podepsaného svým držitelem. Jedna možnost by byla optimální než ostatní v závislosti na scénáři aplikace. Bez ohledu na možnost proces zahrnuje dva základní kroky, generování dvojice veřejných a soukromých klíčů a podepsání veřejného klíče do certifikátu.

![Tok pro generování certifikátů X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Podrobnosti o tom, jak tyto kroky provést, se liší u různých poskytovatelů služeb.

### <a name="purchasing-an-x509-ca-certificate"></a>Nákup certifikátu certifikační autority X.509

Zakoupení certifikátu certifikační autority má tu výhodu, že známá kořenová certifikační autorita působí jako důvěryhodná třetí strana, která se při připojení zařízení randí za legitimitu zařízení IoT. Společnost X by tuto možnost zvolila, pokud má v úmyslu smart-X-Widget komunikovat s produkty nebo službami třetích stran po počátečním připojení k IoT Hubu.

Chcete-li zakoupit certifikát certifikační autority X.509, společnost X by zvolila poskytovatele služeb kořenových certifikátů. Vyhledávání na internetu pro frázi 'Root CA' přinese dobré vede. Kořenová certifikační autorita bude řídit společnost X o tom, jak vytvořit dvojici veřejného a soukromého klíče a jak vygenerovat žádost o podpis certifikátu (CSR) pro jejich služby. Zástupce odpovědnosti za sociální odpovědnost je formální proces žádosti o certifikát od certifikační autority. Výsledkem tohoto nákupu je certifikát pro použití jako certifikát autority. Vzhledem k všudypřítomnosti certifikátů X.509 je pravděpodobné, že certifikát byl správně formátován podle standardu RFC 5280 společnosti IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Vytvoření certifikátu certifikační autority X.509 podepsaného svým držitelem

Proces vytvoření certifikátu certifikační autority X.509 s vlastním podpisem je podobný nákupu s výjimkou zapojení podepisujícího třetí strany, jako je kořenová certifikační autorita. V našem příkladu společnost X podepíše certifikát autority namísto kořenové certifikační autority. Společnost X může zvolit tuto možnost pro testování, dokud nebudou připraveni k zakoupení certifikátu autority. Společnost X může také používat certifikát certifikační autority X.509 podepsaný svým držitelem ve výrobě, pokud smart-X-Widget není určen k připojení k žádným službám třetích stran mimo služby IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrace certifikátu X.509 do služby IoT Hub

Společnost X musí zaregistrovat certifikační autoritu X.509 do služby IoT Hub, kde bude sloužit k ověření inteligentních widgetů X při jejich připojení. Jedná se o jednorázový proces, který umožňuje možnost ověření a správy libovolného počtu zařízení Smart-X-Widget. Tento proces je jednorázový z důvodu vztahu 1:N mezi certifikátem autority a zařízeními a také představuje jednu z hlavních výhod použití metody ověřování certifikační autority X.509. Alternativou je nahrát jednotlivé kryptografické otisky certifikátu pro každé zařízení Smart-X-Widget, čímž se zvyšují provozní náklady.

Registrace certifikátu Certifikační autority X.509 je dvoustupňový proces, nahrávání certifikátu a ověřování vlastnictví certifikátu.

![Registrace certifikátu X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Nahrání certifikátu certifikační autority X.509

Proces nahrávání certifikátu certifikační autority X.509 je právě takový, že nahrajte certifikát certifikační autority do služby IoT Hub. IoT Hub očekává certifikát v souboru. Společnost X jednoduše nahraje soubor certifikátu. Soubor certifikátu nesmí za žádných okolností obsahovat žádné soukromé klíče. Osvědčené postupy vyplývající ze standardů, kterými se řídí infrastruktura veřejných klíčů (PKI), nařizují, že znalost soukromé společnosti X v tomto případě sídlí výhradně v rámci společnosti X.

### <a name="proof-of-possession-of-the-certificate"></a>Doklad o vlastnictví osvědčení

Certifikát certifikační autority X.509, stejně jako jakýkoli digitální certifikát, je veřejná informace, která je náchylná k odposlouchávání. Jako takový může odposlouchávač zachytit certifikát a pokusit se jej nahrát jako svůj vlastní. V našem příkladu ioT Hub chce ujistěte se, že certifikát CA Company-X je nahrávání skutečně patří společnosti X. Činí tak tím, že vyzývá Společnost X k prokázání, že ve skutečnosti mají certifikát prostřednictvím [prokázání vlastnictví (PoP) tok](https://tools.ietf.org/html/rfc5280#section-3.1). Tok proof-of possession znamená, že služba IoT Hub generuje náhodné číslo, které má být podepsáno společností X pomocí jeho soukromého klíče. Pokud společnost X postupovala podle osvědčených postupů veřejné hodu a chránila jejich soukromý klíč, byla by pouze schopna správně reagovat na výzvu proof-of-possession. IoT Hub pokračuje v registraci certifikátu Certifikační autority X.509 po úspěšné odpovědi na výzvu proof-of-possession.

Úspěšná odpověď na výzvu proof-of-possession z IoT Hubu dokončí registraci certifikační autority X.509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Přihlášení zařízení do řetězu důvěryhodnosti certifikátů

IoT vyžaduje, aby každé zařízení mělo jedinečnou identitu. Tyto identity jsou ve formulářových certifikátech pro schémata ověřování na základě certifikátů. V našem příkladu to znamená, že každý Smart-X-Widget musí mít jedinečný certifikát zařízení. Jak se společnost-X nastavení pro to ve svém dodavatelském řetězci?

Jedním ze způsobů, jak to udělat, je předgenerovat certifikáty pro Smart-X-Widgety a svěřit znalosti odpovídajících jedinečných soukromých klíčů zařízení partnerům dodavatelského řetězce. Pro společnost X to znamená svěřit Factory-Y a Technician-Z. I když se jedná o platnou metodu, přichází s výzvami, které je třeba překonat, aby byla zajištěna důvěra takto:

1. Nutnost sdílet soukromé klíče zařízení s partnery dodavatelského řetězce, kromě ignorování osvědčených postupů PKI nikdy sdílení soukromých klíčů, je budování důvěry v dodavatelském řetězci nákladné. To znamená, že kapitálové systémy, jako jsou zabezpečené místnosti pro soukromé klíče zařízení, a procesy, jako jsou pravidelné bezpečnostní audity, musí být instalovány. Oba zvyšují náklady dodavatelského řetězce.

2. Bezpečné účtování zařízení v dodavatelském řetězci a pozdější správa v nasazení se stává úkolem 1:1 pro každou dvojici klíč-zařízení od generace jedinečného certifikátu zařízení (tedy soukromého klíče) až po vyřazení zařízení. To vylučuje správu skupin zařízení, pokud koncept skupin je explicitně integrován do procesu nějak. Bezpečné účetnictví a správa životního cyklu zařízení se proto stávají velkou provozní zátěží. V našem příkladu by společnost X nesla toto břemeno.

Ověřování certifikátů certifikační autority X.509 nabízí elegantní řešení již před eposu pomocí certifikačních řetězců. Řetěz certifikátů je výsledkem certifikační autority, která podepisuje zprostředkující certifikační autoritu, která zase podepisuje jinou zprostředkující certifikační autoritu a pokračuje tak, dokud poslední zprostředkující certifikační autorita nepodepíše zařízení. V našem příkladu společnost X podepisuje Factory-Y, což zase podepisuje Technician-Z, který konečně podepíše Smart-X-Widget.

![Hierarchie řetězového řetězce certifikátů](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Nad kaskádou certifikátů v řetězci představuje logické předání autority. Mnoho dodavatelských řetězců se řídí tímto logickým předáním, kdy je každá zprostředkující certifikační autorita přihlášena do řetězce při přijímání všech certifikátů certifikační autority a poslední zprostředkující certifikační autorita konečně podepíše každé zařízení a vloží všechny certifikáty autority z řetězce do přístroje. To je běžné, když smluvní výrobní společnost s hierarchií továren pověří konkrétní továrnu, aby výrobu. Zatímco hierarchie může být několik úrovní hluboké (například podle zeměpisné oblasti / typ produktu / výrobní řádek), pouze továrna na konci dostane k interakci se zařízením, ale řetěz je udržována z horní části hierarchie.

Alternativní řetězce mohou mít různé zprostředkující ca interakci se zařízením v takovém případě certifikační autority interakci se zařízením vloží obsah řetězu certifikátů v tomto okamžiku. Hybridní modely jsou také možné, pokud pouze některé certifikační autority mají fyzickou interakci se zařízením.

V našem příkladu factory-y i technik-z komunikovat s Smart-X-Widget. Zatímco Společnost-X vlastní Smart-X-Widget, ve skutečnosti s ním fyzicky neinteraguje v celém dodavatelském řetězci. Certifikát řetězce důvěry pro Smart-X-Widget proto zahrnují Společnost-X podpisu Factory-Y, který zase podepíše Technik-Z, který pak poskytne konečný podpis Smart-X-Widget. Výroba a instalace Smart-X-Widget zahrnují Factory-Y a Technician-Z pomocí příslušných zprostředkujících certifikátů CA k podpisu každého Smart-X-Widgets. Konečným výsledkem celého procesu jsou smart-X-widgety s jedinečnými certifikáty zařízení a řetězem certifikátů, které jdou až k certifikátu Společnosti-X CA.

![Řetězec důvěry od certifikátů jedné společnosti k certifikátům jiné společnosti](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

To to je dobrý bod pro kontrolu hodnoty X.509 CA metody. Namísto předběžného generování a předávání certifikátů pro každý Smart-X-Widget do dodavatelského řetězce musela společnost-X podepsat factory-y pouze jednou. Namísto sledování každého zařízení v průběhu životního cyklu zařízení může společnost X nyní sledovat a spravovat zařízení prostřednictvím skupin, které přirozeně vyplývají z procesu dodavatelského řetězce, například zařízení instalovaná technikem-Z po červenci nějakého roku.

V neposlední řadě metoda ověřování certifikační autority vylučuje zabezpečenou odpovědnost do dodavatelského řetězce výroby zařízení. Z důvodu procesu řetězu certifikátů jsou akce každého člena v řetězci kryptograficky zaznamenány a ověřitelné.

Tento proces se opírá o určité předpoklady, které musí být vynořil pro úplnost. Vyžaduje nezávislé vytvoření jedinečného páru veřejného a soukromého klíče zařízení a ochranu soukromého klíče v rámci zařízení. Naštěstí existují bezpečné křemíkové čipy ve formě hardwarových zabezpečených modulů (HSM), které jsou schopné interně generovat klíče a chránit soukromé klíče. Společnost X stačí přidat jeden z těchto čipů do komponenty Smart-X-Widget je kusovník.

## <a name="device-connection"></a>Připojení zařízení

Předchozí části výše byly budování až připojení zařízení. Tím, že jste do služby IoT Hub jednou jednoduše zaregistrovali certifikát Certifikační autority X.509, jak se potenciálně miliony zařízení připojují a ověřují od prvního okamžiku?  Jednoduché; prostřednictvím stejného nahrávání certifikátu a proof-of-possession toku jsme se dříve setkali s registrací certifikátu X.509 CA.

Zařízení vyrobená pro ověřování certifikační autority X.509 jsou vybavena jedinečnými certifikáty zařízení a řetězem certifikátů z příslušného výrobního dodavatelského řetězce. Připojení zařízení, a to i vůbec poprvé, se děje ve dvoukrocích procesu: nahrávání řetězce certifikátů a proof-of-possession.

Během nahrávání řetězce certifikátů zařízení nahraje svůj jedinečný certifikát zařízení spolu s řetězem certifikátů nainstalovaným v něm do služby IoT Hub. Pomocí předem registrovaného certifikátu Certifikační autority X.509 může ioT Hub kryptograficky ověřit několik věcí, že nahraný řetěz certifikátů je vnitřně konzistentní a že řetěz vznikl platným vlastníkem certifikátu Certifikační autority X.509. Právě byl s procesem registrace X.509 CA, IoT Hub by zahájit proces proof-of-vlastnictví výzvu-reakce zjistit, že řetězec a tedy certifikát zařízení skutečně patří k zařízení nahrávání. Činí tak generováním náhodné výzvy, která má být podepsána zařízením pomocí jeho soukromého klíče pro ověření službou IoT Hub. Úspěšná odpověď aktivuje službu IoT Hub, aby přijala zařízení jako autentické a udělila mu připojení.

V našem příkladu by každý inteligentní widget X-Widget nahrál svůj jedinečný certifikát zařízení spolu s certifikáty Ca Factory-Y a Technician-Z X.509 a pak by reagoval na výzvu proof-of-possession z IoT Hubu.

![Tok z jednoho certifikátu do druhého, pop výzva z rozbočovače](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Všimněte si, že základem důvěry spočívá v ochraně soukromých klíčů, včetně soukromých klíčů zařízení. Proto nemůžeme dostatečně zdůraznit význam bezpečných křemíkových čipů ve formě hardwarových zabezpečených modulů (HSM) pro ochranu soukromých klíčů zařízení a celkovou best practice nikdy nesdílet žádné soukromé klíče, jako je jedna továrna, která svěřuje své soukromý klíč.