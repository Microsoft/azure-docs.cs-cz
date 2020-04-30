---
title: Publikování nabídky imagí Azure Containers | Azure Marketplace
description: Jak publikovat nabídku kontejneru Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 58e096a3b25b16e54cf2f18935dcf4a2d44cd767
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146216"
---
# <a name="publish-container-offer"></a>Publikace nabídky kontejnerů

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek Azure Container do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v části [vytvoření kontejneru Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) pro správu migrovaných nabídek.

 Po vytvoření nové nabídky pomocí stránky **Nová nabídka** můžete tuto nabídku publikovat. Vyberte **publikovat** a spusťte proces publikování.

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "jít živě".

![Kroky publikování pro nabídku kontejneru](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

V následující tabulce jsou popsány jednotlivé kroky publikování. Také je uvedena předpokládaná doba pro dokončení každého kroku.


|  **Krok publikování**           | **Interval**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | 15 min   | Budou ověřeny informace nabídky a nastavení nabídky.                        |
| Certifikace                  | 1 týden | Nabídka se analyzuje prostřednictvím certifikačního týmu Azure. V nabídce se zkontrolují viry, malware, dodržování bezpečnostních předpisů a problémy se zabezpečením. Nabídka se zkontroluje, aby se zjistilo, jestli splňuje všechna kritéria způsobilosti. Další informace najdete v tématu [požadavky](./cpp-prerequisites.md) a [Příprava technických prostředků](./cpp-create-technical-assets.md). V případě, že dojde k problému, je poskytnuta zpětná vazba. |
| Balení | 1 hodina  | Technické prostředky nabídky jsou zabaleny pro zákazníky a jsou nakonfigurované a nastavené systémy pro potenciální zákazníky. |
|  Odhlášení vydavatele             |  -        | Poslední kontrola a potvrzení vydavatele před tím, než bude nabídka živá. Nabídku můžete nasadit ve vybraných předplatných (v informacích o nabídce) a ověřit, že splňuje všechny vaše požadavky.  Vyberte možnost **Přejít do provozu** , aby vaše nabídka mohla přejít k dalšímu kroku. |
| Balení                 | 1 hodina | Nabídka dokončená se replikuje na Marketplace v produkčních systémech a oblastech. | 
| Živé                           | 4 dny |Nabídka je uvolněna, replikována do požadovaných oblastí a zpřístupněna veřejnosti. |

Povolí až 10 pracovních dní pro dokončení procesu publikování a nabídka se uvolní. Po dokončení procesu publikování se vaše nabídka kontejneru zobrazí v [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Další kroky

[Aktualizovat existující nabídku kontejneru na Azure Marketplace](./cpp-update-existing-offer.md)
