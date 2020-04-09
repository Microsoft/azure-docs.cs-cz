---
title: Publikování nabídky modulu Azure IoT Edge | Azure Marketplace
description: Jak publikovat nabídku modulu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9d78ff53fb520dcfc80a812d53ae188e340722af
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983190"
---
# <a name="publish-iot-edge-module-offer"></a>Publikování nabídky modulu IoT Edge

>[!Important]
>dubna 2020 začneme přesouvat správu nabídek modulu IoT Edge do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky modulu IoT Edge](https://aka.ms/AzureCreateIoT) pro správu migrovaných nabídek.

 Po vytvoření nové nabídky poskytnutím informací na stránce **Nová nabídka** můžete nabídku publikovat. Chcete-li zahájit proces publikování, vyberte **publikovat.**

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "přejít do provozu".

![Kroky publikování pro nabídku modulu IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

Následující tabulka popisuje každý krok publikování s časovým odhadem (maximálně) k dokončení každého kroku.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikování**           | **Čas**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | 15 min   | Informace o nabídce a nastavení nabídky jsou ověřeny.                        |
| Certifikace                  | 2 týdny | Nabídku analyzuje certifikační tým Azure. Tento krok provede kontroly virů, malwaru, dodržování předpisů v oblasti bezpečnosti a zabezpečení. Ověří také, zda tato nabídka modulu IoT Edge splňuje všechna kritéria způsobilosti (viz [požadavky](./cpp-prerequisites.md) a [příprava technických prostředků).](./cpp-create-technical-assets.md) Pokud je nalezen problém, je k dispozici zpětná vazba. |
| Balení | 1 hodina  | Technické prostředky nabídky jsou baleny pro použití zákazníkem a systémy zájemců jsou konfigurovány a nastaveny. |
|  Odhlášení vydavatele             |  -        | Konečná recenze a potvrzení vydavatele před tím, než nabídka začne být v provozu. Nabídku můžete nasadit ve vybraných předplatných (v krocích informací o nabídce) a ověřit, zda splňuje všechny vaše požadavky.  Vyberte **Přejít na živo,** aby se vaše nabídka mohla přesunout k dalšímu kroku. |
| Balení                 | 1 hodina | Dokončená nabídka je replikována v produkčních systémech a oblastech tržiště. | 
| Živé                           | 4 dny |Nabídka je uvolněna, replikována do požadovaných oblastí a zpřístupněna veřejnosti. |

Vyčkejte až 10 pracovních dnů, než proces publikování skončí, a nabídka bude vydána. Po dokončení procesu publikování bude nabídka modulu IoT Edge uvedena na [webu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Další kroky

- [Aktualizace nabídky existujícího modulu IoT Edge na Azure Marketplace](./cpp-update-existing-offer.md)
