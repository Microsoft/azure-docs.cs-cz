---
title: Řešení potíží s běžnými chybami nasazení
titleSuffix: Azure Load Balancer
description: Popisuje řešení běžných chyb při nasazení nástrojů pro vyrovnávání zatížení Azure.
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94695550"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Řešení běžných chyb při nasazení Azure pomocí Azure Load Balancer

Tento článek popisuje některé běžné chyby při nasazení Azure Load Balancer a obsahuje informace o řešení chyb. Pokud hledáte informace o kódu chyby a tyto informace nejsou v tomto článku k dispozici, dejte nám prosím na starosti. V dolní části této stránky můžete nechat svůj názor. Zpětná vazba je sledována s problémy GitHubu.

## <a name="error-codes"></a>Kódy chyb

| Kód chyby | Podrobnosti a zmírnění |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Musí souhlasit obě veřejné IP SKU i Load Balancer SKU. Zajistěte, aby se Azure Load Balancer a veřejné skladové jednotky IP shodovaly. Pro produkční úlohy se doporučuje standardní skladová jednotka (SKU). Další informace o [rozdílech v SKU](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | V případě, že je SKU Neurčeno nebo není nasazené bez standardních veřejných IP adres, je pro službu Virtual Machine Scale Sets výchozí hodnota Basic Load Balancer Znovu nasaďte sadu škálování virtuálního počítače se standardními veřejnými IP adresami pro jednotlivé instance, abyste měli jistotu, že je vybraná možnost Standard Load Balancer, nebo při nasazování sady škálování virtuálních počítačů z Azure Portal vyberte jenom standardní. |
|MaxAvailabilitySetsInLoadBalancerReached | Back-end fond Load Balancer může obsahovat maximálně 150 skupin dostupnosti. Pokud nemáte skupiny dostupnosti explicitně definované pro virtuální počítače ve fondu back-end, každý z nich přejde do své vlastní skupiny dostupnosti. Nasazení 150 samostatných virtuálních počítačů by tedy znamenalo, že by mělo mít 150 skupiny dostupnosti, a tím by se dosáhlo limitu. Můžete nasadit skupinu dostupnosti a přidat do ní další virtuální počítače jako alternativní řešení. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | Pro nástroj pro vyrovnávání zatížení Basic SKU musí být síťové rozhraní a nástroj pro vyrovnávání zatížení ve stejné skupině dostupnosti. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Pro daný typ nástroje pro vyrovnávání zatížení (interní, veřejný) se stejným portem back-end a protokolem, na který odkazuje stejná sada škálování virtuálního počítače, nemůžete mít více než jedno pravidlo. Aktualizujte pravidlo, aby se toto duplicitní vytvoření pravidla změnilo. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Pro daný typ nástroje pro vyrovnávání zatížení (interní, veřejný) se stejným portem back-end a protokolem, na který odkazuje stejná sada škálování virtuálního počítače, nemůžete mít více než jedno pravidlo. Aktualizujte parametry pravidla pro změnu tohoto duplicitního vytvoření pravidla. |
|AnotherInternalLoadBalancerExists| Můžete mít jenom jednu Load Balancer typu interní odkazování na stejnou sadu virtuálních počítačů nebo síťových rozhraní v back-endu Load Balancer. Aktualizujte nasazení, aby se zajistilo, že vytváříte jenom jeden Load Balancer stejného typu. |
|CannotUseInactiveHealthProbe| Nemůžete mít sondu, kterou nepoužívá žádné pravidlo nakonfigurované pro stav Virtual Machine Scale set. Ujistěte se, že se aktivně používá test, který je nastavený. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Nemůžete mít více než jeden nástroj pro vyrovnávání zatížení stejného typu (interní, veřejný). Můžete mít maximálně jednu interní a jednu veřejnou Load Balancer. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Základní Load Balancer není podporována pro skupinu virtuálních počítačů s více umístěními nebo pro sadu škálování virtuálních počítačů zóny dostupnosti. Místo toho použijte Standard Load Balancer. |
|MarketplacePurchaseEligibilityFailed | Přepněte se na správný účet pro správu a umožněte nákup z důvodu předplatného, které je předplatným EA. Další informace najdete [tady](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase). |
|ResourceDeploymentFailure| Pokud je váš nástroj pro vyrovnávání zatížení ve stavu selhání, postupujte podle těchto kroků a obnovte ho ze stavu selhání:<ol><li>Přejít na https://resources.azure.com a přihlaste se pomocí přihlašovacích údajů Azure Portal.</li><li>Vyberte **Čtení/zápis**.</li><li>Na levé straně rozbalte **Předplatná** a pak rozbalte předplatné s Load Balancerem, který chcete aktualizovat.</li><li>Rozbalte **Skupiny prostředků** a pak rozbalte skupinu prostředků s Load Balancerem, který chcete aktualizovat.</li><li>Vyberte **Microsoft. Network**  >  **LoadBalancers** a pak vyberte Load Balancer, který se má aktualizovat **LoadBalancer_1**.</li><li>Na stránce zobrazení **LoadBalancer_1** vyberte **získat**  >  **Úpravy**.</li><li>Aktualizace hodnoty **ProvisioningState** **z neúspěšného** **dokončení.**</li><li>Vyberte **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Další kroky

* Prohlédněte si [tabulku porovnání Azure Load Balancer SKU](./skus.md) .
* Další informace o [omezeních Azure Load Balancer](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)