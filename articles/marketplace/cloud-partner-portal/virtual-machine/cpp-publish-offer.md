---
title: Publikování nabídky virtuálních počítačů na webu Azure Marketplace
description: Seznamy kroky potřebné k publikování existujícího virtuálního počítače nabízet na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 6796c2871cf8a5928beed2ab557cefdfe8ecaae9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938598"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikování nabídky virtuálních počítačů

 Posledním krokem po nabídky definované na portálu a vytvoří přidružené technické prostředky je odeslat nabídku k publikování. Následující diagram znázorňuje hlavní kroky v procesu publikování "vysílat živě":

![Nabízejí publikování kroky pro virtuální počítač](./media/publishvm_013.png)

Následující tabulka popisuje tyto kroky a poskytuje odhad maximální doba pro jejich dokončení:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publikování kroku**           | **čas**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadované součásti         | 15 min   | Poskytuje informace a nabídku, nastavení se ověří.                        |
| Testování jednotek ověřování (volitelné) | 2 hodiny | Pokud jste vybrali k povolení Test Drive, Microsoft ověřuje konfigurace Test Drive, nasazení a replikace prostřednictvím vybraných oblastech. |
| Certifikace                  | 3 dny | Nabídka se analyzuje tým certifikace Azure. Tento krok provede kontroly viry, malware, dodržování předpisů zabezpečení a problémy se zabezpečením. Pokud je nalezen problém poskytnutí zpětné vazby. |
| Zřizování                   | 4 dny   | Nabídky virtuálních počítačů jsou replikovány v marketplace produkčními systémy.               |
| Balení a registrace generování potenciálních zákazníků | \< 1 hodina  | Technické prostředky nabídky jsou zabaleny pro používání zákazníka a systémy potenciálních zákazníků jsou nakonfigurovány a nastavení. |
|  Vydavatel schvalování             |  -        | Zkontrolujte poslední vydavatele a potvrzení před uvedete nabídky. Ověřit, zda splňuje všechny požadavky, můžete nasadit vaši nabídku ve vybraných předplatných (v krocích informace nabídky).  |
| Zřizování                   | 4 dny | Dokončené nabídky virtuálních počítačů jsou replikovány v marketplace produkční systémy a oblastech. | 
| Živé                           | 4 dny | Nabídky virtuálních počítačů je vydána, replikovaná do oblastí, povinné a k dispozici veřejně. |
|  |  |

Povolit až 16 dnů pro dokončení tohoto procesu.  Po projít postup publikování vaší nabídky virtuálních počítačů nebude uvedené [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

