---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571961"
---
| Prostředek | Výchozí omezení |
| --- | :--- |
| Skupiny kontejnerů na [předplatné](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Počet kontejnerů na skupinu kontejnerů | 60 |
| Počet svazků na skupinu kontejnerů | 20 |
| Počet portů na IP adresu | 5 |
| Vytvoření kontejneru za hodinu |300<sup>1</sup> |
| Vytvoření kontejneru za 5 minut | 100<sup>1</sup> |
| Odstranění kontejneru za hodinu | 300<sup>1</sup> |
| Odstranění kontejneru za 5 minut | 100<sup>1</sup> |
| Více kontejnerů na skupinu kontejnerů | Jenom Linux<sup>2</sup> |
| Svazky Azure Files | Jenom Linux<sup>2</sup> |
| Svazky GitRepo | Jenom Linux<sup>2</sup> |
| Tajné svazky | Jenom Linux<sup>2</sup> |

<sup>1</sup> Pokud si chcete vyžádat zvýšení limitu, vytvořte [žádost o podporu Azure][azure-support].<br />
<sup>2</sup> Podpora Windows pro tuto funkci je naplánovaná.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
