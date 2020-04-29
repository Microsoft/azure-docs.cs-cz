---
title: Moduly IoT Edge Azure Marketplace
description: IoT Edge modul nabízí v Azure Marketplace pro vydavatele aplikací a služeb.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 762d9947046f159e992f09211bfcd76ff8d6712e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684344"
---
# <a name="iot-edge-modules"></a>Moduly IoT Edge

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platforma je zajištěna pomocí cloudu Azure.  Tato platforma umožňuje uživatelům nasadit cloudové úlohy pro přímé spouštění na zařízeních IoT.  Modul IoT Edge může spouštět offline úlohy a provádět analýzu dat místně. Tento typ nabídky pomáhá ušetřit šířku pásma, chránit místní a citlivá data a nabízí dobu odezvy s nízkou latencí.  Nyní máte možnost využít tyto předem připravené úlohy. Až do této chvíle byly k dispozici jenom několik řešení od společnosti Microsoft.  Museli byste investovat čas a prostředky do sestavení vlastních řešení IoT.

Představujeme [IoT Edge moduly v Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)teď máme jediné místo, kde vydavatelé zveřejňují a prodávají svá řešení pro cílovou skupinu IoT. Vývojáři IoT můžou nakonec najít a koupit možnosti pro zrychlení vývoje řešení.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Klíčové výhody IoT Edgech modulů v Azure Marketplace:

| **Vydavatelům**    | **Pro zákazníky (vývojáři IoT)**  |
| :------------------- | :-------------------|
| Oslovit miliony vývojářů, kteří chtějí sestavovat a nasazovat řešení IoT Edge.  | Vytvořte IoT Edge řešení s jistotou použití zabezpečených a testovaných komponent. |
| Publikujte jednou a spusťte napříč jakýmkoli IoT Edge hardwarem, který podporuje kontejnery. | Vyhledáním a nasazením IoT Edge modulů od prvního a jiného výrobce se zkrátí doba uvedení na trh pro konkrétní potřeby. |
| Monetizovat s možnostmi flexibilní fakturace <ul> <li> Bezplatné a přineste si vlastní licenci (BYOL). </li> </ul> | Nakupujete podle svého výběru modelů fakturace. <ul> <li> Bezplatné a přineste si vlastní licenci (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co je modul IoT Edge?

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiku na hraničních zařízeních ve formě modulů. Azure IoT Edge moduly jsou nejmenší výpočetní jednotky spravované IoT Edge a můžou obsahovat služby Microsoftu (například Azure Stream Analytics), služby třetích stran nebo vlastní kód specifický pro řešení. Další informace o IoT Edgech modulech najdete v tématu [principy Azure IoT Edgech modulů](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Jaký je rozdíl mezi typem nabídky kontejneru a typem nabídky modulu IoT Edge?**

Typ nabídky IoT Edge modul je konkrétní typ kontejneru, který je spuštěný v IoT Edgem zařízení. Je k dispozici s výchozím nastavením konfigurace pro spuštění v kontextu IoT Edge a volitelně používá sadu IoT Edge Module SDK pro integraci s modulem runtime IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publikování modulu IoT Edge

**Výběr pravého prezentaceu**

IoT Edge moduly jsou publikovány pouze do Azure Marketplace, AppSource se nevztahují.  Další informace o rozdílech a cílové skupině v různých prodejní místa najdete v tématu [Určení možnosti publikování pro vaše řešení](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Možnosti fakturace**

Web Marketplace v současnosti podporuje **bezplatné** možnosti fakturace a přináší **vlastní licenci (BYOL)** pro moduly IoT Edge.
 
**Možnosti publikování**

Ve všech případech by IoT Edge moduly měly vybrat možnost publikování v režimu **Transact** .  Další podrobnosti o možnostech publikování najdete v tématu Volba [Možnosti publikování](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) .  

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

Všechny podmínky Microsoft Azure Marketplace smluv a zásad se vztahují na nabídky modulu IoT Edge.  Kromě toho existují požadavky a technické požadavky pro IoT Edge moduly.  

**Předpoklady**

Chcete-li publikovat modul IoT Edge do Azure Marketplace, je nutné splnit následující požadavky:

- Přístup k partnerskému centru. Další informace najdete v tématu [Příručka pro publikování Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hostování modulu IoT Edge v Azure Container Registry. 
- Připravte si metadata modulu IoT Edge, například (nevyčerpávající seznam): 
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevné velikosti obrázků, včetně 40x40px, 90x90px, 115x115px, 255x115px)
    - Podmínky použití a zásad ochrany osobních údajů
    - Výchozí konfigurace modulu (trasa, nevlákenovaná požadovaná vlastnost, createOptions, proměnné prostředí)
    - Dokumentace
    - Kontaktní údaje podpory

**Technické požadavky**

Hlavní technické požadavky pro modul IoT Edge, aby bylo možné získat certifikaci a publikovat ho v Azure Marketplace, je podrobně popsáno v [technickém assetu příprava IoT Edge modulu](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="documentation-and-resources"></a>Dokumentace a prostředky

[Vytvoření nabídky modulu IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) – postup publikování nové nabídky modulu IoT Edge v partnerském centru.

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali,

- [Seznamte](https://azuremarketplace.microsoft.com/sell) se s Marketplace.

Pokud se chcete zaregistrovat v partnerském centru a začít vytvářet novou nabídku nebo pracovat na stávající nabídce,

- Přihlaste se do [partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Informace o tom, jak publikovat nabídku modulu IoT Edge, najdete v tématu [Vytvoření nabídky modulu IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) .
