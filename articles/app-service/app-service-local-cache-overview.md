---
title: Přehled služby Azure App Service místní mezipaměti | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit, změna velikosti a zjistit stav funkce Azure App Service místní mezipaměti
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
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
ms.author: cephalin
ms.openlocfilehash: 6729c87dcc9a85e2e3ccb6b4822213d38e2ba6f7
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666110"
---
# <a name="azure-app-service-local-cache-overview"></a>Přehled služby Azure App Service místní mezipaměti

> [!NOTE]
> Místní mezipaměť se nepodporuje v aplikace Function App nebo kontejnerizovaných aplikací služby App Service, například na [App Service v Linuxu](containers/app-service-linux-intro.md).


Obsah Azure webové aplikace je uložená ve službě Azure Storage a se zobrazí až trvalý způsobem jako sdílené složky obsahu. Tento návrh je určená pro práci s celou řadu aplikací a má následující atributy:  

* Obsah je sdílen mezi více instancí virtuálních počítačů (VM) webové aplikace.
* Obsah je trvalý a je možné upravovat prostřednictvím spouštění webových aplikací.
* Soubory protokolu a diagnostická data jsou k dispozici v rámci stejné sdílené složce obsahu.
* Publikování nového obsahu přímo aktualizuje složku obsahu. Můžete hned zobrazit stejný obsah prostřednictvím webu SCM a funkční webovou aplikaci (obvykle některé technologie, jako je ASP.NET zahájení restartování webové aplikace na některé změny souborů chcete získat nejnovější obsah).

Mnoho webových aplikací používají jednu nebo všechny tyto funkce, ale některé webové aplikace stačí vysoce výkonné, jen pro čtení úložiště obsahu, který můžete spustit z s vysokou dostupností. Tyto aplikace můžete těžit z instance virtuálního počítače konkrétní místní mezipaměti.

Funkce Azure App Service místní mezipaměti obsahuje webové role zobrazení obsahu. Tento obsah je zápis. ale zahození mezipaměti obsahu úložiště, který je vytvořen asynchronní místního spuštění. Když mezipaměť připravená, lokality přepne ke spouštění na obsah uložený v mezipaměti. Webové aplikace, které běží v místní mezipaměti má následující výhody:

* Je odolný vůči latenci, ke kterým dochází při přístupu k obsahu v Azure Storage.
* Jsou to imunní upgrady plánované nebo neplánované výpadky a jakékoli jiné poruchy pomocí služby Azure Storage, ke kterým dochází na serverech, které slouží jako sdílené složky obsahu.
* Mají menšímu počtu restartování aplikace kvůli změnám sdílenou složku úložiště.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Jak se mění místní mezipaměť chování služby App Service
* _D:\home_ odkazuje na místní mezipaměti, která se vytvoří při spuštění aplikace na instanci virtuálního počítače. _D:\Local_ pokračuje tak, aby odkazoval do dočasného úložiště specifické pro virtuální počítače.
* Místní mezipaměť obsahuje jednorázový kopii _/lokality_ a _/siteextensions_ složky sdíleného úložiště obsahu v _D:\home\site_ a _D:\home\ siteextensions_v uvedeném pořadí. Soubory se zkopírují do místní mezipaměti při spuštění aplikace. Velikost dvě složky pro každou aplikaci je omezený na 300 MB ve výchozím nastavení, ale můžete to zvýšit až 2 GB.
* Místní mezipaměť je pro čtení i zápis. Po aplikaci přesune virtuální počítače nebo získá restartování je však zahodí všechny změny. Nepoužívejte pro aplikace, které obsahují důležitá data v úložišti obsahu místní mezipaměti.
* _D:\home\LogFiles_ a _D:\home\Data_ obsahovat soubory protokolů a dat aplikací. Dvě podsložky jsou uložené místně v instancích virtuálních počítačů a pravidelně se zkopírují do sdíleného úložiště obsahu. Aplikace můžete zachovat soubory protokolů a dat podle jejich zápis do těchto složek. Kopírovat do sdíleného úložiště obsahu je však best effort, takže je možné pro soubory protokolů a dat dojít ke ztrátě z důvodu selhání i s náhlými instance virtuálního počítače.
* [Streamování protokolů](web-sites-enable-diagnostic-log.md#streamlogs) je ovlivněna best effort kopírování. Může sledovat až minutu trvající zpoždění v proudu protokolů.
* Do sdíleného úložiště obsahu, dojde ke změně ve struktuře složek _LogFiles_ a _Data_ složky pro aplikace, které používají místní mezipaměti. V nich, které následují vzor pojmenování spočívající v "Jedinečný identifikátor" + časové razítko nyní existují podsložky. Jednotlivé podsložky odpovídá instance virtuálního počítače, ve kterém aplikace běží, nebo byla spuštěna.
* V jiných složkách _D:\home_ zůstanou v místní mezipaměti a nejsou zkopírovány do sdíleného úložiště obsahu.
* Nasazení aplikace pomocí libovolné podporované metody publikuje přímo do odolného úložiště sdíleného obsahu. Chcete-li aktualizovat _D:\home\site_ a _D:\home\siteextensions_ složek v místní mezipaměti, aplikace je potřeba restartovat. Chcete-li bezproblémový životní cyklus, naleznete v tématu informace dále v tomto článku.
* Zobrazení obsahu výchozího webu SCM pokračuje se, že sdíleného úložiště obsahu.

## <a name="enable-local-cache-in-app-service"></a>Povolit místní mezipaměti ve službě App Service
Pomocí kombinace nastavení vyhrazené aplikace nakonfigurujete místní mezipaměti. Můžete nakonfigurovat tato nastavení aplikace pomocí následujících metod:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurovat místní mezipaměti pomocí webu Azure portal
<a name="Configure-Local-Cache-Portal"></a>

Povolit místní mezipaměti na základě aplikace na web pomocí tohoto nastavení aplikace: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Nastavení aplikace Azure portal: místní mezipaměti](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurovat místní mezipaměti pomocí Azure Resource Manageru
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

## <a name="change-the-size-setting-in-local-cache"></a>Změnit nastavení velikosti v místní mezipaměti
Ve výchozím nastavení, místní mezipaměť je **300 MB**. To zahrnuje /site a /siteextensions složek, které jsou zkopírovány z úložiště obsahu, jakož i místně vytvořené složky protokoly a data. Chcete-li tento limit zvýšit, použijte nastavení aplikace, které `WEBSITE_LOCAL_CACHE_SIZEINMB`. Můžete zvětšit velikost až **2 GB** (2000 MB) za webové aplikace.

## <a name="best-practices-for-using-app-service-local-cache"></a>Osvědčené postupy pro použití místní mezipaměti aplikace App Service
Doporučujeme použít ve spojení s místní mezipaměti [přípravných prostředí](../app-service/web-sites-staged-publishing.md) funkce.

* Přidat *vždy navrchu* nastavení aplikace, které `WEBSITE_LOCAL_CACHE_OPTION` s hodnotou `Always` do vaší **produkční** slot. Pokud používáte `WEBSITE_LOCAL_CACHE_SIZEINMB`, také přidat jako vždy navrchu nastavení na produkční slot.
* Vytvoření **pracovní** pozice a publikování pro přípravný slot. Obvykle nemají nastavený přípravný slot na použití místní mezipaměti k povolení si bezproblémový životní cyklus sestavení nasazení testování pro přípravu, je-li získat výhody místní mezipaměti pro produkční slot.
* Otestujte svůj web proti přípravný slot.  
* Až budete připravení, vydávání [operace prohození](../app-service/web-sites-staged-publishing.md#Swap) mezi přípravným a produkčním prostředím sloty.  
* Rychlé nastavení zahrnují název a vždy navrchu do slotu. Proto při přípravný slot získá přepnutím do produkčního prostředí, zdědí nastavení místní mezipaměti aplikace. Nově prohodil se slot produkčního prostředí se spustí v místní mezipaměti za pár minut a bude možné provozní teplotu jako součást slotu zahřívání po přepnutí. Proto po dokončení prohození slotů produkčního slotu běží v místní mezipaměti.

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Jak poznám, že pokud místní mezipaměť se vztahuje na svou webovou aplikaci?
Pokud webové aplikace potřebuje vysoce výkonné, spolehlivé úložiště obsahu, nepoužívá na ukládání obsahu pro zápis důležitá data za běhu a je menší než 2 GB celkové velikosti, potom odpověď je "Ano"! K získání celkové velikosti /site a /siteextensions složek, můžete použít rozšíření webu "Azure Web Apps využití disku."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak poznám, že pokud mé lokality se přepnulo na použití místní mezipaměti?
Pokud používáte funkci místní mezipaměti s přípravných prostředí, operace prohození se nedokončí, dokud je provozní místní mezipaměti teplotu. Pokud chcete zkontrolovat, pokud je váš web spuštěn místní mezipaměti, můžete zkontrolovat proměnné prostředí pracovní proces `WEBSITE_LOCALCACHE_READY`. Postupujte podle pokynů na [proměnnou prostředí pracovní proces](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) stránky pro přístup k proměnné prostředí pracovní proces na více instancí.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Které jsem právě publikoval nových změn, ale Moje webová aplikace není vypadá to, že je. Proč?
Pokud vaše webová aplikace používá místní mezipaměti, budete muset restartuje váš web. Chcete-li získat nejnovější změny. Nechcete a publikujte změny do produkční lokality? Zobrazí se možnosti slotu v předchozí části osvědčené postupy.

### <a name="where-are-my-logs"></a>Kde jsou moje protokoly?
S místní mezipaměti vypadat trochu jinak složek s daty a protokoly. Ale struktura podsložkách zůstává stejné, s tím rozdílem, že jsou v části do podsložky se formát "virtuální počítač identifikátor" + časové razítko nestled podsložky.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Mám místní mezipaměti povolena, ale získá restartování pořád svou webovou aplikaci. Proč je, že? Můžu manažerem že místní mezipaměti pomohla s časté aplikace restartuje.
Místní mezipaměť zabránit restartování storage související webové aplikace. Však vaší webové aplikace mohli stále projít restartování během upgradu plánované infrastruktury virtuálního počítače. Celkové restartování aplikace, které zaznamenáte pomocí místní mezipaměti by měla být méně.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Místní mezipaměti vyloučit všechny adresáře z byly zkopírovány na rychlejší místním disku?
Jako součást kroku, který kopíruje obsah úložiště je vyloučena libovolnou složku s názvem úložiště. Díky tomu se scénáři, ve kterém obsahu webu mohou obsahovat úložištěm správy zdrojového kódu, který nemusí být potřeba v každodenní operace webové aplikace. 
