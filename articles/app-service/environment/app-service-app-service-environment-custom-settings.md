---
title: Vlastní nastavení pro App Service Environment – Azure
description: Vlastní nastavení konfigurace pro služby App Service Environment
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 6463759dbd217cd054f838c09c7cfcf99a06aa2c
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390820"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Vlastní nastavení konfigurace pro služby App Service Environment
## <a name="overview"></a>Přehled
Protože App Service Environment (ase) jsou izolované pro jediného zákazníka, existují určitá nastavení konfigurace, které mohou být použity výhradně App Service Environment. Tento článek popisuje různé konkrétních vlastních nastavení, které jsou k dispozici pro App Service Environment.

Pokud nemáte službu App Service Environment, přečtěte si téma [vytvoření služby App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

Vlastní nastavení služby App Service Environment můžete ukládat pomocí pole v novém **clusterSettings** atribut. Tento atribut nachází ve slovníku "Properties" *hostingEnvironments* entity Azure Resource Manageru.

Následující fragment kódu ukazuje šablony Resource Manageru se používá zkratka **clusterSettings** atribut:

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

**ClusterSettings** atribut mohou být součástí šablony Resource Manageru k aktualizaci služby App Service Environment.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Použití Průzkumníka prostředků Azure k aktualizaci služby App Service Environment
Alternativně můžete aktualizovat služby App Service Environment pomocí [Azure Resource Exploreru](https://resources.azure.com).  

1. V Průzkumníku prostředků, běžte do uzlu pro App Service Environment (**předplatná** > **resourceGroups** > **poskytovatelé**  >  **Microsoft.Web** > **hostingEnvironments**). Pak klikněte na konkrétní služby App Service Environment, kterou chcete aktualizovat.
2. V pravém podokně klikněte na tlačítko **r/w** v horním panelu nástrojů umožňující interaktivní úpravy v Průzkumníku prostředků.  
3. Klikněte na modrý **upravit** tlačítko, chcete-li upravit šablony Resource Manageru.
4. Posuňte se dolů v pravém podokně. **ClusterSettings** atribut je úplně dole, ve kterém můžete zadat nebo aktualizovat jeho hodnotu.
5. Zadejte (nebo zkopírujte a vložte) pole hodnot konfigurace, které chcete zahrnout **clusterSettings** atribut.  
6. Klikněte na zelené **UMÍSTIT** tlačítko, které se nachází v horní části pravého podokna se zapsat změny do služby App Service Environment.

Ale odešlete změny, bude trvat přibližně 30 minut počtem front-endové systémy v App Service Environment tato změna projevila.
Například pokud služba App Service Environment má čtyři front-endů, bude trvat přibližně dvě hodiny pro dokončení aktualizace konfigurace. Při změně konfigurace se nasazuje, žádná jiná operace škálování nebo jejich změn konfigurace může proběhnout ve službě App Service Environment.

## <a name="disable-tls-10-and-tls-11"></a>Zákaz protokolu TLS 1.0 a TLS 1.1

Pokud chcete spravovat nastavení protokolu TLS na základě aplikace app, pak můžete použít pokyny, opatřeného [nastavení vynucení protokolu TLS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions) dokumentaci. 

Pokud chcete zakázat všechny příchozí protokol TLS 1.0 a TLS 1.1 provoz pro všechny aplikace ve službě ASE, můžete nastavit následující **clusterSettings** položky:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

Název nastavení říká 1.0, ale při konfiguraci, zakáže protokol TLS 1.0 a TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Změna pořadí sady šifer TLS
Další otázkou od zákazníků, je-li, můžete upravit seznam šifer vyjednaném svůj server a jde tohoto dosáhnout úpravou **clusterSettings** jak je znázorněno níže. Seznam dostupných šifrovacích sad můžete získat z [článku na webu MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Pokud nesprávné hodnoty jsou nastavené pro šifrovací sady, nemůže rozumět SChannel, veškerá komunikace TLS na váš server může přestat fungovat. V takovém případě budete muset odebrat *FrontEndSSLCipherSuiteOrder* položky z **clusterSettings** a odeslat aktualizovanou šablonu Resource Manageru se vrátit zpět na výchozí šifrovací sada nastavení.  Prosím tuto funkci používejte opatrně.
> 
> 

## <a name="get-started"></a>Začínáme
Lokality šablony správce prostředků Azure rychlý Start obsahuje šablonu s základní definice [vytváření služby App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
