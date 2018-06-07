---
title: Koncepty zabezpečení Azure IoT Hub X.509 | Microsoft Docs
description: Koncept - pochopení hodnota X.509 certificate autority certifikáty ve výrobním zařízení IoT a ověřování.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 1f7a02f66a8d87f33d7bac9068628dbd29e5bd7c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635691"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Koncepční přehled o certifikáty X.509 certifikační Autority v odvětví IoT

Tento článek popisuje hodnotu pomocí certifikátu X.509 – certifikáty certifikační autority v zařízení výrobní IoT a ověřování do služby IoT Hub.  Obsahuje informace o napájení řetězu instalační program a zvýraznit výhody.

Tento článek popisuje:

* Jaké jsou certifikáty X.509 certifikační Autority a jak získat je
* Postup registrace svůj certifikát X.509 certifikační Autority do služby IoT Hub
* Jak nastavit výrobní dodavatelský řetězec pro ověřování založené na certifikační Autority X.509
* Jak zařízení, které jsou podepsané certifikační Autority X.509 připojit ke službě IoT Hub

## <a name="overview"></a>Přehled

Ověřování certifikační autoritou (CA) X.509 je přístup pro ověřování zařízení do služby IoT Hub pomocí metody, která výrazně zjednodušuje správu vytváření a životního cyklu identitu zařízení v zásobovací řetězec.

Jedinečný atribut ověřování X.509 certifikační Autority je vztah jeden mnoho, který obsahuje certifikát Certifikační autority s jeho podřízené zařízení.  Tento vztah umožňuje registraci libovolný počet zařízení do služby IoT Hub tak, že zaregistrujete certifikát certifikační Autority X.509 jednou, v opačném případě jedinečných certifikátů zařízení musí být předem zaregistrovaných pro každé zařízení, než se zařízení může připojit. Tento vztah jeden mnoho také zjednodušuje operace správy životního cyklu certifikátů zařízení.

Další důležité atribut ověřování X.509 certifikační Autority je zjednodušení logistiky řetězu napájení.  Zabezpečené ověřování zařízení vyžaduje, aby každé zařízení obsahuje jedinečný tajný klíč jako klíč jako základ pro vztah důvěryhodnosti. V ověřování pomocí certifikátů je tento tajný klíč soukromý klíč. Typické zařízení výrobní tok zahrnuje několik kroků a správce klíče.  Správa zařízení privátní klíče napříč více správce klíče a udržování vztahu důvěryhodnosti je bezpečně složitá a nákladná.  Pomocí certifikačních autorit řeší tento problém tím podepisování každý správce do řetěz kryptografického než pověření je s privátní klíče zařízení.  Každý správce zase podepisuje zařízení v jejich odpovídajících proces kroku výrobní toku.  Celkový výsledek je optimální zásobovací řetězec s integrovanou odpovědnosti za použití kryptografického řetěz certifikátů.  Je vhodné poznamenat, že tento proces poskytuje nejvyšší zabezpečení při ochraně zařízení jejich jedinečný privátní klíče.  K tomuto elementu end doporučujeme použít z zabezpečení moduly HSM (Hardware) podporující interně generování privátním klíčům, které nikdy zobrazit světlým den.

Tento článek nabízí pomocí ověřování X.509 certifikační Autority, z instalačního programu, zadejte řetězec připojení zařízení začátku do konce zobrazení při provádění použití v praxi příkladu přesvědčit zúčastněné pochopení.

## <a name="introduction"></a>Úvod

Certifikát X.509 certifikační Autority je digitální certifikát, jehož vlastníka můžete podepisování jiných certifikátů.  Tento digitální certifikát totiž vyhovuje formátování standard předepsané standard na IETF RFC 5280 certifikát X.509 a je certifikační autorita (CA), protože jeho držitel můžete podepisování jiných certifikátů.

Použití certifikační autority X.509 odhalíte nejlépe ve vztahu k konkrétní příklady.  Vezměte v úvahu společnosti-X, maker čipové-X-widgetů určené pro odborníky v oblasti instalace. Společnost X outsources výrobní a instalaci.  Smlouvy o výrobce Factory-Y výroba čipové-X-pomůcky a poskytovatele služeb technika Z instalace. Společnost X chce že čipové-X-pomůcky přímo se dodává z objektu pro vytváření-Y až technika Z pro instalaci a že připojuje přímo k společnosti-značkou instanci služby IoT Hub po instalaci bez dalšího zásahu od společnosti X. To lze provést, společnosti X muset dokončit několik operací jednorázové instalace primárního čipové-X-pomůcky pro automatické připojení.  Scénář začátku do konce pamatovat zbývající části tohoto článku je strukturu:

* Získání certifikátu X.509 certifikační Autority

* Registrovat certifikát X.509 certifikační Autority do služby IoT Hub

* Přihlašování zařízení do řetěz certifikátů

* Připojení zařízení

## <a name="acquire-the-x509-ca-certificate"></a>Získání certifikátu X.509 certifikační Autority

Společnost X má povolenou možnost zakoupení certifikát X.509 certifikační Autority od certifikační autority veřejnou kořenovou nebo vytvořit procesem podepsaný svým držitelem.  Jednou z možností by optimální z nich, v závislosti na scénáři aplikace.  Bez ohledu na to možnost proces zahrnuje dva základní kroky, generování páru veřejného a privátního klíče RSA a přihlášení veřejný klíč k certifikátu.

![IMG. csr toku](./media/csr-flow.png)

Podrobnosti o tom, jak provést tyto kroky se liší od různých zprostředkovatelů služby.

### <a name="purchasing-an-x509-ca-certificate"></a>Zakoupení certifikát X.509 certifikační Autority.

Zakoupení certifikátu certifikační Autority výhodou je, že act dobře známé kořenové certifikační Autority jako důvěryhodné třetích stran k ručit za legitimitu zařízení IoT, když se zařízení připojí. Společnost X by tuto možnost zvolte, pokud se chcete čipové-X-pomůcky pro interakci s produkty třetích stran nebo služby po počátečním připojení ke službě IoT Hub.

Chcete-li zakoupit certifikát X.509 certifikační Autority, by společnosti X zvolte poskytovatele služeb kořenové certifikáty. Hledání v Internetu pro frázi "kořenové certifikační Autority, předá dobrý zájemce.  Kořenové certifikační Autority na tom, jak vytvořit pár veřejného a privátního klíče a jak vygenerovat žádost (Podepsání certifikátu) pro své služby průvodce společnosti X.  Zástupce oddělení služeb je proces formální použití certifikátu od certifikační autority.  Výsledek tento nákup se certifikát pro použití jako certifikát autoritu.  S ohledem na všudypřítomnost technologií certifikáty X.509, bude pravděpodobně mít byla správně naformátován a umožňuje společnosti IETF RFC 5280 standardní certifikátu.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Vytvořit certifikát podepsaný svým držitelem X.509 Certifikační autority

Proces vytvoření certifikát podepsaný svým držitelem X.509 Certifikační autority je podobná nákupu s výjimkou zahrnující podepisující osoba třetí strany jako kořenové certifikační autority. V našem příkladu společnosti X podepíše certifikát jeho autority místo z kořenové certifikační autority.  Tuto možnost pro testování, dokud se připraveni k nákupu certifikát autority může zvolte společnosti X. Společnost X také používat certifikát podepsaný svým držitelem X.509 Certifikační autority v produkčním prostředí, pokud čipové-X-pomůcky není určen pro připojení k žádné služby třetích stran mimo službu IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrovat certifikát X.509 do služby IoT Hub

Společnost X je potřeba zaregistrovat X.509 certifikační Autority do služby IoT Hub, kde bude sloužit k ověření čipové-X-pomůcky, protože se připojují.  To je jednorázový proces, který umožňuje možnost ověření a spravovat libovolný počet zařízení čipové-X-pomůcky.  Tento proces je jednorázové kvůli vztah jeden mnoho mezi autority a zařízení a také představuje jeden z hlavních výhod pomocí metody ověřování X.509 certifikační Autority.  Alternativou je nahrát kryptografické otisky certifikátů jednotlivých pro každé zařízení čipové-X-pomůcky a přidání do provozních nákladů.

Registrace certifikátu X.509 certifikační Autority je dvoustupňový proces, nahrávání certifikátu a certifikát ověření z – u sebe.

![img-pop-flow](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Nahrávání certifikátu X.509 certifikační Autority

Certifikát certifikační Autority X.509 nahrát proces je, že právě, odešlete certifikát certifikační Autority do služby IoT Hub.  IoT Hub očekává, že certifikát v souboru. Společnost X jednoduše odešle soubor certifikátu. Soubor certifikátu za žádných okolností nesmí obsahovat všem privátním klíčům.  Doporučené postupy vyplývající ze standardů pro řízení infrastruktury veřejných klíčů (PKI) vyžaduje dané znalosti společnosti-značkou privátní se nachází v tomto případě výhradně v rámci společnosti X.

### <a name="proof-of-possession-of-the-certificate"></a>Ověření vlastnictví certifikátu

Certifikát certifikační Autority X.509, stejně jako žádné digitální certifikáty, je veřejné informace, které je ohrožena útoky založenými na odposlechu.  Jako takový úmysly může zachytávat certifikát a odešlete ji jako vlastní.  V našem příkladu by chtěl IoT Hub Ujistěte se, že certifikát certifikační Autority, který odesílá společnosti X skutečně patří společnosti X. Dělá tak náročné společnosti x k prokáže, že certifikát přenést prostřednictvím ve skutečnosti měl [tok ověření z vlastnictví (PoP)](https://tools.ietf.org/html/rfc5280#section-3.1). Tok ověření vlastnictví zahrnuje IoT Hub generování náhodné číslo podepsání společnosti-X pomocí jeho privátní klíč.  Pokud společnosti X a osvědčené postupy infrastruktury veřejných KLÍČŮ a jejich privátní klíč chráněný pak pouze se bude v pozici správně reagovat na výzvy důkaz vlastnictví.  IoT Hub pokračuje pro zaregistrování certifikátu X.509 CA při úspěšná odpověď výzvy důkaz vlastnictví.

Úspěšná odpověď na výzvu ověření vlastnictví ze služby IoT Hub dokončení registrace X.509 certifikační Autority.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Přihlašování zařízení do řetěz certifikátů

IoT vyžaduje každé zařízení měl jedinečnou identitu.  Tyto identity jsou ve tvaru certifikáty pro ověřování pomocí certifikátů schémata.  V našem příkladu to znamená, že každý čipové-X-pomůcky musí mít certifikát jedinečný zařízení.  Jak společnosti X instalační program pro tuto ve svém řetězu napájení?

Jedním ze způsobů přejděte o to je k předběžnému generování certifikáty pro čipové-X-pomůcek a pověření znalosti odpovídající privátní klíče jedinečné zařízení s partnery řetězu napájení.  Ve společnosti X to znamená, že pověření Y objektu pro vytváření a technika Z.  I když jde o platnou metodou, součástí výzvy, které musí předcházet zajistit vztahu důvěryhodnosti, následujícím způsobem:

1. Museli sdílet s partnery zadejte řetězec, kromě ignoruje infrastruktury veřejných KLÍČŮ privátní klíče zařízení osvědčených postupů nikdy sdílení privátních klíčů, díky vytváření vztah důvěryhodnosti v nákladné zásobovací řetězec.  Znamená to, kapitálové systémy jako zabezpečené místnostmi úklidové zařízení privátní klíče a procesy, jako jsou audity pravidelné zabezpečení je potřeba nainstalovat.  Jak přidat náklady zásobovací řetězec.

2. Úloha 1: 1 pro každou dvojici klíč zařízení z bodu generace (proto privátní klíč) jedinečný certifikát zařízení k vyřazení zařízení se změní na bezpečně monitorování účtů pro zařízení v zásobovací řetězec a později je správa v nasazení. To vylučuje správu skupiny zařízení, pokud koncept skupin je explicitně integrované do procesu nějakým způsobem. Zabezpečené monitorování účtů a zařízení správy životního cyklu, tedy stane operations velkou zátěž.  V našem příkladu by společnosti X berte tato zatížení.

Ověřování pomocí certifikátu X.509 certifikační Autority nabízí elegantní řešení pro výše uvedené problémy prostřednictvím řetězů certifikátů.  Řetěz certifikátů výsledkem podpisového zprostředkující certifikační Autority, který naopak podepíše jinou zprostředkující certifikační Autority a tak přejde, dokud konečné zprostředkující certifikační Autority přihlásí zařízení certifikační Autority.  V našem příkladu podepisuje společnosti X Factory-Y, které následně podepíše technika-Z, která podepisuje nakonec čipové-X-pomůcky.

![img cert řetězu hierarchie](./media/cert-chain-hierarchy.png)

Výše cascade certifikát v řetězu uvede logické ruční – vypnuté autority.  Mnoho řetězců postupujte podle této logické ruční – vypnuté kterým každý zprostředkující certifikační Autority získá přihlášeni řetězu při přijímání všech nadřazeného certifikáty certifikační Autority a poslední zprostředkující certifikační Autority nakonec podepisuje každé zařízení a vložit všechny certifikáty autority z řetězu do zařízení. To je běžné, když kontrakt výrobní společnost s hierarchií objektů Factory zadává konkrétní objekt factory uděláte výrobní.  Při hierarchie může být několik úrovní hloubky (například typ nebo výrobní řádku geography nebo produktu), pouze objekt factory na konci získá k interakci se zařízením, ale řetězu zachovaný od nejvyšší úrovně v hierarchii.

Alternativní řetězy může mít různé zprostředkující certifikační Autority práce se zařízením, ve kterém případ certifikační Autority interakci s zařízení vloží obsahu řetěz certifikátu v tomto bodě.  Hybridní modely je také možné, kde jen některé certifikační autority má fyzické interakci se zařízením.

V našem příkladu Y objektu pro vytváření a technika Z komunikovat s čipovou-X-pomůcky.  Při společnosti X vlastní čipové-X-pomůcky, je ve skutečnosti není fyzicky s ní pracovat v celé zásobovací řetězec.  Řetěz certifikátů důvěryhodnosti čipové-X-pomůcky proto tvoří společnosti X podepisování Factory-Y, které následně podepíše technika-Z, který pak bude poskytovat konečné podpis čipové-X-pomůcky. Výrobce a instalaci čipové-X-pomůcky tvoří objekt pro vytváření-Y a technika-Z pomocí jejich odpovídajících certifikátů zprostředkující certifikační Autority k podepsání každé čipové-X-pomůcky. Konečný výsledek tohoto celý proces je čipové-X-pomůcky s certifikáty jedinečný zařízení a řetěz certifikátů vztahu důvěryhodnosti, má certifikát certifikační Autority společnosti X.

![img cert dílu výrobce řetězce](./media/cert-mfr-chain.png)

Toto je vhodný bod chcete-li zkontrolovat hodnotu metody X.509 certifikační Autority.  Místo předem generování a předání certifikáty každých čipové-X-pomůcky do zásobovací řetězec, ruční společnosti X pouze jednou podepsat Factory-Y.  Místo nutnosti sledovat každé zařízení v průběhu životního cyklu zařízení, nemusí společnosti X sledovat a spravovat zařízení prostřednictvím skupiny, které přirozeně dostáváme proces zadejte řetězec, například zařízení nainstalovat pomocí technik Z po července některé roku.

Poslední ale zase infuses certifikační Autority metoda ověřování zabezpečení odpovědnosti za do zařízení výrobní zásobovací řetězec. Z důvodu procesu řetěz certifikátu je akce všech členů v řetězu kryptograficky zaznamenaná a ověřitelné.

Tento proces závisí na určité předpoklady, které musí být prezentované pro úplnost.  Vyžaduje nezávislé vytvoření zařízení jedinečné veřejného a privátního klíče RSA a že se privátní klíč chráněný v rámci zařízení.  Naštěstí čipy zabezpečení silicon ve tvaru z zabezpečené moduly HSM (Hardware) umožňuje interně generování klíčů a ochranu privátních klíčů neexistuje.  Společnost X stačit přidat jenom jednu z těchto čipy do kusovník součást čipové-X-ovládacího prvku.

## <a name="device-connection"></a>Připojení zařízení

Předchozí části výše mít byla vytváření připojení zařízení.  Jednoduše registrací certifikát X.509 certifikační Autority do služby IoT Hub jeden času, jak potenciálně miliony zařízení připojit a získat ověření od prvního?  Jednoduché; prostřednictvím stejné nahrávání certifikátu a ověření vlastnictví toku dříve došlo s registrace certifikátu X.509 certifikační Autority.

Zařízení vyrobila pro ověřování X.509 certifikační Autority jsou vybaveny zařízení jedinečných certifikátů a řetěz certifikátů z jejich odpovídajících výrobní zásobovací řetězec.  Připojení zařízení, i pro úplně první čas probíhá ve dvou krocích: certifikát řetězu odeslání a ověření u sebe.

Zařízení během nahrávání řetěz certifikátů, odešle svůj jedinečný certifikát zařízení společně s řetěz certifikátů, které jsou v něm nainstalován do služby IoT Hub.  Pomocí předem zaregistrovaných certifikátu X.509 certifikační Autority, můžete Centrum IoT kryptograficky ověřit několik věcí, že řetězu odeslaný certifikát je interně konzistentní a že řetězu byla vytvořena vlastníkem platný certifikát certifikační Autority X.509.  Právě byl se proces registrace X.509 certifikační Autority, IoT Hub by zahájit proces ověření vlastnictví výzvy a odezvy že řetězu a proto zařízení certifikát ve skutečnosti patří do zařízení se nahrávání.  Dělá to tak vygenerováním náhodných výzvy podepsání zařízení pomocí jeho privátní klíč pro ověření službou IoT Hub.   Úspěšná odpověď aktivuje IoT Hub přijmout zařízení jako platná a udělit mu připojení.

V našem příkladu by každý čipové-X-pomůcky nahrát svůj jedinečný certifikát zařízení společně s certifikáty Y objektu pro vytváření a X.509 technika Z certifikační Autority a pak odpověď na výzvu ověření vlastnictví ze služby IoT Hub.

![img zařízení pop toku](./media/device-pop-flow.png)

Všimněte si, že základ pro vztah důvěryhodnosti postavená na ochraně privátních klíčů, včetně privátního klíče zařízení.  Proto nelze kladen dostatek význam zabezpečené silicon čipy v formuláře z zabezpečené moduly HSM (Hardware) pro ochranu privátního klíče zařízení a celkové osvědčený postup, nikdy sdílení všem privátním klíčům, jako jeden objekt pro vytváření pověření jiný jeho privátní klíč.

