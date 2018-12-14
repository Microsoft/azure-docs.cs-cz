---
title: Funkce operačního systému ve službě App Service – Azure
description: Další informace o funkci operačního systému k dispozici pro webové aplikace, back-EndY mobilních aplikací a API apps ve službě Azure App Service
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
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bb17748968d0875cae132730a02f490eab966690
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388805"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkce operačního systému ve službě Azure App Service
Tento článek popisuje běžné funkce operačního systému směrný plán, který je k dispozici pro všechny aplikace Windows běží na [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Tato funkce zahrnuje soubor, sítě a přístup k registru a diagnostické protokoly a události. 

> [!NOTE] 
> [Linuxové aplikace](containers/app-service-linux-intro.md) ve službě App Service, spusťte ve své vlastní kontejnery. Je povolen přístup k hostitelskému operačnímu systému, mají kořenový přístup ke kontejneru. Podobně pro [aplikací spouštěných v kontejnerech Windows](app-service-web-get-started-windows-container.md), máte přístup pro správu do kontejneru, ale žádný přístup k hostitelskému operačnímu systému. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Úrovně plánu služby App Service
Služby App Service běží v hostitelském prostředí s více tenanty zákaznických aplikací. Aplikace nasazené v **Free** a **Shared** úrovně spustit v pracovních procesů na sdílených virtuálních počítačích, zatímco aplikace nasazené v **standardní** a **Premium**  úrovně spouštět virtuální počítače vyhrazené speciálně pro aplikace přidružené k jediného zákazníka.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Protože App Service podporuje bezproblémové škálování prostředí mezi různé vrstvy, konfiguraci zabezpečení pro aplikace služby App Service se nezmění. Tím se zajistí, že aplikace není odlišným najednou, selhání neočekávaně, když plán služby App Service se přepne z jedné vrstvy do druhé.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Vývojářské platformy
Cenové úrovně služby App Service řízení množství výpočetních prostředků (procesor, diskové úložiště, paměť a odchozí síťovou komunikaci) k dispozici pro aplikace. Šířka framework funkce, které jsou k dispozici pro aplikace však zůstává stejný bez ohledu na škálování úrovně.

App Service podporuje celou řadu vývojářských platforem, včetně ASP.NET, klasického ASP, node.js, PHP a Pythonu – všechny z nich spustit jako rozšíření v rámci služby IIS. Abyste zjednodušili a normalizovat konfiguraci zabezpečení, aplikace služby App Service obvykle běží různých vývojářských platforem s jejich výchozí nastavení. Přizpůsobení svrchní oblasti rozhraní API a funkce pro každé rozhraní, jednotlivé vývojové mohlo být jeden ze způsobů konfigurace aplikací. App Service používá místo toho obecnější přístup tím, že běžné základní funkce operačního systému bez ohledu na to rozhraní pro vývoj vaší aplikace.

Následující části shrnují obecné typy funkce operačního systému k dispozici pro aplikace služby App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Přístup k souborům
Existují různé jednotky v rámci služby App Service, včetně místní jednotky a síťové jednotky.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Místní jednotky pevných disků
Ve své podstatě služby App Service je služba spuštěná nad infrastrukturou Azure PaaS (platforma jako služba). V důsledku toho místní jednotky, které jsou "připojeno" k virtuálnímu počítači se stejnými typy jednotky dostupné pro všechny role pracovních procesů spuštěných v Azure. To zahrnuje:

- Jednotky operačního systému (jednotku D:\)
- Disku s aplikací, který obsahuje soubory balíčku Azure cspkg používá výhradně ve službě App Service (a znepřístupníme pro zákazníky)
- "User" jednotka (jednotka C:\), jejichž velikost se liší v závislosti na velikosti virtuálního počítače. 

Je důležité monitorovat vaše využití disku s růstem vaší aplikace. Když se dosáhne kvóty disku může mít nežádoucí účinky do vaší aplikace. Příklad: 

- Aplikace může vyvolat chybu s informacemi není dostatek místa na disku.
- Když přejdete do konzoly Kudu, může se zobrazit chyby na disku.
- Nasazování z VSTS nebo sady Visual Studio může selhat s `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`.
- Vaše aplikace může dojít ke snížení nízký výkon.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Síťových jednotkách (označuje se také jako UNC sdílené složky)
Jeden jedinečné aspekty služby App Service, která umožňuje jednoduché nasazení aplikací a údržbu se, že veškerý obsah uživatele ukládají na sadu sdílené složky UNC. Tento model se mapuje dobře běžný vzor obsahu úložiště využitá službou místní webové hostitelské prostředí, která mají víc serverů s vyrovnáváním zatížení. 

V rámci služby App Service je počet UNC sdílené složky vytvořené v každé datové centrum. Procentuální podíl obsahu uživatele pro všechny zákazníky v každé datové centrum je přidělena na každé sdílené složky UNC. Kromě toho všechny obsahu pro jednoho zákazníka předplatné je vždy umístit na stejnou cestu UNC souboru sdílet. 

Kvůli práci služeb Azure budou průběžně měnit konkrétního virtuálního počítače za hostování sdílenou jednotku UNC. Je zaručeno, že sdílené složky UNC se připojí jiné virtuální počítače, jak kliknou, přesměrují se nahoru a dolů během normálního chodu operací Azure. Z tohoto důvodu aplikace by nikdy domněnky pevně zakódované, že informace o počítači v cestě k souboru UNC zůstane stabilní v čase. Místo toho měli používat praktické *umělá proměnná* absolutní cesta **D:\home\site** poskytující služby App Service. Toto umělá proměnná absolutní cesta způsobem portable, aplikace a uživatel – bez ohledu na odkazující na jeden z vlastní aplikace. S použitím **D:\home\site**, jeden můžou přenášet sdílené soubory z aplikací bez nutnosti konfigurovat nový absolutní cestu pro každý přenos.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy souborů udělení přístupu k aplikaci
Vyhrazené adresářovou strukturu má každé zákaznické předplatné na konkrétní sdílenou jednotku UNC v rámci datového centra. Zákazník může mít více aplikací vytvořených v rámci konkrétní datového centra, takže všechny adresáře, které patří do předplatného jednoho zákazníka se vytvářejí na stejnou cestu UNC sdílené složky. Sdílená složka může zahrnout adresáře třeba kroky týkající se obsahu, chyba a diagnostické protokoly a dřívějších verzích aplikace vytvořená pomocí správy zdrojového kódu. Podle očekávání, na základě aplikace adresáře jsou k dispozici pro oprávnění ke čtení a zápisu za běhu aplikace kód aplikace.

Na místní diskové jednotky připojené k virtuálnímu počítači, na kterém běží aplikace služby App Service si vyhrazuje blok místa na jednotce C:\ pro dočasné ukládání místních konkrétní aplikace. I když má aplikace přístup Úplné čtení a zápis do vlastní místní dočasné úložiště, že úložiště ve skutečnosti není určena pro použití přímo kódem aplikace. Místo toho cílem je zajistit ukládání dočasných souborů pro službu IIS a web aplikačních architektur. Velikost dočasné místní úložiště k dispozici pro každou aplikaci, aby zabránila jednotlivými aplikacemi ve využívání nadměrné množství úložiště místního souboru také limity služby App Service.

Adresář pro dočasné soubory ASP.NET jsou dva příklady jak služba App Service používá místní dočasné úložiště a komprimované soubory v adresáři služby IIS. Systém kompilace technologie ASP.NET používá jako umístění mezipaměti dočasné kompilace adresáře "Temporary ASP.NET Files". Služba IIS používá "IIS dočasné komprimované soubory" adresář k uložení výstupu zkomprimovanou odpověď. Oba tyto typy souborů využití (stejně jako ostatní) budou přemapovány na dočasný místní úložiště pro aplikaci ve službě App Service. Přemapování se zajistí, že funkce pokračuje podle očekávání.

Každou aplikaci ve službě App Service běží jako identitu náhodné jedinečný s nízkým oprávněním pracovního procesu nazývá "identita fondu aplikací", zde popsané dál: [ https://www.iis.net/learn/manage/configuring-security/application-pool-identities ](https://www.iis.net/learn/manage/configuring-security/application-pool-identities). Kód aplikace tuto identitu použije pro základní přístup jen pro čtení na jednotce operačního systému (jednotku D:\). To znamená, že kód aplikace může seznam běžných struktur adresářů a čtení běžných souborů na jednotce operačního systému. I když to může zdát poněkud obecné úrovni přístupu stejné adresářů a souborů jsou dostupné při zřizování role pracovního procesu v Azure hostovaná služba a číst obsah jednotky. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Přístup k souborům na více instancí
Domovský adresář obsahuje obsah aplikace a do ní můžete zapisovat kód aplikace. Pokud aplikace běží na více instancí, domovský adresář se sdílí mezi všemi instancemi tak, aby všechny instance stejného adresáře. Ano Pokud aplikace ukládá nahraných souborech do domovského adresáře, tyto soubory jsou například okamžitě k dispozici pro všechny instance. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Síťový přístup
Kód aplikace můžete použít protokol TCP/IP a protokoly založené na protokolu UDP odchozí síťové připojení k přístupnými koncovými body Internet, které zpřístupňují služby pro externí. Aplikace můžou používat tyto stejné protokoly pro připojení ke službám v rámci Azure&#151;například tím, že připojení HTTPS ke službě SQL Database.

Je také omezenou funkčností a vytvořit jedno připojení místního zpětné smyčky a mají aplikace naslouchat na tomto soketu zpětnou smyčku místní aplikace. Tato funkce je především k povolení aplikace, které naslouchat na místní zpětné smyčky sockets jako součást své funkce. Každé aplikaci, která se zobrazí "privátní" zpětné smyčky připojení. Aplikace "A" nemůže naslouchat na soket místní zpětné smyčky stanovené aplikace "B".

Pojmenované kanály podporují také jako mechanismus meziprocesové komunikace (IPC) mezi různými procesy, které souhrnně spuštění aplikace. Například modul FastCGI služby IIS spoléhá na pojmenované kanály ke koordinaci jednotlivých procesů, na kterých běží stránky PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Spuštění kódu, procesy a paměti
Jak je uvedeno výše, aplikace běží v rámci služby s nízkým oprávněním pracovních procesů pod identitou fondu náhodné aplikace. Kód aplikace má přístup k paměťový prostor, přidružené pracovní proces, stejně jako všechny podřízené procesy, které mohou být vytvoří podřízený proces CGI procesy nebo jiné aplikace. Však nelze jednu aplikaci přístup, paměť nebo data z jiné aplikace i v případě, že je na stejném virtuálním počítači.

Aplikace můžete spustit skripty nebo stránek zapsaných pomocí podporované webové vývojářské platformy. Služby App Service není nakonfigurovat nastavení jakékoli webové rozhraní framework omezenější režimy. Aplikace ASP.NET běžící ve službě App Service je třeba spustit v "úplné" vztahu důvěryhodnosti na rozdíl od více omezený režim vztahu důvěryhodnosti. Webové platformy, včetně rozhraní classic ASP a ASP.NET, můžete volat komponenty modelu COM v procesu (ale ne out komponent modelu COM, proces) jako ADO (ActiveX Data Objects), které jsou registrované ve výchozím nastavení v operačním systému Windows.

Aplikace můžete spustit a spustit libovolný kód. Je povolené, může aplikace provádět věci, jako je vytvořit podřízený proces příkazového prostředí nebo spusťte skript prostředí PowerShell. I v případě, že libovolný kód a procesů může být vytvořený z aplikace, spustitelné programy a skripty jsou však stále omezena na oprávněních udělených k nadřazené fondu aplikací. Například můžete aplikaci spustit spustitelný soubor, který provádí odchozích volání HTTP, ale, že stejné spustitelného souboru nelze pokus o odpojení IP adresu virtuálního počítače z jeho síťový adaptér Volání odchozí síťové může kód s nízkými oprávněními, ale pokus znovu nakonfigurovat nastavení sítě na virtuálním počítači vyžaduje oprávnění správce.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostické protokoly a události
Informace o protokolu je další sadu dat, která se některé aplikace, pokus o přístup. Typy protokolu informace, které jsou k dispozici pro kód spuštěný ve službě App Service zahrnuje diagnostiky a protokolovat informace generované aplikace, která je také snadný přístup k aplikaci. 

Například protokoly W3C HTTP vygenerovaný aktivní aplikací jsou k dispozici, buď v adresáři protokolu v umístění v síti sdílené složky vytvořené pro aplikace, nebo k dispozici v úložišti objektů blob, pokud zákazník má nastavení protokolování W3C do úložiště. Tato možnost umožňuje protokoly bez rizika překročení limitů úložiště souboru, který je přidružený k síťové sdílené složky se dají shromáždit velké množství.

V souvislosti podobně jako v reálném čase diagnostické informace z aplikací .NET můžete také protokolovat .NET trasování a diagnostické infrastruktury pomocí možnosti, jak zapsat informace trasování do sdílené síťové složky buď aplikace, případně k umístění úložiště objektů blob.

Oblasti diagnostické protokolování a trasování, které nejsou k dispozici pro aplikace, jsou běžné protokoly událostí Windows (například systém, aplikace a zabezpečení protokoly událostí) a Windows trasování událostí pro Windows. Vzhledem k tomu, že informace o trasování událostí pro Windows trasování může být potenciálně zobrazitelné celého (se správným ACL), jsou blokovány pro čtení a zápisu do událostí trasování událostí pro Windows. Vývojáři si všimnout, že volání rozhraní API pro čtení a zápis trasování událostí pro Windows, událostí a běžné protokoly událostí Windows se zobrazí pro práci, ale důvodem je, že služby App Service je "faking" volání tak, aby byly zobrazeny úspěšné. Ve skutečnosti kód aplikace nemá přístup k těmto datům událostí.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Přístup k registru
Aplikace mají přístup jen pro čtení k většině (ale ne všechny) z registru virtuálního počítače, na kterém běží. V praxi to znamená, že klíče registru, které umožňují přístup jen pro čtení do místní skupiny uživatelů jsou přístupné pro aplikace. Jedna oblast registru, který není aktuálně podporován pro čtení nebo zápis je nastavení HKEY\_aktuální\_podregistru uživatele.

Oprávnění k zápisu do registru je blokovaný, včetně přístupu ke klíčům registru všechny jednotlivé uživatele. Z pohledu aplikace, oprávnění k zápisu do registru by nikdy spoléhat v prostředí Azure od aplikací můžete (a dělat) migrují na různých virtuálních počítačů. Pouze trvalého zapisovatelné úložiště, který může být závisejí aplikace je struktura aplikaci adresář s obsahem ze sdílené složky App Service UNC. 

## <a name="more-information"></a>Další informace

[Azure Web App sandboxu](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – nejnovější informace o prostředí pro spuštění služby App Service. Na této stránce se spravují přímo vývojovému týmu služby App Service.

> [!NOTE]
> Pokud chcete začít používat službu Azure App Service před registrací k účtu Azure, přejděte k možnosti [Vyzkoušet službu App Service](https://azure.microsoft.com/try/app-service/), kde můžete okamžitě vytvořit krátkodobou úvodní webovou aplikaci. Není vyžadována platební karta a nevzniká žádný závazek.
> 
> 


