---
title: Nabídka pro publikování Azure IoT Edge modulu | Azure Marketplace
description: Publikování nabídky modulu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ef73956b1b0d3e7bed6e91cde0b92bcc3e432795
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141848"
---
# <a name="publish-iot-edge-module-offer"></a>Publikování nabídky modulu IoT Edge

>[!Important]
>Od 13. dubna 2020 začneme přesouvat správu nabídek IoT Edge modulů do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Podle pokynů v tématu [vytvoření IoT Edge modulu nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) můžete spravovat migrované nabídky.

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
