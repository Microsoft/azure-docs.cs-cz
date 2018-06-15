---
title: Přehled služby Azure App Service místní mezipaměti | Microsoft Docs
description: Tento článek popisuje, jak povolit, přizpůsobit a dotaz na stav funkce Azure App Service místní mezipaměti
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
ms.openlocfilehash: 75f2dcb80514105ed663ba1fe5f7adccc05af1fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836382"
---
# <a name="azure-app-service-local-cache-overview"></a>Přehled služby Azure App Service místní mezipaměti
Službě Azure web app obsah se uloží do úložiště Azure a je prezentované až trvanlivý způsobem jako sdílenou složku obsahu. Tento návrh je určená pro práci s celou řadu aplikací a má následující atributy:  

* Obsah je sdílet mezi více instancí virtuálního počítače (VM) webové aplikace.
* Obsah je odolné a mohou být upravena spuštění webové aplikace.
* Soubory protokolu a diagnostických dat jsou k dispozici v rámci stejné sdílené složce obsahu.
* Publikování nový obsah přímo aktualizuje složku obsahu. Můžete zobrazit okamžitě stejný obsah prostřednictvím webu SCM a funkční webovou aplikaci (obvykle některé technologie, jako je ASP.NET iniciovat restartování webové aplikace na některé změny souborů, chcete-li získat nejnovější obsah).

I mnoho webové aplikace pomocí jednoho nebo všech těchto funkcí, třeba některé webové aplikace jen vysoce výkonné, jen pro čtení úložiště obsahu, který můžete spustit z s vysokou dostupností. Tyto aplikace mohou těžit z instance virtuálního počítače konkrétní místní mezipaměti.

Funkce Azure App Service místní mezipaměti poskytuje webové role zobrazení obsahu. Tento obsah je mezipaměť zápisu. ale zahození obsahu úložiště, které se vytvoří asynchronně na místě spuštění. Když mezipaměti je připraven, web je přepnuta ke spouštění na obsah v mezipaměti. Webové aplikace, které běží v místní mezipaměti mají následující výhody:

* Jsou odolný vůči latenci, ke kterým dochází, když přistupují k obsahu v Azure Storage.
* Jsou imunní plánované upgrady nebo neplánované výpadky způsobené nástrojem a žádné jiné poruchy s Azure Storage, ke kterým došlo na serverech, které slouží sdílené složce obsahu.
* Mají menšímu počtu restartování aplikace kvůli změnám sdílenou složku úložiště.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Jak místní mezipaměti změní chování služby App Service
* Místní mezipaměť je kopie složky /site a /siteextensions webové aplikace. Vytváří se na místní instanci virtuálního počítače při spuštění webové aplikace. Velikost místní mezipaměti na webové aplikace je omezena na 300 MB ve výchozím nastavení, ale můžete ho zvýšit až 2 GB.
* Místní mezipaměť je pro čtení a zápis. Ale všechny změny budou zahozeny při webové aplikace přesune virtuální počítače a získá restartování. Nepoužívejte pro aplikace, které obsahují důležitá data v úložišti obsahu místní mezipaměti.
* Webové aplikace může dál zapisovat soubory protokolu a diagnostických dat, stejně jako aktuálně. Soubory protokolu a data, ale jsou uloženy místně na virtuálním počítači. Pak se kopírují přes pravidelně do sdíleného úložiště obsahu. Kopírovat do sdíleného úložiště obsahu je nejlepší možný úsilí – zápisu, které voláními by mohly být ztraceny z důvodu k nečekané chybě instance virtuálních počítačů.
* Dojde ke změně v této struktuře složek LogFiles a dat pro webové aplikace, které používají místní mezipaměti. Jsou teď podsložky v úložiště LogFiles Data a složky, které dodržují formát pojmenování "Jedinečný identifikátor" + časové razítko. Jednotlivé podsložky odpovídá instance virtuálního počítače, kde běží webové aplikace, nebo byla spuštěna.  
* Publikování změn do webové aplikace pomocí některého z publikování mechanismů bude publikovat do sdíleného úložiště obsahu. Jedná se o návrhu, protože chceme publikovaných článků odolný. Aktualizace mezipaměti místní webové aplikace, je třeba restartovat. To vypadat třeba nadměrné krok? Chcete-li životní cyklus bezproblémové, přečtěte si informace dále v tomto článku.
* D:\Home bodů do místní mezipaměti. D:\Local dál tak, aby odkazoval na dočasné úložiště specifické pro virtuální počítač.
* Výchozí zobrazení obsahu webu SCM i nadále tohoto sdíleného úložiště obsahu.

## <a name="enable-local-cache-in-app-service"></a>Povolit místní mezipaměti ve službě App Service
Pomocí kombinace nastavení vyhrazené aplikace nakonfigurujete místní mezipaměti. Tato nastavení aplikací můžete nakonfigurovat pomocí následujících metod:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Nakonfigurujte místní mezipaměti pomocí portálu Azure
<a name="Configure-Local-Cache-Portal"></a>

Můžete povolit místní mezipaměti na základě aplikací na web pomocí tohoto nastavení aplikace:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Nastavení aplikace portálu Azure: místní mezipaměti](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Nakonfigurujte místní mezipaměti pomocí Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```

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

## <a name="change-the-size-setting-in-local-cache"></a>Změnit velikost nastavení v místní mezipaměti
Ve výchozím nastavení, je velikost místní mezipaměti **300 MB**. To zahrnuje /site a /siteextensions složek, které jste zkopírovali z úložiště obsahu, jakož i místně vytvořené složky protokoly a data. Chcete-li tento limit zvýšit, použijte nastavení aplikace `WEBSITE_LOCAL_CACHE_SIZEINMB`. Velikost můžete zvýšit až **2 GB** (2000 MB) na webové aplikace.

## <a name="best-practices-for-using-app-service-local-cache"></a>Osvědčené postupy pro použití místní mezipaměti služby aplikace
Doporučujeme použít místní mezipaměti ve spojení s [přípravného prostředí](../app-service/web-sites-staged-publishing.md) funkce.

* Přidat *trvalé* nastavení aplikace `WEBSITE_LOCAL_CACHE_OPTION` s hodnotou `Always` k vaší **produkční** slot. Pokud používáte `WEBSITE_LOCAL_CACHE_SIZEINMB`, také přidat jako trvalé nastavení na produkční slot.
* Vytvoření **pracovní** pozice a publikování na vaše pracovní slot. Obvykle není nastavený přípravný slot použít místní mezipaměť umožnit bezproblémové životního cyklu sestavení nasazení testování pro přípravu, je-li získat výhody místní mezipaměti pro produkční slot.
* Testování webu proti vaše pracovní pozici.  
* Až budete připravení, vydávání [operace prohození](../app-service/web-sites-staged-publishing.md#Swap) mezi pracovní a provozní přihrádek.  
* Trvalé nastavením patří název a trvalé pro slot. Proto když pracovní pozici získá prohodily do produkčního prostředí, dědit nastavení aplikace místní mezipaměti. Nově prohodil produkční slot se spustí v místní mezipaměti po několika minutách a bude možné provozní teplotu jako součást slotu zahřívání po odkládacího souboru. Proto po dokončení prohození slotů produkční slot běží v místní mezipaměti.

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Jak poznám, pokud se místní mezipaměti vztahuje se webová aplikace?
Pokud vaše webová aplikace potřebuje vysoce výkonné a spolehlivé úložiště obsahu, nepoužívá ukládání obsahu pro zápis důležitých dat za běhu a je menší než 2 GB v celková velikost, potom odpověď je "Ano"! Chcete-li získat celkovou velikost složky /site a /siteextensions, můžete použít rozšíření lokality "Azure Web Apps využití disku."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak poznám, pokud má mé lokality přepnul na používání místní mezipaměti?
Pokud používáte funkci místní mezipaměti s pracovní prostředí, operace prohození se nedokončí, dokud je jestli místní mezipaměti. Pokud chcete zkontrolovat, zda je váš web spuštěn proti místní mezipaměti, můžete zkontrolovat proměnnou prostředí pracovní proces `WEBSITE_LOCALCACHE_READY`. Postupujte podle pokynů na [proměnnou prostředí pracovní proces](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) stránky pro přístup k proměnné prostředí pracovní proces na několik instancí.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Právě publikování nových změn, ale webová aplikace pravděpodobně nemají je. Proč?
Pokud vaše webová aplikace používá místní mezipaměti, budete muset restartovat váš web, získání nejnovějších změn. Nechcete, aby při publikování změn produkční lokality? V tématu Možnosti slot v předchozí části osvědčené postupy.

### <a name="where-are-my-logs"></a>Kde jsou moje protokoly?
S místní mezipaměť vypadá trochu odlišně složek s daty a protokoly. Ale strukturu podsložkách zůstává stejná, s tím rozdílem, že jsou v podsložce s formátu "virtuální počítač identifikátor" + časové razítko nestled podsložky.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Jsou povolena místní mezipaměť, avšak Moje webové aplikace stále získá restartovat. Co to znamená? Ačkoli že místní mezipaměti pomohly s častým aplikace restartuje.
Místní mezipaměti Zabraňte související s úložištěm webové aplikace restartuje. Však vaší webové aplikace může stále projít restartování při upgradech plánované infrastruktury virtuálního počítače. Celkové restartování aplikace, které prostředí s místní mezipaměti povolena by měl být méně.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Místní mezipaměti vyloučit všechny adresáře z kopírování rychlejší místní jednotku?
Jako součást kroku, který zkopíruje obsah, úložiště je vyloučen všechny složky s názvem úložiště. To pomáhá s scénáře, kde obsahu webu může obsahovat úložiště řízení zdrojů, nemusí být potřeba v rámci každodenní operace webové aplikace. 
