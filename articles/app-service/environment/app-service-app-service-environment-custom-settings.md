---
title: Konfigurace vlastního nastavení
description: Nakonfigurujte nastavení, která se vztahují na celé Azure App Service prostředí. Naučte se, jak to udělat pomocí Azure Resource Manager šablon.
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226385"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Vlastní nastavení konfigurace pro App Service prostředí
## <a name="overview"></a>Přehled
Vzhledem k tomu, že App Service prostředí (služby ASE) jsou izolované pro jednoho zákazníka, existují určitá nastavení konfigurace, která je možné použít výhradně pro App Service prostředí. Tento článek popisuje různé konkrétní vlastní nastavení, která jsou k dispozici pro App Service prostředí.

Pokud nemáte App Service Environment, přečtěte si téma [vytvoření App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

Přizpůsobení App Service Environment můžete ukládat pomocí pole v New atributu **clusterSettings** . Tento atribut se nachází ve slovníku Properties entity *hostingEnvironments* Azure Resource Manager.

Následující zkrácený Správce prostředků fragment šablony ukazuje atribut **clusterSettings** :

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

Atribut **clusterSettings** může být součástí šablony Správce prostředků pro aktualizaci App Service Environment.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Aktualizace App Service Environment pomocí Azure Resource Explorer
Alternativně můžete App Service Environment aktualizovat pomocí [Azure Resource Explorer](https://resources.azure.com).  

1. V Průzkumník prostředků přejít na uzel pro App Service Environment (**Subscriptions**  >  **resourceGroups**  >  **providers**  >  **Microsoft. Web**  >  **hostingEnvironments**). Pak klikněte na konkrétní App Service Environment, kterou chcete aktualizovat.
2. V pravém podokně klikněte na tlačítko **pro čtení a zápis** na horním panelu nástrojů, čímž povolíte interaktivní úpravy v Průzkumník prostředků.  
3. Klikněte na modré tlačítko pro **Úpravy** , aby bylo možné šablonu správce prostředků upravovat.
4. Posuňte se do dolní části pravého podokna. Atribut **clusterSettings** je v horní části, kde můžete zadat nebo aktualizovat jeho hodnotu.
5. Zadejte (nebo zkopírujte a vložte) pole hodnot konfigurace, které chcete v atributu **clusterSettings** .  
6. Kliknutím na zelené tlačítko pro **vložení** , které se nachází v horní části pravého podokna, potvrďte změnu App Service Environment.

Tuto změnu můžete ale odeslat přibližně 30 minut vynásobený počtem front-endy v App Service Environment, aby se změna projevila.
Například pokud má App Service Environment čtyři front-endy, bude trvat přibližně dvě hodiny, než se aktualizace konfigurace dokončí. Během změny konfigurace se nemůžou provádět žádné další operace škálování ani operace změny konfigurace v App Service Environment.

## <a name="enable-internal-encryption"></a>Povolit interní šifrování

App Service Environment funguje jako černý rámeček, kde nelze zobrazit interní součásti nebo komunikaci v rámci systému. Pokud chcete povolit vyšší propustnost, šifrování není ve výchozím nastavení povolené mezi interními součástmi. Systém je zabezpečený, protože provoz je nepřístupný ke sledování nebo přístupu. Pokud máte požadavek na dodržování předpisů, přestože vyžaduje úplné šifrování cesty k datům z koncového konce, existuje způsob, jak povolit šifrování úplné cesty k datům pomocí clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
Nastavení InternalEncryption na hodnotu true šifruje interní síťový provoz ve vašem pomocném mechanismu mezi front-endy a pracovními procesy, šifruje stránkovací soubor a také šifruje pracovní disky. Jakmile je InternalEncryption clusterSetting povolený, může to mít dopad na výkon systému. Když provedete změnu a povolíte InternalEncryption, váš pomocný stav bude v nestabilním stavu, dokud se změna zcela nerozšíří. Dokončení šíření změny může trvat několik hodin, a to v závislosti na tom, kolik instancí jste v pomocném mechanismu. Důrazně doporučujeme, abyste InternalEncryption při použití pomocného mechanismu nepovolili. Pokud potřebujete povolit InternalEncryption v aktivně používaném pomocném mechanismem, důrazně doporučujeme, abyste přepnuli provoz do prostředí zálohování, dokud se operace nedokončí. 


## <a name="disable-tls-10-and-tls-11"></a>Zákaz protokolu TLS 1.0 a TLS 1.1

Pokud chcete v aplikaci spravovat nastavení TLS podle aplikace, můžete použít pokyny uvedené v dokumentaci k [Nastavení Vynutilí TLS](../configure-ssl-bindings.md#enforce-tls-versions) . 

Pokud chcete zakázat všechny příchozí přenosy TLS 1,0 a TLS 1,1 pro všechny aplikace v pomocném mechanismu služby, můžete nastavit následující položku **clusterSettings** :

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

Název nastavení říká 1,0, ale při konfiguraci zakáže protokol TLS 1,0 i TLS 1,1.

## <a name="change-tls-cipher-suite-order"></a>Změna pořadí šifrovací sady TLS
Pomocného nástroje podporuje změnu šifrovací sady z výchozího nastavení. Výchozí sadou šifr je stejná sada, která se používá ve službě pro více tenantů. Změna šifrovacích sad má vliv na celé nasazení App Service tím, že je to možné jenom v samoobslužném přihlášení k jednomu tenantovi. Pro pomocného objekt pro pořízení jsou vyžadovány dvě šifrovací sady; TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 a TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256. Pokud chcete, aby váš správce služby pracoval s nejsilnějšími a největšími sadami šifrovacích sad, použijte pouze dvě požadované šifry. Chcete-li nakonfigurovat pomocného mechanismu pro použití pouze těch šifr, které vyžaduje, upravte **clusterSettings** , jak je uvedeno níže. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> Pokud jsou pro šifrovací sadu, kterou SChannel nedokáže pochopit, nastavené nesprávné hodnoty, může přestat fungovat veškerá komunikace TLS s vaším serverem. V takovém případě budete muset odebrat položku *FrontEndSSLCipherSuiteOrder* z **clusterSettings** a odeslat aktualizovanou šablonu správce prostředků a vrátit se k výchozímu nastavení šifrovací sady.  Použijte prosím tuto funkci opatrně.

## <a name="get-started"></a>Začínáme
Web šablony pro rychlý Start Správce prostředků pro Azure obsahuje šablonu se základní definicí pro [vytvoření App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
