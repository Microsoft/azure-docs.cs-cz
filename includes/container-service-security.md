---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: cc72fb39bf62915be10493a780538121f26ca835
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002267"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(NEPOUŽÍVANÉ) Zabezpečení kontejnerů Dockeru v Azure Container Service

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

Tento článek uvádí důležité informace a doporučení pro zabezpečení kontejnerů Dockeru nasazených ve službě Azure Container Service. Řada těchto důležitých informací platí obecně pro kontejnery Dockeru nasazené v Azure nebo jiných prostředích. 

## <a name="image-security"></a>Zabezpečení image

Kontejnery se vytváří z imagí uložených v jednom nebo několika úložištích. Tato úložiště můžou patřit do veřejných nebo privátních registrů kontejnerů. Příkladem veřejného registru je [Docker Hub](https://hub.docker.com/). Příkladem privátního registru je [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), který je možné nainstalovat místně nebo ve virtuálním privátním cloudu. Existují také cloudové služby privátních registrů kontejnerů, mezi které patří služba [Azure Container Registry](../articles/container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Veřejné a privátní image
Stejně jako u jakéhokoli veřejně publikovaného softwarového balíčku obecně platí, že veřejně dostupné image kontejneru nezaručují zabezpečení. Image kontejnerů se skládají z několika vrstev softwaru a každá z nich může obsahovat ohrožení zabezpečení. Klíčem je seznámit se s původem image kontejneru, včetně vlastníka image (pro určení, jestli jde o spolehlivý zdroj, nebo ne), vrstev softwaru, ze kterých se skládá, a verzí softwaru. 

Pokud například přejdete do oficiálního [úložiště nginx](https://hub.docker.com/_/nginx/) v Docker Hubu a na kartu **Tags** (Značky), uvidíte barevně rozlišená ohrožení zabezpečení každé image. Každá barva znázorňuje ohrožení zabezpečení vrstvy softwaru image. Další informace o zjišťování ohrožení zabezpečení v Docker Hubu najdete v článku [Understanding official repos on Docker Hub](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/) (Principy oficiálních úložišť v Docker Hubu).

![Image nginx v Docker Hubu](./media/container-service-security/docker-hub-nginx.png)

Podnikům velmi záleží na zabezpečení a za účelem ochrany před útoky na zabezpečení by měly ukládat image v privátním registru, jako je Azure Container Registry nebo Docker Trusted Registry, a načítat je z něj. Azure Container Registry kromě poskytování spravovaného privátního registru také podporuje [ověřování na základě instančního objektu](../articles/container-registry/container-registry-authentication.md) prostřednictvím Azure Active Directory pro toky základního ověřování, včetně přístupu na základě role pro oprávnění jen pro čtení, zápis a vlastníka.

### <a name="image-security-scanning"></a>Kontrola zabezpečení imagí

I v případě, že používáte privátní registr, je vhodné použít řešení kontroly imagí pro zajištění dalšího ověřování zabezpečení. Každá vrstva softwaru v imagi kontejneru je potenciálně náchylná k ohrožením zabezpečení nezávisle na ostatních vrstvách v imagi kontejneru. S tím, jak společnosti začínají nasazovat produkční úlohy založené na technologiích kontejnerů, se stává důležitou kontrola imagí k zajištění prevence bezpečnostních hrozeb pro jejich organizace. 

Pomocí řešení monitorování a kontroly zabezpečení, jako jsou mimo jiné [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) a [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry), je možné kontrolovat image kontejnerů v privátním registru a identifikovat potenciální ohrožení zabezpečení. Je důležité pochopit, jakou hloubku kontroly různá řešení poskytují. Některá řešení například můžou u vrstev image křížově ověřovat jenom výskyt známých ohrožení zabezpečení. Tato řešení nemusí být schopná ověřit na vrstvě image software vytvořený prostřednictvím určitého softwaru správce balíčků. Jiná řešení mají hlubší integraci kontroly a můžou najít ohrožení zabezpečení v jakémkoli zabaleném softwaru.

### <a name="production-deployment-rules-and-audit"></a>Pravidla a audit produkčního nasazení
Jakmile je aplikace nasazená v produkčním prostředí, je velmi důležité nastavit nakolik pravidel, která zajistí, že image používané v produkčních prostředích jsou zabezpečené a neobsahují žádná ohrožení zabezpečení.

* Image obsahující ohrožení zabezpečení, byť méně důležitá, by se zpravidla neměly spouštět v produkčním prostředí. Kromě toho by v ideálním případě měly být všechny image nasazené v produkčním prostředí uložené v privátním registru s omezeným přístupem. Je také důležité udržovat nízký počet produkčních imagí, aby bylo možné je efektivně spravovat.

* Protože je obtížné určit původ softwaru z veřejně dostupných imagí kontejnerů, doporučuje se sestavovat image ze zdroje pro zajištění znalosti původu vrstvy. Když se objeví ohrožení zabezpečení v imagi kontejneru, kterou jste si sami sestavili, můžete rychleji najít řešení. U veřejné image byste museli najít kořen veřejné image a opravit ho nebo získat zabezpečenou image od vydavatele.

* Ani důkladně zkontrolovaná image nasazená v produkčním prostředí nezaručuje aktuálnost po celou dobu životnosti aplikace. Může docházet k hlášení ohrožení zabezpečení pro vrstvy image, která dříve nebyla známa nebo se objevila až po nasazení do produkčního prostředí. Je nutné pravidelně provádět audit imagí nasazených v produkčním prostředí, aby se identifikovaly image, které jsou zastaralé nebo nějakou dobu nebyly aktualizované. K aktualizaci imagí kontejnerů bez výpadků je možné použít metodologie modrozeleného nasazování a mechanismy průběžné aktualizace. Kontrolu imagí je možné provádět pomocí nástrojů popsaných v předchozí části. 

* Kanál průběžné integrace (CI) pro sestavování imagí a integrovanou kontrolu zabezpečení může pomoci udržovat zabezpečené privátní registry se zabezpečenými imagemi kontejnerů. Zjišťování ohrožení zabezpečení integrované v řešení průběžné integrace zajišťuje, že se image, které projdou všemi testy, nasdílí do privátního registru, ze kterého se nasazují produkční úlohy. Selhání kanálu průběžné integrace zajistí, že se ohrožené image nenasdílí do privátního registru používaného k nasazování produkčních úloh. V případě velkého počtu imagí kanál také automatizuje kontrolu jejich zabezpečení. Jinak může být ruční auditování imagí za účelem zjišťování ohrožení zabezpečení zdlouhavé a náchylné k chybám.

## <a name="host-level-container-isolation"></a>Izolace kontejneru na úrovni hostitele
Když zákazník nasazuje aplikace typu kontejner do prostředků Azure, nasazují se na úrovni předplatného ve skupinách prostředků a nejsou určené pro více tenantů. To znamená, že pokud zákazník sdílí předplatné s ostatními, mezi dvěma nasazeními ve stejném předplatném není možné vytvořit žádnou hranici. Z tohoto důvodu není zaručeno zabezpečení na úrovni kontejneru. 

Také je důležité pochopit, že kontejnery sdílejí jádro a prostředky hostitele (kterým je ve službě Azure Container Service virtuální počítač Azure v clusteru). Proto je nutné kontejnery v produkčním prostředí spouštět v uživatelském režimu bez oprávnění správce. Spouštění kontejneru s kořenovými oprávněními může ohrozit celé prostředí. S přístupem ke kontejneru na kořenové úrovni může hacker získat na hostiteli přístup k úplným oprávněním správce. Kromě toho je důležité spouštět kontejnery se systémy souborů jen pro čtení. Tím se komukoli s přístupem k ohroženému kontejneru zabrání ve psaní škodlivých skriptů pro systém souborů a v získaní přístupu k dalším souborům. Podobně je důležité omezit prostředky (jako je paměť, procesor a šířka pásma sítě) přidělené kontejneru. Tím pomůžete zabránit hackerům v zabírání prostředků a provádění nelegálních aktivit, jako jsou podvody s platebními kartami nebo těžba Bitcoinů, které by mohly bránit spouštění ostatních kontejnerů na hostiteli nebo v clusteru.

## <a name="orchestrator-considerations"></a>Důležité informace o orchestrátorech

Pro každý orchestrátor dostupný ve službě Azure Container Service platí specifické aspekty zabezpečení. Měli byste například omezit přímý přístup přes SSH k uzlům orchestrátorů ve službě Container Service. Místo toho byste ke správě kontejnerového prostředí bez přístupu k hostitelům měli používat uživatelská rozhraní jednotlivých orchestrátorů nebo nástroje pro příkazový řádek (jako například `kubectl` pro Kubernetes). Další informace najdete v tématu [Vzdálené připojení ke clusteru Kubernetes, DC/OS nebo Docker Swarm](../articles/container-service/kubernetes/container-service-connect.md).

Další informace o zabezpečení specifické pro orchestrátor najdete v následujících zdrojích:

* **Kubernetes:**[Security Best Practices for Kubernetes Deployment](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/) (Osvědčené postupy zabezpečení pro nasazení Kubernetes)

* **DC/OS:**[Securing Your Cluster](https://dcos.io/docs/1.8/administration/securing-your-cluster/) (Zabezpečení clusteru)

* **Docker Swarm:**[Docker Security](https://www.docker.com/docker-security) (Zabezpečení Dockeru)

## <a name="next-steps"></a>Další postup

* Další informace o architektuře Dockeru a zabezpečení kontejnerů najdete v článku [Introduction to Container Security](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) (Úvod do zabezpečení kontejnerů).

* Informace o zabezpečení platformy Azure najdete v [Azure Security Center](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure).
