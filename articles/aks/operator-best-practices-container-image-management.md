---
title: Doporučené postupy pro obsluhu – Správa imagí kontejnerů ve službě Azure Kubernetes Services (AKS)
description: Naučte se osvědčené postupy pro správu a zabezpečení kontejnerů ve službě Azure Kubernetes Service (AKS) od operátora clusteru.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105115"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro správu a zabezpečení imagí kontejneru ve službě Azure Kubernetes (AKS)

Zabezpečení kontejneru a kontejneru je zásadní prioritou při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS). Kontejnery se zastaralými základními bitovými kopiemi nebo neopravenými běhovými moduly představují bezpečnostní riziko a možný vektor útoku. 

Integrace a spouštění nástrojů pro kontrolu a nápravu v kontejnerech při sestavení a běhu minimalizuje rizika. Dříve jste zachytili chybu zabezpečení nebo zastaralý základní obrázek, tím lépe Zabezpečte svůj cluster. 

V tomto článku *"kontejnery"* znamenají:
* Image kontejneru uložené v registru kontejnerů.
* Spuštěné kontejnery.

Tento článek se zaměřuje na zabezpečení vašich kontejnerů v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vyhledejte a opravte slabá místa v obrazech.
> * Automaticky spouštět a znovu nasazovat image kontejneru při aktualizaci základní image.

Můžete si také přečíst osvědčené postupy pro [zabezpečení clusteru][best-practices-cluster-security] a [zabezpečení pod][best-practices-pod-security].

V Security Center můžete také použít [zabezpečení kontejnerů][security-center-containers] , které vám pomůžou zkontrolovat ohrožení zabezpečení v kontejnerech. [Azure Container Registry integrace][security-center-acr] s Security Center pomáhá chránit vaše image a Registry před chybami zabezpečení.

## <a name="secure-the-images-and-run-time"></a>Zabezpečení obrázků a doby běhu

> **Osvědčené postupy** 
>
> Vyhledá v imagí v kontejneru chyby zabezpečení. Nasaďte jenom ověřené image. Pravidelně aktualizujte základní image a modul runtime aplikace. Znovu nasaďte úlohy do clusteru AKS.

Při přijímání kontejnerů na bázi kontejneru budete chtít ověřit zabezpečení imagí a za běhu používaného k vytváření vlastních aplikací. Jak se vyhnout zavlečení chyb zabezpečení do nasazení? 
* Do pracovního postupu nasazení zahrňte proces pro skenování imagí kontejnerů pomocí nástrojů, jako je [TwistLock][twistlock] nebo [azurová][aqua].
* Povoluje nasazení pouze ověřených imagí.

![Kontrola a náprava imagí kontejnerů, ověřování a nasazení](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Například můžete použít kanál průběžné integrace a průběžného nasazování (CI/CD) k automatizaci kontrol, ověřování a nasazení imagí. Azure Container Registry zahrnuje tyto možnosti kontroly ohrožení zabezpečení.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automaticky vytvářet nové image na základě aktualizace základního obrázku

> **Osvědčené postupy** 
>
> Při použití základních imagí pro Image aplikací použijte automatizaci k vytváření nových imagí při aktualizaci základní image. Vzhledem k tomu, že aktualizované základní image obvykle zahrnují opravy zabezpečení, aktualizujte všechny image kontejneru aplikací pro příjem dat.

Pokaždé, když se aktualizuje základní image, měli byste také aktualizovat všechny podřízené kontejnery. Tento proces sestavení Integrujte do kanálů ověřování a nasazení, jako je například [Azure Pipelines][azure-pipelines] nebo Jenkinse. Tyto kanály zajistí, že se vaše aplikace i nadále spouštějí na aktualizovaných bitových kopiích. Po ověření imagí kontejneru aplikace je možné AKS nasazení aktualizovat tak, aby běžely nejnovější a zabezpečené image.

Azure Container Registry úlohy mohou také automaticky aktualizovat image kontejneru při aktualizaci základní image. Pomocí této funkce sestavíte několik základních imagí a aktualizujete je, aby se vyřešily chyby a opravy zabezpečení.

Další informace o aktualizacích základních imagí najdete v tématu [Automatizace sestavení imagí při aktualizaci základní image s Azure Container Registry úkoly][acr-base-image-update].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na zabezpečení kontejnerů. Chcete-li implementovat některé z těchto oblastí, přečtěte si následující články:

* [Automatizace sestavení imagí na základě aktualizace základního obrázku pomocí úloh Azure Container Registry][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md