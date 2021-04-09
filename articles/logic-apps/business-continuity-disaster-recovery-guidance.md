---
title: Provozní kontinuita a zotavení po havárii
description: Navrhněte strategii pro ochranu dat, rychlé obnovení z rušivých událostí, obnovení prostředků vyžadovaných důležitými obchodními funkcemi a udržování kontinuity podnikových prostředí pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89658197"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Provozní kontinuita a zotavení po havárii pro Azure Logic Apps

Abychom vám pomohli snížit dopad a důsledky, které mají v podnikání a na zákaznících nepředvídatelné události, ujistěte se, že máte k dispozici řešení [ *zotavení po havárii* (Dr)](https://en.wikipedia.org/wiki/Disaster_recovery) , abyste mohli chránit data, rychle obnovit prostředky, které podporují důležité obchodní funkce, a nechat provoz běžící na zachování [ *kontinuity podnikových služeb* (Bc)](https://en.wikipedia.org/wiki/Business_continuity_planning). Výpadky můžou například zahrnovat výpadky, ztráty v základní infrastruktuře nebo součásti, jako jsou úložiště, sítě nebo výpočetní prostředky, neobnovitelné chyby aplikace nebo dokonce i celá ztráta datového centra. Když máte připravený řešení pro provozní kontinuitu a zotavení po havárii (BCDR), může váš podnik nebo organizace reagovat rychleji na přerušení, plánovaná nebo neplánovaná a snížit prostoje pro vaše zákazníky.

Tento článek poskytuje pokyny a strategie BCDR, které můžete použít při vytváření automatizovaných pracovních postupů pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Pracovní postupy Logic Apps vám pomůžou snadněji integrovat a orchestrovat data mezi aplikacemi, Cloud Services a místními systémy tím, že se sníží množství kódu, který musíte napsat. Při plánování BCDR nezapomeňte vzít v úvahu nejen své aplikace logiky, ale také tyto prostředky Azure, které používáte s vašimi Logic Apps:

* [Připojení](../connectors/apis-list.md) vytvořená z aplikací logiky pro jiné aplikace, služby a systémy. Další informace najdete v části [připojení k prostředkům](#resource-connections) dále v tomto tématu.

* Místní [brány dat](../logic-apps/logic-apps-gateway-connection.md) , což jsou prostředky Azure, které vytvoříte a používáte ve svých aplikacích logiky pro přístup k datům v místních systémech. Každý prostředek brány představuje samostatnou [instalaci brány dat](../logic-apps/logic-apps-gateway-install.md) na místním počítači. Další informace najdete v části [místní brány dat](#on-premises-data-gateways) dále v tomto tématu.

* [Účty pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , kde definujete a ukládáte artefakty, které aplikace logiky používají pro scénáře [podnikové integrace B2B (Business-to-Business)](../logic-apps/logic-apps-enterprise-integration-overview.md) . Můžete například [nastavit zotavení po havárii mezi oblastmi pro účty pro integraci](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Prostředí integračních služeb (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , kde můžete vytvářet aplikace logiky, které běží v instanci izolované Logic Apps runtime v rámci služby Azure Virtual Network. Tyto aplikace logiky pak můžou získat přístup k prostředkům chráněným za bránou firewall v této virtuální síti.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primární a sekundární umístění

Každá aplikace logiky musí určit umístění, které chcete použít pro nasazení. Toto umístění je buď veřejná oblast v globálním víceklientském prostředí Azure, jako je například "Západní USA" nebo ISE), kterou jste dříve vytvořili a nasadili do služby Azure Virtual Network. Spouštění Logic Apps v ISE je podobné spouštění Logic Apps v globální oblasti Azure, což znamená, že se vaše strategie zotavení po havárii může vztahovat i na oba scénáře. ISEs ale mají další okolnosti, jako je například konfigurace přístupu k prostředkům, které jsou k dispozici pouze pro ISEs.

> [!NOTE]
> Pokud vaše aplikace logiky funguje i s artefakty B2B, například obchodními partnery, smlouvami, schématy, mapami a certifikáty, které jsou uložené v účtu pro integraci, musí váš účet pro integraci i aplikace logiky určovat stejné umístění.

Tato strategie zotavení po havárii se zaměřuje na nastavení vaší primární aplikace logiky pro [*převzetí služeb při selhání*](https://en.wikipedia.org/wiki/Failover) v pohotovostním nebo zálohovacím programu aplikace logiky v alternativním umístění, kde je k dispozici i Azure Logic Apps. Tímto způsobem, pokud primární utrpí ztráty, přerušení nebo selhání, může sekundární aplikace trvat. Tato strategie vyžaduje, aby vaše sekundární aplikace logiky a závislé prostředky byly nasazené a připravené v alternativním umístění.

Pokud budete postupovat podle dobrých postupů DevOps, už k definování a nasazení aplikací logiky a jejich závislých prostředků používáte [šablony Azure Resource Manager](../azure-resource-manager/management/overview.md) . Šablony Správce prostředků poskytují možnost použít jednu definici nasazení a potom použít soubory parametrů k poskytnutí konfiguračních hodnot, které se mají použít pro každé umístění nasazení. Tato možnost znamená, že můžete nasadit stejnou aplikaci logiky do různých prostředí, například pro vývoj, testování a produkci. Stejnou aplikaci logiky můžete nasadit i do různých oblastí Azure nebo ISEs, které podporují strategie zotavení po havárii, které používají [spárované oblasti](../best-practices-availability-paired-regions.md).

Pro strategii převzetí služeb při selhání musí vaše aplikace logiky a umístění splňovat tyto požadavky:

* Sekundární instance aplikace logiky má přístup ke stejným aplikacím, službám a systémům jako primární instance aplikace logiky.

* Obě instance aplikace logiky mají stejný typ hostitele. To znamená, že obě instance se nasadí do oblastí v globálním víceklientském prostředí Azure, nebo se obě instance nasadí do ISEs, což umožní vašim Logic Apps přímý přístup k prostředkům ve službě Azure Virtual Network. Osvědčené postupy a další informace o spárovaných oblastech pro BCDR najdete v tématu [provozní kontinuita a zotavení po havárii (BCDR): spárované oblasti Azure](../best-practices-availability-paired-regions.md).

  Například primární i sekundární umístění musí být ISEs, když je primární aplikace logiky spuštěna v ISE a používá [konektory s ISE verzí](../connectors/apis-list.md#ise-connectors), akce HTTP pro volání prostředků ve službě Azure Virtual Network nebo obojí. V tomto scénáři musí vaše sekundární aplikace logiky mít také podobné nastavení jako primární aplikace logiky v sekundárním umístění.

  > [!NOTE]
  > V případě pokročilejších scénářů můžete kombinovat více tenantů Azure i ISE jako umístění. Ujistěte se však, že zvažujete a rozumíte [rozdílům mezi tím, jak aplikace logiky běží v ISE oproti více tenantů Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Pokud používáte ISEs, ujistěte se [, že jsou škálované nebo mají dostatečnou kapacitu](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) pro zpracování zatížení.

#### <a name="example-multi-tenant-azure"></a>Příklad: víceklientské Azure

Tento příklad ukazuje primární a sekundární instance aplikace logiky, které jsou nasazeny do samostatných oblastí v globálním více klientech Azure pro tento scénář. Jedna [šablona správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definuje obě instance aplikace logiky i závislé prostředky, které tyto aplikace logiky vyžadují. Samostatné soubory parametrů určují konfigurační hodnoty, které se mají použít pro každé umístění nasazení:

![Instance primárních a sekundárních aplikací logiky v různých umístěních](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Příklad: prostředí integrační služby

Tento příklad ukazuje předchozí primární a sekundární instance aplikace logiky, ale nasazené na samostatné ISEs. Jedna šablona Správce prostředků definuje obě instance aplikace logiky, závislé prostředky, které tyto aplikace logiky vyžadují, a ISEs jako umístění pro nasazení. Samostatné soubory parametrů definují konfigurační hodnoty, které se mají použít pro nasazení v každém umístění:

![Primární a sekundární aplikace logiky v různých umístěních](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Připojení k prostředkům

Azure Logic Apps poskytuje [integrované triggery a akce a také stovky spravovaných konektorů](../connectors/apis-list.md) , které může vaše aplikace logiky použít k práci s jinými aplikacemi, službami, systémy a dalšími prostředky, jako jsou účty Azure Storage, SQL Server databáze, pracovní nebo školní e-mailové účty atd. Pokud vaše aplikace logiky potřebuje k těmto prostředkům přístup, vytvoříte připojení, která ověřují přístup k těmto prostředkům. Každé připojení je samostatný prostředek Azure, který existuje v určitém umístění a nemůže ho používat prostředky v jiných umístěních.

Pro vaši strategii zotavení po havárii zvažte umístění, kde existují závislé prostředky relativní vzhledem k instancím vaší aplikace logiky:

* Vaše primární instance a závislé prostředky existují v různých umístěních. V takovém případě se vaše sekundární instance může připojit ke stejným závislým prostředkům nebo koncovým bodům. Měli byste však vytvořit připojení specificky pro vaši sekundární instanci. Pokud vaše primární umístění nebude k dispozici, vaše sekundární připojení nebudou ovlivněna.

  Předpokládejme například, že vaše primární aplikace logiky se připojuje k externí službě, jako je Salesforce. Dostupnost a umístění externí služby jsou obvykle nezávislé na dostupnosti vaší aplikace logiky. V takovém případě se vaše sekundární instance může připojit ke stejné službě, ale měla by mít své vlastní připojení.

* Jak primární instance, tak i závislé prostředky existují ve stejném umístění. V takovém případě by měly mít závislé prostředky zálohy nebo replikované verze v jiném umístění, aby vaše sekundární instance stále měla přístup k těmto prostředkům.

  Předpokládejme například, že se vaše primární aplikace logiky připojí ke službě, která je ve stejném umístění nebo oblasti, například Azure SQL Database. Pokud tato celá oblast nebude k dispozici, služba Azure SQL Database v této oblasti je také nejspíš nedostupná. V takovém případě byste chtěli, aby sekundární instance používala replikovanou nebo záložní databázi spolu se samostatným připojením k této databázi.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Místní brány dat

Pokud vaše aplikace logiky běží ve víceklientské architektuře Azure a potřebuje přístup k místním prostředkům, jako je například SQL Server databáze, je nutné nainstalovat místní [bránu dat](../logic-apps/logic-apps-gateway-install.md) do místního počítače. Pak můžete vytvořit prostředek brány dat v Azure Portal tak, aby aplikace logiky mohla používat bránu při vytváření připojení k prostředku.

Prostředek brány dat je přidružený k umístění nebo oblasti Azure stejným způsobem jako prostředek aplikace logiky. V strategii zotavení po havárii zajistěte, aby brána dat zůstala dostupná pro použití vaší aplikace logiky. Pokud máte více instalací brány, můžete [pro bránu povolit vysokou dostupnost](../logic-apps/logic-apps-gateway-install.md#high-availability) .

> [!NOTE]
> Pokud vaše aplikace logiky běží v prostředí ISE (Integration Service Environment) a používá pro místní zdroje dat jenom konektory ISE se správou verzí, nepotřebujete bránu dat, protože konektory ISE poskytují přímý přístup k místnímu prostředku.
>
> Pokud pro místní prostředek, který chcete, není k dispozici žádný konektor ISE, může vaše aplikace logiky stále vytvořit připojení pomocí konektoru, který není ISE, který běží v globálním více tenantů Azure, ne ve vašem ISE. Toto připojení ale vyžaduje místní bránu dat.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Role aktivní-aktivní versus aktivní – pasivní

Můžete nastavit primární a sekundární umístění, aby instance aplikace logiky v těchto umístěních mohly hrát tyto role:

| Role primárního-sekundárního | Description |
|------------------------|-------------|
| *Aktivní – aktivní* | Primární a sekundární instance aplikace logiky v obou umístěních aktivně zpracovávají požadavky následujícími způsoby: <p><p>- *Vyrovnávání zatížení*: obě instance můžou na každou instanci naslouchat i vyrovnávání zatížení, a to v případě potřeby. <p>- *Konkurenční spotřebitelé*: obě instance můžou fungovat jako konkurenční spotřebitelé, takže instance budou soutěžit na zprávy z fronty. Pokud dojde k selhání jedné instance, převezme další instance úlohu. |
| *Aktivní – pasivní* | Primární instance aplikace logiky aktivně zpracovává celou úlohu, zatímco sekundární instance je pasivní (zakázaná nebo neaktivní). Sekundární čeká na signál, že primární je nedostupný nebo nefunguje kvůli přerušení nebo selhání a přebírá úlohu jako aktivní instanci. |
| Vrchní | Některé Logic Apps hrají roli aktivní-aktivní, zatímco jiné aplikace logiky hrají roli aktivní-pasivní. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Příklady aktivní – aktivní

Tyto příklady znázorňují nastavení aktivní-aktivní, kde obě instance aplikace logiky aktivně zpracovávají požadavky nebo zprávy. Některé další systémy nebo služba distribuuje požadavky nebo zprávy mezi instancemi, například jednu z těchto možností:

* "Fyzický" Nástroj pro vyrovnávání zatížení, jako je například hardware, který směruje provoz

* "Měkký" Nástroj pro vyrovnávání zatížení, jako je [Azure Load Balancer](../load-balancer/load-balancer-overview.md) nebo [Azure API Management](../api-management/api-management-key-concepts.md). Pomocí API Management můžete určit zásady, které určují, jak vyrovnávat zatížení příchozího provozu. Nebo můžete použít službu, která podporuje sledování stavu, například [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  I když tento příklad primárně ukazuje Azure Load Balancer, můžete použít možnost, která nejlépe vyhovuje potřebám vašeho scénáře:

  ![Instalační program aktivní-aktivní, který používá nástroj pro vyrovnávání zatížení nebo stavovou službu](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Každá instance aplikace logiky funguje jako příjemce a obě instance soutěží o zprávy z fronty:

  ![Instalační program aktivní-aktivní, který používá konkurenční příjemce](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Příklady aktivních – pasivní

Tento příklad ukazuje nastavení aktivní – pasivní, kde je primární instance aplikace logiky aktivní na jednom místě, zatímco sekundární instance zůstane neaktivní v jiném umístění. Pokud dojde k přerušení nebo selhání v primárním prostředí, může operátor spustit skript, který aktivuje sekundární objekt pro převzetí úlohy.

![Nastavení "aktivní – pasivní", které používá konkurenční příjemce](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Kombinace s aktivní-aktivní a aktivní – pasivní

Tento příklad ukazuje kombinované nastavení, kde primární umístění má obě instance aplikace logiky, zatímco sekundární umístění má instance aplikace logiky s aktivním režimem. Pokud dojde k narušení nebo selhání v primárním umístění, aktivní aplikace logiky v sekundárním umístění, které už zpracovává částečnou úlohu, může převzít celou úlohu.

* V primárním umístění naslouchá aktivní aplikace logiky Azure Service Bus frontě zpráv, zatímco jiná aktivní aplikace logiky kontroluje e-maily pomocí triggeru cyklického dotazování Office 365 Outlook.

* V sekundárním umístění funguje aktivní aplikace logiky s aplikací logiky v primárním umístění tím, že naslouchá a soutěží o zprávy ze stejné Service Bus fronty. Mezitím neaktivní neaktivní aplikace logiky počká na pohotovostní režim, aby kontrolovala e-maily, když je primární umístění nedostupné, ale je *zakázané* , aby se předešlo přečtení e-mailů

![Kombinace "aktivní-pasivní" a "aktivní-pasivní", která používá triggery opakování](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stav a historie aplikace logiky

Když se vaše aplikace logiky spustí a začne běžet, stav aplikace je uložený ve stejném umístění, kde se aplikace spustila a je nepřenosná do jiného umístění. Pokud dojde k selhání nebo přerušení, všechny probíhající instance pracovního postupu budou opuštěny. Když máte nastavené primární a sekundární umístění, začnou se nové instance pracovního postupu spouštět v sekundárním umístění.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Snížit opuštěné instance v průběhu

Chcete-li minimalizovat počet nedokončených instancí pracovních postupů, můžete si vybrat z různých vzorů zpráv, které lze implementovat, například:

* [Vzor fixního směrovacího lístku](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Tento model podnikové zprávy rozdělí obchodní proces do menších fází. Pro každou fázi nastavíte aplikaci logiky, která zpracovává úlohu pro danou fázi. Pro komunikaci mezi sebou vaše aplikace logiky používá protokol asynchronního zasílání zpráv, například Azure Service Bus fronty nebo témata. Pokud rozdělíte proces do menších fází, snížíte tím počet obchodních procesů, které se mohou zablokovat v neúspěšné instanci aplikace logiky. Obecnější informace o tomto modelu najdete v tématu [Enterprise Integration Patterns-Routing list](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Tento příklad ukazuje vzor směrovacího směrovacího místa, kde každá aplikace logiky představuje fázi a používá Service Bus frontu ke komunikaci s další aplikací logiky v procesu.

  ![Rozdělení obchodního procesu na fáze reprezentované Logic Apps, které vzájemně komunikují pomocí Azure Service Bus fronty](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Pokud primární i sekundární instance aplikace logiky sledují stejný vzor směrování ve svých místech, můžete [model konkurenčních příjemců](/azure/architecture/patterns/competing-consumers) implementovat nastavením [rolí aktivních-Active](#roles) pro tyto instance.

* [Vzor správce procesů (zprostředkovatel)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Náhled bez vzoru časového limitu](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Přístup k historii aktivačních událostí a spuštění

Pokud chcete získat další informace o spuštěných pracovních postupech vaší aplikace logiky, můžete si prohlédnout aktivační událost a historii spuštění aplikace. Historie spouštění historie aplikace logiky je uložená ve stejném umístění nebo oblasti, kde se aplikace logiky spustila, což znamená, že tuto historii nemůžete migrovat na jiné místo. V případě převzetí služeb při selhání primární instancí na sekundární instanci můžete přistupovat pouze k triggerům každé instance a spustit historii v příslušných umístěních, kde byly tyto instance spuštěny. Můžete ale získat informace o umístění – nezávislá informace o historii vaší aplikace logiky tím, že nastavíte aplikace logiky tak, aby odesílaly diagnostické události do pracovního prostoru Azure Log Analytics. Pak můžete zkontrolovat stav a historii napříč Logic Apps, které běží na více místech.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Doprovodné materiály k typu triggeru

Typ triggeru, který používáte ve svých aplikacích logiky, určuje možnosti, jak můžete nastavit aplikace logiky napříč umístěními v strategii zotavení po havárii. Tady jsou dostupné typy triggerů, které můžete použít ve službě Logic Apps:

* [Trigger opakování](#recurrence-trigger)
* [Aktivační událost cyklického dotazování](#polling-trigger)
* [Aktivační událost žádosti](#request-trigger)
* [Trigger Webhooku](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger opakování

Aktivační událost **opakování** je nezávislá na konkrétní službě nebo koncovém bodu a aktivuje se výhradně v závislosti na zadaném plánu a dalších kritériích, například:

* Pevná frekvence a interval, například každých 10 minut
* Pokročilejší plán, například poslední pondělí v měsíci v 5:00. odp.

Když se aplikace logiky spustí s triggerem opakování, budete muset nastavit primární a sekundární instance aplikace logiky s [rolemi aktivní – pasivní](#roles). Aby se snížila *Doba obnovení* (RTO), která odkazuje na dobu trvání pro obnovení obchodního procesu po přerušení nebo havárii, můžete nastavit instance aplikace logiky s kombinací [rolí aktivních](#roles) a [pasivních aktivních rolí](#roles). V tomto nastavení rozdělíte plán napříč umístěními.

Předpokládejme například, že máte aplikaci logiky, která musí běžet každých 10 minut. Můžete nastavit aplikace logiky a umístění tak, aby v případě, že primární umístění nebude k dispozici, sekundární umístění mohlo převzít práci:

![Kombinace "aktivní-pasivní" a "pasivní-aktivní", která používá triggery opakování](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* V primárním umístění nastavte [role aktivní – pasivní](#roles) pro tyto aplikace logiky:

  * U *aktivní* povolené aplikace logiky nastavte aktivační událost opakování na začátku hodiny a zopakujte každých 20 minut, například 9:00 dop. 9:20 DOP. a tak dále.

  * V případě *pasivní* zakázané aplikace logiky nastavte Trigger opakování na stejný plán, ale začněte 10 minutami po hodinách a zopakujte každých 20 minut, například 9:10 dop. 9:30 dop. a tak dále.

* V sekundárním umístění nastavte [pasivní – aktivní](#roles) pro tyto aplikace logiky:

  * U *pasivní* zakázané aplikace logiky nastavte aktivační událost opakování na stejný plán jako aktivní aplikace logiky v primárním umístění, které je v horní části hodiny, a pak každých 20 minut, například 9:00 dop. 9:10 DOP. a tak dále.

  * U *aktivní* povolené aplikace logiky nastavte Trigger opakování na stejný plán jako v pasivní aplikaci logiky v primárním umístění, který začíná 10 minut po hodině a opakuje se každých 20 minut, například 9:10 dop. 9:20 a tak dále.

Když teď dojde k výpadku události v primárním umístění, aktivujte aplikaci pasivní aplikace logiky v alternativním umístění. To znamená, že pokud při hledání tohoto selhání dojde k překročení času, toto nastavení omezí počet zmeškaných opakování během této prodlevy.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Aktivační událost cyklického dotazování

K pravidelné kontrole, jestli jsou nová data pro zpracování dostupná z konkrétní služby nebo koncového bodu, může vaše aplikace logiky použít Trigger *cyklického dotazování* , který opakovaně volá službu nebo koncový bod na základě pevného plánu opakování. Data, která služba nebo koncový bod poskytují, můžou mít některý z těchto typů:

* Statická data, která popisují data, která jsou vždy k dispozici pro čtení
* Nestálá data, která popisují data, která již nejsou k dispozici po čtení

Abyste se vyhnuli opakovanému čtení stejných dat, potřebuje aplikace logiky pamatovat si, která data byla dříve čtena, a to buď na straně klienta, nebo na straně serveru, služby nebo systému.

* Logic Apps, které pracují se stavem na straně klienta, používají triggery, které můžou udržovat stav.

  Například Trigger, který čte novou zprávu z e-mailové schránky, vyžaduje, aby Trigger mohl pamatovat naposledy přečtenou zprávu. Tímto způsobem aktivační událost spustí aplikaci logiky pouze v případě, že dorazí do další nepřečtené zprávy.

* Aplikace logiky, které pracují se serverem, službou nebo stavem na straně systému, používají hodnoty vlastností nebo nastavení, které jsou na straně serveru, služby nebo systému.

  Například Trigger založený na dotazech, který čte řádek z databáze, vyžaduje, aby řádek měl `isRead` sloupec, který je nastaven na hodnotu `FALSE` . Pokaždé, když Trigger Načte řádek, aplikace logiky tento řádek aktualizuje změnou `isRead` sloupce z `FALSE` na `TRUE` .

  Tento přístup na straně serveru funguje podobně jako u Service Bus front nebo témat s sémantikou řazení do fronty, kde Trigger může číst a uzamknout zprávu, zatímco aplikace logiky zpracovává zprávu. Když aplikace logiky dokončí zpracování, aktivační událost odstraní zprávu z fronty nebo tématu.

V perspektivě zotavení po havárii se při nastavování primárních a sekundárních instancí aplikace logiky ujistěte, že máte účet pro toto chování na základě toho, jestli vaše aplikace logiky sleduje stav na straně klienta nebo na straně serveru:

* V případě aplikace logiky, která funguje se stavem na straně klienta, se ujistěte, že aplikace logiky nečte stejnou zprávu více než jednou. Aktivní instance aplikace logiky může mít v daném čase pouze jedno umístění. Ujistěte se, že je instance aplikace logiky v alternativním umístění neaktivní nebo zakázaná, dokud nedojde k převzetí primární instance do alternativního umístění.

  Například aktivační událost sady Office 365 Outlook udržuje stav na straně klienta a sleduje časové razítko posledního přečteného e-mailu, aby se předešlo čtení duplicitního.

* U aplikace logiky, která spolupracuje se stavem na straně serveru, můžete nastavit instance aplikace logiky tak, aby hrály [role typu aktivní – aktivní](#roles) , kde fungují jako konkurenční spotřebitelé nebo [role aktivní – pasivní](#roles) , kde alternativní instance čeká na převzetí služeb při selhání primární instancí do alternativního umístění.

  Například čtení z fronty zpráv, jako je Azure Service Bus fronta, používá stav na straně serveru, protože služba Řízení front zpráv udržuje zámky na zprávách, aby ostatní klienti nemohli číst stejné zprávy.

  > [!NOTE]
  > Pokud vaše aplikace logiky potřebuje číst zprávy v určitém pořadí, například z fronty Service Bus, můžete použít model konkurenčního příjemce, ale pouze v kombinaci s Service Bus relacemi, které se také označují jako [ *sekvenční convoy* vzor](/azure/architecture/patterns/sequential-convoy). V opačném případě musíte nastavit instance aplikace logiky s rolemi aktivní – pasivní.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Aktivační událost žádosti

Trigger **žádosti** umožňuje, aby se aplikace logiky vyvolala z jiných aplikací, služeb a systémů a obvykle se používá k poskytnutí těchto možností:

* Přímá REST API pro vaši aplikaci logiky, kterou můžou ostatní volat

  Například použijte Trigger Request pro spuštění aplikace logiky, aby ostatní Logic Apps mohli zavolat Trigger pomocí akce **volání pracovního postupu Logic Apps** .

* [Webhook](#webhook-trigger) nebo mechanismus zpětného volání pro vaši aplikaci logiky

* Způsob, jakým lze ručně spustit uživatelské operace nebo rutiny pro volání aplikace logiky, například pomocí skriptu prostředí PowerShell, který provede konkrétní úlohu

V perspektivě zotavení po havárii je Trigger požadavku pasivním přijímačem, protože aplikace logiky neprovádí žádnou práci a čeká, dokud některá jiná služba nebo systém explicitně nevolá Trigger. Jako pasivní koncový bod můžete nastavit primární a sekundární instance následujícími způsoby:

* [Aktivní – aktivní](#roles): obě instance aktivně zpracovávají požadavky nebo volání. Volající nebo směrovač vyrovnává nebo distribuuje provoz mezi těmito instancemi.

* [Aktivní – pasivní](#roles): je aktivní jenom primární instance a zpracovává veškerou práci, zatímco sekundární instance čeká na přerušení nebo selhání primárního prostředí. Volající nebo směrovač určí, kdy se má volat sekundární instance.

Jako doporučená architektura můžete použít Azure API Management jako proxy pro aplikace logiky, které používají triggery požadavků. API Management poskytuje [integrovanou odolnost mezi různými oblastmi a schopnost směrovat provoz napříč několika koncovými body](../api-management/api-management-howto-deploy-multi-region.md).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Trigger Webhooku

Trigger *Webhooku* poskytuje možnosti vaší aplikace logiky přihlásit se k odběru služby PŘEDÁNÍM *adresy URL zpětného volání* této službě. Vaše aplikace logiky pak může naslouchat a čekat na to, aby se na tomto koncovém bodu služby nastalo konkrétní událost. Když dojde k události, služba zavolá Trigger Webhooku pomocí adresy URL zpětného volání, které pak spustí aplikaci logiky. Pokud je tato možnost povolená, Trigger Webhooku se přihlásí k odběru služby. Pokud je zakázaná, aktivační událost se odhlásí od služby.

V perspektivě zotavení po havárii nastavte primární a sekundární instance, které využívají triggery webhooků, k přehrání rolí aktivní – pasivní, protože z předplaceného koncového bodu by měly být přijímány události nebo zprávy jenom jedna instance.

## <a name="assess-primary-instance-health"></a>Vyhodnotit stav primární instance

Aby vaše strategie zotavení po havárii fungovala, vaše řešení potřebuje způsob, jak provádět tyto úlohy:

* [Ověřit dostupnost primární instance](#check-primary-availability)
* [Monitorovat stav primární instance](#monitor-primary-health)
* [Aktivovat sekundární instanci](#activate-secondary)

Tato část popisuje jedno řešení, které můžete použít přímo nebo jako základ pro vlastní návrh. Tady je přehled vizuálního přehledu pro toto řešení:

![Vytvoření aplikace sledovací logiky, která monitoruje aplikaci logiky kontroly stavu v primárním umístění](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Zkontroluje dostupnost primární instance.

Chcete-li zjistit, zda je primární instance k dispozici, spuštěná a schopná pracovat, můžete vytvořit aplikaci logiky "kontroly stavu", která je ve stejném umístění jako primární instance. Tuto aplikaci pro kontrolu stavu pak můžete zavolat z alternativního umístění. Pokud aplikace pro kontrolu stavu úspěšně odpoví, je základní infrastruktura služby Azure Logic Apps v této oblasti k dispozici a funguje. Pokud aplikace pro kontrolu stavu nereaguje, můžete předpokládat, že umístění již není v pořádku.

Pro tuto úlohu vytvořte základní aplikaci logiky pro kontrolu stavu, která provede tyto úlohy:

1. Přijme volání z aplikace pro sledovací zařízení pomocí triggeru žádosti.

1. Odpověď se stavem, který označuje, zda je zaškrtnutá aplikace logiky stále fungovat pomocí akce Response.

   > [!IMPORTANT]
   > Aplikace logiky kontroly stavu musí použít akci odpovědi, aby aplikace reagovala synchronně, ne asynchronně.

1. Pokud chcete dále určit, jestli je primární umístění v pořádku, můžete posoudit stav všech dalších služeb, které v tomto umístění komunikují s cílovou aplikací logiky. Stačí rozšířit aplikaci logiky pro kontrolu stavu a vyhodnotit stav těchto dalších služeb.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Vytvoření aplikace logiky sledovacích aplikací

Pokud chcete monitorovat stav primární instance a volat aplikaci logiky kontroly stavu, vytvořte v *alternativním umístění* aplikaci logiky pro "sledovací". Můžete například nastavit sledovací logiku aplikace, takže pokud volání logiky kontroly stavu selže, sledovací zařízení může Odeslat výstrahu vašemu týmu provozu, aby mohla prozkoumat selhání a proč primární instance nereaguje.

> [!IMPORTANT]
> Ujistěte se, že je vaše aplikace logiky sledovacích aplikací v *umístění, které se liší od primárního umístění*. Pokud služba Logic Apps v primárním umístění dochází k problémům, vaše aplikace sledovací logiky možná není spuštěná.

Pro tuto úlohu v sekundárním umístění vytvořte aplikaci sledovací logiky, která provede tyto úlohy:

1. Spuštění na základě pevného nebo plánovaného opakování pomocí triggeru opakování.

   Opakování můžete nastavit na hodnotu, která je nižší než úroveň tolerance pro cíl doby obnovení (RTO).

1. Pomocí akce HTTP zavolejte v primárním umístění aplikaci logiky kontroly stavu, například:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktivace sekundární instance

K automatické aktivaci sekundární instance můžete vytvořit aplikaci logiky, která volá rozhraní API pro správu, jako je [Azure Resource Manager konektor](/connectors/arm/) , který aktivuje příslušné Logic Apps v sekundárním umístění. Po určitém počtu selhání můžete rozšířit aplikaci pro sledovací prostředí, aby volala tuto aplikaci logiky aktivace.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Shromažďování diagnostických dat

Můžete nastavit protokolování pro vaši aplikaci logiky a odeslat výsledná diagnostická data službám, jako je Azure Storage, Azure Event Hubs a Azure Log Analytics pro další zpracování a zpracování.

* Pokud chcete tato data používat s Azure Log Analytics, můžete data zpřístupnit pro primární i sekundární umístění tím, že nastavíte **nastavení diagnostiky** aplikace logiky a odešlete data do více pracovních prostorů Log Analytics. Další informace najdete v tématech [nastavení protokolů Azure monitor a shromažďování diagnostických dat pro Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md).

* Pokud chcete data odeslat Azure Storage nebo Azure Event Hubs, můžete data zpřístupnit pro primární i sekundární umístění tím, že nastavíte geografickou redundanci. Další informace najdete v těchto článcích:<p>

  * [Azure Blob Storage zotavení po havárii a převzetí služeb při selhání účtu](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geografické zotavení po havárii](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Další kroky

* [Přehled odolnosti pro Azure](/azure/architecture/framework/resiliency/overview)
* [Kontrolní seznam k odolnosti pro konkrétní služby Azure](/azure/architecture/checklist/resiliency-per-service)
* [Správa dat pro odolnost v Azure](/azure/architecture/framework/resiliency/data-management)
* [Zálohování a zotavení po havárii pro aplikace Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Zotavení z přerušení služeb na úrovni celé oblasti](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Smlouvy o úrovni služeb Microsoftu (SLA) pro služby Azure](https://azure.microsoft.com/support/legal/sla/)
