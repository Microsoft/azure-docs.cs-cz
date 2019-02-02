---
title: Koncepty zabezpečení Azure IoT Hub X.509 | Dokumentace Microsoftu
description: Koncept – Principy hodnotu X.509 certifikační autority certifikáty ve výrobním zařízení IoT a ověřování.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: e3d6464f3e69868b4903d999e52a37b0520018c6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659452"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Koncepční přehled o certifikáty webu X.509 v odvětví IoT

Tento článek vysvětluje výhody použití certifikáty X.509 certifikační autority (CA) v zařízení výrobní IoT a ověřování do služby IoT Hub.  Obsahuje informace o napájení zřetězit instalační program a zvýraznit výhody.

Tento článek popisuje:

* Co jsou certifikáty webu X.509 a jak je získat
* Postup při registraci vašeho certifikátu webu X.509 do služby IoT Hub
* Jak nastavit výrobní společnost dodavatelského řetězce pro ověřování založené na X.509 certifikační Autority
* Jak připojit zařízení s podmíněným Přístupem X.509 podepsaný do služby IoT Hub

## <a name="overview"></a>Přehled

Ověřování X.509 certifikační autoritu (CA) je přístup pro ověřování zařízení do IoT Hubu pomocí metody, která výrazně zjednodušuje správu vytváření a životního cyklu identit zařízení v dodavatelském řetězci.

Rozlišovací atribut ověřování X.509 certifikační Autority je vztah jeden mnoho, který obsahuje certifikát certifikační Autority s jeho podřízených zařízení.  Tento vztah umožňuje zaregistrovat libovolný počet zařízení do služby IoT Hub pomocí registrace certifikátu webu X.509 jednou, v opačném případě jedinečných certifikátů zařízení musí být předem registrované pro každé zařízení, aby zařízení mohlo připojit. Tento vztah jeden mnoho zjednodušuje také operace správy životního cyklu certifikátů zařízení.

Další důležité atribut ověřování X.509 certifikační Autority je zjednodušení logistiky dodavatelského řetězce.  Zabezpečené ověřování zařízení vyžaduje, aby každé zařízení obsahuje jedinečné tajný kód jako klíč jako základ pro vztah důvěryhodnosti. V ověřování na základě certifikátů je tento tajný kód privátní klíč. Zařízení typické výrobní flow zahrnuje více kroků a správce.  Bezpečně spravovat soukromé klíče zařízení napříč více správce a zachování důvěryhodnosti je složitá a nákladná.  Použití certifikační autority se tento problém řeší tím podepisování každý správce do řetěz kryptografického spíše než pověření s privátní klíče zařízení.  Každý správce pak zaregistruje zařízení v kroku jejich odpovídajících procesu výroby toku.  Celkový výsledek je optimální dodavatelský řetězec s integrovanou zodpovědnost pomocí kryptografických řetěz certifikátů.  Je vhodné poznamenat, že tento proces poskytuje nejvyšší zabezpečení, když zařízení chránit jejich jedinečných privátních klíčů.  K tomuto účelu doporučujeme použít nástroje zabezpečení moduly HSM (Hardware) schopný interně vytvářet privátní klíče, které se nikdy zobrazí indikátor den.

Tento článek nabízí pohled začátku do konce použití ověřování X.509 certifikační Autority, z instalačního programu dodavatelského řetězce k připojení zařízení, při vytváření se použije jako příklad reálného světa ztuhnout principy.

## <a name="introduction"></a>Úvod

Certifikát X.509 certifikační Autority je digitální certifikát, jehož vlastník může podepisování jiných certifikátů.  Tento digitální certifikát totiž vyhovuje formátování standard předepsané standard RFC 5280 sdružení IETF pro certifikát X.509 a je certifikační autorita (CA), protože jeho držitel můžete podepisování jiných certifikátů.

Použití certifikační Autority X.509 nejlépe odhalíte ve vztahu k konkrétní příklad.  Vezměte v úvahu společnosti-X, Tvůrce Smart-X-widgetů pro profesionální instalace. Společnost X outsources výrobní a instalaci.  Smlouvy o výrobce Factory-Y výroba Smart-X-Widgetů a poskytovatele služeb Z technik pro instalaci. Společnost X chce, aby Smart-X-widgetu přímo se dodává z Factory-Y až technik Z pro instalaci a se připojuje přímo ke společnosti – každé instanci služby IoT Hub po instalaci bez dalšího zásahu od společnosti X. Dosáhnete toho, třeba splnit několik operací jednorázová nastavení vymazat Smart-X-widgetu pro automatické připojení společnosti X.  Se scénářem začátku do konce v úvahu zbývající část tohoto článku strukturovaná následujícím způsobem:

* Získání certifikátu X.509 certifikační Autority

* Registrace certifikátu webu X.509 do služby IoT Hub

* Přihlášení zařízení do řetěz certifikátů

* Připojení zařízení

## <a name="acquire-the-x509-ca-certificate"></a>Získání certifikátu X.509 certifikační Autority

Společnost X má povolenou možnost zakoupení od certifikační autority veřejného kořenového certifikátu webu X.509 nebo vytvoření nového procesem podepsaný svým držitelem.  Jednou z možností by optimální místo druhého v závislosti na scénáři aplikace.  Bez ohledu na to, možnost proces zahrnuje dva základní kroky, generování dvojice veřejného/soukromého klíče a přihlašování veřejný klíč k certifikátu.

![img-csr-flow](./media/csr-flow.png)

Podrobnosti o tom, jak provádět tyto kroky se liší od různých poskytovatelů služeb.

### <a name="purchasing-an-x509-ca-certificate"></a>Nákup certifikátu webu X.509

Nákup certifikátu certifikační Autority je výhodou act dobře známé kořenové certifikační Autority jako důvěryhodné třetích stran k ručit pro legitimitu zařízení IoT, když se zařízení připojí. Společnost X by tuto možnost zvolte, pokud tito pracovníci Smart-X-Widget pro interakci s produkty třetích stran nebo služby po počátečním připojení ke službě IoT Hub.

Pokud chcete zakoupit certifikátu webu X.509, byste zvolili společnosti X zprostředkovatele kořenové certifikáty služby. Vyhledávání na Internetu pro frázi "Kořenové certifikační Autority" budou poskytovat dobré potenciálních zákazníků.  Kořenové certifikační Autority pokyny pro vytvoření dvojice veřejného/soukromého klíče a jak vygenerovat podepsání certifikátu žádosti (CSR) pro služby společnosti X.  Zástupce je formální proces žádosti o certifikát od certifikační autority.  Výsledek tento nákup je certifikát pro použití jako certifikát autority.  S ohledem na všudypřítomnost technologií certifikáty X.509, bude pravděpodobně byly správně naformátovaná tak jeho IETF RFC 5280 standardní certifikát.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Vytváří se certifikát X.509 podepsaný certifikační Autoritou

Proces vytvoření certifikát X.509 podepsaný certifikační Autoritou se podobá při nákupu s výjimkou zahrnující podepisující třetích stran stejně jako kořenové certifikační autority. V našem příkladu společnosti X podepíše certifikát jeho autority místo kořenové certifikační autority.  Společnost X může tuto možnost zvolte pro testování, dokud nebudou připravené zakoupit certifikát autority. Společnost X také použít certifikát CÚ X.509 podepsaného svým držitelem v produkčním prostředí, pokud Smart-X-widgetu není určený pro připojení ke všem službám třetích stran mimo službu IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Zaregistrovat certifikát X.509 do služby IoT Hub

Společnost X musí zaregistrovat certifikační Autority X.509 do služby IoT Hub, kde bude sloužit k ověřování Smart-X-Widgetů, jak se připojit.  Toto je jednorázový proces, který nabízí možnost ověřovat a spravovat libovolný počet zařízení Smart-X-widgetu.  Tento proces je jednorázový kvůli vztah jeden mnoho mezi certifikační autoritu a zařízení a také se považuje za jednu z hlavních výhod pomocí metody ověřování X.509 certifikační Autority.  Alternativou je nahrát kryptografické otisky jednotlivých certifikátů pro každé zařízení Smart-X-widgetu a přidání do provozních nákladů.

Registrace certifikátu webu X.509 je dvoustupňový proces, nahrání certifikátu a certifikát důkaz vlastnictví.

![img-pop-flow](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Nahrání certifikátu X.509 certifikační Autority

Certifikátu webu X.509 odeslání procesu je přesně to, že nahrajete certifikát certifikační Autority do služby IoT Hub.  IoT Hub očekává, že certifikát v souboru. Společnost X jednoduše nahraje soubor certifikátu. Soubor certifikátu za žádných okolností nesmí obsahovat všem privátním klíčům.  Osvědčené postupy z normy, které upravují infrastruktury veřejných klíčů (PKI) určuje dané znalosti společnosti-bezpodmínečný privátní se nachází v tomto případě výhradně v rámci společnosti X.

### <a name="proof-of-possession-of-the-certificate"></a>Ověření vlastnictví certifikátu

Certifikátu webu X.509, stejně jako žádné digitální certifikáty, je veřejné informace, které jsou náchylné k odposlouchávání.  V důsledku toho může úmysly zachytit certifikát a pokusu o odeslání jako svoje vlastní.  V našem příkladu by chtěli služby IoT Hub Ujistěte se, že certifikát certifikační Autority, který nahrává společnosti X ve skutečnosti patří společnosti X. Učiní tak náročné společnosti-x do prokáže, že certifikát přenést prostřednictvím ve skutečnosti měl [důkaz vlastnictví (PoP) tok](https://tools.ietf.org/html/rfc5280#section-3.1). Důkaz vlastnictví flow zahrnuje služby IoT Hub generuje náhodné číslo podepsání firemní-X pomocí jeho privátní klíč.  Pokud společnosti X a osvědčené postupy infrastruktury veřejných KLÍČŮ a jejich privátní klíč chráněný pak pouze by v pozici, aby správně reagovat na výzvy důkaz vlastnictví.  IoT Hub pokračuje k registraci certifikátu X.509 certifikační Autority po úspěšné odpovědi výzvy důkaz vlastnictví.

Úspěšná odpověď na výzvu důkaz vlastnictví ze služby IoT Hub dokončení registrace X.509 certifikační Autority.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Přihlášení zařízení do řetěz certifikátů

IoT vyžaduje, aby každé zařízení mít jedinečnou identitu.  Tyto identity jsou formuláře certifikáty pro ověřování prostřednictvím certifikátu schémata.  V našem příkladu to znamená, že každý Smart-X-widgetu musí mít jedinečný certifikát.  Jak společnosti X instalační program pro to v jeho dodavatelský řetězec?

Jedním ze způsobů přejít o to je předem vygenerovat certifikáty pro čipové-X-Widgetů a pověření znalost odpovídající privátní klíče jedinečná zařízení s partnery dodavatelského řetězce.  Pro společnosti-X to znamená, že pověření Factory-Y a technik Z.  Když je platnou metodu, dodává s problémy, které musí být překonat zajistit vztahu důvěryhodnosti:

1. Museli sdílet s partnery dodavatelského řetězce, kromě infrastruktury veřejných KLÍČŮ je ignorována privátní klíče zařízení k osvědčeným postupům nikdy sdílení privátní klíče, díky vytváření důvěryhodnosti v dodavatelském řetězci nákladné.  To znamená, že kapitálových systémy, jako je zabezpečené místnosti na dům zařízení privátního klíče a procesy, jako jsou auditů zabezpečení je potřeba nainstalovat.  Přidat i náklady na dodavatelský řetězec.

2. Úkol 1: 1 pro každý pár klíč zařízení z bodu generování zařízení jedinečný certifikát (tedy privátní klíč) k vyřazení zařízení se změní bezpečně monitorování účtů pro zařízení v dodavatelském řetězci a později je správa v nasazení. Správa skupin zařízení to vylučuje Pokud koncept skupin je explicitně integrované do procesu nějakým způsobem. Zabezpečené monitorování účtů a zařízení správy životního cyklu, proto se stane zatížení náročné operace.  V našem příkladu by obsahovat společnosti X tato zatížení.

Ověřování pomocí certifikátu X.509 certifikační Autority nabízí elegantní řešení pro výše uvedené problémy prostřednictvím řetězy certifikátů.  Řetěz certifikátů vyplývá z podpisového zprostředkující certifikační Autorita, která pak podepíše jiné zprostředkující certifikační Autority a tak pokračuje až do konečného zprostředkující certifikační Autority zaregistruje zařízení certifikační Autority.  V našem příkladu podepíše společnosti X Factory-Y, který pak podepíše technika-Z, který nakonec podepíše Smart-X-widgetu.

![img-cert-chain-hierarchy](./media/cert-chain-hierarchy.png)

Nad sebe certifikáty v řetězu uvede logických odevzdání autority.  Dodavatelské mnoho postupujte podle tohoto logického odevzdání kterým získá každý zprostředkující certifikační Autority podepsané do řetězce při příjmu všechny upstreamové certifikáty certifikační Autority, a poslední zprostředkující certifikační Autority a nakonec podepíše každé zařízení a všechny certifikáty autority z řetězce vložení do zařízení. To je běžné, pokud kontrakt výrobní společnost s hierarchií továren provizí konkrétní factory provedete výroby.  Hierarchie může být několik úrovní hluboko (třeba podle zeměpisné oblasti/typu/výrobní řádku produktu), pouze objekt pro vytváření na konci získá komunikovat se zařízením, ale řetězci je spravovaná na nejvyšší úrovni hierarchie.

Náhradní řetězce může mít jinou zprostředkující certifikační Autority interakce se zařízením, ve kterém případ certifikační Autority interakce se zařízením vloží obsah řetěz certifikátu v tomto okamžiku.  Hybridní modely jsou také možné kde jenom pro některé certifikační autority má fyzický interakce se zařízením.

Objekt pro vytváření Y a Z technik pracovat v našem příkladu Smart-X-widgetu.  Zatímco společnosti X vlastní Smart-X-widgetu, je ve skutečnosti nekomunikuje fyzicky s ním v celé dodavatelského řetězce.  Řetěz certifikátů důvěryhodnosti Smart-X-pomůcky proto tvoří společnosti X podepisování Factory-Y, který pak podepíše technika-Z, který pak bude poskytovat konečné podpis Smart-X-widgetu. Výroba a instalace Smart-X-widgetu tvoří Factory-Y a technik-pomocí jejich odpovídajících zprostředkujících certifikátů CA pro přihlášení každého Smart-X-pomůcky. Konečný výsledek celý tento proces je Smart-X-pomůcky jedinečná zařízení certifikáty a řetěz certifikátů má certifikát certifikační Autority společnosti X.

![img-cert-mfr-chain](./media/cert-mfr-chain.png)

To je dobrý bod zkontrolovat hodnotu metody X.509 certifikační Autority.  Místo generování předem a předání certifikáty pro každou Smart-X-widgetu do dodavatelský řetězec, společnost X platili jen jednou přihlásit Factory-Y.  Namísto toho, aby ke sledování každého zařízení v průběhu životního cyklu zařízení, může společnost X nyní sledovat a spravovat zařízení prostřednictvím skupiny, které přirozeně základě procesů dodavatelských řetězců, například zařízení nainstaloval Z technik po dne roku některé.

Poslední, ale ne nejméně infuses certifikační Autority metody ověřování zabezpečené zodpovědnost do výroby dodavatelský řetězec zařízení. Z důvodu procesu řetěz certifikátu je akcí pro každého člena v řetězci kryptograficky zaznamenané a ověřitelné.

Tento proces se opírá o určité předpoklady, které musí být prezentované pro úplnost.  Vyžaduje nezávislé vytvoření zařízení jedinečné veřejného/soukromého klíče dvojice a že se privátní klíč chráněný v rámci zařízení.  Naštěstí zabezpečené silicon čipy v podobě z zabezpečených modulů HSM (Hardware) dokáže interně generování klíčů a ochranu privátních klíčů existovat.  Společnost X stačí pro přidání jednoho z těchto čipy do kusovník komponenty Smart-X-ovládacího prvku.

## <a name="device-connection"></a>Připojení zařízení

Předchozí části výše mít bylo vytváření připojení zařízení.  Když si zaregistrujete jednoduše certifikátu webu X.509 do služby IoT Hub jedenkrát, jak potenciálně miliony zařízení a získat se ověřil z první?  Jednoduché. prostřednictvím stejné nahrání certifikátu a důkaz vlastnictví toku dříve došlo s registrací certifikátu webu X.509.

Zařízení od pro ověření X.509 certifikační Autority jsou vybavené jedinečných certifikátů zařízení a řetěz certifikátů z jejich odpovídajících výrobní dodavatelského řetězce.  Připojení zařízení i pro úplně první čas, dojde ve dvou krocích: certifikát řetězu nahrávání a důkaz vlastnictví.

Při nahrávání řetěz certifikátů zařízení odešle svůj jedinečný certifikát zařízení společně s řetěz certifikátů, které jsou nainstalované v něm do služby IoT Hub.  Pomocí předem zaregistrovaných certifikát X.509 certifikační Autority, můžete službu IoT Hub kryptograficky ověřit pár věcí, řetěz certifikátů nahraných je interně konzistentní a že řetězu pocházelo vlastníkem platný certifikát certifikační Autority X.509.  Právě se s procesem registrace X.509 certifikační Autority, IoT Hub by zahájení procesu důkaz vlastnictví – výzva odezva pro ověření, který řetězci a proto zařízení certifikát ve skutečnosti patří do zařízení, pak ho nahrát.  Dělá to tak, že generování náhodného složité být podepsány zařízení pomocí jeho privátní klíč pro ověření ve službě IoT Hub.   Úspěšná odpověď aktivuje IoT Hub a přijmout zařízení jako platná a udělit mu připojení.

V našem příkladu by každý Smart-X-widgetu nahrát svůj jedinečný certifikát zařízení společně s certifikáty Y objekt pro vytváření a X.509 technik Z certifikační Autority a pak odpověď na výzvu důkaz vlastnictví ze služby IoT Hub.

![img-device-pop-flow](./media/device-pop-flow.png)

Všimněte si, že základ pro vztah důvěryhodnosti spočívá v ochranu privátních klíčů, včetně privátního klíče zařízení.  Proto nelze kladen dostatek důležitost zabezpečené silicon čipy v podobě sady zabezpečených modulů HSM (Hardware) pro ochranu privátních klíčů zařízení a celkově osvědčený postup nikdy sdílení všem privátním klíčům, jako je jeden objekt pro vytváření pověření jiný jeho privátní klíč.

