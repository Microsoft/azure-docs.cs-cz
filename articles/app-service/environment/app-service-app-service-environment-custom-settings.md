---
title: Konfigurace vlastního nastavení
description: Nakonfigurujte nastavení, která se vztahují na celé Azure App Service prostředí. Naučte se, jak to udělat pomocí Azure Resource Manager šablon.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 10/03/2020
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 88163c07d570df5e0ff343776c17c463010ce368
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713286"
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

App Service Environment funguje jako černý rámeček, kde nelze zobrazit interní součásti nebo komunikaci v rámci systému. Pokud chcete povolit vyšší propustnost, šifrování není ve výchozím nastavení povolené mezi interními součástmi. Systém je zabezpečený, protože provoz je zcela nepřístupný ke sledování nebo přístupu. Pokud máte požadavek na dodržování předpisů, přestože vyžaduje úplné šifrování cesty k datům z koncového konce, existuje způsob, jak to povolit s clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
Tím se zašifruje interní síťový provoz ve vašem přihlašování mezi front-endy a pracovními procesy, zašifruje se stránkovací soubor a také zašifruje pracovní disky. Jakmile je InternalEncryption clusterSetting povolený, může to mít dopad na výkon systému. Když provedete změnu a povolíte InternalEncryption, váš pomocný stav bude v nestabilním stavu, dokud se změna zcela nerozšíří. Dokončení šíření změny může trvat několik hodin, a to v závislosti na tom, kolik instancí jste v pomocném mechanismu. Důrazně doporučujeme, abyste tuto možnost nepovolili u pomocného mechanismu, když se používá. Pokud potřebujete tuto možnost povolit u aktivně využívaného mechanismu přihlašování, důrazně doporučujeme, abyste přepnuli provoz do prostředí zálohování, dokud se operace nedokončí. 


## <a name="disable-tls-10-and-tls-11"></a>Zakázat TLS 1,0 a TLS 1,1

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
Další otázkou od zákazníků je, že pokud můžou upravit seznam šifr vydaných serverem a můžete to dosáhnout úpravou **clusterSettings** , jak je znázorněno níže. Seznam šifrovacích sad, které jsou k dispozici, lze načíst z [tohoto článku na webu MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
    }
],
```

> [!WARNING]
> Pokud jsou pro šifrovací sadu, kterou SChannel nedokáže pochopit, nastavené nesprávné hodnoty, může přestat fungovat veškerá komunikace TLS s vaším serverem. V takovém případě budete muset odebrat položku *FrontEndSSLCipherSuiteOrder* z **clusterSettings** a odeslat aktualizovanou šablonu správce prostředků a vrátit se k výchozímu nastavení šifrovací sady.  Použijte prosím tuto funkci opatrně.

## <a name="get-started"></a>Začínáme
Web šablony pro rychlý Start Správce prostředků pro Azure obsahuje šablonu se základní definicí pro [vytvoření App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
