---
title: Azure IoT Hub vysoká dostupnost a zotavení po havárii | Dokumenty společnosti Microsoft
description: Popisuje funkce Azure a IoT Hub, které vám pomůžou vytvářet vysoce dostupná řešení Azure IoT s možnostmi zotavení po havárii.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 615dc1b7bd1a31069a542ebb7ea44693c404cb40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499112"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Vysoká dostupnost služby IoT Hub a zotavení po havárii

Jako první krok k implementaci odolného řešení IoT musí architekti, vývojáři a vlastníci firem definovat cíle provozukapacit pro řešení, která budují. Tyto cíle lze definovat především na základě konkrétních obchodních cílů pro každý scénář. V tomto kontextu článek [Azure Kontinuita kontinuity technické pokyny](https://docs.microsoft.com/azure/architecture/resiliency/) popisuje obecný rámec, který vám pomůže přemýšlet o kontinuitě podnikání a zotavení po havárii. Dokument [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) poskytuje pokyny k architektuře strategií pro aplikace Azure k dosažení vysoké dostupnosti (HA) a zotavení po havárii (DR).

Tento článek popisuje funkce HA a ZOTAVENÍ po havárii nabízené konkrétně službou IoT Hub. Široké oblasti uvedené v tomto článku jsou:

- Ha v rámci regionu
- Mezioblastdra DR
- Dosažení cross region HA

V závislosti na cílech provozu, které definujete pro vaše řešení IoT, byste měli určit, které z níže uvedených možností nejlépe vyhovují vašim obchodním cílům. Začlenění některé z těchto alternativ HA/DR do vašeho řešení IoT vyžaduje pečlivé vyhodnocení kompromisů mezi:

- Úroveň odolnosti, kterou požadujete 
- Složitost implementace a údržby
- Dopad COGS

## <a name="intra-region-ha"></a>Ha v rámci regionu

Služba IoT Hub poskytuje ha v rámci oblasti implementací redundance téměř ve všech vrstvách služby. Smlouvy [O sla publikované službou IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) je dosaženo využitím těchto redundance. Vývojáři řešení IoT nevyžadují žádnou další práci, aby využili výhod těchto funkcí HA. I když IoT Hub nabízí přiměřeně vysokou záruku dostupnosti, přechodná selhání lze stále očekávat jako u jakékoli distribuované výpočetní platformy. Pokud teprve začínáte s migrací řešení do cloudu z místního řešení, vaše zaměření se musí přesunout od optimalizace "střední doba mezi selháními" na "střední dobu obnovení". Jinými slovy, přechodné selhání je třeba považovat za normální při práci s cloudem v mixu. Vhodné [zásady opakování](iot-hub-reliability-features-in-sdks.md) musí být integrovány do komponent, které interagují s cloudovou aplikací, aby bylo možné řešit přechodné chyby.

> [!NOTE]
> Některé služby Azure také poskytují další úrovně dostupnosti v rámci oblasti integrací s [zónami dostupnosti (AZs)](../availability-zones/az-overview.md). AZ s aktuálně nejsou podporovány službou IoT Hub.

## <a name="cross-region-dr"></a>Mezioblastdra DR

Může nastat některé vzácné situace, kdy datové centrum dojde k rozšířené výpadky z důvodu výpadků napájení nebo jiných selhání zahrnující fyzické prostředky. Takové události jsou vzácné, během nichž intra region HA schopnosti popsané výše nemusí vždy pomoci. IoT Hub poskytuje několik řešení pro obnovení z těchto rozšířených výpadků. 

Možnosti obnovení, které jsou k dispozici zákazníkům v takové situaci, jsou [převzetí služeb při selhání iniciované společností Microsoft](#microsoft-initiated-failover) a ruční převzetí služeb při [selhání](#manual-failover). Zásadní rozdíl mezi těmito dvěma je, že Microsoft iniciuje bývalý a uživatel iniciuje druhé. Ruční převzetí služeb při selhání také poskytuje nižší cíl doby obnovení (RTO) ve srovnání s možností převzetí služeb při selhání iniciované společností Microsoft. Konkrétní RTO nabízené s každou možností jsou popsány v následujících částech. Pokud se použije některá z těchto možností převzetí služeb při selhání služby IoT hub z primární oblasti, centrum se stane plně funkční v odpovídající [geograficky spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Obě tyto možnosti převzetí služeb při selhání nabízejí následující cíle bodu obnovení (RPO):

| Datový typ | Cíle bodu obnovení (RPO) |
| --- | --- |
| Registr identit |0-5 minut ztráta dat |
| Data dvojčete zařízení |0-5 minut ztráta dat |
| Zprávy z cloudu na zařízení<sup>1</sup> |0-5 minut ztráta dat |
| Nadřazené<sup>úlohy 1</sup> a zařízení |0-5 minut ztráta dat |
| Zprávy typu zařízení-cloud |Všechny nepřečtené zprávy jsou ztraceny |
| Zprávy o monitorování operací |Všechny nepřečtené zprávy jsou ztraceny |
| Zprávy o zpětné vazbě z cloudu na zařízení |Všechny nepřečtené zprávy jsou ztraceny |

<sup>1.</sup> Zprávy cloud-zařízení a nadřazené úlohy se neobnoví jako součást ručního převzetí služeb při selhání.

Po dokončení operace převzetí služeb při selhání pro centrum IoT hub se očekává, že všechny operace ze zařízení a back-endové aplikace budou pokračovat v práci bez nutnosti ručního zásahu. To znamená, že vaše zprávy mezi zařízeními a cloudem by měly nadále fungovat a celý registr zařízení je neporušený. Události vyzařované prostřednictvím event gridu mohou být spotřebovány prostřednictvím stejného předplatného nakonfigurovaného dříve, pokud jsou tato odběry služby Event Grid stále k dispozici.

> [!CAUTION]
> - Název a koncový bod integrovaného bodu událostí služby IoT Hub kompatibilní s centrem událostí a nakonfigurované skupiny spotřebitelů jsou odebrány (jedná se o chybu, která bude opravena před květnem 2020). Při příjmu telemetrických zpráv z integrovaného koncového bodu pomocí klienta Event Hub nebo hostitele procesoru událostí byste měli k navázání připojení [použít připojovací řetězec služby IoT hub.](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) Tím je zajištěno, že vaše back-endové aplikace budou i nadále fungovat bez nutnosti ručního zásahu po převzetí služeb při selhání. Pokud používáte název a koncový bod kompatibilní s centrem událostí přímo ve vaší aplikaci, budete muset [překonfigurovat skupinu spotřebitelů, kterou používají, a načíst nový koncový bod kompatibilní s centrem událostí](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) po převzetí služeb při selhání, abyste mohli pokračovat v operacích. Pokud k připojení integrovaného koncového bodu používáte Azure Functions nebo Azure Stream Analytics, možná budete muset provést **restartování**.
>
> - Při směrování do úložiště doporučujeme vyjmenovávat objekty BLOB nebo soubory a pak je iterace, aby se zajistilo, že se budou číst všechny objekty BLOB nebo soubory bez jakýchkoli předpokladů o oddílu. Rozsah oddílů může potenciálně změnit během převzetí služeb při selhání iniciované společností Microsoft nebo ruční převzetí služeb při selhání. Pomocí rozhraní API objektů [BLOB seznamu](https://docs.microsoft.com/rest/api/storageservices/list-blobs) můžete vytvořit výčet seznamu objektů BLOB nebo [seznamu rozhraní API ADLS Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) pro seznam souborů. 

## <a name="microsoft-initiated-failover"></a>Převzetí služeb při selhání iniciované společností Microsoft

Převzetí služeb při selhání iniciované společností Microsoft je vykonáváno společností Microsoft ve výjimečných situacích k převzetí služeb při selhání všech center IoT z postižené oblasti do odpovídající geograficky spárované oblasti. Tento proces je výchozí možnost (žádný způsob, jak pro uživatele odhlásit) a nevyžaduje žádný zásah od uživatele. Společnost Microsoft si vyhrazuje právo určit, kdy bude tato možnost uplatněna. Tento mechanismus nezahrnuje souhlas uživatele před převzetím služeb při selhání centra uživatele. Převzetí služeb při selhání iniciované společností Microsoft má cíl čas obnovení (RTO) 2-26 hodin. 

Velké RTO je, protože společnost Microsoft musí provést operaci převzetí služeb při selhání jménem všech dotčených zákazníků v této oblasti. Pokud používáte méně kritické řešení IoT, které může udržet prostoje zhruba jeden den, je v pořádku, abyste se závislosti na této možnosti k uspokojení celkových cílů zotavení po havárii pro vaše řešení IoT. Celková doba pro operace za běhu, které mají být plně funkční, jakmile je tento proces spuštěn, je popsán v části "Čas obnovení".

## <a name="manual-failover"></a>Ruční převzetí služeb při selhání

Pokud vaše obchodní uptime cíle nejsou splněny RTO, které microsoft inicioval převzetí služeb při selhání poskytuje, zvažte použití ruční převzetí služeb při selhání ke spuštění procesu převzetí služeb při selhání sami. RTO pomocí této možnosti může být kdekoli mezi 10 minut až pár hodin. RTO je aktuálně funkce počtu zařízení registrovaných proti instanci služby IoT hub převzetí služeb při selhání. Můžete očekávat, že RTO pro rozbočovač hostující přibližně 100 000 zařízení bude v ballparku 15 minut. Celková doba pro operace za běhu, které mají být plně funkční, jakmile je tento proces spuštěn, je popsán v části "Čas obnovení".

Možnost ručnípřevzetí služeb při selhání je vždy k dispozici pro použití bez ohledu na to, zda primární oblast dochází k prostojů nebo ne. Proto by tato možnost mohla být potenciálně použita k provedení plánovaných převzetí služeb při selhání. Jedním z příkladů použití plánovaných převzetí služeb při selhání je provádět pravidelné překračovat převzetí služeb při selhání. Upozornění však je, že plánovaná operace převzetí služeb při selhání má za následek prostoje pro rozbočovač e-ti pro období definované RTO pro tuto možnost a také vede ke ztrátě dat, jak je definováno v tabulce RPO výše. Můžete zvážit nastavení instance testovacího centra IoT, která by pravidelně uplatňovala plánovanou možnost převzetí služeb při selhání, abyste získali důvěru ve vaši schopnost zprovoznit komplexní řešení, když dojde ke skutečné havárii.

Podrobné pokyny najdete v [tématu Výuka: Provedení ručního převzetí služeb při selhání pro centrum IoT hub](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Zkušební vrtáky pro běh

Testovací cvičení by se neměla provádět na rozbočovačích IoT, které se používají ve vašem produkčním prostředí.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Nepoužívejte ruční převzetí služeb při selhání k migraci centra IoT hub do jiné oblasti

Ruční převzetí služeb při selhání by *neměl* být používán jako mechanismus pro trvalou migraci rozbočovače mezi geograficky spárované oblasti Azure. Tím se zvyšuje latence pro operace prováděné proti služby IoT hub ze zařízení doma ve staré primární oblasti.

## <a name="failback"></a>Navrácení služeb po obnovení

Při selhání zpět do staré primární oblasti lze dosáhnout aktivací akce převzetí služeb při selhání jindy. Pokud byla provedena původní operace převzetí služeb při selhání k obnovení z rozšířeného výpadku v původní primární oblasti, doporučujeme, aby rozbočovač se nezdařilo zpět do původního umístění, jakmile toto umístění se zotavil z výpadku situace.

> [!IMPORTANT]
> - Uživatelé mohou provádět pouze 2 úspěšné operace převzetí služeb při selhání a 2 úspěšné operace navrácení služeb po selhání za den.
>
> - Operace převzetí služeb při selhání zpět nebo na vrácení služeb při selhání nejsou povoleny. Mezi těmito operacemi musíte počkat 1 hodinu.

## <a name="time-to-recover"></a>Čas na zotavení

Zatímco fQDN (a tedy připojovací řetězec) instance centra IoT hub zůstává stejné post převzetí služeb při selhání, základní IP adresa se změní. Proto celkový čas pro operace runtime prováděné proti instanci služby IoT hub, aby se plně funkční po aktivaci procesu převzetí služeb při selhání lze vyjádřit pomocí následující funkce.

Doba obnovení = RTO [10 min - 2 hodiny pro ruční převzetí služeb při selhání | 2 - 26 hodin pro převzetí služeb při selhání iniciované společností Microsoft] + zpoždění šíření DNS + Čas, který klientská aplikace převzala k aktualizaci jakékoli IP adresy ioT hubu uložené v mezipaměti.

> [!IMPORTANT]
> Sady IoT SDK neuváděte ip adresu centra IoT. Doporučujeme, aby uživatelské kód propojení s sadami SDK není do mezipaměti IP adresu centra IoT hub.

## <a name="achieve-cross-region-ha"></a>Dosáhnout cross region HA

Pokud vaše obchodní uptime cíle nejsou splněny RTO, které buď Microsoft iniciované převzetí služeb při selhání nebo ruční převzetí služeb při selhání nabízí, měli byste zvážit implementaci mechanismu s podporou převzetí služeb při selhání pro jedno zařízení automatické mezi oblastmi.
Kompletní zpracování topologií nasazení v řešeních IoT je mimo rozsah tohoto článku. Článek popisuje regionální model nasazení *převzetí služeb při selhání* pro účely vysoké dostupnosti a zotavení po havárii.

V modelu místní převzetí služeb při selhání back-end řešení běží především v jednom umístění datového centra. Sekundární centrum IoT a back-end se nasazují v jiném umístění datového centra. Pokud centrum IoT v primární oblasti trpí výpadkem nebo dojde k přerušení síťového připojení ze zařízení do primární oblasti, zařízení používají sekundární koncový bod služby. Dostupnost řešení můžete zlepšit implementací modelu převzetí služeb při selhání mezi oblastmi namísto toho, abyste zůstali v rámci jedné oblasti. 

Na vysoké úrovni, k implementaci modelu převzetí služeb při selhání místní pomocí ioT hubu, je třeba provést následující kroky:

* **Sekundární služba IoT hub a logika směrování zařízení**: Pokud je služba v primární oblasti přerušena, musí se zařízení začít připojovat k sekundární oblasti. Vzhledem k tomu, že většina souvisejících služeb je uvědomělá stavem, je běžné, že správci řešení aktivují proces převzetí služeb při selhání mezi oblastmi. Nejlepší způsob, jak komunikovat nový koncový bod do zařízení při zachování kontroly nad procesem, je mít je pravidelně kontrolovat *službu concierge* pro aktuální aktivní koncový bod. Služba concierge může být webová aplikace, která je replikována a udržována dostupná pomocí technik přesměrování DNS (například pomocí [Azure Traffic Manageru).](../traffic-manager/traffic-manager-overview.md)

   > [!NOTE]
   > Služba IoT hub není ve Službě Azure Traffic Manager u podporovaného typu koncového bodu. Doporučujeme integrovat navrhovanou službu concierge se Správcem provozu Azure tím, že implementuje rozhraní API pro sondu koncového bodu.

* **Replikace registru identit**: Aby bylo možné být použitelné, sekundární centrum IoT musí obsahovat všechny identity zařízení, které se mohou připojit k řešení. Řešení by mělo zachovat geograficky replikované zálohy identit zařízení a nahrát je do sekundárního centra IoT hub před přepnutím aktivního koncového bodu pro zařízení. V tomto kontextu je užitečná funkce exportu identity zařízení služby IoT Hub. Další informace naleznete v [průvodci vývojáři služby IoT Hub – registrem identit](iot-hub-devguide-identity-registry.md).

* **Slučování logiky**: Když primární oblast bude znovu k dispozici, musí být všechny stavy a data, které byly vytvořeny v sekundární lokalitě, migrovány zpět do primární oblasti. Tento stav a data se většinou týkají identit zařízení a metadat aplikací, které musí být sloučeny s primárním centrem IoT hub a dalšími úložišti specifickými pro aplikaci v primární oblasti. 

Chcete-li tento krok zjednodušit, měli byste použít idempotentní operace. Idempotentní operace minimalizují vedlejší účinky z případné konzistentní distribuce událostí a z duplicitních nebo mimoobjednávkových doručení událostí. Kromě toho by měla být aplikační logika navržena tak, aby tolerovala potenciální nekonzistence nebo mírně zastaralý stav. Tato situace může nastat z důvodu další čas potřebný pro systém léčit na základě cílů bodu obnovení (RPO).

## <a name="choose-the-right-hadr-option"></a>Zvolte správnou volbu HA/DR

Zde je přehled možností HA /DR uvedených v tomto článku, které lze použít jako referenční rámec pro výběr správné možnosti, která funguje pro vaše řešení.

| Možnost HA/DR | Rto | Cíl bodu obnovení | Vyžaduje ruční zásah? | Složitost implementace | Dodatečný dopad na náklady|
| --- | --- | --- | --- | --- | --- |
| Převzetí služeb při selhání iniciované společností Microsoft |2 - 26 hodin|Odkazovat na tabulku RPO výše|Ne|Žádný|Žádný|
| Ruční převzetí služeb při selhání |10 min - 2 hodiny|Odkazovat na tabulku RPO výše|Ano|Velmi nízko. Tuto operaci stačí spustit z portálu.|Žádný|
| Napříč regionem HA |< 1 min|Závisí na četnosti replikace vašeho vlastního řešení HA|Ne|Vysoká|> 1x náklady na 1 IoT hub|

## <a name="next-steps"></a>Další kroky

* [Co je Azure IoT Hub?](about-iot-hub.md)
* [Začínáme s IoT Huby (Úvodní příručka)](quickstart-send-telemetry-dotnet.md)
* [Kurz: Provedení ručního převzetí služeb při selhání pro centrum IoT hub](tutorial-manual-failover.md)