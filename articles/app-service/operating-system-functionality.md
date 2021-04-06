---
title: Funkce operačního systému
description: Přečtěte si o funkcích operačního systému v Azure App Service ve Windows. Zjistěte, jaké typy souborů, sítí a přístup k registru vaše aplikace získá.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: 949e408544e25cb55622cf2a1b1d2dddb92350a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001503"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkce operačního systému na Azure App Service
Tento článek popisuje běžné funkce operačního systému pro základní hodnoty, které jsou k dispozici pro všechny aplikace pro Windows běžící na [Azure App Service](./overview.md). Tato funkce zahrnuje přístup k souboru, k síti a k registru a diagnostické protokoly a události. 

> [!NOTE] 
> [Aplikace pro Linux](overview.md#app-service-on-linux) v App Service spustit ve svých vlastních kontejnerech. Není povolený žádný přístup k hostitelskému operačnímu systému, máte ke kontejneru přístup rootem. Stejně tak pro [aplikace spuštěné v kontejnerech Windows](quickstart-custom-container.md?pivots=container-windows)máte k kontejneru přístup pro správu, ale k hostitelskému operačnímu systému nemáte přístup. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service vrstev plánu
App Service spouští aplikace zákazníka v hostitelském prostředí s více klienty. Aplikace nasazené na úrovni **Free** a **Shared** běží v pracovních procesech na sdílených virtuálních počítačích, zatímco aplikace nasazené na úrovních **Standard** a **Premium** běží na virtuálních počítačích, které jsou vyhrazené speciálně pro aplikace přidružené k jednomu zákazníkovi.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Vzhledem k tomu, že App Service podporuje bezproblémové škálování mezi různými úrovněmi, konfigurace zabezpečení vydaná pro App Service aplikace zůstane stejná. Tím se zajistí, že se aplikace neočekávaně chovají jinak, neočekávané způsoby, když App Service plán přepíná z jedné vrstvy do druhé.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Vývojové architektury
App Service cenové úrovně řídí množství výpočetních prostředků (CPU, diskového úložiště, paměti a odchozího přenosu v síti) dostupných pro aplikace. Šířka funkcí architektury dostupných pro aplikace však zůstává stejná bez ohledu na úroveň škálování.

App Service podporuje různé vývojové platformy, včetně ASP.NET, klasických prostředí ASP, node.js, PHP a Pythonu – všechna spuštění jako rozšíření v rámci služby IIS. Aby bylo možné zjednodušit a normalizovat konfiguraci zabezpečení, aplikace App Service obvykle spouští různé vývojové architektury s jejich výchozími nastaveními. Jedním z přístupů ke konfiguraci aplikací bylo, že by bylo možné přizpůsobit oblast a funkčnost rozhraní API pro jednotlivé vývojové platformy. App Service místo toho získá obecnější přístup tím, že umožní běžné standardní hodnoty funkcí operačního systému bez ohledu na vývojové rozhraní aplikace.

V následujících částech najdete souhrn obecných druhů funkcí operačního systému, které jsou dostupné pro App Service aplikace.

<a id="FileAccess"></a>

## <a name="file-access"></a>Přístup k souborům
V App Service existují různé jednotky, včetně místních jednotek a síťových jednotek.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Místní jednotky
App Service je služba, která běží nad infrastrukturou Azure PaaS (platforma jako služba). V důsledku toho jsou místní jednotky připojené k virtuálnímu počítači stejné typy jednotek, které jsou k dispozici pro všechny role pracovního procesu spuštěné v Azure. Sem patří:

- Jednotka operačního systému (D:\ disky
- Jednotka aplikace, která obsahuje soubory cspkg balíčku Azure, používané výhradně App Service (a nepřístupné pro zákazníky)
- "Uživatelská" jednotka (C:\ jednotka), jejíž velikost se liší v závislosti na velikosti virtuálního počítače. 

Při zvětšování vaší aplikace je důležité monitorovat využití disku. Pokud je dosažena kvóta disku, může to mít nepříznivý vliv na vaši aplikaci. Například: 

- Aplikace může vyvolat chybu oznamující, že na disku není dostatek místa.
- Při procházení konzoly Kudu se může zobrazit chyba disku.
- Nasazení z Azure DevOps nebo Visual studia může selhat s `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)` .
- Vaše aplikace může zpomalit výkon.

<a id="NetworkDrives"></a>

### <a name="network-drives-unc-shares"></a>Síťové jednotky (sdílené složky UNC)
Jedním z jedinečných aspektů App Service, které usnadňuje nasazení a údržbu aplikací, je, že veškerý obsah uživatele je uložený v sadě sdílených složek UNC. Tento model se dobře mapuje na společný vzor úložiště obsahu používaného místními prostředími pro hostování webů s více servery s vyrovnáváním zatížení. 

V rámci App Service je v každém datovém centru vytvořeno několik sdílených složek UNC. Procentuální podíl obsahu uživatele pro všechny zákazníky v každém datovém centru je přidělen všem sdíleným složkám UNC. Kromě toho všechen obsah souborů pro předplatné jednoho zákazníka je vždycky umístěný ve stejné sdílené složce UNC. 

V důsledku toho, jak služby Azure fungují, se konkrétní virtuální počítač zodpovědný za hostování sdílené složky UNC v průběhu času změní. Je zaručeno, že sdílené složky UNC budou připojeny k různým virtuálním počítačům při jejich zapínání a vystavování během normálního provozu v rámci Azure. Z tohoto důvodu by aplikace nikdy nevedly pevně zakódované předpoklady, že informace o počítači v cestě k souboru ve formátu UNC zůstanou v průběhu času stabilní. Místo toho by měli používat pohodlný D:\home\site *umělé* absolutní cesty  , který App Service poskytuje. Tato umělá absolutní cesta poskytuje přenosnou, aplikační a uživatelsky nezávislá metodu pro odkazování na vlastní aplikaci. Pomocí **D:\home\site** může jeden přenést sdílené soubory z aplikace do aplikace, aniž by bylo nutné konfigurovat novou absolutní cestu pro každý přenos.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy přístupu k souborům udělených aplikaci
Předplatné každého zákazníka má vyhrazenou adresářovou strukturu v konkrétní sdílené složce UNC v rámci datového centra. Zákazník může mít v rámci konkrétního datového centra vytvořené několik aplikací, takže všechny adresáře patřící do jednoho zákaznického předplatného se vytvoří ve stejné sdílené složce UNC. Sdílená složka může obsahovat adresáře, jako jsou například pro obsah, chyby a diagnostické protokoly, a dřívější verze aplikace vytvořené správou zdrojových kódů. Jak bylo očekáváno, adresáře aplikací zákazníka jsou k dispozici pro přístup pro čtení a zápis za běhu v kódu aplikace aplikace.

Na místních jednotkách připojených k virtuálnímu počítači, na kterém běží aplikace, App Service rezervuje blok místa na C:\. jednotka pro dočasné místní úložiště specifické pro aplikaci. I když má aplikace úplný přístup pro čtení i zápis k vlastnímu dočasnému místnímu úložišti, toto úložiště opravdu není určené k použití přímo v kódu aplikace. Místo toho záměr je poskytnout dočasné úložiště souborů pro službu IIS a architektury webových aplikací. App Service taky omezuje velikost dočasného místního úložiště dostupného pro každou aplikaci, aby jednotlivé aplikace bránily využívání nadměrného množství místních úložišť souborů.

Dva příklady, jak App Service používá dočasné místní úložiště, jsou adresářem pro dočasné soubory ASP.NET a adresář pro komprimované soubory IIS. Systém kompilace ASP.NET využívá dočasný adresář ASP.NET Files jako dočasné umístění mezipaměti kompilace. Služba IIS pomocí adresáře "dočasné komprimované soubory služby IIS" ukládá komprimovaný výstup odpovědi. Oba tyto typy použití souborů (stejně jako ostatní) jsou přemapovány v App Service na dočasné místní úložiště pro aplikaci. Toto přemapování zajišťuje, že funkčnost bude pokračovat podle očekávání.

Každá aplikace v App Service běží jako náhodná jedinečná identita pracovního procesu s nízkými oprávněními označovaná jako identita fondu aplikací, která je popsána dále: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) . Kód aplikace používá tuto identitu pro základní přístup jen pro čtení k jednotce operačního systému (D:\ jednotka). To znamená, že kód aplikace může vypsat společné adresářové struktury a číst běžné soubory na jednotce operačního systému. I když se to může zdát poněkud široká úroveň přístupu, stejné adresáře a soubory jsou přístupné při zřizování role pracovního procesu v hostované službě Azure a čtení obsahu jednotky. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Přístup k souborům napříč více instancemi
Domovský adresář obsahuje obsah aplikace a kód aplikace do něj může zapisovat. Pokud aplikace běží na více instancích, domovský adresář se sdílí mezi všemi instancemi, aby všechny instance viděli stejný adresář. Takže pokud aplikace například ukládá nahrané soubory do domovského adresáře, jsou tyto soubory okamžitě k dispozici všem instancím. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Síťový přístup
Kód aplikace může používat protokoly TCP/IP a UDP k vytváření odchozích síťových připojení k internetovým koncovým bodům, které zveřejňují externí služby. Aplikace můžou používat stejné protokoly pro připojení ke službám v rámci Azure, třeba navázáním připojení HTTPS k SQL Database.

Existují také omezené možnosti, jak si aplikace navázat jedno připojení k místní zpětné smyčce a že aplikace naslouchají tomuto místnímu soketu. Tato funkce je primárně navržena tak, aby v rámci svých funkcí povolovala aplikace, které na místních soketech zpětné vazby naslouchají. Každá aplikace se dohlíží na "privátní" připojení zpětné smyčky. Aplikace "A" nemůže naslouchat místnímu soketu zpětné smyčky, který vytvořila aplikace "B".

Pojmenované kanály jsou podporované taky jako mechanismus komunikace mezi procesy (IPC) mezi různými procesy, které společně spouštějí aplikaci. Modul IIS FastCGI například spoléhá na pojmenované kanály, aby koordinovaly jednotlivé procesy, které spouštějí stránky PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Spuštění kódu, procesy a paměť
Jak bylo uvedeno dříve, aplikace běží v pracovních procesech s nízkými oprávněními pomocí náhodné identity fondu aplikací. Kód aplikace má přístup k prostoru paměti přidruženému k pracovnímu procesu a všem podřízeným procesům, které mohou být vytvořeny procesy CGI nebo jinými aplikacemi. Jedna aplikace ale nemůže získat přístup k paměti nebo datům jiné aplikace, i když se nachází na stejném virtuálním počítači.

Aplikace můžou spouštět skripty nebo stránky napsané s podporovanými rozhraními pro vývoj webu. App Service nekonfiguruje žádná nastavení webového rozhraní na více režimů s omezením. Například aplikace ASP.NET spuštěné v App Service běžet v "plném" vztahu důvěryhodnosti na rozdíl od více omezeného režimu důvěryhodnosti. Webové architektury, včetně klasických ASP a ASP.NET, mohou volat komponenty modelu COM v rámci procesu (ale ne mimo procesy komponent modelu COM), jako jsou ADO (rozhraní ADO (ActiveX Data Objects)), které jsou ve výchozím nastavení registrovány v operačním systému Windows.

Aplikace můžou vytvořit libovolný kód a spustit ho. Je možné, že aplikace provede akce, jako je například vytvoření příkazového prostředí nebo spuštění skriptu PowerShellu. I když lze v aplikaci vytvořit libovolný kód a procesy, spustitelné programy a skripty jsou stále omezeny na oprávnění udělená nadřazenému fondu aplikací. Aplikace může například vytvořit spustitelný soubor, který provede odchozí volání HTTP, ale stejný spustitelný soubor se nemůže pokusit zrušit vazbu IP adresy virtuálního počítače z jeho síťové karty. Zpřístupnění odchozího síťového volání může mít kód s nízkou úrovní oprávnění, ale při pokusu o překonfigurování nastavení sítě na virtuálním počítači budete potřebovat oprávnění správce.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostické protokoly a události
Informace protokolu jsou další sada dat, ke kterým se aplikace pokouší získat přístup. Typy informací o protokolech, které jsou k dispozici pro kód běžící v App Service, zahrnují informace o diagnostice a protokolu vygenerované aplikací, která je také snadno přístupná pro aplikaci. 

Například protokoly W3C HTTP vygenerované aktivní aplikací jsou k dispozici buď v adresáři protokolu v umístění sdílené síťové složky vytvořeném pro aplikaci, nebo dostupné v úložišti objektů blob, pokud zákazník nastavil protokolování W3C do úložiště. Možnost druhá umožňuje shromažďovat velké množství protokolů bez rizika překračujícího limity úložiště souborů spojené se sdílenou síťovou složkou.

V podobných informacích o diagnostice v reálném čase z aplikací .NET se taky dají protokolovat pomocí infrastruktury trasování a diagnostiky .NET s možnostmi zápisu informací o trasování do síťové sdílené složky aplikace nebo do umístění úložiště objektů BLOB.

Oblasti protokolování a trasování diagnostiky, které nejsou k dispozici pro aplikace, jsou události ETW systému Windows a běžné protokoly událostí systému Windows (například protokoly událostí systému, aplikace a zabezpečení). Vzhledem k tomu, že informace o trasování ETW můžou být zobrazitelné na úrovni počítače (se správnými seznamy ACL), zablokují se přístup pro čtení a zápis událostí ETW. Vývojáři si můžou všimnout, že volání rozhraní API pro čtení a zápis událostí ETW a běžné protokoly událostí Windows se zdají fungovat, ale to znamená, že App Service jsou voláním "Faking", aby se zobrazila úspěšná. Ve skutečnosti nemá kód aplikace žádný přístup k těmto datům události.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Přístup k registru
Aplikace mají přístup jen pro čtení k mnoha (ale ne všem) registru virtuálního počítače, na kterém běží. V praxi to znamená, že aplikace budou mít k dispozici klíče registru, které umožňují přístup jen pro čtení k místní skupině uživatelů. Jedna oblast registru, která se v tuto chvíli nepodporuje pro přístup pro čtení nebo zápis, je HKEY \_ aktuálním \_ podregistrem uživatele.

Přístup pro zápis do registru je zablokovaný, včetně přístupu k jakýmkoli klíčům registru pro jednotlivé uživatele. Z pohledu aplikace by se v prostředí Azure nikdy neměl spoléhat na přístup k zápisu do registru, protože aplikace se můžou (a dělat) migrovat mezi různými virtuálními počítači. Jediným trvalým úložištěm, které může být závislé na aplikaci, je struktura adresáře obsahu pro aplikaci uloženou ve sdílených složkách App Service UNC. 

## <a name="remote-desktop-access"></a>Přístup ke vzdálené ploše

App Service neposkytuje přístup ke vzdálené ploše pro instance virtuálních počítačů.

## <a name="more-information"></a>Další informace

[Azure App Service izolovaný prostor (sandbox)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – nejaktuálnější informace o spouštěcím prostředí App Service. Tuto stránku uchovává přímo vývojový tým App Service.