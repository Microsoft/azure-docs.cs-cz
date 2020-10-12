---
title: Azure IoT Hub vysoká dostupnost a zotavení po havárii | Microsoft Docs
description: Popisuje funkce Azure a IoT Hub, které vám pomůžou vytvářet vysoce dostupná řešení Azure IoT s možnostmi zotavení po havárii.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: d4a5ad36e9d6d71ad88d0b5c56b6079f34483347
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021422"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Vysoká dostupnost služby IoT Hub a zotavení po havárii

Jako první krok k implementaci odolného řešení IoT, architektům, vývojářům a vlastníkům podnikových aplikací musí definovat cíle pro dobu provozu pro řešení, která vytváří. Tyto cíle lze definovat hlavně na základě konkrétních obchodních cílů pro každý scénář. V tomto kontextu se v článku [technické postupy pro provozní kontinuitu Azure](https://docs.microsoft.com/azure/architecture/resiliency/) popisuje obecné rozhraní, které vám pomůžou zamyslet se na provozní kontinuitu a zotavení po havárii. Dokument pro [zotavení po havárii a vysokou dostupnost pro aplikace Azure](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) poskytuje pokyny pro architekturu pro aplikace Azure, které umožňují dosáhnout vysoké dostupnosti (ha) a zotavení po havárii (Dr).

Tento článek popisuje funkce HA a zotavení po havárii, které nabízí konkrétně služba IoT Hub. Hlavními oblastmi popsanými v tomto článku jsou:

- HA uvnitř oblasti
- Vzájemná oblast DR
- Dosažení mezi oblastí HA

V závislosti na záměrech v době provozu, které definujete pro vaše řešení IoT, byste měli určit, které z níže uvedených možností nejlépe vyhovují vašim obchodním cílům. Zahrnutí kteréhokoli z těchto alternativ HA/DR do řešení IoT vyžaduje pečlivé hodnocení kompromisů mezi těmito částmi:

- Úroveň odolnosti, kterou požadujete 
- Složitost implementace a údržby
- Dopad na spotřebu

## <a name="intra-region-ha"></a>HA uvnitř oblasti

Služba IoT Hub poskytuje vysokou oblast HA tím, že implementuje redundance ve skoro všech vrstvách služby. [Smlouvu SLA publikovanou službou IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) se dosahuje tím, že se tyto redundance využívají. Vývojáři řešení IoT nevyžadují žádnou další práci, aby mohli využívat tyto funkce HA. I když IoT Hub nabízí dostatečně vysokou zaručenou dobu provozu, přechodné chyby se u jakékoli distribuované výpočetní platformy můžou pořád očekávat. Pokud teprve začínáte s migrací vašich řešení do cloudu z místního řešení, je nutné, aby se v rámci optimalizace "střední doba mezi selháními" a "znamenala" na obnovení "znamenalo posunutí. Jinými slovy, přechodná selhání se považují za normální při práci s cloudem v kombinaci. Příslušné [zásady opakování](iot-hub-reliability-features-in-sdks.md) musí být integrované pro součásti, které pracují s cloudovou aplikací pro řešení přechodných chyb.

> [!NOTE]
> Některé služby Azure také poskytují další vrstvy dostupnosti v rámci oblasti integrací s [zóny dostupnosti (AZs)](../availability-zones/az-overview.md). Služba IoT Hub aktuálně nepodporuje AZs.

## <a name="cross-region-dr"></a>Vzájemná oblast DR

Pokud se v datovém centru rozšíří výpadky z důvodu výpadků napájení nebo jiných selhání týkajících se fyzických prostředků, může dojít k několika vzácným situacím. Tyto události jsou vzácné, během kterých výše popsaná funkce HA v rámci oblasti HA není vždy popsána v tématu. IoT Hub poskytuje několik řešení pro zotavení po delších výpadkech. 

Možnosti obnovení dostupné zákazníkům v takové situaci představují [převzetí služeb při selhání](#microsoft-initiated-failover) a [ruční převzetí služeb při](#manual-failover)selhání iniciované společností Microsoft. Základní rozdíl mezi těmito dvěma verzemi spočívá v tom, že společnost Microsoft zahájí bývalé a uživatel ji zahájí. Ruční převzetí služeb při selhání v porovnání s možností převzetí služeb při selhání spouštěné Microsoftem zahrnuje taky dobu kratšího cíle obnovení (RTO). Konkrétní RTO, které nabízí jednotlivé možnosti, jsou popsány v následujících částech. Když se v některé z těchto možností provede převzetí služeb IoT Hub ze své primární oblasti, bude centrum plně funkční v odpovídající [geografické oblasti Azure](../best-practices-availability-paired-regions.md).

Obě tyto možnosti převzetí služeb při selhání nabízejí následující cíle bodů obnovení (RPO):

| Datový typ | Cíle bodu obnovení (RPO) |
| --- | --- |
| Registr identit |0-5 min. ztráta dat |
| Data vlákna zařízení |0-5 min. ztráta dat |
| Zprávy z cloudu na zařízení<sup>1</sup> |0-5 min. ztráta dat |
| Nadřazená úloha<sup>1</sup> a zařízení |0-5 min. ztráta dat |
| Zprávy typu zařízení-cloud |Ztratí se všechny nepřečtené zprávy. |
| Zprávy monitorování operací |Ztratí se všechny nepřečtené zprávy. |
| Zprávy o zpětné vazbě z cloudu na zařízení |Ztratí se všechny nepřečtené zprávy. |

<sup>1</sup> Zprávy typu cloud-zařízení a nadřazené úlohy se neobnoví jako součást ručního převzetí služeb při selhání.

Až se operace převzetí služeb při selhání pro Centrum IoT dokončí, očekává se, že všechny operace ze zařízení a back-endové aplikace budou pokračovat v práci bez nutnosti ručního zásahu. To znamená, že zprávy ze zařízení do cloudu by měly fungovat i nadále a celý registr zařízení zůstane beze změny. Události emitované prostřednictvím Event Grid můžou být využívány prostřednictvím stejných předplatných nakonfigurovaných dříve, dokud jsou tyto Event Grid odběry i nadále k dispozici. Pro vlastní koncové body se nevyžadují žádné další zpracování.

> [!CAUTION]
> - Název a koncový bod, který je kompatibilní s centrem událostí, se po převzetí služeb při selhání změní na koncový bod IoT Hub integrovaných událostí. Při přijímání zpráv telemetrie z integrovaného koncového bodu pomocí klienta centra událostí nebo hostitele procesoru událostí byste měli připojení vytvořit [pomocí připojovacího řetězce služby IoT Hub](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) . Tím zajistíte, že vaše back-endové aplikace budou dál fungovat, aniž by bylo nutné ruční zásah po převzetí služeb při selhání. Pokud v aplikaci přímo použijete název a koncový bod kompatibilní s centrem událostí, budete muset po převzetí služeb při selhání [Načíst nový koncový bod kompatibilní](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) s centrem událostí, aby bylo možné pokračovat v operacích. 
>
> - Pokud k připojení vestavěného koncového bodu událostí používáte Azure Functions nebo Azure Stream Analytics, může být nutné provést **restart**. Důvodem je to, že během předchozích posunů převzetí služeb při selhání už nejsou platné.
>
> - Při směrování do úložiště doporučujeme vypsat objekty blob nebo soubory a potom je v nich vymezit, aby se zajistilo, že všechny objekty blob nebo soubory budou čteny bez nutnosti vytvářet žádné předpoklady oddílu. Rozsah oddílu se může během převzetí služeb při selhání nebo ručního převzetí služeb při selhání iniciovat společnosti Microsoft změnit. K vytvoření výčtu seznamů objektů BLOB nebo [seznamu adls Gen2 rozhraní API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) pro seznam souborů můžete použít [rozhraní list API blobů](https://docs.microsoft.com/rest/api/storageservices/list-blobs) . Další informace najdete v tématu [Azure Storage jako koncový bod směrování](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

## <a name="microsoft-initiated-failover"></a>Převzetí služeb při selhání iniciované Microsoftem

Převzetí služeb při selhání iniciované společností Microsoft je ve výjimečných situacích vykonáváno pro převzetí služeb při selhání všech Center IoT z ovlivněné oblasti do odpovídající geografické oblasti. Tento proces je výchozí možností (žádný způsob, jak uživatelům odhlásit) a nevyžaduje od uživatele zásah. Společnost Microsoft si vyhrazuje právo stanovit, kdy bude tato možnost uplatněna. Tento mechanismus nezahrnuje souhlas uživatele před převzetím služeb při selhání centra uživatele. Převzetí služeb při selhání iniciované Microsoftem má za cíl čas obnovení (RTO) 2-26 hodin. 

Velký RTO je, protože Microsoft musí provést operaci převzetí služeb při selhání jménem všech ovlivněných zákazníků v této oblasti. Pokud používáte méně důležité řešení IoT, které může trvat zhruba denně výpadky, můžete se na tuto možnost pořídit, abyste splnili celkové cíle zotavení po havárii pro vaše řešení IoT. Celková doba, po kterou se běhové operace stanou plně funkční, když se tento proces aktivuje, je popsaný v části "doba pro obnovení".

## <a name="manual-failover"></a>Ruční převzetí služeb při selhání

Pokud nesplňuje vaše cíle provozní doby provozu RTO, kterou iniciovala služba převzetí služeb při selhání, zvažte použití ručního převzetí služeb při selhání. RTO použití této možnosti může být kdekoli v rozmezí 10 minut až několik hodin. RTO je aktuálně funkcí počtu zařízení zaregistrovaných v instanci centra IoT, u které došlo k převzetí služeb při selhání. Můžete očekávat, že RTO pro centrum hostující přibližně 100 000 zařízení bude v jen po dobu 15 minut. Celková doba, po kterou se běhové operace stanou plně funkční, když se tento proces aktivuje, je popsaný v části "doba pro obnovení".

Možnost ručního převzetí služeb při selhání je vždy dostupná pro použití bez ohledu na to, jestli má primární region výpadky nebo ne. Proto tuto možnost můžete použít k provádění plánovaných převzetí služeb při selhání. Jedním z příkladů použití plánovaného převzetí služeb při selhání je provedení pravidelného přechodu k převzetí služeb při selhání. V takovém případě se jedná o slovo s upozorněním, ale výsledkem plánované operace převzetí služeb při selhání je výpadek centra po dobu určenou RTO pro tuto možnost a také způsobí ztrátu dat, jak je definováno v tabulce RPO výše. Je možné zvážit nastavení instance testovacího centra IoT, aby bylo možné naplánovat možnost plánovaného převzetí služeb při selhání, a získat tak jistotu, že vaše možnosti budou fungovat i v případě, že dojde k reálné havárii.

Ruční převzetí služeb při selhání je dostupné bez dalších poplatků pro centra IoT vytvořená po 18. května 2017

Podrobné pokyny najdete v tématu [kurz: provedení ručního převzetí služeb při selhání pro Centrum IoT.](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Spuštění testovacích cvičení

V centrech IoT, které se používají v produkčním prostředí, by se neměly provádět testovací cvičení.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Nepoužívejte ruční převzetí služeb při selhání k migraci centra IoT do jiné oblasti.

Ruční převzetí služeb při *selhání by se nemělo používat* jako mechanismus k trvalé migraci vašeho centra mezi geograficky spárované oblasti Azure. Tím se zvyšuje latence operací prováděných proti službě IoT Hub ze zařízení s domovským umístěním ve staré primární oblasti.

## <a name="failback"></a>Navrácení služeb po obnovení

Navrácení služeb po obnovení do staré primární oblasti se dá dosáhnout tak, že akci převzetí služeb při selhání vykonáte jiným časem. Pokud se původní operace převzetí služeb při selhání provedla při obnovení z rozšířeného výpadku v původní primární oblasti, doporučujeme, aby se centrum po obnovení do původního umístění obnovilo z situace výpadku.

> [!IMPORTANT]
> - Uživatelé můžou provádět jenom 2 úspěšné převzetí služeb při selhání a 2 úspěšné operace navrácení služeb po obnovení za den.
>
> - Zpět na back-vrácení služeb při selhání nebo převzetí služeb při selhání se nepovoluje. Mezi těmito operacemi musíte počkat 1 hodinu.

## <a name="time-to-recover"></a>Doba obnovení

I když plně kvalifikovaný název domény (a tudíž připojovací řetězec) instance centra IoT zůstane stejný po převzetí služeb při selhání, pak se změní podkladová IP adresa. Vzhledem k tomu, že po aktivaci procesu převzetí služeb při selhání může být celková doba pro běhové operace prováděná vůči instanci služby IoT Hub plně funkční, můžete ji vyjádřit pomocí následující funkce.

Doba obnovení = RTO [10 min-2 hodiny pro ruční převzetí služeb při selhání (2-26 hodin) pro převzetí služeb při selhání spouštěné Microsoftem] + prodleva šíření DNS + doba, kterou klientská aplikace aktualizuje v mezipaměti IoT Hub IP adresa.

> [!IMPORTANT]
> Sady SDK pro IoT neobsahují mezipaměť IP adresy centra IoT. Doporučujeme, aby uživatelský kód, který se bude používat v sadách SDK, neměl ukládat IP adresu pro Centrum IoT.

## <a name="achieve-cross-region-ha"></a>Dosažení mezi oblastí HA

Pokud se RTO vaše cíle provozu vaší firmy, které nabízí Microsoft iniciované převzetí služeb při selhání nebo ruční převzetí služeb při selhání, měli byste zvážit implementaci mechanismu převzetí služeb při selhání automatických oblastí v jednotlivých zařízeních.
Kompletní zpracování topologií nasazení v řešeních IoT je mimo rámec tohoto článku. Tento článek popisuje model nasazení pro *místní převzetí služeb při selhání* pro účely vysoké dostupnosti a zotavení po havárii.

V rámci modelu regionálního převzetí služeb při selhání se back-end řešení spouští hlavně v jednom umístění datového centra. Sekundární centrum IoT a back-end se nasazují v jiném umístění datového centra. Pokud služba IoT Hub v primární oblasti zpomaluje výpadek nebo dojde k přerušení připojení k síti ze zařízení do primární oblasti, zařízení použijí sekundární koncový bod služby. Dostupnost řešení můžete vylepšit implementací modelu převzetí služeb při selhání mezi oblastmi a nemusíte přitom zamezit v jedné oblasti. 

Aby bylo možné implementovat model regionálního převzetí služeb při selhání pomocí IoT Hub, je nutné provést následující kroky:

* **Sekundární logika služby IoT Hub a směrování zařízení**: Pokud dojde k přerušení služby ve vaší primární oblasti, zařízení musí začít připojovat se k vaší sekundární oblasti. Vzhledem k tomu, že se jedná o charakter většiny služeb, které jsou součástí, je běžné, že správci řešení můžou aktivovat proces převzetí služeb při selhání mezi oblastmi. Nejlepším způsobem, jak komunikovat s novým koncovým bodem do zařízení a přitom zachovat kontrolu nad procesem, je nechat pravidelně kontrolovat službu *concierge* pro aktuální aktivní koncový bod. Služba concierge může být webová aplikace, která je replikována a udržována dosažitelná pomocí technik přesměrování DNS (například pomocí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > Služba IoT Hub není podporovaným typem koncového bodu v Azure Traffic Manager. Doporučením je integrovat navrhovanou službu concierge do Azure Traffic Manageru tím, že implementuje rozhraní API sondy stavu koncového bodu.

* **Replikace registru identit**: aby bylo možné použít, sekundární centrum IoT musí obsahovat všechny identity zařízení, které se mohou připojit k řešení. Řešení by mělo uchovávat geograficky replikované zálohy identit zařízení a odeslat je do sekundárního služby IoT Hub předtím, než přepnete aktivní koncový bod pro daná zařízení. V tomto kontextu je užitečná funkce exportu identity zařízení IoT Hub. Další informace najdete v tématu [IoT Hub příručka pro vývojáře – registr identit](iot-hub-devguide-identity-registry.md).

* **Sloučení logiky**: když bude primární region opět k dispozici, všechny stavy a data, které byly vytvořeny v sekundární lokalitě, musí být migrovány zpět do primární oblasti. Tato data a data se většinou vztahují k identitám zařízení a metadatům aplikací, které je potřeba sloučit s primárním centrem IoT a dalšími obchody pro konkrétní aplikace v primární oblasti. 

Pro zjednodušení tohoto kroku byste měli použít operace idempotentní. Operace idempotentní minimalizují vedlejší účinky z konečné souvislé distribuce událostí a z duplicitních nebo neseřazených doručení událostí. Kromě toho by měla být logika aplikace navržena tak, aby tolerována potenciální nekonzistence nebo mírně neaktuálního stavu. K této situaci může dojít v důsledku dodatečné doby potřebné k tomu, aby systém mohl na základě cílů bodu obnovení (RPO) provést zacelení.

## <a name="choose-the-right-hadr-option"></a>Výběr pravé možnosti HA/DR

Tady je souhrn možností HA/DR prezentovaných v tomto článku, které se dají použít jako rámec Reference k výběru správné možnosti, která funguje pro vaše řešení.

| Možnost HA/DR | RTO | RPO | Vyžaduje ruční zásah? | Složitost implementace | Dodatečný dopad na náklady|
| --- | --- | --- | --- | --- | --- |
| Převzetí služeb při selhání iniciované Microsoftem |2-26 hodin|Odkaz na tabulku RPO výše|No|Žádné|Žádné|
| Ruční převzetí služeb při selhání |10 minut – 2 hodiny|Odkaz na tabulku RPO výše|Yes|Velmi nízká. Tuto operaci musíte aktivovat jenom z portálu.|Žádné|
| HA mezi oblastmi |< 1 min.|Závisí na četnosti replikace vlastního řešení HA.|No|Vysoké|> 1x náklady 1 centra IoT|

## <a name="next-steps"></a>Další kroky

* [Co je Azure IoT Hub?](about-iot-hub.md)
* [Začínáme se službou IoT Hub (rychlý Start)](quickstart-send-telemetry-dotnet.md)
* [Kurz: provedení ručního převzetí služeb při selhání pro Centrum IoT](tutorial-manual-failover.md)
