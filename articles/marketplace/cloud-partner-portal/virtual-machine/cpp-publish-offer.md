---
title: Publikování nabídky virtuálního počítače v Azure Marketplace
description: Obsahuje seznam kroků potřebných k publikování stávající nabídky virtuálního počítače na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 1b07f3f3edab47f8f75835dffd4cc3f89f17ab63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824412"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikování nabídky virtuálního počítače

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
| Registrace pro vytváření balíčků a vedoucích | \< 1 hodina  | Technické prostředky nabídky jsou zabaleny pro zákazníky a jsou nakonfigurované a nastavené systémy pro potenciální zákazníky. |
|  Schvalování vydavatele             |  -        | Poslední kontrola a potvrzení vydavatele před tím, než bude nabídka živá. Nabídku můžete nasadit ve vybraných předplatných (v informacích o nabídce) a ověřit, že splňuje všechny vaše požadavky.  |
| Zřizování                   | 4 dny | Dokončená nabídka virtuálních počítačů se replikuje v produkčních systémech a oblastech Marketplace. | 
| Živé                           | 4 dny | Nabídka virtuálních počítačů se uvolní, replikuje se do požadovaných oblastí a zpřístupní se veřejnosti. |
|  |  |

Povolí až 16 dní, než se tento proces dokončí.  Po provedení těchto kroků publikování se vaše nabídka VM zobrazí v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

