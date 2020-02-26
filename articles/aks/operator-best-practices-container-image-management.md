---
title: Operátor osvědčené postupy – Správa imagí kontejnerů ve službě Azure Kubernetes služby (AKS)
description: Podívejte se operátor clusteru osvědčené postupy pro postupy při správě a zabezpečení imagí kontejnerů ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594736"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro správu image kontejnerů a zabezpečení ve službě Azure Kubernetes Service (AKS)

Vývoj a spouštění aplikací ve službě Azure Kubernetes Service (AKS), je zabezpečení kontejnerů a imagí kontejneru je důležitým aspektem. Kontejnery, které zahrnují aktuální základní Image nebo modulů runtime bez opravy zabezpečení aplikace představovat bezpečnostní riziko a možný útok. Chcete-li minimalizovat těchto rizik, by měla integrace nástroje, které vyhledat a opravit problémy v kontejnerech v okamžiku sestavení, jakož i prostředí runtime. Dříve v procesu, který zachycuje ohrožení zabezpečení nebo aktuální základní image bezpečnější clusteru. V tomto článku *kontejnery* znamenají image kontejneru uložené v registru kontejnerů a spuštěné kontejnery.

Tento článek se zaměřuje na tom, jak zabezpečit své kontejnery ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vyhledání a oprava ohrožení zabezpečení image
> * Automaticky aktivovat a opětovné nasazení imagí kontejnerů, když dojde k aktualizaci základní image

Můžete si také přečíst osvědčené postupy pro [zabezpečení clusteru][best-practices-cluster-security] a [zabezpečení pod][best-practices-pod-security].

V Security Center můžete také použít [zabezpečení kontejnerů][security-center-containers] , které vám pomůžou zkontrolovat ohrožení zabezpečení v kontejnerech.  K ochraně imagí a registru před chybami zabezpečení se taky [Azure Container Registry integrace][security-center-acr] s Security Center.

## <a name="secure-the-images-and-run-time"></a>Zabezpečení Image a čas spuštění

**Doprovodné materiály k osvědčeným postupům** – naskenujte image kontejneru pro ohrožení zabezpečení a nasaďte jenom image, které prošlé ověřením. Pravidelné aktualizace základní Image a doba spuštění aplikace a potom zopakujte nasazení úloh v clusteru AKS.

Jeden problém s přijetím založených na kontejnerech úlohy je ověření zabezpečení imagí a modul runtime používá k vytváření vlastních aplikací. Jak jste se ujistit, že nezpůsobíte slabá místa zabezpečení do vašeho nasazení? Pracovní postup nasazení by měl zahrnovat proces skenování imagí kontejnerů pomocí nástrojů, jako je [TwistLock][twistlock] nebo [azurová][aqua], a pak jenom nasazovat ověřené image.

![Kontrola a napravit imagí kontejnerů, ověření a nasazení](media/operator-best-practices-container-security/scan-container-images-simplified.png)

V příkladu skutečných můžete použít průběžné integrace a průběžného nasazování (CI/CD) kanálu k automatizaci image kontrol, ověření a nasazení. Služba Azure Container Registry zahrnuje tyto nedostatky zabezpečení možností skenování.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automaticky vytvářet nové bitové kopie na aktualizací základních imagí

**Doprovodné materiály k osvědčeným postupům** – při použití základních imagí pro Image aplikací používejte automatizaci k vytváření nových imagí při aktualizaci základní image. Jak obvykle tyto základní Image obsahují opravy zabezpečení, aktualizujte všechny aplikace pro příjem dat imagí kontejnerů.

Pokaždé, když se aktualizuje základní image, Image všechny podřízené kontejneru musí také aktualizovat. Tento proces sestavení by měl být integrovaný do kanálů ověřování a nasazení, jako je [Azure Pipelines][azure-pipelines] nebo Jenkinse. Tyto kanály zajišťuje, že vaše aplikace dál běžet aktualizovaný na základě bitové kopie. Po ověření imagí kontejnerů aplikací AKS nasazení je pak aktualizovat pro spuštění imagí nejnovější, zabezpečené.

Úkoly v Azure Container Registry lze aktualizovat také automaticky imagí kontejnerů, když dojde k aktualizaci základní image. Tato funkce umožňuje malý počet základní Image, ale taky popustit Novoroční pravidelně aktualizuje opravami chyb a zabezpečení.

Další informace o aktualizacích základních imagí najdete v tématu [Automatizace sestavení imagí při aktualizaci základní image s Azure Container Registry úkoly][acr-base-image-update].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na tom, jak zabezpečit vaše kontejnery. K provedení některých z těchto oblastí, naleznete v následujících článcích:

* [Automatizace sestavení imagí na základě aktualizace základního obrázku pomocí úloh Azure Container Registry][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration