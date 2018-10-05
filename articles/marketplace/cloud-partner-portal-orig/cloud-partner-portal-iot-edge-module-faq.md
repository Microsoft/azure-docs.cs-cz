---
title: Nejčastější dotazy k Marketplace modul IoT Edge | Dokumentace Microsoftu
description: IoT Edge Module nejčastější dotazy k Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809249"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Nejčastější dotazy k Marketplace modul IoT Edge


## <a name="what-is-the-edge-module-marketplace"></a>Co je Marketplace modulu Edge?


Marketplace modul IoT Edge je seznam *certified* hraniční moduly, které jsou předem připravené *zjistitelné* prostřednictvím Azure Marketplace.

![Moduly IoT Edge](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Pokud budou moduly Edge viditelné? 


V [portálu Azure marketplace](https://ms.portal.azure.com/) (ověření prostředí) v kategorii Internet of Things označené jako "Modul IoT Edge".

A [webu Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (anonymní prostředí) v kategorii Internet of Things označené jako "Modul IoT Edge".

## <a name="is-it-open-to-partners"></a>Je otevřená pro partnery?


Zatím ne. V současné době pouze od společnosti Microsoft moduly se publikují v části IoT Edge na webu marketplace. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Proč partneři publikovat své moduly IoT Edge?


-   Pro zvýšení jejich viditelnost produktu přidáním tohoto kanálu uvedení na trh a připraveném jejich řešení.

-   Chcete-li získat další potenciálních zákazníků. V rámci webu Azure Marketplace se můžete získat podrobné informace o tom, kteří je chtějí ve svých řešeních.

-   Chcete-li buďte mezi prvními, kdo se využívat další možnosti finanční zhodnocení.

## <a name="what-is-the-onboarding-process"></a>Co je proces zařazení do systému?


Zatímco dosud neotevřel, proces zařazení do systému se provede v Tržišti Azure Marketplace. Podrobné pokyny jsou v [Průvodce publikováním webu Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Odkazovat na transact výpis typu for Containers. 

Partneři se nejdřív se stát vydavatelem pro Azure Marketplace. Pak budou moct připojit své hraniční moduly prostřednictvím [portál partnerů cloudu](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Existují žádné možnosti finanční zhodnocení?


Není připravené nyní. Naším prvním cílem je otevřít na trhu s *bezplatné* nebo *používání vlastní licence* hraniční moduly. Budeme přidávat další funkce finanční zhodnocení, jako jsou model fakturace spotřeby.

## <a name="what-is-bring-your-own-license-byol"></a>Co je přenést vaše – vlastní licence (BYOL)?


Oficiální definice v [zásadách pro účast na Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) je:

- *Zákazníkům získat právo k přístupu nebo využití nabídky mimo Azure Marketplace a neúčtují poplatky webu Azure Marketplace za použití nabídky na webu Azure Marketplace.*

Záleží jen na partnerům licencí softwaru a shromažďování výnosy pro něj.

## <a name="can-partners-publish-a-freemium-module"></a>Modul "freemium" publikovat partnerů?


Ano freemium moduly, které jsou moduly, které jsou k dispozici zdarma, ale s určitými omezeními bude pokládán za žádné publikace.

## <a name="is-intellectual-property-protected"></a>Je chráněný duševního vlastnictví?


Modul Edge je kontejner Dockeru kompatibilní. Záleží jen na partnerů k ochraně jejich duševní vlastnictví (IP), zabalené v distribuované kontejnery.

## <a name="where-will-the-modules-be-hosted"></a>Pokud bude hostovat moduly?


Moduly IoT Edge bude hostovaný v registru kontejneru vlastněným microsoftem, který bude možné anonymně dotazu-, jako je Docker Hubu.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Jaké jsou plány integraci mezi na webu Azure Marketplace a nástroji Azure IoT?

Vytvoříme užší integraci mezi na webu Azure Marketplace a nástroji Azure IoT. Máme v úvahu příklady k povolení některých procházení přímo z portálu služby IoT Hub nebo přímo z Visual Studio Code marketplace modul IoT Edge.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>Které operační systém nebo architektura by měly podporovat Můj kontejner?

Moduly IoT Edge, musí podporovat stejný operační systém nebo architektura matice jako IoT Edge v produkčním prostředí. Tento seznam je zachován v [podpora služby Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support). Modul musí například aktuálně podporují Linux x64 a Linux ARM32.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Existují další omezení certifikace je potřeba vědět?

Stále pracujeme na přesnou sadu certifikace omezení. Naše zásady jsou:

-   Zvyšuje se úroveň kvality nad rámec množství.

-   Kontejnery specifické pro IoT Edge (ne náhodné ty).

-   Na podnikové úrovni produkčního prostředí.

-   nasazení 1 kliknutím (minimální sadu výchozí konfigurační hodnoty za předpokladu).

## <a name="any-other-questions"></a>Další dotazy?


Kontakt [Azure IoT hraniční zavádění](mailto:azureiotedgeonboarding@service.microsoft.com).
