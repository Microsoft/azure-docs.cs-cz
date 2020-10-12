---
title: Doporučené postupy pro obsluhu – Správa imagí kontejnerů ve službě Azure Kubernetes Services (AKS)
description: Naučte se osvědčené postupy pro správu a zabezpečení kontejnerů ve službě Azure Kubernetes Service (AKS) od operátora clusteru.
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 018dc1802d4f0b4b498d3993b8753990598a16ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86251140"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro správu a zabezpečení imagí kontejneru ve službě Azure Kubernetes (AKS)

Při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS) je důležité zvážit zabezpečení kontejnerů a imagí kontejnerů. Kontejnery, které obsahují zastaralé základní Image nebo neopravené moduly runtime, představují bezpečnostní riziko a možný vektor útoku. Chcete-li minimalizovat tato rizika, měli byste integrovat nástroje, které hledají a řeší problémy ve vašich kontejnerech v době sestavení a také za běhu. V dřívější verzi procesu se zachytí chyba zabezpečení nebo neaktuální bitová kopie, což je bezpečnější cluster. V tomto článku *kontejnery* znamenají image kontejneru uložené v registru kontejnerů a spuštěné kontejnery.

Tento článek se zaměřuje na zabezpečení vašich kontejnerů v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vyhledat a napravit chyby v obrazech
> * Automaticky spouštět a znovu nasazovat image kontejneru při aktualizaci základní image

Můžete si také přečíst osvědčené postupy pro [zabezpečení clusteru][best-practices-cluster-security] a [zabezpečení pod][best-practices-pod-security].

V Security Center můžete také použít [zabezpečení kontejnerů][security-center-containers] , které vám pomůžou zkontrolovat ohrožení zabezpečení v kontejnerech.  K ochraně imagí a registru před chybami zabezpečení se taky [Azure Container Registry integrace][security-center-acr] s Security Center.

## <a name="secure-the-images-and-run-time"></a>Zabezpečení obrázků a doby běhu

**Doprovodné materiály k osvědčeným postupům** – naskenujte image kontejneru pro ohrožení zabezpečení a nasaďte jenom image, které prošlé ověřením. Pravidelně aktualizujte základní image a modul runtime aplikace a pak znovu nasaďte úlohy do clusteru AKS.

Jednou z obav při přijetí úloh založených na kontejnerech je ověřování zabezpečení imagí a běhu používaného k vytváření vlastních aplikací. Jak zajistíte, že do nasazení nezavádíte slabá místa zabezpečení? Pracovní postup nasazení by měl zahrnovat proces skenování imagí kontejnerů pomocí nástrojů, jako je [TwistLock][twistlock] nebo [azurová][aqua], a pak jenom nasazovat ověřené image.

![Kontrola a náprava imagí kontejnerů, ověřování a nasazení](media/operator-best-practices-container-security/scan-container-images-simplified.png)

V reálném příkladu můžete použít kanál průběžné integrace a průběžného nasazování (CI/CD) k automatizaci kontrol, ověřování a nasazení imagí. Azure Container Registry zahrnuje tyto možnosti kontroly ohrožení zabezpečení.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automaticky vytvářet nové image na základě aktualizace základního obrázku

**Doprovodné materiály k osvědčeným postupům** – při použití základních imagí pro Image aplikací používejte automatizaci k vytváření nových imagí při aktualizaci základní image. Jelikož základní image obvykle zahrnují opravy zabezpečení, aktualizujte všechny image kontejneru aplikací pro příjem dat.

Pokaždé, když se aktualizuje základní image, by se měly aktualizovat taky všechny image kontejneru pro příjem dat. Tento proces sestavení by měl být integrovaný do kanálů ověřování a nasazení, jako je [Azure Pipelines][azure-pipelines] nebo Jenkinse. Tyto kanály zajistí, že se vaše aplikace i nadále spouštějí na aktualizovaných bitových kopiích. Po ověření imagí kontejneru aplikace je možné AKS nasazení aktualizovat tak, aby běžely nejnovější a zabezpečené image.

Azure Container Registry úlohy mohou také automaticky aktualizovat image kontejneru při aktualizaci základní image. Tato funkce umožňuje vytvořit malý počet základních imagí a pravidelně je uchovávat s opravami chyb a zabezpečení.

Další informace o aktualizacích základních imagí najdete v tématu [Automatizace sestavení imagí při aktualizaci základní image s Azure Container Registry úkoly][acr-base-image-update].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na zabezpečení kontejnerů. Chcete-li implementovat některé z těchto oblastí, přečtěte si následující články:

* [Automatizace sestavení imagí na základě aktualizace základního obrázku pomocí úloh Azure Container Registry][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/azure-container-registry-integration.md
