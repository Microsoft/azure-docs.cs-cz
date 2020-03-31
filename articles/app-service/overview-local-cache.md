---
title: Místní mezipaměť
description: Zjistěte, jak funguje místní mezipaměť ve službě Azure App Service a jak povolit, změnit velikost a dotazovat se na stav místní mezipaměti vaší aplikace.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 1945730acaddb0c1c7ee1b28eeb926635efad643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227881"
---
# <a name="azure-app-service-local-cache-overview"></a>Přehled místní mezipaměti služby Azure App Service

> [!NOTE]
> Místní mezipaměť není podporována v aplikacích function nebo kontejnerizovaných aplikacích služby App Service, například v [kontejnerech Windows](app-service-web-get-started-windows-container.md) nebo v [app service na Linuxu](containers/app-service-linux-intro.md).


Obsah služby Azure App Service se uložených ve službě Azure Storage a je vynořil a trvanlivým způsobem jako sdílené složky obsahu. Tento návrh je určen pro práci s různými aplikacemi a má následující atributy:  

* Obsah se sdílí mezi více instancemi virtuálních počítačů (VM) aplikace.
* Obsah je odolný a lze jej upravit spuštěním aplikací.
* Soubory protokolu a diagnostické datové soubory jsou k dispozici ve stejné složce sdíleného obsahu.
* Publikování nového obsahu přímo aktualizuje složku obsahu. Můžete okamžitě zobrazit stejný obsah prostřednictvím webu SCM a spuštěné aplikace (obvykle některé technologie, jako je ASP.NET spustit restartování aplikace na některé změny souborů získat nejnovější obsah).

Zatímco mnoho aplikací používá jednu nebo všechny tyto funkce, některé aplikace potřebují pouze vysoce výkonné úložiště obsahu jen pro čtení, ze kterého mohou běžet s vysokou dostupností. Tyto aplikace můžou těžit z instance virtuálního virtuálního připojení konkrétní místní mezipaměti.

Funkce Místní mezipaměti služby Azure App Service poskytuje zobrazení webové role vašeho obsahu. Tento obsah je mezipamětní pro zápis, ale zahození obsahu úložiště, která je vytvořena asynchronně v místě spuštění. Když je mezipaměť připravena, web je přepnut tak, aby běžel proti obsahu uloženému v mezipaměti. Aplikace, které běží v místní mezipaměti, mají následující výhody:

* Jsou imunní vůči latencím, ke kterým dochází při přístupu k obsahu ve službě Azure Storage.
* Jsou imunní vůči plánovaným upgradům nebo neplánovaným prostojům a dalším přerušením pomocí služby Azure Storage, ke kterým dochází na serverech, které slouží sdílené složce obsahu.
* Mají méně restartování aplikací kvůli změnám sdílené složky úložiště.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Jak místní mezipaměť mění chování služby App Service
* _D:\home_ odkazuje na místní mezipaměť, která se vytvoří na instanci virtuálního počítači při spuštění aplikace. _D:\local_ nadále přejděte na dočasné úložiště specifické pro virtuální montovnu.
* Místní mezipaměť obsahuje jednorázovou kopii složek _/site_ a _/siteextensions_ úložiště sdíleného obsahu v _d:\home\site_ a _D:\home\siteextensions_. Soubory se zkopírují do místní mezipaměti při spuštění aplikace. Velikost těchto dvou složek pro každou aplikaci je ve výchozím nastavení omezena na 300 MB, ale můžete ji zvětšit až na 2 GB. Pokud zkopírované soubory překročí velikost místní mezipaměti, služba App Service tiše ignoruje místní mezipaměť a čte ze vzdálené sdílené složky.
* Místní mezipaměť je čtení a zápis. Všechny změny se však zahodí, když aplikace přesune virtuální počítače nebo se restartuje. Nepoužívejte místní mezipaměť pro aplikace, které ukládají důležitá data v úložišti obsahu.
* _D:\home\LogFiles_ a _D:\home\Data_ obsahují soubory protokolu a data aplikací. Dvě podsložky jsou uloženy místně v instanci virtuálního počítači a pravidelně se kopírují do sdíleného úložiště obsahu. Aplikace mohou ukládat soubory protokolu a data jejich zápisem do těchto složek. Kopie do úložiště sdíleného obsahu je však nejlepší úsilí, takže je možné pro soubory protokolu a data, která mají být ztraceny z důvodu náhlého selhání instance virtuálního aplikace.
* [Streamování protokolu](troubleshoot-diagnostic-logs.md#stream-logs) je ovlivněno kopií nejlepšího úsilí. V streamovaných protokolech můžete pozorovat až jednominutové zpoždění.
* V úložišti sdíleného obsahu dojde ke změně ve struktuře složek _LogFiles_ a _Data_ pro aplikace, které používají místní mezipaměť. Nyní jsou v nich podsložky, které se řídí vzorem pojmenování "jedinečný identifikátor" + časové razítko. Každá z podsložek odpovídá instanci virtuálního aplikace, kde je aplikace spuštěná nebo spuštěná.
* Ostatní složky v _d:\home_ zůstávají v místní mezipaměti a nejsou zkopírovány do sdíleného úložiště obsahu.
* Nasazení aplikace prostřednictvím jakékoli podporované metody publikuje přímo do úložiště trvalého sdíleného obsahu. Chcete-li aktualizovat složky _D:\home\site_ a _D:\home\siteextensions_ v místní mezipaměti, je třeba aplikaci restartovat. Chcete-li, aby byl životní cyklus bezproblémový, přečtěte si informace dále v tomto článku.
* Výchozí zobrazení obsahu webu SCM je i nadále zobrazením úložiště sdíleného obsahu.

## <a name="enable-local-cache-in-app-service"></a>Povolení místní mezipaměti ve službě App Service
Místní mezipaměti nakonfigurujete pomocí kombinace nastavení rezervované aplikace. Tato nastavení aplikace můžete nakonfigurovat pomocí následujících metod:

* [Portál Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurace místní mezipaměti pomocí portálu Azure
<a name="Configure-Local-Cache-Portal"></a>

Místní mezipaměť můžete povolit na základě webové aplikace pomocí tohoto nastavení aplikace:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Nastavení aplikace Portálu Azure: Místní mezipaměť](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurace místní mezipaměti pomocí Správce prostředků Azure
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
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Změna nastavení velikosti v místní mezipaměti
Ve výchozím nastavení je velikost místní mezipaměti **300 MB**. To zahrnuje /site a /siteextensions složky, které jsou zkopírovány z úložiště obsahu, stejně jako všechny místně vytvořené protokoly a datové složky. Chcete-li tento limit zvýšit, použijte nastavení `WEBSITE_LOCAL_CACHE_SIZEINMB`aplikace . Velikost můžete zvětšit až na **2 GB** (2000 MB) na aplikaci.

## <a name="best-practices-for-using-app-service-local-cache"></a>Doporučené postupy pro používání místní mezipaměti služby App Service
Doporučujeme používat místní mezipaměť ve spojení s funkcí [Pracovní prostředí.](../app-service/deploy-staging-slots.md)

* Přidejte *sticky* nastavení `WEBSITE_LOCAL_CACHE_OPTION` rychlé aplikace `Always` s hodnotou do **produkčního slotu.** Pokud používáte `WEBSITE_LOCAL_CACHE_SIZEINMB`, přidejte ho také jako rychlé nastavení do produkčního slotu.
* Vytvořte **pracovní** slot a publikujte do pracovního slotu. Obvykle nenastavíte pracovní slot pro použití místní mezipaměti k povolení bezproblémového životního cyklu sestavení a nasazení testu pro pracovní, pokud získáte výhody místní mezipaměti pro produkční slot.
* Otestujte svůj web proti pracovnímu slotu.  
* Až budete připraveni, vydejte [operaci prohození](../app-service/deploy-staging-slots.md#Swap) mezi pracovními a produkčními sloty.  
* Rychlé nastavení obsahuje název a přilnavost k patici. Takže když pracovní slot získá zaměněny do produkčního prostředí, zdědí nastavení aplikace místní mezipaměti. Nově vyměněný produkční slot bude po několika minutách spuštěn proti místní mezipaměti a bude zahřát jako součást zahřívání slotu po výměně. Takže po dokončení zaměňování slotů je produkční slot spuštěn proti místní mezipaměti.

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Jak poznám, jestli se místní mezipaměť vztahuje na mou aplikaci?
Pokud vaše aplikace potřebuje vysoce výkonné a spolehlivé úložiště obsahu, nepoužívá úložiště obsahu k zápisu důležitých dat za běhu a je menší než 2 GB celkové velikosti, pak je odpověď "ano"! Chcete-li získat celkovou velikost složek /site a /siteextensions, můžete použít rozšíření webu "Využití disku Azure Web Apps".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak poznám, že můj web přešel na používání místní mezipaměti?
Pokud používáte funkci Místní mezipaměť s pracovníprostředí, operace odkládacího nastavení se nedokončí, dokud se místní mezipaměť nezahřeje. Chcete-li zkontrolovat, zda je web spuštěn v části `WEBSITE_LOCALCACHE_READY`Místní mezipaměť, můžete zkontrolovat proměnnou prostředí pracovního procesu . Pomocí pokynů na stránce [proměnné prostředí pracovního procesu](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) získáte přístup k proměnné prostředí pracovního procesu ve více instancích.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Právě jsem publikoval nové změny, ale moje aplikace se nezdá, že je má. Proč?
Pokud vaše aplikace používá místní mezipaměť, musíte web restartovat, abyste získali nejnovější změny. Nechcete publikovat změny v produkčním webu? Podívejte se na možnosti slotu v předchozí části doporučených postupů.

### <a name="where-are-my-logs"></a>Kde jsou moje záznamy?
S místní mezipaměti, vaše protokoly a datové složky vypadají trochu jinak. Struktura podsložek však zůstává stejná, s tím rozdílem, že podsložky jsou zasazeny pod podsložku s formátem "jedinečný identifikátor virtuálního počítače" + časové razítko.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Mám povolenou místní mezipaměť, ale moje aplikace se stále restartuje. Proč to tak je? Myslel jsem, že místní cache pomohl s častými restarty aplikací.
Místní mezipaměť pomáhá zabránit restartování aplikací souvisejících s úložištěm. Vaše aplikace však může stále projít restartování během plánované upgrady infrastruktury virtuálního počítače. Celková aplikace se restartuje, že dojde k místní mezipaměti povoleno by měl být méně.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Vylučují místní mezipaměti z kopírování adresářů na rychlejší místní jednotku?
Jako součást kroku, který zkopíruje obsah úložiště, je vyloučena jakákoli složka s názvem úložiště. To pomáhá se scénáři, kde obsah webu může obsahovat úložiště správy zdrojového kódu, které nemusí být potřeba v každodenním provozu aplikace. 
