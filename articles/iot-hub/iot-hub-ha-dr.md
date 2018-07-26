---
title: Azure IoT Hub vysokou dostupnost a zotavení po havárii | Dokumentace Microsoftu
description: Popisuje funkce Azure a IoT Hub, které pomáhají při vytváření vysoce dostupných řešení Azure IoT s po havárii funkcím pro obnovení.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: rkmanda
ms.openlocfilehash: 22c53a85d46cbcb1ed9592d7373ea044e8910297
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248294"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub vysokou dostupnost a zotavení po havárii
Jako první krok k implementaci odolných IoT řešení, architekty, vývojáři a vlastníci obchodních musí definovat cíle doby provozu pro řešení, která navíc teď připravují. Tyto cíle lze definovat především podle konkrétních obchodních cílů pro jednotlivé scénáře. V tomto kontextu, článek [Azure obchodní kontinuity podnikových procesů technickou pomoc]( https://docs.microsoft.com/azure/architecture/resiliency/) popisuje obecné rozhraní framework, můžete uvažovat o obchodní kontinuity podnikových procesů a zotavení po havárii. [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](_https://msdn.microsoft.com/library/dn251004.aspx) dokument obsahuje pokyny k architektuře strategie pro aplikace Azure, abyste dosáhli vysoké dostupnosti (HA) a zotavení po havárii (DR). 

Tento článek popisuje vysokou dostupnost a zotavení po Havárii funkcí, které nabízí specificky službou IoT Hub. Jsou různé oblasti popsané v tomto článku 

- Uvnitř oblasti HA
- Mezi různými oblastmi zotavení po Havárii
- Dosažení mezi různými oblastmi HA

V závislosti na cíli doby provozu, které definujete pro řešení IoT měli byste určit, která z možností uvedených níže podle vašich obchodních cílů. Všechny tyto možnosti HA/DR začleňte do vašeho řešení IoT vyžaduje pečlivé hodnocení kompromisy mezi:
- Úroveň odolnosti proti chybám, které potřebujete 
- Implementace a správa složitosti
- Náklady na prodané zboží dopad


## <a name="intra-region-ha"></a>Uvnitř oblasti HA
Služba IoT Hub poskytuje vysokou dostupnost uvnitř oblasti implementací redundance v téměř všechny vrstvy služby. [SLA publikované ve službě IoT Hub](_https://azure.microsoft.com/support/legal/sla/iot-hub) dosahuje tím, že použití těchto redundance. Žádná další práce nevyžadovala vývojáři řešení IoT, abyste mohli využívat tyto funkce HA. I když službu IoT Hub nabízí záruka poměrně vysoké dostupnosti, stále dají očekávat stejně jako u jakékoli distribuované výpočetní platforma přechodná selhání. Pokud jste právě začínáte se migrace řešení do cloudu z místního řešení, fokus musí přesunout z optimalizaci "střední doby mezi poruchami" "Průměrný čas potřebný k obnovení". Jinými slovy přechodná selhání se považuje za normální při provozu díky cloudu v kombinaci. Odpovídající [zásady opakování](_https://channel9.msdn.com/Shows/Internet-of-Things-Show/Retry-logic-in-device-SDKs-for-Azure-IoT-Hub) musí být součástí komponent, které interagují s aplikací cloudového řešit přechodná selhání. 

> [!NOTE]
> Některé služby Azure také poskytuje další úrovně dostupnosti v rámci oblasti díky integraci s [zóny dostupnosti (AZs)](_https://docs.microsoft.com/azure/availability-zones/az-overview). AZs nejsou aktuálně podporované ve službě IoT Hub.

## <a name="cross-region-dr"></a>Mezi různými oblastmi zotavení po Havárii
Může dojít k některé výjimečné situace při dojde delším výpadkům kvůli výpadku napájení nebo jiné chyby týkající se fyzických prostředků datového centra. Tyto události se vyskytují jen vzácně během které uvnitř oblasti výše popsané funkce HA vždy nepomůže. IoT Hub poskytuje několik řešení pro zotavení z těchto delším výpadkům. Možnosti obnovení, který je dostupný pro zákazníky v takové situaci se "Microsoft iniciované převzetí" a "ruční převzetí služeb při selhání". Základní rozdíl mezi těmito dvěma je, že Microsoft zahájí první a uživatel spustí druhém. Ruční převzetí služeb při selhání poskytuje také nižší plánovaná doba obnovení (RTO) ve srovnání s možnost převzetí služeb při selhání Microsoft inicioval. Konkrétní RTO nabízí s jednotlivými možnostmi jsou popsány v následujících částech. Když některý z těchto možností k provedení převzetí služeb při selhání služby IoT hub z jeho primární oblasti se provede, stane centra plně funkční z odpovídajících [geograficky spárované oblasti Azure](_https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 


Obě tyto možnosti převzetí služeb při selhání nabídky následujících bodů obnovení (rpo):

| Typ dat | Plánovaných bodů obnovení (RPO) |
| --- | --- |
| Registr identit |0 – 5 minut ztráty dat |
| Data dvojčete zařízení |0 – 5 minut ztráty dat |
| Zprávy typu cloud zařízení ** |0 – 5 minut ztráty dat |
| Úlohy nadřazené ** a zařízení |0 – 5 minut ztráty dat |
| Zprávy typu zařízení-cloud |Všechny nepřečtené zprávy jsou ztraceny |
| Monitorování zprávy operací |Všechny nepřečtené zprávy jsou ztraceny |
| Zprávy typu cloud zařízení zpětné vazby |Všechny nepřečtené zprávy jsou ztraceny |

Po dokončení operace převzetí služeb při selhání pro službu IoT hub se všechny operace v aplikacích zařízení a back-end očekává pokračovat v práci bez nutnosti ručního zásahu.

> [!CAUTION]
> - Po převzetí služeb při selhání změnit název kompatibilní s centrem událostí a koncový bod služby IoT Hub integrovaného koncového bodu události. Když přijímáte telemetrické zprávy z integrovaného koncového bodu pomocí třídy event processor host nebo event hub klienta, měli byste [pomocí připojovacího řetězce centra IoT](_https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin#read-from-the-built-in-endpoint) k navázání připojení. Tím se zajistí, že back-endové aplikace i nadále fungovat bez nutnosti ručního zásahu příspěvek převzetí služeb při selhání. Pokud použijete název kompatibilní s centrem událostí a koncový bod v back-end aplikace přímo, je potřeba změnit konfiguraci aplikace podle [načítání nový název kompatibilní s centrem událostí a koncového bodu](_https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin#read-from-the-built-in-endpoint) po převzetí služeb při selhání pokračovat operace. 
>
> - Po převzetí služeb při selhání můžete využívat události generované přes službu Event Grid prostřednictvím stejné předplatná nakonfigurovali v předchozích krocích, dokud budete mít k dispozici tyto odběry služby Event Grid.
>
> - ** Nadřazené úlohy a typu cloud zařízení zprávy získat ne obnovit jako součást ruční převzetí služeb při selhání v rámci nabídky verze preview této funkce.

### <a name="microsoft-initiated-failover"></a>Microsoft iniciované převzetí služeb při selhání
Převzetí služeb při selhání Microsoft inicioval se provede microsoftem ve velmi vzácných situacích převzít služby při selhání všech centra IoT z ovlivněné oblasti odpovídající geograficky spárované oblasti. Toto je výchozí možnost (žádný způsob, jak uživatelům odhlásit) a nevyžaduje žádný zásah od uživatele. Společnost Microsoft si vyhrazuje práva ke zjišťování, když se tato možnost bude využito. Tento mechanismus nezahrnuje souhlas uživatele, než centrum uživatele se převzaly při selhání. Převzetí služeb při selhání Microsoft inicioval se plánovaná doba obnovení (RTO), 2-26 hodin. Velké RTO totiž Microsoft musíte provést operaci převzetí služeb při selhání jménem všech ovlivněných zákazníků v dané oblasti. Pokud používáte méně kritické řešení IoT, které může tolerovat výpadek přibližně za den, je ok můžete zavést závislost na tuto možnost splnit celkové cíle obnovení po havárii pro vaše řešení IoT. Celková doba běhu operations budou plně funkční, jakmile tento proces se aktivuje, je popsaný v části "Doba obnovení". 

### <a name="manual-failover-preview"></a>Ruční převzetí služeb při selhání (preview)

Pokud nejsou splněny vaše obchodní cíle doby provozu podle RTO poskytuje tento Microsoft iniciované převzetí služeb při selhání byste měli zvážit použití ruční převzetí služeb při selhání spustit proces převzetí služeb při selhání, sami. RTO, tato možnost může být kdekoli mezi 10 minut do několika hodin. RTO je aktuálně funkce počet zařízení zaregistrovaný s instancí centra IoT se převzetí služeb při selhání. Můžete očekávat RTO rozbočovače hostování přibližně 100 000 zařízení v ballpark 15 minut. Celková doba běhu operations budou plně funkční, jakmile tento proces se aktivuje, je popsaný v části "Doba obnovení".

Možnost ručního převzetí služeb při selhání je vždy k dispozici pro použití bez ohledu na to, zda primární oblast má výpadek nebo ne. Proto tato možnost může potenciálně používá k provedení plánovaného převzetí služeb při selhání. Jeden příklad použití plánované převzetí služeb při selhání je přechod k podrobnostem pravidelné převzetí služeb při selhání. Slovo upozornění však je, že výsledky operace plánované převzetí služeb při selhání nebude znamenat výpadek pro rozbočovač za období definované RTO pro tuto možnost a také vede ke ztrátě dat, podle výše uvedené tabulce cíle bodu obnovení. Zvažte nastavení testu IoT hub instance vykonávat získejte jistotu budete moct zprovoznit řešení začátku do konce až když se stane, skutečné po havárii pravidelně plánované převzetí služeb při selhání možnost. 

> [!IMPORTANT]
> - Cvičení testu se nebude provádět na centra IoT hub, které se používají v produkčním prostředí. 
>
> - Ruční převzetí služeb při selhání není vhodné používat jako mechanismus trvale migrace mezi oblastmi Azure geograficky spárované rozbočovače. To by způsobilo zvýšenou latencí pro operace, které se provádí proti centra ze zařízení s adresami v původní primární oblast. 
>
> - Ruční převzetí služeb při selhání je aktuálně ve verzi preview a není k dispozici v následujících oblastech Azure. Východní USA, západní USA, Severní Evropa, západní Evropa, Brazílie – Jih, střed USA – jih.

### <a name="failback"></a>Navrácení služeb po obnovení

Selhání zpátky do původní primární oblasti se dá dosáhnout aktivací převzetí služeb při selhání akce jiný čas. Pokud k zotavení z výpadku rozšířené v původní primární oblasti se provedla původní operace převzetí služeb při selhání, doporučujeme centra by měl být nepovedlo zpět do původního umístění po tomto umístění se zotavuje ze situace výpadek. 

> [!IMPORTANT]
> - Uživatelé jsou povoleny pouze k provedení operace 2 úspěšné navrácení služeb po obnovení za den a 2 úspěšné převzetí služeb při selhání.
>
> - Zpět zálohovat převzetí služeb při selhání a navrácení služeb po obnovení operace nejsou povoleny. Uživatelé budou muset počkat 1 hodinou mezi tyto operace.

### <a name="time-to-recover"></a>Čas obnovení

Když plně kvalifikovaný název domény (a tedy připojovací řetězec) instance IoT hubu zůstává stejný příspěvek převzetí služeb při selhání, základní IP adresa se změní. Proto celkový čas pro modul runtime operace prováděné s vaší instancí centra IoT se plně funkční po procesu převzetí služeb při selhání se aktivuje lze vyjádřit pomocí následující funkce.

Obnovení = RTO [10 minut – 2 hodin pro ruční převzetí služeb při selhání | 2-26 hodin Microsoft iniciované převzetí služeb při selhání] + zpoždění šíření DNS + doba, za kterou klientské aplikaci aktualizovat všechny mezipaměti IoT Hub IP adresu. 

> [!IMPORTANT]
> Sady SDK pro IoT Neukládat do mezipaměti IP adresu služby IoT hub. Doporučujeme vám, že uživatelský kód propojení spolu se sadami SDK by neměly ukládat do mezipaměti IP adresu služby IoT hub. 

## <a name="achieve-cross-region-ha"></a>Dosažení mezi oblastmi. vysokou dostupnost
Pokud svoje obchodní cíle doby provozu nejsou splněno RTO, že Microsoft iniciované převzetí služeb při selhání nebo ruční převzetí služeb při selhání možnosti poskytují, měli byste zvážit, implementovat mechanismus převzetí služeb při selhání automatické mezi různými oblastmi na zařízení. Dokončení zpracování topologií nasazení v řešeních IoT sahá nad rámec tohoto článku. Tento článek popisuje článek *regionální převzetí služeb při selhání* model nasazení za účelem vysoké dostupnosti a zotavení po havárii.

V modelu regionální převzetí služeb při selhání řešení back end spuštění primárně na jednom místě datacentra. Sekundární centra IoT a back-endu se nasazují do jiného umístění datového centra. Pokud služby IoT hub v primární oblasti odkážete výpadku nebo dojde k přerušení připojení k síti ze zařízení do primární oblasti, zařízení použijte koncový bod služby sekundární. Může zlepšit dostupnost řešení díky implementaci modelu převzetí služeb při selhání mezi oblastmi místo zůstávají v rámci jedné oblasti. 

Na vysoké úrovni k implementaci modelu regionální převzetí služeb při selhání službou IoT Hub, je třeba provést následující kroky:

* **Sekundární služby IoT hub a zařízení směrování logiky**: Pokud narušení služby v primární oblasti, zařízení musí začínat připojení k sekundární oblasti. Vzhledem k s ohledem na stav povaze většina služeb zahrnutých, je běžné, že správců řešení, které spustí proces převzetí služeb při selhání mezi oblastmi. Je nejlepší způsob, jak komunikovat se zachováním potřebné kontroly nad procesu, nový koncový bod do zařízení, aby pravidelně kontrolovat *concierge* aktuální aktivní koncový bod služby. Služba Concierge, která může být webové aplikace, která je replikována a udržovat dostupný pomocí technik DNS přesměrování (například pomocí [Azure Traffic Manager][Azure Traffic Manager]).

   > [!NOTE]
   > Služby Azure IoT hub se nejedná o typ podporovaných koncových bodů v Azure Traffic Manageru. Doporučuje se službu navrhovaných concierge integrovat s Azure traffic Manageru tak, že ho implementovat sondu stavu koncového bodu rozhraní API.

* **Replikace registru identit**: má být použitelná, sekundární služby IoT hub musí obsahovat všechny identity zařízení, které se můžete připojit k řešení. Řešení musí zachovat geograficky replikovaných záloh identit zařízení a jejich nahrávání do služby IoT hub sekundární před přepnutím aktivní koncový bod pro zařízení. Funkce exportu identity zařízení služby IoT Hub je užitečná v tomto kontextu. Další informace najdete v tématu [Příručka pro vývojáře IoT Hub – registr identit][IoT Hub developer guide - identity registry].
* **Slučování logiky**: když primární oblast opět k dispozici, všechny stav a data, které byly vytvořeny v sekundární lokalitě se musí migrovat zpět do primární oblasti. Tento stav a data většinou se týkají identit zařízení a metadata aplikace, které je potřeba sloučit s primární služby IoT hub a jiných úložišť specifické pro aplikaci v primární oblasti. Pro zjednodušení tohoto kroku, měli byste použít idempotentní operace. Idempotentní operace minimalizovala vedlejších účinků z konečné konzistentní distribuci událostí a duplicitní hodnoty nebo mimo pořadí doručení událostí. Kromě toho logika aplikace by se měly navrhovat tolerovat potenciálním nekonzistencím nebo mírně zastaralý stav. Tato situace může nastat z důvodu další čas potřebný pro systém a oprava podle plánovaných bodů obnovení (RPO).

## <a name="choose-the-right-hadr-option"></a>Zvolte možnost zprava HA/DR
Heres souhrn HA/DR možností uvedených v tomto článku, který může sloužit jako referenční rámec zvolit možnost zprava, který vám vyhovuje řešení

| Možnost HA/DR | RTO | CÍL BODU OBNOVENÍ | Vyžaduje ruční zásah? | Implementace složitost | Další náklady dopad|
| --- | --- | --- | --- | --- | --- | --- |
| Microsoft iniciované převzetí služeb při selhání |2 - 26 hodin.|Přečtěte si výše uvedené tabulce cíle bodu obnovení|Ne|Žádný|Žádný
| Ruční převzetí služeb při selhání |10 minut – 2 hodiny|Přečtěte si výše uvedené tabulce cíle bodu obnovení|Ano|Velmi nízké. Potřebujete aktivovat tuto operaci z portálu.|Žádný
| Pro různé oblasti HA |< 1 min.|Závisí na četnosti replikace vašeho vlastního řešení HA|Ne|Vysoký|1 > x náklady na 1 IoT hub 

## <a name="next-steps"></a>Další postup
Další informace o službě Azure IoT Hub na následujících odkazech:

* [Začínáme s IoT hub (kurz)][lnk-get-started]
* [Co je Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[What is Azure IoT Hub?]: about-iot-hub.md
