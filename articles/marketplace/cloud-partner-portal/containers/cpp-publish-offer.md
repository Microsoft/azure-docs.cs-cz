---
title: Publikování nabídky imagí Azure Containers | Azure Marketplace
description: Jak publikovat nabídku kontejneru Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: cf422e3d77fa0722fe10a52073cf89ab2a76dcc0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823169"
---
# <a name="publish-container-offer"></a>Publikace nabídky kontejnerů

 Po vytvoření nové nabídky pomocí stránky **Nová nabídka** můžete tuto nabídku publikovat. Vyberte **publikovat** a spusťte proces publikování.

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "jít živě".

![Kroky publikování pro nabídku kontejneru](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

V následující tabulce jsou popsány jednotlivé kroky publikování. Také je uvedena předpokládaná doba pro dokončení každého kroku.


|  **Krok publikování**           | **Interval**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | 15 min   | Budou ověřeny informace nabídky a nastavení nabídky.                        |
| Certifikace                  | 1 týden | Nabídka se analyzuje prostřednictvím certifikačního týmu Azure. V nabídce se hledají viry, malware, dodržování bezpečnostních předpisů a problémy se zabezpečením. V rámci této nabídky se kontroluje, že splňuje všechna kritéria způsobilosti. Další informace najdete v tématu [požadavky](./cpp-prerequisites.md) a [Příprava technických prostředků](./cpp-create-technical-assets.md). V případě, že dojde k problému, je poskytnuta zpětná vazba. |
| Balení | 1 hodina  | Technické prostředky nabídky jsou zabaleny pro zákazníky a jsou nakonfigurované a nastavené systémy pro potenciální zákazníky. |
|  Odhlášení vydavatele             |  -        | Poslední kontrola a potvrzení vydavatele před tím, než bude nabídka živá. Nabídku můžete nasadit ve vybraných předplatných (v informacích o nabídce) a ověřit, že splňuje všechny vaše požadavky.  Vyberte možnost **Přejít do provozu** , aby vaše nabídka mohla přejít k dalšímu kroku. |
| Balení                 | 1 hodina | Nabídka dokončená se replikuje na Marketplace v produkčních systémech a oblastech. | 
| Živé                           | 4 dny |Nabídka je uvolněna, replikována do požadovaných oblastí a zpřístupněna veřejnosti. |

Povolí až 10 pracovních dní pro dokončení procesu publikování a nabídka se uvolní. Po dokončení procesu publikování se vaše nabídka kontejneru zobrazí v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Další kroky

[Aktualizovat existující nabídku kontejneru na Azure Marketplace](./cpp-update-existing-offer.md)
