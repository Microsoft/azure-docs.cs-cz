---
title: Režimy připojení a požadavky
description: Vysvětluje možnosti připojení k datovým službám s podporou ARC Azure pro vaše prostředí do Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 44c1c1860cbea20a7a00da5a396e4d82d79efd8b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936454"
---
# <a name="connectivity-modes-and-requirements"></a>Režimy připojení a požadavky

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Režimy připojení

K dispozici je několik možností, jak v prostředí Azure ARC s povolenými datovými službami do Azure navázat spojení. Vzhledem k to, že se požadavky liší v závislosti na podnikových zásadách, předpisech státní správy nebo dostupnosti síťového připojení k Azure, si můžete vybrat z následujících režimů připojení.

Datové služby s podporou ARC Azure vám umožňují připojit se k Azure ve dvou různých režimech připojení: přímo připojená a nepřímo připojená.  Získáte tak flexibilitu při výběru množství dat odesílaných do Azure a způsobu interakce uživatelů s řadičem dat ARC. V závislosti na zvoleném režimu připojení můžou být některé funkce datových služeb s podporou ARC Azure k dispozici nebo nemusí být k dispozici.

Důležité: Pokud jsou datové služby s podporou ARC Azure přímo připojené k Azure, můžou uživatelé používat [rozhraní Azure Resource Manager API](/rest/api/resources/), Azure CLI a Azure Portal k provozování datových služeb Azure ARC. Prostředí přímo připojeného režimu se podobá tomu, jak byste použili jinou službu Azure s zřizováním/rušením zřizování, škálováním, konfigurací a tak i na všech těchto Azure Portal.  Pokud jsou datové služby s podporou ARC Azure nepřímo připojené k Azure, pak je Azure Portal zobrazení jen pro čtení. Můžete zobrazit inventarizaci spravovaných instancí SQL a Postgres instancí s škálovatelným škálováním, které jste nasadili, a podrobnosti o nich, ale nemůžete na ně v Azure Portal provádět žádné akce.  V režimu nepřímo připojené se musí všechny akce provádět místně pomocí Azure Data Studio, Azure Data CLI nebo nativních nástrojů Kubernetes, jako je kubectl.

Azure Active Directory a Azure Access Control založené na rolích Azure se navíc dají použít jenom v přímo připojeném režimu, protože existuje závislost na souvislém a přímém připojení k Azure, které tuto funkci poskytují.

Některé služby připojené k Azure jsou k dispozici pouze v případě, že mohou být přímo dostupné, například služby zabezpečení Azure Defender, kontejnerové statistiky a Azure Backup nebo BLOB Storage.

V současné době je ve verzi Preview podporována pouze nepřímo připojený režim.  Přímý připojený režim je plánován pro budoucnost.  Níže je uvedený již nepřipojený režim, který je již v současné době podporován.

||**Nepřímo připojeno**|**Přímo připojeno**|**Nikdy Nepřipojeno**|
|---|---|---|---|
|**Popis**|Nepřímo připojený režim nabízí většinu služeb správy v místním prostředí bez přímého připojení k Azure.  Do Azure se musí poslat _jenom_minimální množství dat pro inventarizaci a účely fakturace. Vyexportuje se do souboru a nahraje se do Azure aspoň jednou za měsíc.  Nevyžaduje se žádné přímé nebo nepřetržité připojení k Azure.  Některé funkce a služby, které vyžadují připojení k Azure, nebudou k dispozici.|Přímý připojený režim nabízí všechny dostupné služby, když je možné navázat přímé připojení s Azure. Připojení se vždycky iniciují _z_ vašeho prostředí do Azure a používají standardní porty a protokoly jako https/443.|Žádná data nelze žádným způsobem odeslat do ani z Azure.|
|**Aktuální dostupnost**| K dispozici ve verzi Preview.|Plánováno pro verzi Preview v budoucnu.|Aktuálně se nepodporuje.|
|**Typické případy použití**|Místní datová centra, která neumožňují připojení v datové oblasti datového centra z oblasti nebo z nich z důvodu dodržování předpisů pro obchodní nebo legislativní nebo nedodržení jiných útoků nebo exfiltrace dat.  Typické příklady: finanční instituce, zdravotnictví a státní péče. <br/><br/>Hraniční umístění lokality, kde hraniční lokalita obvykle nemá připojení k Internetu.  Typické příklady: ropných nebo vojenských aplikací pole.  <br/><br/>Umístění webů Edge, která mají přerušované připojení s dlouhými dobami výpadků.  Typické příklady: Stadium, přepravní lodí. | Organizace, které používají veřejné cloudy.  Typické příklady: Azure, AWS nebo Google Cloud.<br/><br/>Hraniční umístění lokality, kde je obvykle k dispozici a povoleno připojení k Internetu.  Typické příklady: prodejny, výroba.<br/><br/>Podniková datová centra s přísnějšími zásadami pro připojení ke své datové oblasti datacentra k Internetu a z jejich oblasti.  Typické příklady: neregulované firmy, malé nebo střední velikosti firmy|Skutečně "air gapped" prostředí, kde žádná data za žádných okolností nemůžou přijít z datového prostředí a přejít na ně. Typické příklady: hlavní zařízení pro státní správu hlavních tajných prostředků.|
|**Způsob odesílání dat do Azure**|Existují tři možnosti, jak můžete odesílat data o fakturaci a inventáři do Azure:<br><br> 1) data se exportují mimo datovou oblast pomocí automatizovaného procesu, který má připojení k oblasti zabezpečených dat i k Azure.<br><br>2) data se exportují z oblasti dat pomocí automatizovaného procesu v oblasti dat, automaticky zkopírované do méně zabezpečené oblasti a automatizovaný proces v méně zabezpečené oblasti nahrává data do Azure.<br><br>3) data se ručně exportují uživatelem v rámci zabezpečené oblasti, ručně se vynesou z zabezpečené oblasti a ručně se nahrají do Azure. <br><br>První dvě možnosti jsou automatizovaný nepřetržitý proces, který se dá naplánovat tak často, aby se v rámci přenosu dat do Azure mělo jenom dostupné připojení k Azure.|Data se automaticky a neustále odesílají do Azure.|Data se nikdy neodesílají do Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Dostupnost funkcí podle režimu připojení

|**Funkce**|**Nepřímo připojeno**|**Přímo připojeno**|
|---|---|---|
|**Automatická vysoká dostupnost**|Podporováno|Podporováno|
|**Samoobslužné zřizování**|Podporováno<br/>Vytváření se dá udělat prostřednictvím Azure Data Studio, Azure Data CLI nebo Kubernetes Native Tools (Helm, kubectl, OC atd.) nebo pomocí zřizování Kubernetes GitOps pro Azure ARC.|Podporováno<br/>Kromě možností vytváření nepřímo připojeného režimu můžete také vytvořit prostřednictvím Azure Portal, Azure Resource Manager API, Azure CLI nebo šablon ARM. **Nedokončená dostupnost přímo připojeného režimu**
|**Elastická škálovatelnost**|Podporováno|Podporováno<br/>**Nedokončená dostupnost přímo připojeného režimu**|
|**Fakturace**|Podporováno<br/>Fakturační údaje se pravidelně exportují a odesílají do Azure.|Podporováno<br/>Fakturační údaje se automaticky a průběžně odesílají do Azure a projeví se téměř v reálném čase. **Nedokončená dostupnost přímo připojeného režimu**|
|**Řízení zásob**|Podporováno<br/>Data inventáře se pravidelně exportují a odesílají do Azure.|Podporováno<br/>Data inventáře se automaticky a neustále odesílají do Azure a projeví se téměř v reálném čase. **Nedokončená dostupnost přímo připojeného režimu**|
|**Automatické upgrady a opravy**|Podporováno<br/>Řadič dat musí mít buď přímý přístup k Microsoft Container Registry (MCR), nebo musí být image kontejneru z MCR a vloženy do místního privátního registru kontejneru, ke kterému má řadič dat přístup.|Podporováno<br/>**Nedokončená dostupnost přímo připojeného režimu**|
|**Automatické zálohování a obnovování**|Podporováno<br/>Automatické místní zálohování a obnovení.|Podporováno<br/>Kromě automatizovaného místního zálohování a obnovení můžete _volitelně_ odesílat zálohy do Azure Backup pro dlouhodobé uchovávání mimo pracoviště. **Nedokončená dostupnost přímo připojeného režimu**|
|**Sledovaný**|Podporováno<br/>Místní monitorování pomocí řídicích panelů Grafana a Kibana.|Podporováno<br/>Kromě řídicích panelů pro místní monitorování můžete _volitelně_ odesílat data monitorování a protokoly Azure monitor pro monitorování různých lokalit na jednom místě na škále. **Nedokončená dostupnost přímo připojeného režimu**|
|**Authentication**|Použijte místní uživatelské jméno/heslo pro ověření řadiče dat a řídicího panelu. K připojení k instancím databáze použijte přihlašovací údaje SQL a Postgres nebo službu Active Directory.  Použijte zprostředkovatele ověřování K8s k ověřování do rozhraní Kubernetes API.|Kromě metod ověřování pro nepřímo připojený režim můžete místo toho použít _taky_ Azure Active Directory. **Nedokončená dostupnost přímo připojeného režimu**|
|**Access Control na základě rolí (RBAC)**|Použijte Kubernetes RBAC na Kubernetes API. Pro instance databáze použijte SQL a Postgres RBAC.|Volitelně můžete integrovat s Azure Active Directory pro RBAC. **Nedokončená dostupnost přímo připojeného režimu**|
|**Azure Defender**|Nepodporováno|Plánováno pro budoucnost|

## <a name="connectivity-requirements"></a>Požadavky na připojení

**Některé funkce vyžadují připojení k Azure.**

**Veškerá komunikace s Azure je vždy iniciována z vašeho prostředí.** To platí i pro operace, které jsou iniciovány uživatelem v Azure Portal.  V takovém případě se jedná o úkol, který je zařazen do fronty v Azure.  Agent ve vašem prostředí iniciuje komunikaci s Azure a zjistí, jaké úkoly jsou ve frontě, spouští úlohy a hlásí zpět stav/dokončení/selhání do Azure.

|**Typ dat**|**Směr**|**Požadováno/volitelné**|**Další náklady**|**Požadovaný režim**|**Poznámky**|
|---|---|---|---|---|---|
|**Image kontejneru**|Zákazník Microsoft Container Registry->|Vyžadováno|No|Nepřímé nebo přímé|Image kontejneru jsou metodou distribuce softwaru.  V prostředí, které se může připojit k Microsoft Container Registry (MCR) přes Internet, se image kontejneru dají získat přímo z MCR.  V případě, že prostředí nasazení nemá přímé připojení, můžete načíst image z MCR a vložit je do soukromého registru kontejnerů v prostředí nasazení.  V okamžiku vytvoření můžete nakonfigurovat proces vytváření pro vyžádání z privátního registru kontejneru místo MCR. To platí i pro automatizované aktualizace.|
|**Inventář prostředků**|Prostředí zákazníka – > Azure|Vyžadováno|No|Nepřímé nebo přímé|Inventarizace řadičů dat, databázových instancí (PostgreSQL a SQL) je v Azure zachovaná pro účely fakturace a také pro účely vytvoření inventáře všech řadičů dat a instancí databáze na jednom místě, což je zvlášť užitečné, pokud máte více než jedno prostředí s datovými službami Azure ARC.  Jelikož jsou instance zřízené, jsou v Azure navýšené a navýšené škálované/v, v Azure se aktualizuje inventář.|
|**Data telemetrie fakturace**|Prostředí zákazníka – > Azure|Vyžadováno|No|Nepřímé nebo přímé|K fakturaci je potřeba odeslat Azure instance databáze z důvodů fakturace.  V období Preview nejsou k dispozici žádné náklady na datové služby s podporou ARC Azure.|
|**Monitorování dat a protokolů**|Prostředí zákazníka – > Azure|Volitelné|Možná v závislosti na objemu dat (viz [ceny Azure monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Nepřímé nebo přímé|Můžete chtít odeslat místně shromážděná data monitorování a protokoly, které Azure Monitor pro agregaci dat napříč několika prostředími na jednom místě a také použití Azure Monitorch služeb, jako jsou výstrahy, pomocí dat v Azure Machine Learning atd.|
|**Access Control na základě rolí v Azure (RBAC)**|Prostředí zákazníka – > prostředí Azure-> Customer|Volitelné|No|Pouze Direct|Pokud chcete použít Azure pro RBAC, musí být připojení navázáno s Azure za všech okolností.  Pokud nechcete používat Azure pro RBAC, můžete použít místní Kubernetes RBAC.  **Nedokončená dostupnost přímo připojeného režimu**|
|**Azure Active Directory (AD)**|Prostředí zákazníka – > prostředí Azure-> Customer|Volitelné|Možná jste se už mohli zaplatit za Azure AD.|Pouze Direct|Pokud chcete pro ověřování použít Azure AD, musí být připojení navázáno s Azure za všech okolností. Pokud nechcete používat Azure AD k ověřování, můžete nám Active Directory Federation Services (AD FS) (ADFS) prostřednictvím služby Active Directory. **Nedokončená dostupnost přímo připojeného režimu**|
|**Zálohování a obnovení**|Prostředí zákazníka – > prostředí Azure-> Customer|Volitelné|Ano pro náklady na úložiště|Pouze Direct|Možná budete chtít odeslat zálohy, které se budou přenášet místně, Azure Backup pro dlouhodobé uchovávání záloh mimo pracoviště a vrátit je zpátky do místního prostředí pro obnovení. **Nedokončená dostupnost přímo připojeného režimu**|
|**Služby zabezpečení v Azure Defenderu**|Prostředí zákazníka – > prostředí Azure-> Customer|Volitelné|Yes|Pouze Direct|**Nedokončená dostupnost přímo připojeného režimu**|
|**Zřizování a konfigurace se mění z Azure Portal**|Prostředí zákazníka – > prostředí Azure-> Customer|Volitelné|No|Pouze Direct|Změny zřizování a konfigurace lze provádět místně pomocí Azure Data Studio nebo rozhraní příkazového řádku azdata.  V přímo připojeném režimu bude také možné zřídit a provádět změny konfigurace z Azure Portal. **Nedokončená dostupnost přímo připojeného režimu**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Podrobnosti o internetových adresách, portech, šifrování a proxy server podpoře

V současné době je ve fázi Preview podporována pouze nepřímo připojený režim.  V tomto režimu jsou k dispozici pouze tři připojení pro služby, které jsou k dispozici na internetu.  Všechna připojení HTTPS k Azure a Microsoft Container Registry jsou šifrována pomocí protokolu SSL/TLS pomocí úředně podepsaných a ověřitelných certifikátů.

|**Název**|**Zdroj připojení**|**Cíl připojení**|**Protokol**|**Port**|**Může používat proxy**|**Authentication**|**Poznámky**|
|---|---|---|---|---|---|---|---|
|**Microsoft Container Registry (MCR)**|Kubernetes kubelet na všech uzlech Kubernetes nahlásí image kontejneru.|`mcr.microsoft.com`|HTTPS|443|Yes|Žádné|Microsoft Container Registry hostuje image kontejneru s povolenými datovými službami Azure ARC.  Tyto image můžete načíst z MCR a vložit je do soukromého registru kontejnerů a nakonfigurovat proces nasazení řadiče dat tak, aby vyčetl image kontejneru z tohoto privátního registru kontejnerů.|
|**Rozhraní API pro Azure Resource Manager**|Počítač se systémem Azure Data Studio, Azure Data CLI nebo rozhraní příkazového řádku Azure CLI, který se připojuje k Azure.|`login.microsoftonline.com`<br/>`management.azure.com`<br/>`san-af-eastus-prod.azurewebsites.net`<br/>`san-af-eastus2-prod.azurewebsites.net`<br/>`san-af-australiaeast-prod.azurewebsites.net`<br/>`san-af-centralus-prod.azurewebsites.net`<br/>`san-af-westus2-prod.azurewebsites.net`<br/>`san-af-westeurope-prod.azurewebsites.net`<br/>`san-af-southeastasia-prod.azurewebsites.net`<br/>`san-af-koreacentral-prod.azurewebsites.net`<br/>`san-af-northeurope-prod.azurewebsites.net`<br/>`san-af-westeurope-prod.azurewebsites.net`<br/>`san-af-uksouth-prod.azurewebsites.net`<br/>`san-af-francecentral-prod.azurewebsites.net`|HTTPS|443|Yes|Azure Active Directory|Azure Data Studio, Azure Data CLI a Azure CLI se připojují k rozhraním API Azure Resource Manager, aby bylo možné odesílat a načítat data do a z Azure pro některé funkce.|
|**Rozhraní API pro Azure Monitor**|Počítač se systémem Azure Data CLI nebo rozhraním příkazového řádku Azure, který odesílá metriky monitorování nebo protokoly do Azure Monitor.|`login.microsoftonline.com`<br/>`management.azure.com`<br/>`*.ods.opinsights.azure.com`<br/>`*.oms.opinsights.azure.com`<br/>`*.monitoring.azure.com`|HTTPS|443|Yes|Azure Active Directory|Azure Data Studio, Azure Data CLI a Azure CLI se připojují k rozhraním API Azure Resource Manager, aby bylo možné odesílat a načítat data do a z Azure pro některé funkce.|

> **Poznámka:** V současné době se všechna připojení prohlížeče HTTPS/443 k řídicím panelům Grafana a Kibana a z Azure Data CLI do rozhraní API řadiče dat šifrují pomocí certifikátů podepsaných svým držitelem.  V budoucnu bude k dispozici funkce, která vám umožní zadat vlastní certifikáty pro šifrování těchto připojení SSL.

Připojení z Azure Data Studio a Azure Data CLI k serveru rozhraní Kubernetes API používá ověřování Kubernetes a šifrování, které jste nastavili.  Každý uživatel, který používá Azure Data Studio a Azure Data CLI, musí mít ověřené připojení k rozhraní Kubernetes API, aby mohl provádět mnoho akcí souvisejících s datovými službami s podporou ARC Azure.
