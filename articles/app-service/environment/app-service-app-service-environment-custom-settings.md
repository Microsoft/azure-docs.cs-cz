---
title: Vlastní nastavení pro prostředí App Service
description: Vlastní konfigurace nastavení pro prostředí App Service
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
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129511"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Vlastní konfigurace nastavení pro prostředí App Service
## <a name="overview"></a>Přehled
Protože prostředí App Service jsou izolované do jednoho zákazníka, existují určité nastavení konfigurace, které lze použít pouze pro prostředí App Service. Tento článek obsahuje informace o různé zvláštními úpravami, které jsou k dispozici pro prostředí App Service.

Pokud nemáte služby App Service Environment, přečtěte si téma [postup vytvoření služby App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

Přizpůsobení App Service Environment může ukládat pomocí pole v novém **clusterSettings** atribut. Tento atribut je součástí slovník "Vlastnosti" *hostingEnvironments* entity Azure Resource Manager.

Následující fragment kódu ukazuje šablony správce prostředků se používá zkratka **clusterSettings** atribut:

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

**ClusterSettings** atribut můžou být součástí šablony Resource Manageru k aktualizaci služby App Service Environment.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Použití Průzkumníka prostředků Azure k aktualizaci služby App Service Environment
Alternativně můžete aktualizovat App Service Environment pomocí [Průzkumníka prostředků Azure](https://resources.azure.com).  

1. V Průzkumníku prostředků, běžte do uzlu App Service Environment (**odběry** > **Skupinyprostředků** > **zprostředkovatelé**  >  **Microsoft.Web** > **hostingEnvironments**). Klikněte na tlačítko konkrétní App Service Environment, kterou chcete aktualizovat.
2. V pravém podokně klikněte na **pro čtení a zápis** v horním panelu nástrojů povolit interaktivní úpravy v Průzkumníku prostředků.  
3. Klepněte na modrou **upravit** tlačítko aby upravitelné šablony Resource Manageru.
4. Přejděte do dolní části pravého podokna. **ClusterSettings** atribut je úplně dole, kde můžete zadat nebo aktualizovat svou hodnotu.
5. Zadejte (nebo zkopírujte a vložte) pole hodnoty konfigurace, které chcete v **clusterSettings** atribut.  
6. Klikněte na tlačítko se zeleným **PUT** tlačítko, která se nachází v horní části v pravém podokně potvrzení změny služby App Service Environment.

Ale odešlete změny trvá přibližně 30 minut, násobenou počet front zakončení v App Service Environment pro tato změna se projeví.
Například pokud služby App Service Environment má čtyři front-end, bude trvat zhruba dvě hodiny pro dokončení aktualizace konfigurace. Při změně konfigurace je se nasazuje, žádná jiná operace škálování nebo operací změny konfigurace můžete provést v App Service Environment.

## <a name="disable-tls-10"></a>Zakázat protokol TLS 1.0
Opakované otázku od zákazníků, hlavně zákazníkům, kteří se zabývají soulad s normami PCI audity, je uveden postup explicitně zákaz protokolu TLS 1.0 pro svoje aplikace.

Protokol TLS 1.0 je možné zakázat prostřednictvím následujících **clusterSettings** položku:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Pořadí sady šifer TLS změn
Další otázka od zákazníků se pokud mohou upravit seznam šifry vyjednaném jejich serveru a toho lze dosáhnout úpravou **clusterSettings** jak je uvedeno níže. Seznam dostupných šifrovací sady lze načíst z [tohoto článku na webu MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Pokud nesprávné hodnoty jsou nastavené pro sadu šifer, které nelze pochopit SChannel, veškerá komunikace TLS na váš server může přestat fungovat. V takovém případě budete muset odebrat *FrontEndSSLCipherSuiteOrder* položku z **clusterSettings** a odeslat aktualizovanou šablonu správce prostředků se vrátit zpět na výchozí šifrovací sada nastavení.  Použijte prosím tato funkce se zvýšenou opatrností.
> 
> 

## <a name="get-started"></a>Začínáme
Lokality šablony správce prostředků Azure rychlý start zahrnuje šablonu s základní definice [vytváření služby App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
