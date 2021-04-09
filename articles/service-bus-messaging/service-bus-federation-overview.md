---
title: Replikace zpráv a federace mezi oblastmi – Azure Service Bus | Microsoft Docs
description: Tento článek poskytuje přehled replikace událostí a federace mezi oblastmi pomocí Azure Service Bus.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880823"
---
# <a name="message-replication-and-cross-region-federation"></a>Replikace zpráv a federace mezi oblastmi

V rámci oborů názvů Azure Service Bus podporuje [vytváření topologií zřetězených front a odběrů témat pomocí autopřesměrovávání](service-bus-auto-forwarding.md) , které umožňuje implementaci různých vzorů směrování. Můžete například poskytnout partnerům vyhrazené fronty, ke kterým mají oprávnění Odeslat nebo přijmout a které mohou být v případě potřeby dočasně pozastaveny a flexibilní připojení k ostatním entitám, které jsou pro aplikaci privátní. Můžete také vytvořit komplexní topologie mezifází směrování, nebo můžete vytvořit fronty pro vytváření poštovních schránek, které vyprázdní odběry témat v podobě fronty a umožňují větší kapacitu úložiště pro každého předplatitele. 

Mnoho propracovaných řešení také vyžaduje replikaci zpráv napříč hranicemi oboru názvů za účelem implementace těchto a dalších vzorů. Zprávy mohou být nutné přesměrovat mezi obory názvů přidružených k více různým klientům aplikace nebo napříč několika různými oblastmi Azure. 

Vaše řešení bude uchovávat více Service Bus oborů názvů v různých oblastech a replikovat zprávy mezi frontami a tématy a/nebo tím, že budete vyměňovat zprávy se zdroji a cíli, jako je [azure Event Hubs](../event-hubs/event-hubs-about.md), [azure IoT Hub](../iot-fundamentals/iot-introduction.md)nebo [Apache Kafka](https://kafka.apache.org). 

Tyto scénáře jsou zaměřeny na tento článek. 

## <a name="federation-patterns"></a>Vzory federace

K dispozici je řada možných motivů, proč můžete chtít přesouvat zprávy mezi Service Bus entitami, jako jsou fronty nebo témata, nebo mezi Service Bus a dalšími zdroji a cíli. 

V porovnání s podobnou sadou vzorů pro [Event Hubs](../service-bus-messaging/service-bus-federation-overview.md)je federace pro entity podobné entitám složitější, protože fronty zpráv přislibují svým příjemcům výhradní vlastnictví v rámci jakékoli jedné zprávy, a proto se očekává, že bude zachováno pořadí doručení v doručování zpráv a že zprostředkovatel bude koordinovat spravedlivou distribuci zpráv mezi [konkurenčními zákazníky](/azure/architecture/patterns/competing-consumers). 

Existují praktické překážky, včetně omezení [limitu věta](https://en.wikipedia.org/wiki/CAP_theorem), která usnadňují poskytování jednotného zobrazení fronty, která je dostupná ve více oblastech, a která umožňuje rozmístění na regionální úrovni a [konkurenční zákazníky](/azure/architecture/patterns/competing-consumers) , kteří přebírají exkluzivní vlastnictví zpráv. Tato geografická fronta by vyžadovala plně konzistentní replikaci nejen pro zprávy, ale také stav doručení každé zprávy, než mohou uživatelé zpřístupnit zprávy. Cílem plné konzistence pro hypotetickou a regionální distribuovanou frontu je přímý konflikt s klíčovým cílem, který prakticky všichni Azure Service Bus zákazníci mají při zvažování scénářů federace: maximální dostupnost a spolehlivost pro svá řešení. 

Zde uvedené vzory se proto zaměřují na dostupnost a spolehlivost a zároveň se tím lépe vyhne ztrátám informací i zdvojeným zpracováním zpráv. 

### <a name="resiliency-against-regional-availability-events"></a>Odolnost proti událostem regionální dostupnosti 

I když jsou maximální dostupnost a spolehlivost hlavními provozními prioritami pro Service Bus, existuje mnoho způsobů, jak může producent nebo spotřebitel zabránit v komunikaci s přiřazeným "primárním" Service Busm v souvislosti s problémy se sítí nebo překladem IP adres, nebo tam, kde by Service Bus entita mohla být skutečně dočasně nereagovala nebo vrátila chyby. Označený procesor zpráv může být také nedostupný.

Tyto podmínky nejsou "katastrofální důsledky", takže budete chtít opustit regionální nasazení úplně tak, jak můžete dělat při zotavení po havárii, ale obchodní scénář některých aplikací už může mít vliv na události dostupnosti, které posledních nepřesahují více než několik minut nebo dokonce i sekund. Azure Service Bus se často používá v hybridních cloudových prostředích a s klienty, kteří se nacházejí v hraniční síti, například v maloobchodních prodejních, restauracích, bankovních větvích, výrobních lokalitách, logistických zařízeních a letištích. Je možné, že síťové směrování nebo potíže s zahlcením mají vliv na schopnost kterékoli lokality na přístup k přiřazenému koncovému bodu Service Bus, zatímco sekundární koncový bod v jiné oblasti může být dostupný. Ve stejnou dobu mohou systémy, které zpracovávají zprávy přicházející z těchto webů, stále mít nepřekážkový přístup k primárnímu i sekundárnímu koncovému bodu Service Bus. 

Existuje mnoho praktických příkladů takových hybridních cloudových a hraničních aplikací s nízkou obchodní tolerancí pro problémy se síťovými směrováními nebo problémy s přechodnou dostupností Service Bus entity. Patří mezi ně zpracování plateb v maloobchodních sítích, zprovoznění na letištních zařízeních a mobilní telefonní objednávky v restauracích, všechny z nich přicházejí do okamžitého a úplného pozastavení, kdykoli není k dispozici spolehlivá komunikační cesta.

V této kategorii se podíváme na tři různé distribuované vzory: replikace typu "vše aktivní", "aktivní-pasivní" replikace "a" Spillover "replikace. 

#### <a name="all-active-replication"></a>Replikace All-Active

Vzor replikace "všechny aktivní" umožňuje aktivní repliku stejného logického tématu (nebo fronty) k dispozici ve více oborech názvů (a oblastech) a pro všechny zprávy, které budou k dispozici ve všech replikách bez ohledu na to, kam byly zařazeny do fronty. Vzor obvykle zachovává pořadí zpráv vzhledem k libovolnému vydavateli. 

![Všechny aktivní vzory](media/service-bus-federation-overview/mirrored-topics.jpg)

Jak je znázorněno na obrázku, vzorek se obvykle započítá na Service Bus témata. Jedno téma pro každý obor názvů, který se účastní schématu replikace. Každé z těchto témat obsahuje jedno "odběr replikace" pro některá další témata, na která se zprávy budou replikovat. Na ilustraci nahoře máme pár témat a proto jediné předplatné replikace pro příslušné jiné téma. V případě scénáře se třemi obory názvů *{N1, N2, N3}* má téma v oboru názvů *N1* dvě odběry replikace, jednu pro příslušné téma v *N2* a jednu pro příslušné téma v *N3*. 

Každé předplatné replikace má pravidlo, které kombinuje výraz SQL Filter ( `replicated <> 1` ) a akci SQL ( `set replicated = 1` ). Filtr pravidla zajišťuje, že pouze zprávy, ve kterých není nastavena vlastní vlastnost `replication` , nebo nemá hodnotu v `1` tomto předplatném nárokovat, a tato akce nastaví stejnou vlastnost na hodnotu `1` u každé vybrané zprávy hned následně. To má vliv na to, že když se zpráva nakopíruje do odpovídajícího tématu, už není vhodná pro replikaci v opačném směru, proto se vyhneme zprávám skákající mezi replikami.

Předplatné s příslušným pravidlem je možné snadno přidat do libovolného tématu pomocí Azure CLI, jako je to.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

Pro modelování fronty je každé téma omezené jenom na jedno běžné předplatné (jiné než odběry replikace), které všichni uživatelé sdílejí.

> Model replikace All-Active umístí kopii každé zprávy odeslané do kterékoli z témat do každého z těchto témat. To znamená, že váš kód aplikace v každé oblasti uvidí a zpracuje všechny zprávy.
> Tento model je vhodný pro scénáře, ve kterých jsou data sdílena do více oblastí, nebo pokud je nadbytečné zpracování obecně žádoucí. Pokud potřebujete zpracovat každou zprávu pouze jednou, stejně jako u běžné fronty, je třeba vzít v úvahu jeden z následujících dvou vzorů.  

#### <a name="active-passive-replication"></a>Replikace Active-Passive

Vzor replikace "aktivní – pasivní" je variace předchozího vzoru, přičemž aplikace pro posílání a přijímání zpráv a zpráv se aktivně používá pro odesílání a příjem zpráv a zprávy se replikují do sekundárního tématu pro případ, že primární téma může být nedostupné nebo nedostupné. 

![Aktivní pasivní vzor](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

Klíčový rozdíl mezi tímto vzorem a předchozím vzorem spočívá v tom, že replikace je jednosměrná od primárního tématu do sekundárního tématu. Sekundární téma se nikdy nestane primární, ale je to možnost zálohování, pokud je primární téma dočasně nepoužitelné. 

Při použití tohoto vzoru se tento model pokusí pomoci minimalizovat duplicitní zpracování. Během replikace `TimeToLive` je vlastnost Message nastavena na dobu trvání replikovaných zpráv, která odráží očekávanou dobu, během které by selhání primární služby vedlo k převzetí služeb při selhání. Pokud například scénář použití vyžaduje přepnutí uživatele na sekundární během 1 minuty od načtení zpráv z primárního startu se zobrazením problémů, sekundární by měl mít v ideálním případě všechny dostupné zprávy, ke kterým nemůžete získat přístup na primárním, ale minimální počet zpráv, které jste už před tím, než se objevili, nastavili na primární. Pokud nastavíme na `TimeToLive` dvojnásobek tohoto období, 2 minuty během replikace ( `set sys.TimeToLive = '0:2:0'` v akci pravidla), sekundární bude uchovávat jenom zprávy o 2 minuty a zahodí se starší. To znamená, že když se přijímač přepne na sekundární, může rychle přečíst zprávy starší než poslední zpracování a pak je zpracovat z první zprávy, že se ještě nezobrazila. Skutečná doba uchovávání bude záviset na konkrétním případu použití a na rychlém zapnutí a umožňuje přepnout na sekundární aplikaci ve vaší aplikaci. `TimeToLive`Nastavení se respektuje v rozsahu od několika sekund až po dny. 

Zatímco aplikace používá sekundární, může také publikovat přímo do sekundárního tématu, které pak funguje jako jakékoli běžné téma. Po přepnutí do sekundárního uživatele se uživateli zobrazí kombinace replikovaných zpráv a zpráv publikovaných přímo do sekundárního. Aplikace by proto měla nejdřív přepínat publikování zpátky na primární a pořád umožňovat vyprázdnění místně publikovaných zpráv, než se přesměruje příjemce zpět na sekundární. Vzhledem k tomu, že se replikace automaticky obnovuje, jakmile je primární znovu dostupná, získá příjemce v této době nové zprávy publikované v této době, i když s poněkud vyšší latencí. 

> Tento model je vhodný pro scénáře, ve kterých by se měly zpracovat zprávy jenom jednou. Aplikace musí spolupracovat s udržováním přehledu o zprávách, které zpracoval z primární databáze, protože najde duplicity po dobu trvání okna převzetí služeb při selhání v sekundárním umístění a při přepnutí zpět se vrátí duplicity.
> Kritérium odstranění duplicitních dat by mělo být nejlépe dodaných aplikací `MessageId` . `EnqueuedTimeUtc`Hodnota je také vhodná jako indikátor meze, ale aplikace musí umožňovat určitou velikost posunu hodin (několik sekund) mezi primárním a sekundárním systémem jako s jakýmkoli distribuovaným systémem.


#### <a name="spillover-replication"></a>Spillover replikace

Vzor replikace "Spillover" umožňuje aktivní/aktivní použití více entit Service Bus ve více oblastech, abyste se mohli vypořádat se scénářem, ve kterém je Service Bus v pořádku, ale *spotřebitel* se bude přenáročný na počet nevyřízených zpráv nebo je nedostupný. Důvodem může být to, že databáze, která je v procesu příjemce, může být pomalá nebo nedostupná. Tento model funguje s jednoduchými frontami a s předplatnými tématu.  

![Spillover replikace](media/service-bus-federation-overview/spillover.jpg)  

Jak je znázorněno na obrázku, vzor replikace Spillover replikuje zprávy z [fronty nedoručených](service-bus-dead-letter-queues.md) zpráv z fronty nebo předplatného do spárované fronty nebo tématu v jiném oboru názvů. 

V případě neúspěchu se dva obory názvů používají paralelně, přičemž každý z nich získá určitou podmnožinu celkového provozu zpráv a jejich přidružených uživatelů, kteří zpracovávají tuto podmnožinu. Jakmile jeden z příjemců začne vykazovat vysoké míry selhání nebo se zastaví, budou příslušné zprávy končit frontou nedoručených zpráv, a to buď prostřednictvím překročení počtu doručení, nebo z důvodu vypršení platnosti. Úlohy replikace je pak vybere a znovu zařadí do fronty v spárované frontě, kde se pak prezentují v dobrém stavu zákazníka. 

Pokud se zpracování musí uskutečnit v rámci určitého termínu, je `TimeToLive` třeba nastavit frontu a/nebo zprávy tak, aby se zpracování mohlo i nadále vyskytnout v čase sekundárním Spillover, `TimeToLive` jinak může být instance nastavena na polovinu povoleného času.

Stejně jako u [modelu All-Active](#all-active-replication)může aplikace přidat do zprávy indikátor, zda byla zpráva již replikována, pokud se neodrazí mezi dvojicí front, ale jsou místo toho odesílány do pomocné fronty, která slouží jako fronta nedoručených zpráv pro složený vzorek.

> Tento model je vhodný pro scénáře, kdy je důležité se chránit před problémy s dostupností v spotřebitelůch nebo prostředcích, na které se příjemci spoléhají, a také v případě, že dojde k redistribuci špičky provozu na jednom z spárovaných front. Poskytuje taky ochranu proti jednomu z oborů názvů, pokud se uživatelé čtou z obou front, ale prodleva replikace, kterou `TimeToLive` může vypršení platnosti, může způsobit zprávy v tomto časovém intervalu do oddílu v nedostupném oboru názvů. 

### <a name="latency-optimization"></a>Optimalizace latence 

Témata slouží k distribuci informací více příjemcům. V některých případech, zejména u spotřebitelů se geografickou distribucí, může být výhodné replikovat zprávy z tématu do tématu v sekundárním oboru názvů blíže uživatelům.

![Optimalizace latence](media/service-bus-federation-overview/latency-optimization.jpg)  

Pokud například sdílíte data mezi oblastními a kontinentálními centry, je efektivnější přenášet informace jenom jednou mezi centry a příjemci získají kopii dat z těchto Center. 

Přenosy replikace je možné provádět v dávkách, které uživatelé často získávají a vyrovnávají zprávy jednu po jedné. U základní latence sítě 100 MS mezi Severní Amerika a Evropa je potřeba, aby každá zpráva během zpracování dvou zpátečních cest ke vzdálené entitě porovnala 200 MS za účelem získání a vyrovnávání zpráv, a to v porovnání s entitou ve stejné oblasti. 

### <a name="validation-reduction-and-enrichment"></a>Ověřování, snižování a obohacení

Zprávy mohou být odesílány do Service Bus fronty nebo tématu klienty externě na vaše vlastní řešení.

![Ověřování, zmenšení, obohacení](media/service-bus-federation-overview/validation.jpg)  

Tyto zprávy mohou vyžadovat kontrolu kompatibility s daným schématem a nekompatibilní zprávy, které mají být vyřazeny nebo nedoručeny. Je možné, že některé zprávy budou muset být ve složitosti omezené, protože data Vynecháváte a některé z nich může být nutné rozšířit přidáním dat na základě vyhledávání referenčních dat. Operace se dají provádět s vlastními funkcemi v úloze replikace. 

### <a name="stream-to-queue-replication"></a>Streamování replikace do fronty

Azure Event Hubs je ideálním řešením pro zpracování extrémních objemů příchozích událostí. Ale ani Event Hubs ani podobné moduly, jako je Apache Kafka, poskytují [konkurenční zákaznický](/azure/architecture/patterns/competing-consumers) model spravovaný službou, ve kterém může více uživatelů zpracovávat zprávy ze stejného zdroje současně bez rizika jejich duplicitního zpracování, a nakonec tyto zprávy po zpracování vyrovnávat. 

![Integrace](media/service-bus-federation-overview/hub-to-queue.jpg)

Datový proud pro zařazování replikace do fronty přenáší obsah jednoho oddílu centra událostí nebo obsah celého centra událostí do fronty Service Bus, ze kterých se dají zprávy bezpečně zpracovávat, reakční a s konkurenčními spotřebiteli. Tato replikace také umožňuje používat všechny ostatní funkce Service Bus pro tyto zprávy, včetně směrování s tématy a demultiplexování založené na relacích. 

### <a name="consolidation-and-normalization"></a>Konsolidace a normalizace 

Globální řešení se často skládají z regionálních údajů, které jsou z velké části nezávislé, včetně toho, že mají vlastní možnosti zpracování, ale Supra-oblastní a globální perspektivy budou vyžadovat integraci dat, a tudíž i centrální konsolidaci stejných dat, která se vyhodnotí v příslušných regionálních oblastech pro místní perspektivy. 

![Konsolidační](media/service-bus-federation-overview/merge.jpg)

Normalizace je charakter scénáře konsolidace, kde dvě nebo více příchozích sekvencí zpráv mají stejný druh informací, ale s různými strukturami nebo různými kódováními a zprávy musí být překódovány nebo transformované předtím, než mohou být spotřebovány. 

Normalizace může zahrnovat také kryptografickou práci, jako je dešifrování kompletních šifrovaných datových částí a jejich opětovné šifrování s různými klíči a algoritmy pro cílovou skupinu uživatelů pro příjem dat. 

### <a name="splitting-and-routing"></a>Rozdělení a směrování

Service Bus témata a jejich pravidla předplatného se často používají k filtrování datového proudu zpráv pro určitou cílovou skupinu a tato cílová skupina pak získala filtrovanou sadu z předplatného. 

![Rozdělení](media/service-bus-federation-overview/split.jpg)

V globálním systému, ve kterém jsou cílové skupiny pro tyto zprávy globálně distribuované nebo patří do různých aplikací, se replikace dá použít k přenosu zpráv z takového předplatného do fronty nebo tématu v jiném oboru názvů, ze kterého se spotřebovávají.

### <a name="replication-applications-in-azure-functions"></a>Replikační aplikace v Azure Functions

Implementace výše uvedených vzorů vyžaduje škálovatelné a spolehlivé spouštěcí prostředí pro úlohy replikace, které chcete nakonfigurovat a spustit. V Azure je prostředí modulu runtime, které je nejvhodnější pro nestavové úkoly, [Azure Functions](../azure-functions/functions-overview.md). 

Azure Functions může běžet pod [spravovanou identitou Azure](../active-directory/managed-identities-azure-resources/overview.md) tak, aby se úlohy replikace mohly integrovat s pravidly řízení přístupu na základě rolí zdrojové a cílové služby, aniž byste museli spravovat tajné klíče podél cesty replikace. Pro zdroje a cíle replikace, které vyžadují explicitní přihlašovací údaje, Azure Functions může uchovávat konfigurační hodnoty pro tyto přihlašovací údaje v přísně řízeném úložišti v rámci [Azure Key Vault](../key-vault/general/overview.md).

Azure Functions navíc umožňuje úlohám replikace přímo integraci se službami Azure Virtual Networks a [koncovými body služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro všechny služby zasílání zpráv Azure a je snadno integrovaná do [Azure monitor](../azure-monitor/overview.md).

Nejdůležitější Azure Functions mít předem připravené, škálovatelné triggery a výstupní vazby pro [azure Event Hubs](../azure-functions/functions-bindings-service-bus.md), [azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)a [Azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md), vlastní rozšíření pro [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)a [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). Většina aktivačních událostí se dynamicky přizpůsobí požadavkům propustnosti tím, že se počet souběžně prováděných instancí nahoru a dolů na základě dokumentovaných metrik. 

S plánem Azure Functions spotřeby se předem připravené triggery můžou rovnoměrně škálovat na nulu, zatímco nejsou k dispozici žádné zprávy pro replikaci, což znamená, že se vám neúčtují žádné náklady na změnu konfigurace, která je připravená na zálohování. Klíčovým nevýhodouem použití plánu spotřeby je, že latence pro úlohy replikace "vychází z tohoto stavu" je výrazně vyšší než u plánů hostování, ve kterých je infrastruktura udržována v provozu.  

Na rozdíl od všech z Apache Kafka nich většina běžných replikačních modulů pro zasílání zpráv a událostí, jako je například [nástroje MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) , vyžaduje, abyste poskytovali hostitelské prostředí a mohli škálovat replikační modul sami. Který zahrnuje konfiguraci a integraci funkcí zabezpečení a sítě a usnadnění toku dat monitorování, a pak ještě nemáte příležitost k vložení vlastních úloh replikace do toku. 

## <a name="next-steps"></a>Další kroky

V tomto článku jsme prozkoumali řadu vzorů federace a v Azure jsme vysvětlili roli Azure Functions jako modul runtime replikace událostí a zasílání zpráv. 

Dále si můžete přečíst, jak nastavit aplikaci replikátoru pomocí Azure Functions a pak jak replikovat toky událostí mezi Event Hubs a různými systémy pro události a zasílání zpráv:

- [Replikační aplikace v Azure Functions](service-bus-federation-replicator-functions.md)
- [Replikace událostí mezi Service Bus entit](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Směrování událostí do Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Získání událostí z Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif