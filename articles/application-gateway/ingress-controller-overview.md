---
title: Co je kontroler služby Azure Application Gateway pro příchozí přenosy?
description: Tento článek poskytuje Úvod k tomu, co je Application Gateway řadič příchozího přenosu dat.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/11/2019
ms.author: caya
ms.openlocfilehash: 79ddfe6b536dcdd7e930960c51854849b94df1bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513351"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Co je Application Gateway kontroler příchozího přenosu dat?
AGIC () je Kubernetes aplikace, díky které můžou zákazníci [služby Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) využít nativní [Application Gateway](https://azure.microsoft.com/services/application-gateway/) pro vyrovnávání zatížení pro Azure k zveřejnění cloudového softwaru pro Application Gateway Internet. AGIC monitoruje cluster Kubernetes, na kterém je hostovaný, a nepřetržitě aktualizuje Application Gateway, aby se vybrané služby zobrazovaly na internetu.

Kontroler příchozího provozu je spuštěný pod AKS zákazníka. AGIC monitoruje podmnožinu prostředků Kubernetes pro změny. Stav clusteru AKS se převede na Application Gateway specifickou konfiguraci a použije se na [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Výhody Application Gatewayho řadiče pro příchozí přenos dat
AGIC umožňuje vašemu nasazení řídit více clusterů AKS pomocí jednoho kontroleru Application Gateway příchozího přenosu dat. AGIC také pomáhá eliminovat nutnost mít další službu Vyrovnávání zatížení/veřejnou IP adresu před clusterem AKS a před požadavky na cluster AKS zabrání vícenásobnému směrování ve vaší datacestách. Application Gateway mluví s lusky pomocí jejich privátní IP adresy přímo a nevyžaduje služby NodePort nebo KubeProxy. Tím se také zvýší výkon pro vaše nasazení.

Kontroler příchozího přenosu dat podporuje výhradně skladové položky Standard_v2 a WAF_v2, což také přináší výhody automatického škálování. Application Gateway může reagovat při reakci na zvýšení nebo snížení zatížení provozu a odpovídajícím způsobem škálovat, aniž by se musely spotřebovávat žádné prostředky z clusteru AKS.

Použití Application Gateway kromě AGIC také pomáhá chránit cluster AKS tím, že poskytuje zásady TLS a funkce firewallu webových aplikací (WAF).

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC se konfiguruje prostřednictvím [prostředku](http://kubernetes.io/docs/user-guide/ingress/)Kubernetes příchozího přenosu společně se službami a nasazeními/lusky. Poskytuje řadu funkcí, které využívají nativní službu pro vyrovnávání zatížení Azure Application Gateway. Několik názvů:
  - Směrování adres URL
  - Spřažení na základě souborů cookie.
  - Ukončení protokolu SSL
  - Kompletní SSL
  - Podpora veřejných, privátních a hybridních webů
  - Integrovaný Firewall webových aplikací

AGIC je schopen zpracovat více oborů názvů a má ProhibitedTargets, což znamená, že AGIC může nakonfigurovat Application Gateway specificky pro clustery AKS, aniž by to ovlivnilo jiné stávající back-endy. 

## <a name="next-steps"></a>Další kroky

- [**Nasazení bezserverová**](ingress-controller-install-new.md): pokyny k instalaci AGIC, AKS a Application Gateway v infrastruktuře s prázdnou a neslat.
- [**Nasazení brownfield**](ingress-controller-install-existing.md): Nainstalujte AGIC na existující AKS a Application Gateway.

