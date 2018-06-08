---
title: Funkce operačního systému v Azure App Service
description: Další informace o funkci operačního systému k dispozici pro webové aplikace, back-EndY mobilních aplikací a aplikace API v Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
ms.openlocfilehash: 00b5f9c78000fbb9bf86e8c1d8b06e3645795a12
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850150"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkce operačního systému v Azure App Service
Tento článek popisuje běžné funkce operačního systému standardních hodnot, které jsou k dispozici pro všechny aplikace běžící na [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Tato funkce zahrnuje soubor, sítě a přístup k registru a diagnostické protokoly a události. 

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Úrovně plánu služby App Service
Služby App Service zákazníka aplikace běží v hostitelských prostředí s více klienty. Aplikace nasazené v **volné** a **sdílené** vrstev spustit v pracovních procesů na sdílených virtuálních počítačích, zatímco aplikace nasazené v **standardní** a **Premium** vrstev spustit na virtuální počítače vyhrazené speciálně pro aplikace přidružené k jednoho zákazníka.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Protože služby App Service podporuje bezproblémové škálování prostředí mezi různých vrstev, konfigurace zabezpečení pro aplikace služby App Service vynucený zůstává stejná. Tím se zajistí, že aplikace není chovat najednou jinak, selhání neočekávané způsoby, když se plán služby App Service přepne z jedné vrstvy do jiného.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Architektury pro vývoj
Cenové úrovně služby App Service řízení množství výpočetních prostředků (procesor, diskové úložiště, paměti a odchozí síťový) k dispozici pro aplikace. Však šířky framework funkce je k dispozici pro aplikace zůstává stejný bez ohledu na škálování vrstvy.

App Service podporuje celou řadu architektury pro vývoj, včetně ASP.NET, classic ASP, node.js, PHP a Python - všechny spouští jako rozšíření v rámci služby IIS. Aby bylo možné zjednodušit a normalizaci konfigurace zabezpečení, aplikacemi App Service obvykle běží různé architektury pro vývoj ve výchozím nastavení. Jeden ze způsobů konfigurace aplikací může se k útoku na rozhraní API a funkce pro každé rozhraní pro jednotlivé vývoj přizpůsobit. Služby App Service místo trvá více obecné přístup povolením běžné základní funkce operačního systému bez ohledu na to rozhraní pro vývoj aplikace.

Následující části shrnují obecné druhy funkce operačního systému k dispozici pro aplikace služby App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Přístup k souborům
Existují různé jednotky v App Service, včetně místní jednotky a síťové jednotky.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Místní jednotky
Jádro aplikace App Service je služby spuštěné na infrastruktuře Azure PaaS (platforma jako služba). Místní disky, které jsou "připojené" k virtuálnímu počítači v důsledku toho jsou stejné typy jednotku, která je k dispozici pro všechny role pracovního procesu, který běží v Azure. To zahrnuje:

- Jednotky operačního systému (jednotku D:\)
- Aplikaci jednotku, která obsahuje balíček Azure cspkg soubory používané výhradně službou App Service (a nepřístupný zákazníkům)
- "User" jednotka (jednotka C:\), jejichž velikost se liší v závislosti na velikosti virtuálního počítače. 

Je důležité monitorovat využití vašeho disku růstem vaší aplikace. Když je dosaženo kvóty disku, může mít nežádoucí účinky do vaší aplikace.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Síťové jednotky (neboli UNC sdílené složky)
Jedním z jedinečné aspekty aplikační služba, která usnadňuje nasazení aplikace a údržby přehledné je, že je všechny uživatele obsah uložený na sadu sdílené složky UNC. Tento model mapuje dobře běžný vzor úložiště obsahu, které používá místní webových hostitelských prostředích, která mají víc serverů s vyrovnáváním zatížení. 

V rámci služby App Service existuje několik UNC sdílené složky vytvořené v každém datovém centru. Procento obsah uživatele pro všechny zákazníky v každé datového centra je přidělen pro každé sdílené složky UNC. Kromě toho celého obsahu, pro jednoho zákazníka předplatného je vždy umístit na stejnou cestu UNC souboru sdílet. 

Z důvodu jak Azure services práce bude zodpovědná za sdílené složky UNC hostování konkrétní virtuální počítač časem změnit. Je zaručeno, že sdílené složky UNC se připojí jiné virtuální počítače, jako přechodem při běžném průběhu operací Azure nahoru a dolů. Z tohoto důvodu by měla aplikace umožňují nikdy pevně předpoklady, informace o počítači v cestě k souboru UNC zůstane stabilní v čase. Místo toho měli používat vhodného *umělé* absolutní cestu **D:\home\site** poskytující služby App Service. Tato umělé absolutní cesta poskytuje přenosné, aplikace a uživatele – bez ohledu na metodu pro odkaz na aplikaci vlastního. Pomocí **D:\home\site**, jeden můžou přenášet sdílených souborů z aplikace do aplikace bez nutnosti konfigurovat novou absolutní cestu pro každý přenos.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy souboru udělení přístupu k aplikaci
Každý zákazník předplatné má vyhrazené adresářovou strukturu na sdílenou jednotku UNC konkrétní v rámci datového centra. Zákazník může mít víc aplikací v rámci konkrétní datové středisko, vytvořili, všechny adresáře, které patří do jednoho zákazníka předplatného se vytvářejí na stejnou cestu UNC sdílené složky. Sdílené složce může zahrnovat adresáře jsou pro obsah, chyby a diagnostické protokoly a dřívějších verzích aplikace vytvořené zdrojového kódu. Podle očekávání, adresáře aplikace zákazníka jsou k dispozici pro oprávnění ke čtení a zápisu v době běhu aplikace kód aplikace.

Na místní diskové jednotky připojené k virtuálnímu počítači, který spouští aplikaci služby App Service si vyhrazuje bloku místa na jednotce C:\ pro konkrétní aplikaci dočasné místní úložiště. I když aplikace má čtení/zápisu přístup k vlastní dočasné místní úložiště, že úložiště skutečně není určena pro použití přímo v kódu aplikace. Místo toho je záměr zajistit ukládání dočasných souborů pro službu IIS a webové aplikace rozhraní. Velikost dočasné místní úložiště, které jsou k dispozici pro každou aplikaci, aby zabránila jednotlivými aplikacemi nespotřeboval nadměrné množství úložiště místního souboru je také omezení služby App Service.

Jsou dva příklady, jak služba aplikace používá dočasné úložiště místní adresář pro dočasné soubory ASP.NET a komprimované soubory v adresáři služby IIS. Systém kompilace technologie ASP.NET používá jako umístění mezipaměti dočasné kompilace adresáře "Temporary ASP.NET Files". Služba IIS používá adresáři "IIS dočasné komprimované soubory" pro ukládání výstupu zkomprimovanou odpověď. Oba tyto typy souborů využití (stejně jako ostatní) jsou mapovány na dočasné místní úložiště pro aplikaci ve službě App Service. Přemapování zajistí, že funkce pokračuje podle očekávání.

Každá aplikace ve službě App Service spouští jako identitu náhodných jedinečný nízkými oprávněními pracovního procesu názvem "identita fondu aplikací", zde popsané dál: [ http://www.iis.net/learn/manage/configuring-security/application-pool-identities ](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Kód aplikace použije tuto identitu pro základní přístup jen pro čtení k jednotce operačního systému (jednotku D:\). To znamená, že kód aplikace, můžete seznam běžných struktur adresářů a číst společné soubory na jednotce operačního systému. I když to může vypadat jako poněkud obecné úrovni přístupu, stejné adresářů a souborů jsou dostupné při zřizování roli pracovního procesu v Azure hostovaná služba a číst obsah jednotky. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Přístup k souborům ve více instancích
Domovský adresář je obsah aplikace a kód aplikace může do něj zapisovat. Pokud aplikace běží na několik instancí, domovský adresář je sdílena mezi všechny instance, tak, aby všechny instance, najdete v článku do stejného adresáře. Ano, například pokud aplikace uloží odeslané soubory na domovský adresář, tyto soubory jsou okamžitě k dispozici pro všechny instance. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Síťový přístup
Kód aplikace můžete použít protokol TCP/IP a UDP na základě protokolů aby odchozí síťové připojení k přístupné koncové body Internetu, které zveřejňují externích služeb. Aplikace můžete použít tyto stejné protokoly pro připojení ke službám v rámci Azure&#151;například vytvořením připojení pomocí protokolu HTTPS k databázi SQL.

Je také omezené funkce pro aplikace k připojení jeden místní smyčky a aplikace naslouchání že soketem místní smyčky. Tato funkce existuje především k aplikacím, které čekají na místní smyčky sockets jako součást jejich funkce. Každá aplikace uvidí připojení "privátní" zpětné smyčky. Aplikace "A" nemůže naslouchat na soket místní smyčky navázat aplikací "B".

Pojmenované kanály jsou podporovány také jako mechanismus meziprocesová komunikace (IPC) mezi různé procesy, které se souhrnně spustí aplikaci. Například modul FastCGI služby IIS využívá pojmenované kanály pro koordinaci jednotlivých procesů, které spouštějí stránky PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Provádění kódu, procesy a paměti
Jak již bylo uvedeno dříve, aplikace běžet uvnitř nízkými oprávněními pracovních procesů pomocí identita fondu náhodných aplikací. Kód aplikace má přístup do paměti prostoru přidružené pracovní proces, jakož žádné podřízené procesy, které může být vytvořený procesy CGI nebo jiné aplikace. Ale jednu aplikaci nelze přístup k paměti nebo data z jiné aplikace i v případě, že je na jednom virtuálním počítači.

Aplikace můžete spustit skripty nebo napsané pomocí architektury pro vývoj podporované webové stránky. Služby App Service není nakonfigurujte všechna nastavení webové framework na omezenější režimy. Například spusťte ASP.NET aplikace běžící na App Service v "úplná" důvěryhodnosti oproti omezenější režim vztah důvěryhodnosti. Webové rozhraní, včetně classic ASP a ASP.NET, může zavolat komponenty modelu COM v procesu (ale ne mimo proces COM – součásti) jako ADO (ActiveX Data Objects), která jsou zaregistrovaná ve výchozím nastavení operačního systému Windows.

Aplikace můžete vytvořit a spustit libovolný kód. Je povolený pro aplikaci, provádět akce, jako je spawn příkazové prostředí nebo spustit skript prostředí PowerShell. I když libovolný kód a procesů může být vytvořený z aplikace, spustitelné programy a skripty jsou však stále omezen na udělena k fondu aplikací nadřazené oprávnění. Například může aplikace spawn spustitelné soubory, které umožňuje odchozí volání protokolu HTTP, ale, že stejný spustitelný soubor nelze pokus o odpojení IP adresu virtuálního počítače z jeho síťový adaptér. Vytvořit volání odchozí sítě je povolený kódu nízkou úrovní oprávnění, ale pokusu změnit konfiguraci nastavení sítě na virtuálním počítači vyžaduje oprávnění správce.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostické protokoly a události
Informace v protokolu je jinou sadu dat, která některé aplikace pokusí o přístup. Typy protokolu informací, které jsou k dispozici pro kód spuštěný ve službě App Service zahrnuje diagnostiky a protokolovat informace generované aplikaci, která je také snadno dostupná k aplikaci. 

Například protokoly W3C HTTP vygenerovaný active aplikací jsou k dispozici, buď v adresáři protokolu v umístění síťové sdílené složky vytvořené pro aplikaci nebo k dispozici v úložišti objektů blob, pokud zákazník nastavil protokolování W3C do úložiště. Druhou možnost umožňuje velké objemy protokolů, které chcete shromáždit bez riziko překračuje limit úložiště souborů přidružené k síťové sdílené složky.

V souvislosti podobnou v reálném čase diagnostické informace z aplikací .NET můžete taky zaznamená .NET trasování a diagnostiku infrastrukturu, pomocí možnosti zapsat informace trasování do síťové složky, buď aplikace, případně k umístění úložiště objektů blob.

Oblasti diagnostiky protokolování a trasování, která nejsou k dispozici pro aplikace, jsou události Windows ETW a běžné protokoly událostí systému Windows (například systému, aplikace a zabezpečení protokoly událostí). Vzhledem k tomu, že informace o trasování ETW potenciálně lze zobrazit celého systému (s právem seznamy ACL), jsou zablokovány oprávnění ke čtení a zápis na události trasování událostí pro Windows. Vývojáři mohou Všimněte si, že volání rozhraní API pro čtení a zápis trasování událostí a běžné protokoly událostí systému Windows pravděpodobně fungovat, ale je to způsobeno služby App Service je "faking" volání, aby se zobrazily proběhla úspěšně. Ve skutečnosti kódu aplikace nemá přístup k těmto datům událostí.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Přístup k registru
Aplikace mají přístup jen pro čtení k mnohem (ale ne všechny) virtuálního počítače, že jsou spuštěny v registru. V praxi to znamená, že jsou přístupné pro aplikace klíče registru, které povolí přístup jen pro čtení do místní skupiny uživatelů. Jednou z oblastí registru, která není aktuálně podporována pro čtení nebo zápis je nastavení HKEY\_aktuální\_uživatele hive.

Zápis do registru je blokovaný, včetně přístupu k žádné klíče registru na uživatele. Z hlediska aplikace přístup k zápisu do registru by nikdy spoléhat v prostředí Azure vzhledem k tomu, že aplikace se můžou (a provést) získat proběhne migrace na jiné virtuální počítače. Pouze trvalé zapisovatelné úložiště, které může být závisí na aplikaci je struktura adresář s obsahem pro aplikaci, uložené ve sdílené složky UNC služby aplikace. 

## <a name="more-information"></a>Další informace

[Azure izolovaného prostoru webové aplikace](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – nejnovější informace o prostředí pro spuštění služby App Service. Tato stránka se spravuje přímo pomocí vývojový tým služby App Service.

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 


