---
title: Místní mezipaměť
description: Přečtěte si, jak místní mezipaměť funguje v Azure App Service a jak povolit, změnit velikost a zadat dotaz na stav místní mezipaměti vaší aplikace.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: b3c8f6015b4627d86a0665865fba2f3fdd39589d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080707"
---
# <a name="azure-app-service-local-cache-overview"></a>Přehled Azure App Service místní mezipaměti

> [!NOTE]
> Místní mezipaměť není podporovaná v aplikacích Function App ani v kontejnerech App Servicech aplikacích, jako je například v [kontejnerech Windows](quickstart-custom-container.md?pivots=container-windows) nebo v [App Service na systému Linux](overview.md#app-service-on-linux).


Obsah Azure App Service je uložený v Azure Storage a je povrchový způsobem odolný jako sdílený obsah. Tento návrh je určený pro práci s nejrůznějšími aplikacemi a má následující atributy:  

* Obsah se sdílí mezi několika instancemi virtuálního počítače (VM) aplikace.
* Obsah je odolný a dá se upravovat spuštěním aplikací.
* Soubory protokolů a soubory diagnostických dat jsou k dispozici v rámci stejné složky sdíleného obsahu.
* Publikování nového obsahu přímo aktualizuje složku obsahu. Stejný obsah můžete hned zobrazit prostřednictvím webu SCM a běžící aplikace (obvykle některé technologie, jako je ASP.NET, iniciují restart aplikace u některých souborů, aby se získal nejnovější obsah).

I když mnoho aplikací používá jednu nebo všechny tyto funkce, některé aplikace potřebují jenom vysoce výkonné úložiště obsahu jen pro čtení, které můžou spouštět s vysokou dostupností. Tyto aplikace můžou těžit z instance virtuálního počítače konkrétní místní mezipaměti.

Funkce místní mezipaměti Azure App Service poskytuje zobrazení webové role vašeho obsahu. Tento obsah je zápisovou a zahozenou mezipaměť vašeho obsahu úložiště, která je vytvořena asynchronně při spuštění po síti. Když je mezipaměť připravena, je lokalita přepnuta na spuštění proti obsahu v mezipaměti. Aplikace, které běží v místní mezipaměti, mají následující výhody:

* Jsou odolné vůči latencím, ke kterým dochází, když přistupují k obsahu na Azure Storage.
* Jsou imunní na plánované upgrady nebo neplánovaný výpadek a jakékoli další výpadky Azure Storage, ke kterým dochází na serverech, které slouží ke sdílení obsahu.
* Z důvodu změn sdílené složky úložiště mají méně restartování aplikace.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Způsob změny chování App Service v místní mezipaměti
* _D:\home_ odkazuje na místní mezipaměť, která se vytvoří v instanci virtuálního počítače při spuštění aplikace. _D:\Local_ i nadále odkazuje na dočasné úložiště specifické pro virtuální počítače.
* Místní mezipaměť obsahuje jednorázovou kopii složek _site_ a _/siteextensions_ sdíleného úložiště obsahu, a to v _D:\home\site_ a _D:\home\siteextensions_v uvedeném pořadí. Soubory se při spuštění aplikace zkopírují do místní mezipaměti. Velikost obou složek pro každou aplikaci je ve výchozím nastavení omezená na 1 GB, ale dá se zvýšit na 2 GB. Počítejte s tím, že se při zvýšení velikosti mezipaměti trvá načtení mezipaměti déle. Pokud jste zvýšili limit místní mezipaměti na 2 GB a zkopírované soubory překračují maximální velikost 2 GB, App Service bezobslužně ignoruje místní mezipaměť a přečte se ze vzdálené sdílené složky. Pokud není definováno žádné omezení nebo je limit nastaven na hodnotu nižší než 2 GB a zkopírované soubory překračují limit, může nasazení nebo swap selhat s chybou.
* Místní mezipaměť je určena pro čtení i zápis. Jakákoli změna se ale zruší, když aplikace přesune virtuální počítače nebo se restartuje. Pro aplikace, které ukládají klíčová data v úložišti obsahu, nepoužívejte místní mezipaměť.
* _D:\home\LogFiles_ a _D:\home\Data_ obsahují soubory protokolu a data aplikací. Dvě podsložky se ukládají místně na instanci virtuálního počítače a zkopírují se do sdíleného úložiště obsahu pravidelně. Aplikace mohou uchovávat soubory protokolu a data jejich zápisem do těchto složek. Kopírování do sdíleného úložiště obsahu je ale nejlepší, takže je možné soubory protokolů a data ztratit z důvodu náhlého selhání instance virtuálního počítače.
* [Streamování protokolů](troubleshoot-diagnostic-logs.md#stream-logs) je ovlivněno nejlepší kopií. V protokolech streamování můžete sledovat zpoždění v délce jedné minuty.
* V úložišti sdíleného obsahu dojde ke změně struktury složek _protokolů_ a složek _dat_ pro aplikace, které používají místní mezipaměť. V nich jsou teď podsložky, které následují jako vzor pojmenování "jedinečný identifikátor" + časové razítko. Každá z podsložek odpovídá instanci virtuálního počítače, kde je aplikace spuštěná nebo spuštěná.
* Další složky v _D:\home_ zůstanou v místní mezipaměti a nekopírují se do sdíleného úložiště obsahu.
* Nasazení aplikací prostřednictvím jakékoli podporované metody se publikují přímo do trvalého sdíleného úložiště obsahu. Chcete-li aktualizovat složky _D:\home\site_ a _D:\home\siteextensions_ v místní mezipaměti, je nutné aplikaci restartovat. Chcete-li zajistit bezproblémové životní cyklus, přečtěte si informace dále v tomto článku.
* Výchozí zobrazení obsahu webového serveru SCM bude mít nadále k disverzi úložiště sdíleného obsahu.

## <a name="enable-local-cache-in-app-service"></a>Povolit místní mezipaměť v App Service
Místní mezipaměť se konfiguruje pomocí kombinace nastavení rezervovaných aplikací. Tato nastavení aplikace můžete nakonfigurovat pomocí následujících metod:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurace místní mezipaměti pomocí Azure Portal
<a name="Configure-Local-Cache-Portal"></a>

Pomocí tohoto nastavení aplikace povolíte místní mezipaměť na základě jednotlivých webových aplikací: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Nastavení aplikace Azure Portal: místní mezipaměť](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurace místní mezipaměti pomocí Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Změna nastavení velikosti v místní mezipaměti
Ve výchozím nastavení je velikost místní mezipaměti **1 GB**. To zahrnuje složky site a/siteextensions, které se zkopírují z úložiště obsahu, a také všechny místně vytvořené protokoly a složky dat. Pokud chcete tento limit zvýšit, použijte nastavení aplikace `WEBSITE_LOCAL_CACHE_SIZEINMB` . Velikost můžete zvětšit až na **2 GB** (2000 MB) na jednu aplikaci. Všimněte si, že při zvětšování velikosti bude trvat déle, než se načte místní mezipaměť.

## <a name="best-practices-for-using-app-service-local-cache"></a>Osvědčené postupy pro používání App Service místní mezipaměti
V kombinaci s funkcí [přípravného prostředí](../app-service/deploy-staging-slots.md) doporučujeme použít místní mezipaměť.

* Přidejte nastavení aplikace v *rychlém* `WEBSITE_LOCAL_CACHE_OPTION` provozu s hodnotou `Always` do **produkčního** slotu. Pokud používáte `WEBSITE_LOCAL_CACHE_SIZEINMB` , přidejte ho také jako rychlé nastavení do produkčního slotu.
* Vytvoření **přípravného** slotu a publikování do přípravného slotu. Nenastavujete pracovní slot pro použití místní mezipaměti, aby bylo možné bezproblémové životní cyklus Build-Deploy-test pro přípravu, pokud získáte výhody místní mezipaměti pro produkční slot.
* Otestujte svůj web proti přípravnému slotu.  
* Až budete připraveni, vydejte [operaci prohození](../app-service/deploy-staging-slots.md#Swap) mezi vašimi pracovními a provozními sloty.  
* Nastavení Sticky zahrnuje název a rychlé vložení do slotu. Takže když se pracovní slot v produkčním prostředí zařadí do provozu, zdědí nastavení aplikace v místní mezipaměti. Nově vyměněné produkční sloty se po několika minutách spustí s místní mezipamětí a po prohození se zahřeje jako součást slotu zahřívání. Takže když se prohození slotu dokončí, vaše produkční slot běží na místní mezipaměti.

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Jak zjistím, jestli se pro moji aplikaci používá místní mezipaměť?
Pokud vaše aplikace potřebuje vysoce výkonné a spolehlivé úložiště obsahu, nepoužívá úložiště obsahu k zápisu důležitých dat za běhu a je méně než 2 GB v celkové velikosti, odpověď je "Ano"! Pokud chcete získat celkovou velikost složek site a/siteextensions, můžete použít rozšíření lokality "Azure Web Apps disk Usage".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak zjistím, jestli můj web přešl na používání místní mezipaměti?
Pokud používáte funkci místní mezipaměti s přípravnými prostředími, operace přepnutí není dokončena, dokud nebude zahřívání místní mezipaměť. Pokud chcete zjistit, jestli je web spuštěný proti místní mezipaměti, můžete zaškrtnout proměnnou prostředí pracovního procesu `WEBSITE_LOCALCACHE_READY` . Použijte pokyny na stránce [proměnné prostředí pracovního procesu](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) pro přístup k proměnné prostředí pracovního procesu ve více instancích.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Právě jsem publikoval nové změny, ale moje aplikace je pravděpodobně nepoužívá. Proč?
Pokud vaše aplikace používá místní mezipaměť, pak je potřeba restartovat web, aby se získaly nejnovější změny. Nechcete publikovat změny v produkčním webu? Podívejte se na možnosti slotu v předchozí části věnované osvědčeným postupům.

> [!NOTE]
> Možnost [Spustit z balíčku](deploy-run-package.md) nasazení není kompatibilní s místní mezipamětí.

### <a name="where-are-my-logs"></a>Kde se nacházejí moje protokoly?
S místní mezipamětí vaše protokoly a složky dat vypadají trochu jinak. Struktura podsložek ale zůstane stejná, s tím rozdílem, že podsložky se Nestled pod podsložku s formátem "jedinečný identifikátor virtuálního počítače" + časové razítko.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Je povolená místní mezipaměť, ale moje aplikace se pořád restartovala. Proč tomu tak je? Mysleli, že místní mezipaměť pomohla častým restartováním aplikací.
Místní mezipaměť zabraňuje restartování aplikace související s úložištěm. Vaše aplikace ale může i nadále podstoupit restartování během plánovaných upgradů virtuálního počítače. Celkový počet restartování aplikace, u kterých máte zapnutou místní mezipaměť, by neměl být menší.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Vylučuje místní mezipaměť kopírování všech adresářů na rychlejší místní disk?
V rámci kroku, který kopíruje obsah úložiště, se vyloučí všechny složky s názvem úložiště. To pomáhá scénářům, kde obsah webu může obsahovat úložiště správy zdrojového kódu, které nemusí být potřeba během každodenního provozu aplikace. 
