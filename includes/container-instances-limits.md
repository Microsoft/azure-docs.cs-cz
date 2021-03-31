---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87384819"
---
| Prostředek | Omezení |
| --- | :--- |
| Standardní skupiny kontejnerů SKU na oblast a předplatné | 100<sup>1</sup> |
| Vyhrazené skupiny kontejnerů SKU na oblast na předplatné | 0<sup>1</sup> |
| Počet kontejnerů na skupinu kontejnerů | 60 |
| Počet svazků na skupinu kontejnerů | 20 |
| Jádra Standard SKU (CPU) na oblast na předplatné | 10<sup>1, 2</sup> | 
| Jádra Standard SKU (CPU) pro procesor K80 GPU na oblast a předplatné | 18<sup>1, 2</sup> |
| Jádra Standard SKU (CPU) pro P100 nebo V100 GPU na oblast v rámci předplatného | 0<sup>1, 2</sup> |
| Počet portů na IP adresu | 5 |
| Velikost protokolu instance kontejneru – spuštěná instance | 4 MB |
| Velikost protokolu instance kontejneru – zastavená instance | 16 KB nebo 1 000 řádků |
| Vytvoření kontejneru za hodinu |300<sup>1</sup> |
| Vytvoření kontejneru za 5 minut | 100<sup>1</sup> |
| Odstranění kontejneru za hodinu | 300<sup>1</sup> |
| Odstranění kontejneru za 5 minut | 100<sup>1</sup> |


<sup>1</sup> Pokud chcete požádat o zvýšení limitu, vytvořte [support Request Azure][azure-support]. Bezplatné předplatné, včetně [bezplatného účtu Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) a [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/) , nemá nárok na zvýšení limitu nebo kvóty. Pokud máte bezplatné předplatné, můžete [upgradovat](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) na předplatné s průběžnými platbami.<br />
<sup>2</sup> . Výchozí omezení pro předplatné s průběžnými [platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) Omezení se může lišit pro ostatní typy kategorií.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
