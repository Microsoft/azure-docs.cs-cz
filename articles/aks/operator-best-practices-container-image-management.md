---
title: Operátor osvědčené postupy – Správa imagí kontejnerů ve službě Azure Kubernetes služby (AKS)
description: Podívejte se operátor clusteru osvědčené postupy pro postupy při správě a zabezpečení imagí kontejnerů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: ea39bceaa6b58e84def9635436d902002e33cd14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514519"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro správu image kontejnerů a zabezpečení ve službě Azure Kubernetes Service (AKS)

Vývoj a spouštění aplikací ve službě Azure Kubernetes Service (AKS), je zabezpečení kontejnerů a imagí kontejneru je důležitým aspektem. Kontejnery, které zahrnují aktuální základní Image nebo modulů runtime bez opravy zabezpečení aplikace představovat bezpečnostní riziko a možný útok. Chcete-li minimalizovat těchto rizik, by měla integrace nástroje, které vyhledat a opravit problémy v kontejnerech v okamžiku sestavení, jakož i prostředí runtime. Dříve v procesu, který zachycuje ohrožení zabezpečení nebo aktuální základní image bezpečnější clusteru. V tomto článku *kontejnery* znamená, že oba Image kontejneru uložena v registru kontejnerů a spuštěné kontejnery.

Tento článek se zaměřuje na tom, jak zabezpečit své kontejnery ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vyhledání a oprava ohrožení zabezpečení image
> * Automaticky aktivovat a opětovné nasazení imagí kontejnerů, když dojde k aktualizaci základní image

Můžete si také přečíst osvědčené postupy pro [clusteru zabezpečení] [ best-practices-cluster-security] a [pod zabezpečení][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Zabezpečení Image a čas spuštění

**Osvědčené postupy pro moduly** – kontrola imagí kontejnerů pro ohrožení zabezpečení a pouze nasazení bitové kopie, které jste předali ověření. Pravidelné aktualizace základní Image a doba spuštění aplikace a potom zopakujte nasazení úloh v clusteru AKS.

Jeden problém s přijetím založených na kontejnerech úlohy je ověření zabezpečení imagí a modul runtime používá k vytváření vlastních aplikací. Jak jste se ujistit, že nezpůsobíte slabá místa zabezpečení do vašeho nasazení? Váš pracovní postup nasazení by měla obsahovat procesu kontrolovat Image kontejnerů pomocí nástrojů, jako [Twistlock] [ twistlock] nebo [Aqua][aqua]a pak Povolte jenom ověřené Image k nasazení.

![Kontrola a napravit imagí kontejnerů, ověření a nasazení](media/operator-best-practices-container-security/scan-container-images-simplified.png)

V příkladu skutečných můžete použít průběžné integrace a průběžného nasazování (CI/CD) kanálu k automatizaci image kontrol, ověření a nasazení. Služba Azure Container Registry zahrnuje tyto nedostatky zabezpečení možností skenování.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automaticky vytvářet nové bitové kopie na aktualizací základních imagí

**Osvědčené postupy pro moduly** – použijte základní Image pro obrázky aplikace automatizace použít k vytvoření nových imagí při aktualizaci základní image. Jak obvykle tyto základní Image obsahují opravy zabezpečení, aktualizujte všechny aplikace pro příjem dat imagí kontejnerů.

Pokaždé, když se aktualizuje základní image, Image všechny podřízené kontejneru musí také aktualizovat. Tento proces sestavení by měly být zahrnuty do ověření a kanály nasazení, jako [kanály Azure] [ azure-pipelines] nebo Jenkinse. Tyto kanály zajišťuje, že vaše aplikace dál běžet aktualizovaný na základě bitové kopie. Po ověření imagí kontejnerů aplikací AKS nasazení je pak aktualizovat pro spuštění imagí nejnovější, zabezpečené.

Úkoly v Azure Container Registry lze aktualizovat také automaticky imagí kontejnerů, když dojde k aktualizaci základní image. Tato funkce umožňuje malý počet základní Image, ale taky popustit Novoroční pravidelně aktualizuje opravami chyb a zabezpečení.

Další informace o aktualizacích základní image, najdete v části [automatizace image založena na aktualizací základních imagí s Azure Container Registry úkoly][acr-base-image-update].

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na tom, jak zabezpečit vaše kontejnery. K provedení některých z těchto oblastí, naleznete v následujících článcích:

* [Automatizace sestavování imagí na aktualizací základních imagí s úlohami registru kontejneru Azure][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
