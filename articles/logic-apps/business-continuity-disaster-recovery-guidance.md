---
title: Provozní kontinuita a zotavení po havárii
description: Navrhněte strategii pro ochranu dat, rychle se zotavit z rušivých událostí, obnovte prostředky vyžadované kritickými obchodními funkcemi a udržujte kontinuitu podnikání pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437700"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Kontinuita podnikání a zotavení po havárii pro Azure Logic Apps

Chcete-li snížit dopad a dopady, které mají nepředvídatelné události na vaši firmu a zákazníky, ujistěte se, že máte řešení [ *zotavení po havárii(DR),* ](https://en.wikipedia.org/wiki/Disaster_recovery) abyste mohli chránit data, rychle obnovit prostředky, které podporují důležité obchodní funkce, a udržovat operace spuštěné za účelem zachování [ *kontinuity podnikání* (BC).](https://en.wikipedia.org/wiki/Business_continuity_planning) Přerušení mohou například zahrnovat výpadky, ztráty v základní infrastruktuře nebo součásti, jako jsou prostředky úložiště, sítě nebo výpočetní prostředky, neopravitelné selhání aplikací nebo dokonce úplná ztráta datového centra. Díky připravenosti řešení pro kontinuitu provozu a zotavení po havárii (BCDR) může váš podnik nebo organizace rychleji reagovat na přerušení, plánované nebo neplánované a zkrátit prostoje pro vaše zákazníky.

Tento článek obsahuje pokyny bcdr a strategie, které můžete použít při vytváření automatizovaných pracovních postupů pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Pracovní postupy aplikace logiky vám pomohou snadněji integrovat a organizovat data mezi aplikacemi, cloudovými službami a místními systémy snížením počtu kódu, který musíte napsat. Při plánování pro BCDR, ujistěte se, že považujete nejen vaše aplikace logiky, ale také tyto prostředky Azure, které používáte s aplikacemi logiky:

* [Připojení,](../connectors/apis-list.md) která vytvoříte z aplikací logiky do jiných aplikací, služeb a systémů. Další informace naleznete v [tématu Připojení k prostředkům](#resource-connections) dále v tomto tématu.

* [Místní datové brány,](../logic-apps/logic-apps-gateway-connection.md) což jsou prostředky Azure, které vytvoříte a použijete ve svých aplikacích logiky pro přístup k datům v místních systémech. Každý prostředek brány představuje samostatnou [instalaci brány dat](../logic-apps/logic-apps-gateway-install.md) v místním počítači. Další informace naleznete [v tématu Místní brány dat](#on-premises-data-gateways) dále v tomto tématu.

* [Účty integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kde definujete a ukládáte artefakty, které aplikace logiky používají pro scénáře [podnikové integrace (B2B).](../logic-apps/logic-apps-enterprise-integration-overview.md) Můžete například [nastavit zotavení po havárii mezi oblastmi pro účty integrace](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Prostředí integračních služeb (ISEs),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) kde můžete vytvářet aplikace logiky, které běží v izolované instanci runtime Logic Apps v rámci virtuální sítě Azure. Tyto aplikace logiky pak můžete přistupovat k prostředkům, které jsou chráněny za bránou firewall v této virtuální síti.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primární a sekundární lokality

Každá aplikace logiky musí zadat umístění, které chcete použít pro nasazení. Toto umístění je buď veřejná oblast v globálním víceklientském Azure, jako je "Západní USA", nebo prostředí integrační služby (ISE), které jste dříve vytvořili a nasadili do virtuální sítě Azure. Spuštění aplikací logiky v ise je podobné jako spuštění aplikací logiky v globální oblasti Azure, což znamená, že vaše strategie zotavení po havárii se může vztahovat na oba scénáře. Ises však mají další aspekty, jako je konfigurace přístupu k prostředkům, které jsou k dispozici pouze pro ises.

> [!NOTE]
> Pokud vaše aplikace logiky také pracuje s artefakty B2B, jako jsou obchodní partneři, smlouvy, schémata, mapy a certifikáty, které jsou uloženy v účtu integrace, musí váš účet integrace a aplikace logiky zadat stejné umístění.

Tato strategie zotavení po havárii se zaměřuje na nastavení primární aplikace logiky pro [*převzetí služeb při selhání*](https://en.wikipedia.org/wiki/Failover) do pohotovostní nebo záložní aplikace logiky v alternativním umístění, kde je k dispozici také Azure Logic Apps. Tímto způsobem, pokud primární utrpí ztráty, poruchy nebo selhání, sekundární může vzít na práci. Tato strategie vyžaduje, aby vaše aplikace sekundární logiky a závislé prostředky jsou již nasazeny a připraveny v alternativním umístění.

Pokud se řídíte osvědčenými postupy DevOps, už teď používáte [šablony Azure Resource Manageru](../azure-resource-manager/management/overview.md) k definování a nasazení aplikací logiky a jejich závislých prostředků. Šablony Správce prostředků umožňují použít jednu definici nasazení a potom pomocí souborů parametrů poskytnout hodnoty konfigurace, které se mají použít pro každý cíl nasazení. Tato funkce znamená, že můžete nasadit stejnou aplikaci logiky do různých prostředí, například vývoj, testování a produkční. Můžete také nasadit stejnou aplikaci logiky do různých oblastí Azure nebo ISEs, který podporuje strategie zotavení po havárii, které používají [spárované oblasti](../best-practices-availability-paired-regions.md).

Pro strategii převzetí služeb při selhání musí aplikace a umístění logiky splňovat tyto požadavky:

* Instance aplikace sekundární logiky má přístup ke stejným aplikacím, službám a systémům jako instance primární aplikace logiky.

* Obě instance aplikace logiky mají stejný typ hostitele. Takže buď se obě instance nasazují do oblastí v globálním víceklientském Azure, nebo se obě instance nasazují do ises, které umožňují vašim aplikacím logiky přímý přístup k prostředkům ve virtuální síti Azure. Doporučené postupy a další informace o spárovaných oblastech pro BCDR najdete v [tématu Kontinuita podnikání a zotavení po havárii (BCDR): Spárované oblasti Azure](../best-practices-availability-paired-regions.md).

  Například primární a sekundární umístění musí být ISEs při spuštění primární aplikace logiky v ise a používá [ise verze konektory](../connectors/apis-list.md#ise-connectors), akce HTTP pro volání prostředků ve virtuální síti Azure nebo obojí. V tomto scénáři aplikace sekundární logiky musí mít také podobné nastavení v sekundárním umístění jako primární aplikace logiky.

  > [!NOTE]
  > Pro pokročilejší scénáře můžete kombinovat víceklientské Azure a ISE jako umístění. Ujistěte se však, že zvažte a pochopit [rozdíly mezi jak aplikace logiky běží v ISE versus víceklientské Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Pokud používáte ises, [ujistěte se, že jsou škálovány nebo mají dostatečnou kapacitu](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) pro zpracování zatížení.

#### <a name="example-multi-tenant-azure"></a>Příklad: Azure s více tenanty

Tento příklad ukazuje instance aplikace primární a sekundární logiky, které se nasazují do samostatných oblastí v globálním víceklientském Azure pro tento scénář. Jedna [šablona Správce prostředků](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definuje instance aplikace logiky i závislé prostředky vyžadované těmito aplikacemi logiky. Samostatné soubory parametrů určují hodnoty konfigurace, které se mají použít pro každé umístění nasazení:

![Instance aplikace primární a sekundární logiky v samostatných umístěních](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Příklad: Prostředí integračních služeb

Tento příklad ukazuje předchozí instance aplikace primární a sekundární logiky, ale nasazené do samostatných ises. Jedna šablona Správce prostředků definuje jak instance aplikace logiky, závislé prostředky vyžadované těmito aplikacemi logiky a ises jako umístění nasazení. Samostatné soubory parametrů definují hodnoty konfigurace, které mají být používány pro nasazení v každém umístění:

![Primární a sekundární aplikace logiky na různých místech](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Připojení k prostředkům

Azure Logic Apps poskytuje [integrované aktivační události a akce a stovky spravovaných konektorů,](../connectors/apis-list.md) které vaše aplikace logiky může používat pro práci s jinými aplikacemi, službami, systémy a dalšími prostředky, jako jsou účty Azure Storage, databáze SQL Serveru, e-mailové účty Office 365 Outlook a tak dále. Pokud vaše aplikace logiky potřebuje přístup k těmto prostředkům, vytvořte připojení, která ověřují přístup k těmto prostředkům. Každé připojení je samostatný prostředek Azure, který existuje v určitém umístění a nemůže být použit prostředky v jiných umístěních.

Pro strategii zotavení po havárii zvažte umístění, kde existují závislé prostředky vzhledem k instance aplikace logiky:

* Primární instance a závislé prostředky existují v různých umístěních. V takovém případě se sekundární instance může připojit ke stejným závislým prostředkům nebo koncovým bodům. Měli byste však vytvořit připojení speciálně pro sekundární instanci. Tímto způsobem, pokud vaše primární umístění přestane být k dispozici, sekundární připojení nejsou ovlivněny.

  Předpokládejme například, že vaše primární aplikace logiky se připojí k externí službě, jako je Salesforce. Dostupnost a umístění externí služby jsou obvykle nezávislé na dostupnosti aplikace logiky. V takovém případě se sekundární instance může připojit ke stejné službě, ale měla by mít vlastní připojení.

* Primární instance i závislé prostředky existují ve stejném umístění. V takovém případě by závislé prostředky měly mít zálohy nebo replikované verze v jiném umístění, aby sekundární instance měla stále přístup k těmto prostředkům.

  Předpokládejme například, že vaše primární aplikace logiky se připojí ke službě, která je ve stejném umístění nebo oblasti, například Azure SQL Database. Pokud celá tato oblast přestane být k dispozici, služba Azure SQL Database v této oblasti je také pravděpodobně nedostupná. V takovém případě byste chtěli, aby sekundární instance používala replikovanou nebo záložní databázi spolu se samostatným připojením k této databázi.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Místní brány dat

Pokud vaše aplikace logiky běží ve víceklientské Azure a potřebuje přístup k místním prostředkům, jako jsou databáze SQL Serveru, je třeba nainstalovat [místní bránu dat](../logic-apps/logic-apps-gateway-install.md) v místním počítači. Potom můžete vytvořit prostředek brány dat na webu Azure Portal tak, aby vaše aplikace logiky můžete použít bránu při vytváření připojení k prostředku.

Prostředek brány dat je přidružený k umístění nebo oblasti Azure, stejně jako prostředek aplikace logiky. Ve strategii zotavení po havárii se ujistěte, že brána dat zůstane k dispozici pro aplikaci logiky k použití. Vysokou dostupnost brány můžete [povolit,](../logic-apps/logic-apps-gateway-install.md#high-availability) pokud máte více instalací brány.

> [!NOTE]
> Pokud vaše aplikace logiky běží v prostředí služby integrace (ISE) a používá pouze konektory s verzí ISE pro místní zdroje dat, nepotřebujete bránu dat, protože konektory ISE poskytují přímý přístup k místnímu prostředku.
>
> Pokud žádný konektor s verzí ISE je k dispozici pro místní prostředek, který chcete, vaše aplikace logiky můžete stále vytvořit připojení pomocí konektoru bez ISE, který běží v globálním víceklientského Azure, nikoli vaše ISE. Toto připojení však vyžaduje místní bránu dat.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Role aktivní-aktivní versus aktivní pasivní

Můžete nastavit primární a sekundární umístění tak, aby instance aplikace logiky v těchto umístěních mohly hrát tyto role:

| Primární sekundární role | Popis |
|------------------------|-------------|
| *Aktivní-aktivní* | Instance aplikace primární a sekundární logiky v obou umístěních aktivně zpracovávají požadavky podle následujících vzorů: <p><p>- *Vyrovnávání zatížení*: Můžete mít obě instance poslouchat koncový bod a zatížení provoz každé instance podle potřeby. <p>- *Konkurenční spotřebitelé*: Můžete mít obě instance fungovat jako konkurenční spotřebitelé tak, aby instance soutěžit o zprávy z fronty. Pokud jedna instance selže, druhá instance převezme zatížení. |
| *Aktivní-pasivní* | Instance primární aplikace logiky aktivně zpracovává celou úlohu, zatímco sekundární instance je pasivní (zakázáno nebo neaktivní). Sekundární čeká na signál, že primární není k dispozici nebo nefunguje z důvodu přerušení nebo selhání a převezme zatížení jako aktivní instance. |
| Kombinace | Některé aplikace logiky hrají roli aktivní aktivní, zatímco jiné aplikace logiky hrají roli aktivní pasivní. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Příklady aktivní-aktivní

Tyto příklady ukazují aktivní aktivní nastavení, kde instance aplikace logiky aktivně zpracovávají požadavky nebo zprávy. Některé jiné systémové nebo služby distribuují požadavky nebo zprávy mezi instancemi, například jednu z těchto možností:

* "Fyzický" systém vyrovnávání zatížení, například hardware, který směruje provoz

* "Měkký" vyvyřič zatížení, jako je [Azure Load Balancer](../load-balancer/load-balancer-overview.md) nebo [Azure API Management](../api-management/api-management-key-concepts.md). Pomocí správy rozhraní API můžete určit zásady, které určují, jak vyvažovat příchozí provoz. Nebo můžete použít službu, která podporuje sledování stavu, například [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  I když tento příklad primárně ukazuje Azure Balancer, můžete použít možnost, která nejlépe vyhovuje potřebám vašeho scénáře:

  ![Nastavení "Aktivní aktivní", které používá systém vyrovnávání zatížení nebo stavovou službu](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Každá instance aplikace logiky funguje jako příjemce a obě instance soutěží o zprávy z fronty:

  !["Aktivní aktivní" nastavení, které používá "konkurenční spotřebitele"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Příklady aktivní-pasivní

Tento příklad ukazuje nastavení aktivní pasivní, kde je aktivní instance primární aplikace logiky v jednom umístění, zatímco sekundární instance zůstává neaktivní v jiném umístění. Pokud primární dojde k přerušení nebo selhání, můžete mít operátor spustit skript, který aktivuje sekundární převzít zatížení.

!["Active-pasivní" nastavení, které používá "konkurenční spotřebitele"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Kombinace s aktivním a aktivním-pasivním

Tento příklad ukazuje kombinované nastavení, kde primární umístění má obě instance aktivní aplikace logiky, zatímco sekundární umístění má instance aplikace logiky aktivní pasivní. Pokud primární umístění dojde k přerušení nebo selhání, aktivní aplikace logiky v sekundárním umístění, který je již zpracovává částečné zatížení, můžete převzít celé zatížení.

* V primárním umístění aktivní aplikace logiky naslouchá frontě Azure Service Bus pro zprávy, zatímco jiná aktivní aplikace logiky kontroluje e-maily pomocí aktivační události dotazování Office 365 Outlook.

* V sekundárním umístění aktivní aplikace logiky pracuje s aplikací logiky v primárním umístění nasloucháním a soutěží o zprávy ze stejné fronty service bus. Mezitím pasivní neaktivní aplikace logiky čeká v pohotovostním režimu, aby zkontrolovala e-maily, když primární umístění není k dispozici, ale je *zakázáno,* aby se zabránilo opětovnému čtení e-mailů.

![Kombinace "Aktivní-pasivní" a "aktivní-pasivní", která používá aktivační události opakování](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stav a historie aplikace logiky

Když se aplikace logiky aktivuje a spustí se, stav aplikace se uloží ve stejném umístění, kde byla aplikace spuštěna, a je nepřenosná do jiného umístění. Pokud dojde k selhání nebo narušení, všechny instanci pracovního postupu jsou opuštěny. Když máte nastaveno primární a sekundární umístění, začnou se v sekundárním umístění spuštěny nové instance pracovního postupu.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Snížit opuštěné instanci

Chcete-li minimalizovat počet opuštěných instancí pracovního postupu, můžete si vybrat z různých vzorů zpráv, které můžete implementovat, například:

* [Opraven směrovací vzorek směrové prasniny](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Tento vzor podnikové zprávy, který rozděluje obchodní proces do menších fází. Pro každou fázi nastavíte aplikaci logiky, která zpracovává zatížení pro tuto fázi. Ke vzájemné komunikaci používají aplikace logiky asynchronní protokol zasílání zpráv, jako jsou fronty nebo témata azure service bus. Když rozdělíte proces do menších fází, snížíte počet obchodních procesů, které se mohou zaseknout v instanci aplikace logiky, která selhala. Obecnější informace o tomto vzoru naleznete v tématu [Enterprise integration patterns - Routing slip](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Tento příklad ukazuje směrovací adresu, kde každá aplikace logiky představuje fázi a používá frontu Service Bus ke komunikaci s další aplikací logiky v procesu.

  ![Rozdělení obchodního procesu do fází reprezentovaných aplikacemi logiky, které spolu komunikují pomocí front Azure Service Bus](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Pokud instance aplikace primární i sekundární logiky postupujte podle stejného vzoru směrovací housová stopa ve svých umístěních, můžete implementovat [konkurenční vzor pro spotřebitele](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) nastavením rolí aktivní [aktivní](#roles) pro tyto instance.

* [Vzor vedoucího procesů (zprostředkovatele)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock bez vzoru časového opojení](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Přístup k aktivační události a spuštění historie

Chcete-li získat další informace o spuštění pracovního postupu aplikace logiky v minulosti, můžete zkontrolovat aktivační událost aplikace a spustí historii. Historie spuštění aplikace logiky je uložena ve stejném umístění nebo oblasti, kde byla aplikace logiky spuštěna, což znamená, že tuto historii nelze migrovat do jiného umístění. Pokud vaše primární instance převezme služby při selhání sekundární instance, můžete přistupovat pouze k aktivační události každé instance a spustí historii v příslušných umístěních, kde byly tyto instance spuštěny. Můžete však získat informace o historii aplikace logiky, které vám nesete, nastavením aplikací logiky pro odesílání diagnostických událostí do pracovního prostoru Azure Log Analytics. Potom můžete zkontrolovat stav a historii napříč aplikacemi logiky, které běží na více místech.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Pokyny pro typ aktivační události

Typ aktivační události, který používáte ve svých aplikacích logiky určuje možnosti, jak můžete nastavit aplikace logiky napříč umístěními ve strategii zotavení po havárii. Tady jsou dostupné typy aktivačních událostí, které můžete použít v aplikacích logiky:

* [Aktivační událost opakování](#recurrence-trigger)
* [Aktivační událost dotazování](#polling-trigger)
* [Aktivační událost požadavku](#request-trigger)
* [Spoušť webhooku](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Aktivační událost opakování

Aktivační událost **opakování** je nezávislá na jakékoli konkrétní službě nebo koncovém bodu a aktivuje výhradně na základě zadaného plánu a bez dalších kritérií, například:

* Pevná frekvence a interval, například každých 10 minut
* Pokročilejší plán, například poslední pondělí v měsíci v 17:00

Když vaše aplikace logiky začíná aktivační událost opakování, je třeba nastavit primární a sekundární aplikace logiky instance s [rolemi aktivní pasivní](#roles). Chcete-li zkrátit *cíl doby obnovení* (RTO), který odkazuje na cílovou dobu trvání obnovení obchodního procesu po narušení nebo havárii, můžete nastavit instance aplikace logiky s kombinací rolí aktivní [pasivní](#roles) a [pasivních aktivních rolí](#roles). V tomto nastavení rozdělíte plán mezi umístění.

Předpokládejme například, že máte aplikaci logiky, která musí být spuštěna každých 10 minut. Můžete nastavit aplikace logiky a umístění tak, aby v případě, že primární umístění přestane být k dispozici, sekundární umístění můžete převzít práci:

![Kombinace "Aktivní-pasivní" a "pasivně aktivní", která používá aktivační události opakování](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* V primárním umístění nastavte [role aktivní pasivní](#roles) pro tyto aplikace logiky:

  * Pro *aktivní* povolenou aplikaci logiky nastavte aktivační událost opakování tak, aby začínala v horní části hodiny a opakovala se každých 20 minut, například 9:00, 9:20 a tak dále.

  * Pro *pasivní* zakázané aplikace logiky nastavte aktivační událost opakování na stejný plán, ale spustit na 10 minut po hodině a opakujte každých 20 minut, například 9:10, 9:30 a tak dále.

* V sekundárním umístění nastavte [pasivní aktivní](#roles) pro tyto aplikace logiky:

  * Pro *pasivní* zakázané aplikace logiky nastavte aktivační událost opakování na stejný plán jako aktivní aplikace logiky v primárním umístění, která je v horní části hodiny a opakujte každých 20 minut, například 9:00, 9:10 a tak dále.

  * Pro *aktivní* povolenou aplikaci logiky nastavte aktivační událost opakování na stejný plán jako pasivní aplikace logiky v primárním umístění, což je spustit na 10 minut po hodině a opakovat každých 20 minut, například 9:10 AM, 9:20 a tak dále.

Teď pokud dojde k rušivé události v primárním umístění, aktivujte pasivní aplikaci logiky v alternativním umístění. Tímto způsobem, pokud hledání selhání trvá čas, toto nastavení omezuje počet zmeškaných opakování během tohoto zpoždění.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Aktivační událost dotazování

Chcete-li pravidelně kontrolovat, zda jsou nová data pro zpracování k dispozici z určité služby nebo koncového bodu, aplikace logiky můžete použít *aktivační událost dotazování,* která opakovaně volá službu nebo koncový bod na základě plánu pevné opakování. Data, která poskytuje služba nebo koncový bod, mohou mít některý z těchto typů:

* Statická data, která popisují data, která jsou vždy k dispozici pro čtení
* Nestálá data, která popisují data, která již nejsou po přečtení k dispozici

Chcete-li se vyhnout opakovanému čtení stejných dat, aplikace logiky musí pamatovat, která data byla dříve přečtena udržováním stavu na straně klienta nebo na straně serveru, služby nebo systému.

* Aplikace logiky, které pracují se stavem na straně klienta, používají aktivační události, které mohou udržovat stav.

  Aktivační událost, která čte novou zprávu z e-mailové schránky, například vyžaduje, aby si aktivační událost pamatovala naposledy přečtenou zprávu. Tímto způsobem aktivační událost spustí aplikaci logiky pouze při příchodu další nepřečtené zprávy.

* Aplikace logiky, které pracují se serverem, službou nebo stavem na straně systému, používají hodnoty vlastností nebo nastavení, které jsou na straně serveru, služby nebo systému.

  Aktivační událost založená na dotazu, která čte řádek z `isRead` databáze, například `FALSE`vyžaduje, aby řádek měl sloupec, který je nastaven na . Pokaždé, když aktivační událost přečte řádek, aplikace logiky aktualizuje tento řádek změnou `isRead` sloupce z na `FALSE` `TRUE`.

  Tento přístup na straně serveru funguje podobně pro fronty service bus nebo témata, která mají sémantiku do fronty, kde aktivační událost můžete číst a uzamknout zprávu, zatímco aplikace logiky zpracovává zprávu. Když aplikace logiky dokončí zpracování, aktivační událost odstraní zprávu z fronty nebo tématu.

Z hlediska zotavení po havárii při nastavování primární ch a sekundární instance aplikace logiky se ujistěte, že účet pro toto chování na základě toho, zda aplikace logiky sleduje stav na straně klienta nebo na straně serveru:

* Pro aplikaci logiky, která pracuje se stavem na straně klienta, ujistěte se, že vaše aplikace logiky nečte stejnou zprávu více než jednou. Pouze jedno umístění může mít aktivní instanci aplikace logiky v určitém čase. Ujistěte se, že instance aplikace logiky v alternativním umístění je neaktivní nebo zakázaná, dokud primární instance přejde do alternativního umístění.

  Aktivační událost Aplikace Office 365 Outlook například udržuje stav na straně klienta a sleduje časové razítko naposledy přečteného e-mailu, aby se zabránilo čtení duplicity.

* Pro aplikaci logiky, která pracuje se stavem na straně serveru, můžete nastavit instance aplikace logiky tak, aby hrály buď [role aktivní a aktivní,](#roles) kde pracují jako konkurenční spotřebitelé, nebo [role aktivní pasivní,](#roles) kde alternativní instance čeká, dokud primární instance převezme převzetí do alternativního umístění.

  Například čtení z fronty zpráv, jako je například fronta služby Azure Service Bus, používá stav na straně serveru, protože služba řazení do fronty udržuje zámky zpráv, aby ostatní klienti nečetli stejné zprávy.

  > [!NOTE]
  > Pokud vaše aplikace logiky potřebuje číst zprávy v určitém pořadí, například z fronty service bus, můžete použít konkurenční vzor příjemce, ale pouze v kombinaci s relacemi Service Bus, který je také známý jako [ *sekvenční konvoj* vzor](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy). V opačném případě je nutné nastavit instance aplikace logiky s rolemi aktivní pasivní.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Aktivační událost požadavku

Aktivační **událost Požadavku** umožňuje, aby vaše aplikace logiky byla volatelná z jiných aplikací, služeb a systémů a obvykle se používá k poskytování těchto funkcí:

* Přímé rozhraní REST API pro vaši aplikaci logiky, které mohou volat ostatní

  Například použijte request trigger ke spuštění aplikace logiky, aby ostatní aplikace logiky můžete volat aktivační událost pomocí **akce Volání pracovního postupu – aplikace logiky.**

* Mechanismus [webhooku](#webhook-trigger) nebo zpětného volání pro aplikaci logiky

* Způsob, jak ručně spustit uživatelské operace nebo rutiny pro volání aplikace logiky, například pomocí skriptu prostředí PowerShell, který provádí určitou úlohu

Z hlediska zotavení po havárii request trigger je pasivní příjemce, protože aplikace logiky neprovádí žádnou práci a čeká, dokud některé jiné služby nebo systém explicitně volá aktivační událost. Jako pasivní koncový bod můžete nastavit primární a sekundární instance těmito způsoby:

* [Aktivní aktivní](#roles): Obě instance aktivně zpracovávají požadavky nebo volání. Volající nebo směrovač vyrovnává nebo distribuuje přenosmezi těmito instancemi.

* [Aktivní pasivní](#roles): Aktivní je pouze primární instance a zpracovává veškerou práci, zatímco sekundární instance čeká, dokud primární dojde k narušení nebo selhání. Volající nebo směrovač určuje, kdy má volat sekundární instanci.

Jako doporučenou architekturu můžete použít Azure API Management jako proxy server pro aplikace logiky, které používají aktivační události Požadavku. Správa rozhraní API poskytuje [integrovanou meziregionální odolnost a možnost směrování provozu mezi více koncovými body](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Spoušť webhooku

Aktivační událost *webhooku* poskytuje možnost pro aplikaci logiky přihlásit ke službě předáním *adresu URL zpětného volání* do této služby. Vaše aplikace logiky pak můžete naslouchat a čekat na konkrétní událost dojít v tomto koncovém bodě služby. Když dojde k události, služba volá webhooku aktivační událost pomocí adresy URL zpětného volání, který pak spustí aplikaci logiky. Pokud je povolena, aktivační událost webhooku se přihlásí ke službě. Pokud je zakázána, aktivační událost se odhlásí ze služby.

Z hlediska zotavení po havárii nastavte primární a sekundární instance, které používají aktivační události webhooku k hraní rolí aktivní pasivní, protože pouze jedna instance by měla přijímat události nebo zprávy z odebíraného koncového bodu.

## <a name="assess-primary-instance-health"></a>Posouzení stavu primární instance

Aby vaše strategie zotavení po havárii fungovala, vaše řešení potřebuje způsoby, jak provádět tyto úkoly:

* [Kontrola dostupnosti primární instance](#check-primary-availability)
* [Sledování stavu primární instance](#monitor-primary-health)
* [Aktivace sekundární instance](#activate-secondary)

Tato část popisuje jedno řešení, které můžete použít přímo nebo jako základ pro vlastní návrh. Zde je vizuální přehled na vysoké úrovni pro toto řešení:

![Vytvoření aplikace logiky watchdog, která monitoruje aplikaci logiky kontroly stavu v primárním umístění](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Kontrola dostupnosti primární instance

Chcete-li zjistit, zda je primární instance k dispozici, spuštěná a schopná pracovat, můžete vytvořit aplikaci logiky "kontrola stavu", která je ve stejném umístění jako primární instance. Tuto aplikaci pro kontrolu stavu pak můžete volat z alternativního umístění. Pokud aplikace pro kontrolu stavu úspěšně reaguje, základní infrastruktura pro službu Azure Logic Apps v této oblasti je k dispozici a funguje. Pokud aplikace kontroly stavu nereaguje, můžete předpokládat, že umístění již není v pořádku.

Pro tuto úlohu vytvořte základní aplikaci logiky kontroly stavu, která provádí tyto úkoly:

1. Přijme volání z aplikace watchdog pomocí request trigger.

1. Odpověď se stavem označující, zda zaškrtnutá aplikace logiky stále funguje pomocí akce Odpověď.

   > [!IMPORTANT]
   > Aplikace logiky kontroly stavu musí použít akci Odpověď tak, aby aplikace reagovala synchronně, nikoli asynchronně.

1. Volitelně můžete dále určit, zda je primární umístění v pořádku, můžete zohlednit stav všech dalších služeb, které interagují s cílovou aplikací logiky v tomto umístění. Stačí rozšířit aplikaci logiky kontroly stavu a posoudit stav pro tyto další služby.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Vytvoření aplikace logiky hlídacího psa

Chcete-li sledovat stav primární instance a volat aplikaci logiky kontroly stavu, vytvořte aplikaci logiky "watchdog" v *alternativním umístění*. Můžete například nastavit aplikaci logiky sledovacího zařízení tak, aby pokud volání logiky kontroly stavu selže, watchdog může odeslat výstrahu do vašeho operačního týmu, aby mohl prozkoumat selhání a proč primární instance nereaguje.

> [!IMPORTANT]
> Ujistěte se, že vaše aplikace logiky watchdog je v *umístění, které se liší od primárníumístění*. Pokud služba Logic Apps v primárním umístění dojde k problémům, vaše aplikace logiky watchdog nemusí spustit.

Pro tuto úlohu v sekundárním umístění vytvořte aplikaci logiky watchdog, která provádí tyto úkoly:

1. Spustit na základě pevné nebo plánované opakování pomocí aktivační události opakování.

   Opakování můžete nastavit na hodnotu, která je nižší než úroveň tolerance pro cíl doby obnovení (RTO).

1. Volání aplikace logiky kontroly stavu v primárním umístění pomocí akce HTTP, například:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktivace sekundární instance

Chcete-li automaticky aktivovat sekundární instanci, můžete vytvořit aplikaci logiky, která volá rozhraní API pro správu, jako je [například konektor Azure Resource Manager](https://docs.microsoft.com/connectors/arm/) k aktivaci příslušných aplikací logiky v sekundárním umístění. Můžete rozšířit svou aplikaci watchdog volat tuto aktivační aplikaci logiky po určitém počtu selhání dojít.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Shromažďování diagnostických dat

Můžete nastavit protokolování pro spuštění aplikace logiky a odeslat výsledná diagnostická data do služeb, jako je Azure Storage, Azure Event Hubs a Azure Log Analytics pro další zpracování a zpracování.

* Pokud chcete tato data používat pomocí Azure Log Analytics, můžete data zpřístupnit pro primární i sekundární umístění nastavením **nastavení diagnostiky** aplikace logiky a odesláním dat do více pracovních prostorů Analýzy protokolů. Další informace najdete v tématu [Nastavení protokolů Azure Monitor a shromažďování diagnostických dat pro Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md).

* Pokud chcete odeslat data do Azure Storage nebo Azure Event Hubs, můžete zpřístupnit data pro primární i sekundární umístění nastavením geografické redundance. Další informace najdete v těchto článcích:<p>

  * [Obnovení zabezpečení úložiště objektů blob Azure a převzetí služeb při selhání účtu](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geografické zotavení po havárii](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Další kroky

* [Přehled odolnosti proti chybám pro Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Kontrolní seznam k odolnosti pro konkrétní služby Azure](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Správa dat pro odolnost v Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Zálohování a zotavení po havárii pro aplikace Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Zotavení z přerušení služeb na úrovni celé oblasti](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Smlouvy o úrovni služeb Microsoftu (SLA) pro služby Azure](https://azure.microsoft.com/support/legal/sla/)
