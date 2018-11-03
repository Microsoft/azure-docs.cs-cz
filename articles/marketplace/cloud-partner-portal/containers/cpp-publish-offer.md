---
title: Publikování nabídky image kontejnerů Azure | Dokumentace Microsoftu
description: Jak publikovat v rámci nabídky Azure kontejner.
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
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7533d1a133c9c474bc39f0f64c5f1a8183ab30f8
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979687"
---
# <a name="publish-container-offer"></a>Publikování nabídky kontejneru

 Po vytvoření nové nabídky pomocí **nová nabídka** stránky, můžete publikovat tuto nabídku. Vyberte **publikovat** zahájíte proces publikování.

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "publikování".

![Postup publikování nabídky kontejneru](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

Následující tabulka popisuje každý krok pro publikování. Odhadovaný čas na dokončení každého kroku je také zadána.


|  **Publikování kroku**           | **čas**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadované součásti         | 15 min   | Poskytuje informace a nabídku, nastavení se ověří.                        |
| Certifikace                  | 1 týden | Nabídka se analyzuje tým certifikace Azure. Nabídka je vyhledány viry, malware, dodržování bezpečnostních předpisů a problémy se zabezpečením. Nabídka je zaškrtnuté políčko, pokud chcete zobrazit, zda splňuje všechna kritéria způsobilosti. Další informace najdete v tématu [požadavky](./cpp-prerequisites.md) a [Příprava technických prostředků](./cpp-create-technical-assets.md). Pokud je nalezen problém k dispozici pro zpětnou vazbu. |
| Balení | 1 hodina  | Technické prostředky nabídky jsou zabaleny pro používání zákazníka a systémy potenciálních zákazníků jsou nakonfigurovány a nastavení. |
|  Odhlášení vydavatele             |  -        | Zkontrolujte poslední vydavatele a potvrzení před uvedete nabídky. Ověřit, zda splňuje všechny požadavky, můžete nasadit vaši nabídku ve vybraných předplatných (v krocích informace nabídky).  Vyberte **aktivace** tak vaši nabídku můžete přesunout k dalšímu kroku. |
| Balení                 | 1 hodina | Dokončené nabídky jsou replikovány v marketplace produkční systémy a oblastech. | 
| Živé                           | 4 dny |Nabídka je všeobecně dostupné, replikovaná do oblastí, povinné a k dispozici na veřejnou. |

Povolit pro až 10 pracovních dní pro publikování na dokončení procesu a nabídky se uvolní. Po dokončení procesu publikování vaší nabídky kontejneru budou zobrazeny v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Další postup

[Aktualizace stávající nabídky kontejneru na webu Azure Marketplace](./cpp-update-existing-offer.md)
