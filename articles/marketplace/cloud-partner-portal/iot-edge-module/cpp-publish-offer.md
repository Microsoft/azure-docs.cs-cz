---
title: Nabídka pro publikování Azure IoT Edge modulu | Azure Marketplace
description: Publikování nabídky modulu IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813837"
---
# <a name="publish-iot-edge-module-offer"></a>Publikování nabídky modulu IoT Edge

 Po vytvoření nové nabídky poskytnutím informací na **nové stránce nabídky** můžete tuto nabídku publikovat. Vyberte **publikovat** a spusťte proces publikování.

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "jít živě".

![Postup publikování pro nabídku modulu IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

Následující tabulka popisuje jednotlivé kroky publikování s časovým odhadem (maximem) pro dokončení každého kroku.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikování**           | **Interval**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | 15 min   | Budou ověřeny informace nabídky a nastavení nabídky.                        |
| Certifikace                  | 2 týdny | Nabídka se analyzuje prostřednictvím certifikačního týmu Azure. Tento krok provede kontrolu virů, malwaru, dodržování bezpečnostních předpisů a problémů se zabezpečením. Také se ověří, že tato nabídka modulu IoT Edge splňuje všechna kritéria způsobilosti (viz [požadavky](./cpp-prerequisites.md) a [Příprava technických prostředků](./cpp-create-technical-assets.md)). Pokud je nalezen problém, je poskytnuta zpětná vazba. |
| Balení | 1 hodina  | Technické prostředky nabídky jsou zabaleny pro zákazníky a jsou nakonfigurované a nastavené systémy pro potenciální zákazníky. |
|  Odhlášení vydavatele             |  -        | Poslední kontrola a potvrzení vydavatele před tím, než bude nabídka živá. Nabídku můžete nasadit ve vybraných předplatných (v informacích o nabídce) a ověřit, že splňuje všechny vaše požadavky.  Vyberte možnost **Přejít do provozu** , aby vaše nabídka mohla přejít k dalšímu kroku. |
| Balení                 | 1 hodina | Konečná nabídka se replikuje v produkčních systémech a oblastech Marketplace. | 
| Živé                           | 4 dny |Nabídka je uvolněna, replikována do požadovaných oblastí a zpřístupněna veřejnosti. |

Povolí až 10 pracovních dní pro dokončení procesu publikování a nabídka se uvolní. Po dokončení procesu publikování budou nabídky modulu IoT Edge uvedeny v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Další kroky

- [Aktualizace existující nabídky modulu IoT Edge v Azure Marketplace](./cpp-update-existing-offer.md)
