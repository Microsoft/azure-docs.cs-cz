---
title: Publikovat nabídku modulu Azure IoT Edge | Dokumentace Microsoftu
description: Postup publikování nabídky modul IoT Edge.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 670a2ce205ba5e64418eccc41add36cbecc28212
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430734"
---
# <a name="publish-iot-edge-module-offer"></a>Publikovat nabídku modul IoT Edge

 Po zadání informací v vytvoříte novou nabídku **nová nabídka** stránky, můžete publikovat nabídky. Vyberte **publikovat** zahájíte proces publikování.

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "publikování".

![Nabízejí publikování kroky pro modul IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

Následující tabulka popisuje každý krok pro publikování, čas odhad (maximum) k dokončení každého kroku.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publikování kroku**           | **čas**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadované součásti         | 15 min   | Poskytuje informace a nabídku, nastavení se ověří.                        |
| Certifikace                  | 1 týden | Nabídka se analyzuje tým certifikace Azure. Tento krok provede kontroly viry, malware, dodržování předpisů zabezpečení a problémy se zabezpečením. Bude také ověřit, jestli tato nabídka modul IoT Edge splňuje všechna kritéria způsobilosti (naleznete v tématu [požadavky](./cpp-prerequisites.md) a [Příprava technických prostředků](./cpp-create-technical-assets.md)). Pokud je nalezen problém poskytnutí zpětné vazby. |
| Balení | 1 hodina  | Technické prostředky nabídky jsou zabaleny pro používání zákazníka a systémy potenciálních zákazníků jsou nakonfigurovány a nastavení. |
|  Vydavatel přihlašování vypnuto             |  -        | Zkontrolujte poslední vydavatele a potvrzení před uvedete nabídky. Ověřit, zda splňuje všechny požadavky, můžete nasadit vaši nabídku ve vybraných předplatných (v krocích informace nabídky).  Vyberte **aktivace** tak vaši nabídku můžete přesunout k dalšímu kroku. |
| Balení                 | 1 hodina | Dokončené nabídky jsou replikovány v marketplace produkční systémy a oblastech. | 
| Živé                           | 4 dny |Nabídka je všeobecně dostupné, replikovaná do oblastí, povinné a k dispozici na veřejnou. |

Povolit pro až 10 pracovních dní pro publikování na dokončení procesu a nabídky se uvolní. Po dokončení procesu publikování vaší nabídky modul IoT Edge budou zobrazeny v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Další postup

- [Aktualizace stávající nabídky modul IoT Edge na webu Azure Marketplace](./cpp-update-existing-offer.md)
