---
title: Publikování nabídky virtuálního počítače v Azure Marketplace
description: Obsahuje seznam kroků potřebných k publikování stávající nabídky virtuálního počítače na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: f66ce0c15e976898d5022bf5705a82fe0969ec07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147662"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikování nabídky virtuálního počítače

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v tématu [Vytvoření virtuálního počítače Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) pro správu migrovaných nabídek.

 Poslední krok, po definování nabídky na portálu a vytvoření přidružených technických prostředků, je odeslání nabídky k publikování. Následující diagram znázorňuje hlavní kroky v procesu publikování na "jít živě":

![Kroky publikování pro nabídku virtuálních počítačů](./media/publishvm_013.png)

Následující tabulka popisuje tyto kroky a poskytuje maximální časový odhad jejich dokončení:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikování**           | **Interval**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | 15 min   | Budou ověřeny informace nabídky a nastavení nabídky.                        |
| Ověření testovacích jednotek (volitelné) | 2 hodiny | Pokud jste vybrali možnost Povolit testovací verzi, společnost Microsoft ověří konfiguraci testovacích jednotek, její nasazení a replikaci prostřednictvím vybraných oblastí. |
| Certifikace                  | 3 dny | Nabídka se analyzuje prostřednictvím certifikačního týmu Azure. Tento krok provede kontrolu virů, malwaru, dodržování bezpečnostních předpisů a problémů se zabezpečením. Pokud je nalezen problém, je poskytnuta zpětná vazba. |
| Zřizování                   | 4 dny   | Nabídka virtuálních počítačů se replikuje v produkčních systémech Marketplace.               |
| Registrace pro vytváření balíčků a vedoucích | \<1 hodina  | Technické prostředky nabídky jsou zabaleny pro zákazníky a jsou nakonfigurované a nastavené systémy pro potenciální zákazníky. |
|  Schvalování vydavatele             |  -        | Poslední kontrola a potvrzení vydavatele před tím, než bude nabídka živá. Nabídku můžete nasadit ve vybraných předplatných (v informacích o nabídce) a ověřit, že splňuje všechny vaše požadavky.  |
| Zřizování                   | 4 dny | Dokončená nabídka virtuálních počítačů se replikuje v produkčních systémech a oblastech Marketplace. | 
| Živé                           | 4 dny | Nabídka virtuálních počítačů se uvolní, replikuje se do požadovaných oblastí a zpřístupní se veřejnosti. |
|  |  |

Povolí až 16 dní, než se tento proces dokončí.  Po provedení těchto kroků publikování se vaše nabídka VM zobrazí v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

