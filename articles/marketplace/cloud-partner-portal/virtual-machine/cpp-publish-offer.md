---
title: Publikování nabídky virtuálních strojů na Azure Marketplace
description: Uvádí kroky potřebné k publikování existující nabídky virtuálních strojů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: c35f721131b997dcfdb0f23a91a39329168b757c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277340"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikování nabídky virtuálního počítače

 Posledním krokem po definování nabídky na portálu a vytvoření souvisejících technických prostředků je odeslání nabídky k publikování. Následující diagram znázorňuje hlavní kroky v procesu publikování "přejít do provozu":

![Kroky publikování nabídky virtuálních strojů](./media/publishvm_013.png)

Následující tabulka popisuje tyto kroky a poskytuje maximální odhad času pro jejich dokončení:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Krok publikování**           | **Čas**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | 15 min   | Informace o nabídce a nastavení nabídky jsou ověřeny.                        |
| Ověření testovací jednotky (volitelné) | 2 hodiny | Pokud jste zvolili povolení testovací jednotky, společnost Microsoft ověří konfiguraci testovací jednotky, její nasazení a replikaci prostřednictvím vybraných oblastí. |
| Certifikace                  | 3 dny | Nabídku analyzuje certifikační tým Azure. Tento krok provede kontroly virů, malwaru, dodržování předpisů v oblasti bezpečnosti a zabezpečení. Pokud je nalezen problém, je k dispozici zpětná vazba. |
| Zřizování                   | 4 dny   | Nabídka virtuálních počítače se replikuje v produkčních systémech marketplace.               |
| Registrace balení a generování olova | \<1 hodina  | Technické prostředky nabídky jsou baleny pro použití zákazníkem a systémy zájemců jsou konfigurovány a nastaveny. |
|  Odhlášení vydavatele             |  -        | Konečná recenze a potvrzení vydavatele před tím, než nabídka začne být v provozu. Nabídku můžete nasadit ve vybraných předplatných (v krocích informací o nabídce) a ověřit, zda splňuje všechny vaše požadavky.  |
| Zřizování                   | 4 dny | Dokončená nabídka virtuálních počítače se replikuje v produkčních systémech a oblastech tržiště. | 
| Živé                           | 4 dny | Nabídka virtuálních montovny se uvolní, replikuje do požadovaných oblastí a zpřístupní veřejnosti. |
|  |  |

Po dobu až 16 dnů, než tento proces dokončíte.  Po procházení těchto kroků publikování bude vaše nabídka virtuálních počítačích uvedená na [webu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

