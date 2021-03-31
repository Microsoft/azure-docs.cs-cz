---
title: Co je kontroler služby Azure Application Gateway pro příchozí přenosy?
description: Tento článek poskytuje Úvod k tomu, co je Application Gateway řadič příchozího přenosu dat.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714404"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Co je Application Gateway kontroler příchozího přenosu dat?
AGIC) je Kubernetes aplikace, díky které můžou zákazníci [služby Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) využít Azure azure Native [Application Gateway](https://azure.microsoft.com/services/application-gateway/) (pro vyrovnávání zatížení L7) a zveřejnit tak cloudový software na Internet. Application Gateway AGIC monitoruje cluster Kubernetes, na kterém je hostovaný, a nepřetržitě aktualizuje Application Gateway, aby se vybrané služby zobrazovaly na internetu.

Kontroler příchozího provozu je spuštěný pod AKS zákazníka. AGIC monitoruje podmnožinu prostředků Kubernetes pro změny. Stav clusteru AKS se převede na Application Gateway specifickou konfiguraci a použije se na [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Výhody Application Gatewayho řadiče pro příchozí přenos dat
AGIC pomáhá eliminovat nutnost mít další službu Vyrovnávání zatížení/veřejnou IP adresu před clusterem AKS a před požadavky na cluster AKS zabrání vícenásobnému směrování v DataPath. Application Gateway mluví s lusky pomocí jejich privátní IP adresy přímo a nevyžaduje služby NodePort nebo KubeProxy. Tím se také zvýší výkon pro vaše nasazení.

Kontroler příchozího přenosu dat podporuje výhradně Standard_v2 a WAF_v2 SKU, což také přináší výhody automatického škálování. Application Gateway může reagovat při reakci na zvýšení nebo snížení zatížení provozu a odpovídajícím způsobem škálovat, aniž by se musely spotřebovávat žádné prostředky z clusteru AKS.

Použití Application Gateway kromě AGIC také pomáhá chránit cluster AKS tím, že poskytuje zásady TLS a funkce firewallu webových aplikací (WAF).

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC se konfiguruje prostřednictvím [prostředku](https://kubernetes.io/docs/user-guide/ingress/)Kubernetes příchozího přenosu společně se službami a nasazeními/lusky. Poskytuje řadu funkcí, které využívají nativní službu pro vyrovnávání zatížení Azure Application Gateway. Několik názvů:
  - Směrování adres URL
  - Spřažení na základě souborů cookie.
  - Ukončení protokolu TLS
  - Komplexní TLS
  - Podpora veřejných, privátních a hybridních webů
  - Integrovaná brána firewall webových aplikací

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Rozdíl mezi nasazením Helm a AKS Add-On
Existují dva způsoby, jak nasadit AGIC pro cluster AKS. První způsob je prostřednictvím Helm; druhý je prostřednictvím AKS jako doplněk. Hlavní výhodou nasazení AGIC jako doplňku AKS je to, že je mnohem jednodušší než nasazení prostřednictvím Helm. Pro nové nastavení můžete nasadit novou Application Gateway a nový cluster AKS s povoleným AGIC jako doplněk na jednom řádku v Azure CLI. Doplněk je také plně spravovaná služba, která poskytuje přidané výhody, jako jsou automatické aktualizace a zvýšená podpora. Obě možnosti nasazení AGIC (doplněk Helm a AKS) jsou plně podporovány společností Microsoft. Doplněk navíc umožňuje lepší integraci s AKS jako doplněk první třídy.

Doplněk AGIC je stále nasazený jako pod v clusteru AKS zákazníka, ale existuje několik rozdílů mezi verzí nasazení Helm a doplňkovou verzí AGIC. Níže je uveden seznam rozdílů mezi těmito dvěma verzemi: 
  - Hodnoty nasazení Helm nelze upravovat v doplňku AKS:
    - `verbosityLevel` ve výchozím nastavení se nastaví na 5.
    - `usePrivateIp`bude ve výchozím nastavení nastavené na false. To může být přepsáno pomocí [Anotace use-Private-IP](ingress-controller-annotations.md#use-private-ip) .
    - `shared` není podporován na doplňku. 
    - `reconcilePeriodSeconds` není podporován na doplňku.
    - `armAuth.type` není podporován na doplňku.
  - AGIC nasazené prostřednictvím Helm podporuje ProhibitedTargets, což znamená, že AGIC může nakonfigurovat Application Gateway specificky pro AKS clustery, aniž by to ovlivnilo jiné stávající back-endy. Doplněk AGIC ho v tuto chvíli nepodporuje. 
  - Vzhledem k tomu, že doplněk AGIC je spravovaná služba, zákazníci se automaticky aktualizují na nejnovější verzi doplňku AGIC, na rozdíl od AGIC nasazené prostřednictvím Helm, kde zákazník musí ručně aktualizovat AGIC. 

> [!NOTE]
> Zákazníci můžou nasadit jenom jeden AGIC doplněk na jeden cluster AKS a každý AGIC doplněk teď může cílit jenom na jeden Application Gateway. Pro nasazení, která vyžadují více než jeden AGIC na cluster nebo více AGICs cílících na jeden Application Gateway, pokračujte v používání AGIC nasazeného prostřednictvím Helm. 

## <a name="next-steps"></a>Další kroky
- [**Nasazení AKS Add-On bezserverová**](tutorial-ingress-controller-add-on-new.md): pokyny k instalaci doplňku AGIC, AKS a Application Gateway v infrastruktuře prázdná-SLAT.
- [**Nasazení AKS Add-On brownfield**](tutorial-ingress-controller-add-on-existing.md): nainstalujte doplněk AGIC v clusteru AKS s existujícím Application Gateway.
- [**Nasazení Helm bezserverová**](ingress-controller-install-new.md): Nainstalujte AGIC prostřednictvím Helm, nového AKS clusteru a nové Application Gateway v infrastruktuře s prázdným-.
- [**Nasazení Helm brownfield**](ingress-controller-install-existing.md): nasaďte AGIC prostřednictvím Helm na stávajícím clusteru AKS a Application Gateway.