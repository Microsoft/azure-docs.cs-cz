---
title: Co je řadič příchozího přenosu dat aplikační brány Azure?
description: Tento článek obsahuje úvod k tomu, co je řadič příchozího přenosu dat in-gress brány aplikace.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335802"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Co je řadič příchozího přenosu dat v aplikační bráně?
Řadič vstupního přenosu dat aplikační brány (AGIC) je aplikace Kubernetes, která zákazníkům [služby Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) umožňuje využívat nativní vyvažovač zatížení Azure Pro nativní [aplikační bránu](https://azure.microsoft.com/services/application-gateway/) L7 k vystavení cloudového softwaru k Internetu. AGIC monitoruje cluster Kubernetes, ve kterých je hostován, a průběžně aktualizuje aplikační bránu, takže vybrané služby jsou vystaveny Internetu.

Řadič příchozího přenosu dat běží ve vlastním podu na AKS zákazníka. AGIC monitoruje podmnožinu Kubernetes Resources pro změny. Stav clusteru AKS se přeloží na konkrétní konfiguraci aplikační brány a použije se ve [Správci prostředků Azure (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="benefits-of-application-gateway-ingress-controller"></a>Výhody řadiče příchozího přenosu dat aplikační brány
AGIC umožňuje nasazení řídit více clusterů AKS s jedním řadičem příchozího přenosu dat aplikační brány. AGIC také pomáhá eliminovat potřebu mít další vyrovnávání zatížení/veřejnou IP adresu před clusterem AKS a vyhýbá se více směrování v datové cestě před požadavky dosáhnout clusteru AKS. Aplikace Gateway mluví s pody pomocí jejich privátní IP přímo a nevyžaduje NodePort nebo KubeProxy služby. To také přináší lepší výkon pro vaše nasazení.

Ingress Controller je podporován výhradně Standard_v2 a WAF_v2 sku, což také přináší výhody automatického škálování. Aplikační brána může reagovat v reakci na zvýšení nebo snížení zatížení provozu a škálování odpovídajícím způsobem, aniž by spotřebovávat všechny prostředky z clusteru AKS.

Použití aplikační brány kromě AGIC také pomáhá chránit cluster AKS tím, že poskytuje zásady TLS a funkce brány WAF (Web Application Firewall).

![Azure Aplikační brána + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC je konfigurován prostřednictvím [prostředku Příchozí přenos](https://kubernetes.io/docs/user-guide/ingress/)dat Kubernetes spolu se službou a nasazeními/pody. Poskytuje řadu funkcí, které využívají nativní nástroje pro vyrovnávání zatížení Azure nativní aplikační brány L7. Abychom jmenovali několik:
  - Směrování adres URL
  - Spřažení na základě souborů cookie.
  - Ukončení TLS
  - Komplexní TLS
  - Podpora veřejných, soukromých a hybridních webových stránek
  - Brána firewall pro integrovanou webovou aplikaci

AGIC je schopen zpracovat více oborů názvů a má zakázané cíle, což znamená, že AGIC můžete nakonfigurovat aplikační bránu speciálně pro clustery AKS bez ovlivnění jiných existujících back-endů. 

## <a name="next-steps"></a>Další kroky

- [**Nasazení na zelené poli**](ingress-controller-install-new.md): Pokyny k instalaci AGIC, AKS a aplikační brány na infrastrukturu s prázdnou břidlicí.
- [**Brownfield Nasazení**](ingress-controller-install-existing.md): Nainstalujte AGIC na existující AKS a aplikační brány.

