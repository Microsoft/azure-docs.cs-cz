---
title: Osvědčené postupy pro operátory – správa image kontejnerů ve službách Azure Kubernetes Services (AKS)
description: Seznamte se s doporučenými postupy pro operátorclusteru, jak spravovat a zabezpečit iimage kontejnerů ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594736"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro správu a zabezpečení image kontejnerů ve službě Azure Kubernetes Service (AKS)

Při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS) je zabezpečení kontejnerů a ibi kontejnerů klíčovým aspektem. Kontejnery, které obsahují zastaralé základní bitové kopie nebo neopravené běhové časy aplikací, představují bezpečnostní riziko a možný vektor útoku. Chcete-li tato rizika minimalizovat, měli byste integrovat nástroje, které vyhlednou a nachvíli najdou problémy v kontejnerech a budou je obnovovat. Čím dříve je chyba zabezpečení nebo zastaralá základní bitová kopie zachycena, tím bezpečnější je cluster. V tomto článku *kontejnery* znamená i image kontejneru uložené v registru kontejneru a spuštěné kontejnery.

Tento článek se zaměřuje na zabezpečení kontejnerů v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vyhledání a náprava chyb zabezpečení bitových zjištění
> * Automatické aktivace a opětovné nasazení bitových kopií kontejnerů při aktualizaci základní bitové kopie

Můžete si také přečíst osvědčené postupy pro [zabezpečení clusteru][best-practices-cluster-security] a [pro zabezpečení podu][best-practices-pod-security].

Můžete také použít [zabezpečení kontejneru v Centru zabezpečení,][security-center-containers] které vám pomůže prohledat chyby zabezpečení kontejnerů.  K dispozici je také [integrace registru kontejnerů Azure][security-center-acr] s Security Center pomoci chránit vaše image a registru před chybami zabezpečení.

## <a name="secure-the-images-and-run-time"></a>Zabezpečte obrázky a dobu běhu

**Pokyny pro osvědčené postupy** – prohledái tei chyby zabezpečení a nasadíte pouze bitové kopie, které prošly ověřením. Pravidelně aktualizujte základní bitové kopie a zaběhu aplikací a pak znovu nasazujte úlohy v clusteru AKS.

Jedním z obav při přijímání úloh založených na kontejnerech je ověření zabezpečení bitových kopií a běhu za běhu používaného k vytváření vlastních aplikací. Jak zajistíte, že do nasazení nezavedete slabá místa zabezpečení? Pracovní postup nasazení by měl zahrnovat proces skenování iobrazek kontejnerů pomocí nástrojů, jako je [Twistlock][twistlock] nebo [Aqua][aqua], a pak povolit pouze ověřené bitové kopie, které mají být nasazeny.

![Skenování a náprava iobrazů kontejnerů, ověřování a nasazování](media/operator-best-practices-container-security/scan-container-images-simplified.png)

V reálném příkladu můžete použít kanál průběžné integrace a průběžného nasazení (CI/CD) k automatizaci prohledávačů, ověřování a nasazení image. Azure Container Registry obsahuje tyto možnosti hledání chyb zabezpečení.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatické vytváření nových bitových kopií při aktualizaci základního obrázku

**Osvědčené postupy pokyny** – při použití základní image pro bitové kopie aplikace, použijte automatizaci k vytvoření nové image při aktualizaci základní bitové kopie. Vzhledem k tomu, že tyto základní bitové kopie obvykle obsahují opravy zabezpečení, aktualizujte všechny image kontejneru aplikace.

Při každé aktualizaci základní bitové kopie by měly být aktualizovány také všechny image kontejneru příjem dat. Tento proces sestavení by měl být integrován do kanálů ověřování a nasazování, jako jsou [kanály Azure nebo][azure-pipelines] Jenkins. Tyto kanály zajišťuje, že vaše aplikace nadále běží na aktualizované na základě bitových kopií. Po ověření image kontejneru aplikace lze nasazení AKS aktualizovat tak, aby spouštěla nejnovější zabezpečené bitové kopie.

Úlohy registru kontejneru Azure můžete také automaticky aktualizovat image kontejneru při aktualizaci základní image. Tato funkce umožňuje vytvářet malý počet základních bitových kopií a pravidelně je aktualizovat opravami chyb a zabezpečení.

Další informace o aktualizacích základních bitových bitových bitových zjištění naleznete v [tématu Automatizace sestavení bitových bitových obrazů na základě aktualizace základní bitové kopie pomocí úloh registru kontejnerů Azure][acr-base-image-update].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na zabezpečení kontejnerů. Chcete-li implementovat některé z těchto oblastí, naleznete v následujících článcích:

* [Automatizace sestavení bitové kopie na základě aktualizace základní bitové kopie pomocí úloh registru kontejnerů Azure][acr-base-image-update]

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