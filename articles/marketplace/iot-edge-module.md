---
title: Moduly Azure IoT Edge
description: Nabídka modulu IoT Edge na Azure Marketplace pro vydavatele aplikací a služeb.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: dsindona
ms.openlocfilehash: aadbf33914f919e393a5ec88cf6fc0a6103911b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286145"
---
# <a name="iot-edge-modules"></a>Moduly IoT Edge

Platforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) je podporovaná Azure Cloudem.  Tato platforma umožňuje uživatelům nasadit cloudové úlohy pro spuštění přímo na zařízeních IoT.  Modul IoT Edge může spouštět offline úlohy a provést analýzu dat místně. Tento typ nabídky pomáhá šetřit šířku pásma, chránit místní a citlivá data a nabízí dobu odezvy s nízkou latencí.  Nyní máte možnosti využít tyto předem vytvořené úlohy. Dosud byla k dispozici pouze hrstka řešení první strany od společnosti Microsoft.  Museli jste investovat čas a prostředky do budování vlastních řešení IoT.

Zavedením [modulů IoT Edge na Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)teď máme jediný cíl, kde mohou vydavatelé zpřístupnit a prodat svá řešení cílové skupině IoT. Vývojáři IoT mohou nakonec najít a zakoupit možnosti, aby urychlili vývoj svých řešení.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Hlavní výhody modulů IoT Edge na Azure Marketplace:

| **Pro vydavatele**    | **Pro zákazníky (vývojáři IoT)**  |
| :------------------- | :-------------------|
| Oslovte miliony vývojářů, kteří chtějí vytvářet a nasazovat řešení IoT Edge.  | Sestavte řešení IoT Edge s jistotou použití zabezpečených a testovaných komponent. |
| Publikujte jednou a spouštějte veškerý hardware IoT Edge, který podporuje kontejnery. | Zkraťte čas uvedení na trh vyhledáním a nasazením modulů IoT Edge první a třetí strany pro specifické potřeby. |
| Zpeněžit s flexibilními možnostmi fakturace <ul> <li> Zdarma a přineste si vlastní licenci (BYOL). </li> </ul> | Nakupujte podle svých výběrových modelů fakturace. <ul> <li> Zdarma a přineste si vlastní licenci (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co je modul IoT Edge?

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiku na hraničních zařízeních ve formě modulů. Moduly Azure IoT Edge jsou nejmenší výpočetní jednotky spravované IoT Edge a mohou obsahovat služby Microsoftu (například Azure Stream Analytics), služby třetích stran nebo vlastní kód specifický pro řešení. Další informace o modulech IoT Edge najdete [v tématu Principy modulů Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Jaký je rozdíl mezi typem nabídky Container a typem nabídky modulu IoT Edge?**

Typ nabídky modulu IoT Edge je specifický typ kontejneru spuštěného na zařízení IoT Edge. Dodává se s výchozím nastavením konfigurace pro spuštění v kontextu IoT Edge a volitelně používá modul IoT Edge SDK, který se integruje s runtime IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publikování modulu IoT Edge

**Výběr správného průčelí**

Moduly IoT Edge se publikují jenom na Azure Marketplace, AppSource se nepoužije.  Další informace o rozdílech a cílové skupině v obchodech najdete [v tématu určení možnosti publikování pro vaše řešení](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Možnosti fakturace**

Marketplace v současné době podporuje **možnosti** fakturace Free and **Bring Your Own License (BYOL)** pro moduly IoT Edge.
 
**Možnosti publikování**

Ve všech případech by moduly IoT Edge měly vybrat možnost publikování **Transact.**  Další podrobnosti o možnostech publikování najdete v [tématu Výběr možnosti publikování.](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)  

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

Všechny podmínky smluv a zásad Microsoft Azure Marketplace platí pro nabídky modulů IoT Edge.  Kromě toho existují předpoklady a technické požadavky pro moduly IoT Edge.  

**Předpoklady**

Pokud chcete publikovat modul IoT Edge na Azure Marketplace, musíte splnit následující předpoklady:

- Přístup k portálu partnerů cloudu (CPP). Další informace najdete v [tématu Azure Marketplace a AppSource publikování průvodce](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hostujte svůj modul IoT Edge v registru kontejnerů Azure. 
- Připravte si metadata modulu IoT Edge, například (nevyčerpávající seznam): 
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevné velikosti obrázků včetně 40x40px, 90x90px, 115x115px, 255x115px)
    - Termín použití a zásady ochrany osobních údajů
    - Výchozí konfigurace modulu (trasa, twin požadované vlastnosti, createOptions, proměnné prostředí)
    - Dokumentace
    - Kontaktní údaje podpory

**Technické požadavky**

Primární technické požadavky na modul IoT Edge, aby získal certifikaci a publikování na Azure Marketplace, jsou podrobně popsány v [technických prostředcích modulu Příprava modulu IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Dokumentace a zdroje

[Vytvořte nabídku modulu IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) – kroky pro publikování nové nabídky modulu IoT Edge pomocí portálu pro publikování na cloudu.

## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili,

- [Zaregistrujte se](https://azuremarketplace.microsoft.com/sell) na trhu.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávající nabídce,

- Přihlaste se na [portál cloudových partnerů](https://cloudpartner.azure.com/) a vytvořte nebo dokončete svou nabídku.
- Informace o tom, jak publikovat nabídku modulu IoT Edge, najdete v článku Přehled publikování nabídky nabídky modulu [IoT](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) Edge.
