---
title: Konfigurace vlastního nastavení
description: Nakonfigurujte nastavení, která platí pro celé prostředí služby Azure App Service. Přečtěte si, jak to udělat pomocí šablon Azure Resource Manageru.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 25393007a3cc878737ea5927cb65bcf7ef945313
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057570"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Vlastní nastavení konfigurace pro prostředí služby App Service
## <a name="overview"></a>Přehled
Vzhledem k tomu, že prostředí služby App Service (ASEs) jsou izolované pro jednoho zákazníka, existují určitá nastavení konfigurace, která lze použít výhradně pro prostředí služby App Service. Tento článek dokumentuje různé konkrétní úpravy, které jsou k dispozici pro prostředí služby App Service.

Pokud nemáte prostředí služby App Service, přečtěte si informace [o tom, jak vytvořit prostředí služby App Service](app-service-web-how-to-create-an-app-service-environment.md).

Vlastní nastavení prostředí služby App Service můžete uložit pomocí pole v novém atributu **clusterSettings.** Tento atribut se nachází ve slovníku "Vlastnosti" *entity hostingEnvironments* Azure Resource Manager.

Následující zkrácený fragment šablony Správce prostředků zobrazuje atribut **clusterSettings:**

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

Atribut **clusterSettings** lze zahrnout do šablony Správce prostředků a aktualizovat prostředí služby App Service.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Aktualizace prostředí služby App Service pomocí Průzkumníka prostředků Azure
Případně můžete aktualizovat prostředí služby App Service pomocí [Průzkumníka prostředků Azure](https://resources.azure.com).  

1. V Průzkumníku prostředků přejděte na uzel pro prostředí služby > App Service Environment **(předplatná****resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Pak klikněte na konkrétní prostředí služby App Service, které chcete aktualizovat.
2. V pravém podokně klikněte na **čtení a zápis** na horním panelu nástrojů, abyste povolili interaktivní úpravy v Průzkumníku zdrojů.  
3. Kliknutím na modré tlačítko **Upravit** provedete úpravu šablony Správce prostředků.
4. Přejděte do dolní části pravého podokna. Atribut **clusterSettings** je v dolní části, kde můžete zadat nebo aktualizovat jeho hodnotu.
5. Do atributu **clusterSettings** zadejte (nebo zkopírujte a vložte) požadované pole konfiguračních hodnot.  
6. Kliknutím na zelené tlačítko **PUT,** které se nachází v horní části pravého podokna, potvrďte změnu v prostředí služby App Service.

Bez návrhu na změnu trvá zhruba 30 minut vynásobený počtem front-endů v prostředí služby App Service, aby se změna projevila.
Například pokud app service prostředí má čtyři front-endy, bude trvat zhruba dvě hodiny pro aktualizaci konfigurace na dokončení. Při zavedení změny konfigurace, žádné jiné operace škálování nebo operace změny konfigurace může probíhat v prostředí služby App Service.

## <a name="enable-internal-encryption"></a>Povolit interní šifrování

Prostředí služby App Service funguje jako systém černé skříňky, kde není vidět vnitřní součásti nebo komunikace v rámci systému. Chcete-li povolit vyšší propustnost, šifrování není ve výchozím nastavení povoleno mezi vnitřními součástmi. Systém je bezpečný, protože provoz je zcela nepřístupný pro sledování nebo přístup. Pokud máte požadavek na dodržování předpisů i když to vyžaduje úplné šifrování cesty k datům od začátku do konce, existuje způsob, jak povolit pomocí clusterSetting.  

        "clusterSettings": [
            {
                "name": "InternalEncryption",
                "value": "1"
            }
        ],
 
Po InterEncryption clusterSetting je povolena, může mít vliv na výkon systému. Když provedete změnu povolit InternalEncryption, vaše ase bude v nestabilním stavu, dokud se změna plně rozšíří. Úplné šíření změny může trvat několik hodin, v závislosti na tom, kolik instancí máte ve službě ASE. Důrazně doporučujeme nepovolit na službě ASE, když je používán. Pokud potřebujete povolit v aktivně používané služby ASE, důrazně doporučujeme přesměrovat provoz do prostředí zálohování, dokud nebude operace dokončena. 

## <a name="disable-tls-10-and-tls-11"></a>Zakázání TLS 1.0 a TLS 1.1

Pokud chcete spravovat nastavení TLS v aplikaci podle aplikace, můžete použít pokyny poskytnuté v dokumentaci [k vynucení nastavení TLS.](../configure-ssl-bindings.md#enforce-tls-versions) 

Pokud chcete zakázat všechny příchozí přenosy TLS 1.0 a TLS 1.1 pro všechny aplikace ve službě ASE, můžete nastavit následující položku **clusterSettings:**

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

Název nastavení říká 1.0, ale při konfiguraci zakáže jak TLS 1.0, tak TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Změnit pořadí sady šifrovacísady TLS
Další otázkou od zákazníků je, zda mohou upravit seznam šifer vyjednaných jejich serverem a toho lze dosáhnout úpravou **clusteruNastavení,** jak je znázorněno níže. Seznam dostupných šifrovacích sad lze načíst z [tohoto článku msdn](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Pokud jsou pro šifrovací sadu, které SChannel nerozumí, nastaveny nesprávné hodnoty, může přestat fungovat veškerá komunikace TLS se serverem. V takovém případě budete muset odebrat položku *FrontEndSSLCipherSuiteOrder* z **clusteruNastavení** a odeslat aktualizovanou šablonu Správce prostředků, abyste se vrátili zpět k výchozímu nastavení šifrovací sady.  Tuto funkci používejte opatrně.
> 
> 

## <a name="get-started"></a>Začínáme
Web šablony Azure Quickstart Resource Manager obsahuje šablonu se základní definicí pro [vytvoření prostředí služby App Service](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
