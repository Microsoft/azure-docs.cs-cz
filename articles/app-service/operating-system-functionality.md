---
title: Funkce operačního systému
description: Přečtěte si o funkcích operačního systému ve službě Azure App Service ve Windows. Zjistěte, jaké typy souborů, sítí a přístupů k registru vaše aplikace získá.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: ed84cb2b0cb8d98b12fe787e49c400ba47e4e38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671615"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkce operačního systému ve službě Azure App Service
Tento článek popisuje běžné základní funkce operačního systému, které jsou k dispozici pro všechny aplikace pro Windows spuštěné ve [službě Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Tato funkce zahrnuje přístup k souborům, sítím a registru a protokoly a události diagnostiky. 

> [!NOTE] 
> [Linuxové aplikace](containers/app-service-linux-intro.md) ve službě App Service běží ve vlastních kontejnerech. Není povolen žádný přístup k hostitelskému operačnímu systému, máte root přístup ke kontejneru. Podobně pro [aplikace spuštěné v kontejnerech Systému Windows](app-service-web-get-started-windows-container.md)máte přístup správce ke kontejneru, ale žádný přístup k hostitelskému operačnímu systému. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Úrovně plánu služby App Service
Služba App Service spouští zákaznické aplikace ve víceklientském hostitelském prostředí. Aplikace nasazené na úrovních **Free** a **Shared** běží v pracovních procesech na sdílených virtuálních počítačích, zatímco aplikace nasazené na úrovních **Standard** a **Premium** běží na virtuálních počítačích určených speciálně pro aplikace přidružené k jedinému zákazníkovi.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Vzhledem k tomu, že služba App Service podporuje bezproblémové škálování mezi různými vrstvami, konfigurace zabezpečení vynucená pro aplikace služby App Service zůstává stejná. Tím zajistíte, že se aplikace nebudou náhle chovat jinak, když se neočekávaně nedaří, když se plán služby App Service přepne z jedné úrovně na druhou.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Vývojové rámce
Cenové úrovně služby App Service řídí množství výpočetních prostředků (cpu, diskové úložiště, paměť a odchozí síťový přenos) dostupné pro aplikace. Rozsah funkce rozhraní, které jsou k dispozici pro aplikace však zůstává stejný bez ohledu na škálování vrstev.

Služba App Service podporuje celou řadu vývojových architektur, včetně ASP.NET, klasického prostředí ASP, node.js, PHP a Pythonu – to vše běží jako rozšíření ve službě IIS. Za účelem zjednodušení a normalizace konfigurace zabezpečení aplikace App Service obvykle spouštějí různé vývojové architektury s výchozím nastavením. Jedním z přístupů ke konfiguraci aplikací mohlo být přizpůsobení oblasti a funkcí rozhraní API pro každý jednotlivý vývojový rámec. Služba App Service místo toho používá obecnější přístup tím, že umožňuje společný směrný plán funkčnosti operačního systému bez ohledu na vývojové rozhraní aplikace.

Následující části shrnují obecné druhy funkcí operačního systému, které jsou k dispozici pro aplikace App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Přístup k souborům
V rámci služby App Service existují různé jednotky, včetně místních a síťových jednotek.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Místní jednotky
Ve své podstatě app service je služba spuštěná nad infrastrukturou Azure PaaS (platforma jako služba). V důsledku toho místní jednotky, které jsou "připojené" k virtuálnímu počítači jsou stejné typy jednotek, které jsou k dispozici pro všechny role pracovního procesu spuštěné v Azure. To zahrnuje:

- Jednotka operačního systému (D:\ pohonu)
- Aplikační jednotka, která obsahuje soubory Azure Package cspkg používané výhradně službou App Service (a nepřístupné zákazníkům)
- Jednotka "uživatel" (C:\ disk), jehož velikost se liší v závislosti na velikosti virtuálního počítače. 

Je důležité sledovat využití disku, jak vaše aplikace roste. Pokud je dosaženo diskové kvóty, může mít nepříznivé účinky na vaši aplikaci. Například: 

- Aplikace může vyvolat chybu označující nedostatek místa na disku.
- Při procházení konzole Kudu se mohou zobrazit chyby disku.
- Nasazení z Azure DevOps nebo `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`Visual Studio může selhat s .
- Vaše aplikace může trpět pomalým výkonem.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Síťové jednotky (aka UNC akcie)
Jedním z jedinečných aspektů služby App Service, která usnadňuje nasazení a údržbu aplikací, je, že veškerý uživatelský obsah je uložen v sadě sdílených složek UNC. Tento model se dobře mapuje na běžný vzor úložiště obsahu používaného místními webhostingovými prostředími, která mají více serverů s vyrovnáváním zatížení. 

V rámci služby App Service je v každém datovém centru vytvořenpočet sdílených složek UNC. Procento uživatelského obsahu pro všechny zákazníky v každém datovém centru je přiděleno každé sdílené složce UNC. Kromě toho veškerý obsah souboru pro odběr jednoho zákazníka je vždy umístěn na stejnou sdílenou složku UNC. 

Vzhledem k tomu, jak služby Azure fungují, konkrétní virtuální počítač zodpovědný za hostování sdílené složky UNC se v průběhu času změní. Je zaručeno, že akcie UNC budou připojeny různými virtuálními počítači, protože jsou vyvolány nahoru a dolů během normálního průběhu operací Azure. Z tohoto důvodu by aplikace nikdy neměly provádět pevně zakódované předpoklady, že informace o počítači v cestě k souboru UNC zůstanou v průběhu času stabilní. Místo toho by měli použít pohodlnou *falešnou* absolutní cestu **D:\home\site,** kterou poskytuje služba App Service. Tato falešná absolutní cesta poskytuje přenosnou metodu agnostik aplikace a uživatele pro odkazování na vlastní aplikaci. Pomocí **D:\home\site**lze přenášet sdílené soubory z aplikace do aplikace, aniž by bylo nutné konfigurovat novou absolutní cestu pro každý přenos.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy přístupu k souborům uděleným aplikaci
Každé předplatné zákazníka má vyhrazenou adresářovou strukturu na konkrétní sdílené složce UNC v datovém centru. Zákazník může mít v rámci konkrétního datového centra vytvořeno více aplikací, takže všechny adresáře patřící do jednoho zákaznického předplatného jsou vytvořeny ve stejné sdílené složce UNC. Sdílená složky mohou zahrnovat adresáře, například pro obsah, protokoly chyb a diagnostiky a starší verze aplikace vytvořené pomocí správy zdrojového kódu. Podle očekávání jsou adresáře aplikací zákazníka k dispozici pro čtení a zápis přístup u běhu pomocí kódu aplikace.

Na místních jednotkách připojených k virtuálnímu počítači, na kterých běží aplikace, služba App Service rezervuje na C:\ jednotky pro dočasné místní úložiště specifické pro aplikaci. Přestože aplikace má úplný přístup pro čtení a zápis do vlastního dočasného místního úložiště, toto úložiště opravdu není určeno k přímému použití kódem aplikace. Záměrem je spíše poskytnout dočasné úložiště souborů pro rozhraní IIS a rozhraní webových aplikací. Služba App Service také omezuje množství dočasného místního úložiště, které je k dispozici pro každou aplikaci, aby se zabránilo tomu, že jednotlivé aplikace budou využívat nadměrné množství místního úložiště souborů.

Dva příklady použití dočasného místního úložiště služby App Service jsou adresář pro dočasné ASP.NET soubory a adresář pro komprimované soubory služby IIS. Systém kompilace ASP.NET používá adresář Dočasné ASP.NET soubory jako dočasné umístění mezipaměti kompilace. Služba IIS používá k ukládání komprimovaného výstupu odezvy adresář dočasné komprimované soubory služby IIS. Oba tyto typy použití souborů (stejně jako ostatní) jsou přemapovány ve službě App Service na dočasné místní úložiště pro aplikaci. Toto přemapování zajišťuje, že funkce bude pokračovat podle očekávání.

Každá aplikace ve službě App Service běží jako náhodná jedinečná identita pracovního procesu s [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities)nízkými oprávněními nazvaná "identita fondu aplikací", která je dále popsána zde: . Kód aplikace používá tuto identitu pro základní přístup jen pro čtení k jednotce operačního systému (D:\ jednotky). To znamená, že kód aplikace může seznam společných adresářových struktur a číst běžné soubory na jednotce operačního systému. I když se to může zdát být poněkud široká úroveň přístupu, stejné adresáře a soubory jsou přístupné při zřízení role pracovního procesu v hostované službě Azure a číst obsah jednotky. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Přístup k souborům ve více instancích
Domovský adresář obsahuje obsah aplikace a kód aplikace do něj může zapisovat. Pokud aplikace běží na více instancích, domovský adresář je sdílen mezi všemi instancemi, takže všechny instance vidí stejný adresář. Pokud například aplikace uloží nahrané soubory do domovského adresáře, budou tyto soubory okamžitě k dispozici všem instancím. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Síťový přístup
Kód aplikace může používat protokoly TCP/IP a UDP k připojení odchozích síťových připojení k koncovým bodům přístupným v Internetu, které zveřejňují externí služby. Aplikace můžou používat stejné protokoly k připojení ke službám v rámci Azure, například navázáním připojení HTTPS k databázi SQL.

K dispozici je také omezená možnost pro aplikace navázat jedno připojení místní smyčky zpět a mít aplikaci poslouchat na místní straně zpětné smyčky soketu. Tato funkce existuje především povolit aplikace, které poslouchají na sokety místní smyčky jako součást jejich funkce. Každá aplikace vidí "soukromé" zpětné připojení. Aplikace "A" nemůže poslouchat soket místní smyčky vytvořené aplikací "B".

Pojmenované kanály jsou také podporovány jako mechanismus meziprocesové komunikace (IPC) mezi různými procesy, které společně spouštějí aplikaci. Například modul IIS FastCGI spoléhá na pojmenované kanály ke koordinaci jednotlivých procesů, které spouštějí stránky PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Spuštění kódu, procesy a paměť
Jak již bylo uvedeno dříve, aplikace běží uvnitř pracovních procesů s nízkými oprávněními pomocí náhodné identity fondu aplikací. Kód aplikace má přístup k paměťovému prostoru přidruženému k pracovnímu procesu, stejně jako ke všem podřízeným procesům, které mohou být zplozeny procesy CGI nebo jinými aplikacemi. Jedna aplikace však nemůže získat přístup k paměti nebo datům jiné aplikace, i když je na stejném virtuálním počítači.

Aplikace mohou spouštět skripty nebo stránky napsané pomocí podporovaných webových vývojových architektur. Služba App Service nekonfiguruje žádné nastavení webového rámce pro omezenější režimy. Například ASP.NET aplikace spuštěné ve službě App Service běží v "úplném" vztahu důvěryhodnosti, na rozdíl od omezenějšího režimu důvěryhodnosti. Webové architektury, včetně klasických a a ASP.NET, mohou volat v procesu komponenty COM (ale ne mimo proces com komponenty), jako je ADO (ActiveX Data Objects), které jsou registrovány ve výchozím nastavení v operačním systému Windows.

Aplikace se mohou spouštět a spouštět libovolný kód. Je přípustné pro aplikaci dělat věci, jako je twn příkaz shell nebo spustit skript Prostředí PowerShell. I když však libovolný kód a procesy lze z aplikace vytvořit, spustitelné programy a skripty jsou stále omezeny na oprávnění udělená nadřazenému fondu aplikací. Aplikace může například vytvořit spustitelný soubor, který provádí odchozí volání HTTP, ale stejný spustitelný soubor se nemůže pokusit zrušit vazbu IP adresy virtuálního počítače z jeho nic. Volání odchozí sítě je povoleno kódu s nízkými oprávněními, ale pokus o překonfiguraci nastavení sítě ve virtuálním počítači vyžaduje oprávnění správce.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostické protokoly a události
Informace protokolu je další sada dat, ke které se některé aplikace pokoušejí získat přístup. Typy informací protokolu, které jsou k dispozici pro kód spuštěný ve službě App Service zahrnuje diagnostické a protokolové informace generované aplikací, která je také snadno přístupná aplikaci. 

Protokoly HTTP W3C generované aktivní aplikací jsou například k dispozici buď v adresáři protokolu v umístění sdílené síťové složky vytvořeném pro aplikaci, nebo v úložišti objektů blob, pokud zákazník nastavil protokolování W3C do úložiště. Druhá možnost umožňuje velké množství protokolů, které mají být shromážděny bez rizika překročení limitů úložiště souborů spojených se sdílenou sítí.

V podobném průběhu, v reálném čase diagnostické informace z aplikací .NET lze také protokolovat pomocí .NET trasování a diagnostiky infrastruktury, s možnostmi zapsat informace o trasování buď sdílené síťové služby aplikace nebo alternativně do umístění úložiště objektů blob.

Oblasti protokolování a trasování diagnostiky, které nejsou k dispozici pro aplikace, jsou události Windows ETW a běžné protokoly událostí systému Windows (například protokoly událostí Systém, Aplikace a Zabezpečení). Vzhledem k tomu, že informace o trasování ETW může být potenciálně zobrazitelné celé počítače (se správnými seznamy ACL), přístup pro čtení a zápis k událostem ETW jsou blokovány. Vývojáři si mohou všimnout, že volání rozhraní API pro čtení a zápis událostí ETW a běžné protokoly událostí systému Windows se zdají fungovat, ale to je proto, že služba App Service "předstírá" volání tak, aby se zdá, že jsou úspěšné. Ve skutečnosti kód aplikace nemá přístup k datům této události.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Přístup k registru
Aplikace mají přístup jen pro čtení k hodně (i když ne všechny) registru virtuálního počítače, na který běží. V praxi to znamená, že klíče registru, které umožňují přístup jen pro čtení k místní skupině Users, jsou přístupné aplikacemi. Jednou z oblastí registru, která není aktuálně podporována pro přístup\_\_pro čtení nebo zápis, je podregistr HKEY CURRENT USER.

Přístup pro zápis do registru je blokován, včetně přístupu ke všem klíčům registru pro jednotlivé uživatele. Z pohledu aplikace by se přístup pro zápis do registru nikdy neměl spoléhat v prostředí Azure, protože aplikace se můžou (a dělají) migrovat na příčce různých virtuálních počítačů. Jediné trvalé úložiště, na které může aplikace záviset, je adresářová struktura obsahu pro aplikaci uložená ve sdílených složek UNC služby App Service. 

## <a name="remote-desktop-access"></a>Přístup ke vzdálené ploše

Služba App Service neposkytuje přístup ke vzdálené ploše k instanvám virtuálních počítačů.

## <a name="more-information"></a>Další informace

[Azure App Service izolovaného prostoru](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – nejaktuálnější informace o prostředí provádění služby App Service. Tato stránka je spravována přímo vývojovým týmem služby App Service.

