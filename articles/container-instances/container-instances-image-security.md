---
title: Důležité informace o zabezpečení Azure Container Instances
description: Doporučení k zabezpečení imagí a tajné kódy pro Azure Container Instances a obecné otázky zabezpečení pro libovolnou platformu kontejneru
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943993"
---
# <a name="security-considerations-for-azure-container-instances"></a>Informace o zabezpečení pro Azure Container Instances

Tento článek uvádí důležité informace o zabezpečení pro spuštění kontejnerových aplikací pomocí Azure Container Instances. Témata:

> [!div class="checklist"]
> * **Doporučení zabezpečení** správy imagí a tajné kódy pro Azure Container Instances
> * **Důležité informace týkající se ekosystém kontejneru** během celého životního cyklu kontejneru, pro libovolnou platformu kontejneru

## <a name="security-recommendations-for-azure-container-instances"></a>Doporučení zabezpečení pro Azure Container Instances

### <a name="use-a-private-registry"></a>Používala privátní registr

Kontejnery se vytváří z imagí uložených v jednom nebo několika úložištích. Tato úložiště můžou patřit do veřejného registru, jako je třeba [Docker Hubu](https://hub.docker.com), nebo do privátního registru. Příkladem privátního registru je [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), který je možné nainstalovat místně nebo ve virtuálním privátním cloudu. Můžete také použít cloudové služby privátních registrů kontejnerů, včetně [Azure Container Registry](../container-registry/container-registry-intro.md). 

Veřejně dostupné image kontejneru nezaručují zabezpečení. Image kontejnerů se skládají z několika vrstev softwaru a každá vrstva softwaru může obsahovat ohrožení zabezpečení. Chcete-li snížit hrozbami útoků, by měl ukládat a načíst Image z privátního registru, jako je Azure Container Registry nebo Docker Trusted Registry. Kromě poskytování spravovaného privátního registru, Azure Container Registry podporuje [ověřování na základě instanční objekt služby](../container-registry/container-registry-authentication.md) prostřednictvím Azure Active Directory pro toky základního ověřování. Toto ověření zahrnuje na základě rolí přístup jen pro čtení (pull), zápisu (push) a oprávnění vlastníka.

### <a name="monitor-and-scan-container-images"></a>Monitorovat a kontrolovat Image kontejnerů

Zabezpečení řešení monitorování a kontroly, jako [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) a [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) jsou dostupné v Tržišti Azure Marketplace. Můžete využít k kontrolovat Image kontejnerů v privátním registru a identifikovat potenciální ohrožení zabezpečení. Je důležité pochopit, jakou hloubku kontroly, aby různá řešení poskytují. 

### <a name="protect-credentials"></a>Ochrana přihlašovacích údajů

Kontejnery lze distribuovat mezi několik clusterů a oblastmi Azure. Proto musí zabezpečené přihlašovací údaje požadované pro přihlášení nebo přístup přes rozhraní API, jako jsou hesla nebo tokeny. Ujistěte se, že těchto kontejnerů přenášená i neaktivní uložená přístup pouze uživatelé s oprávněním. Inventarizovat všechny tajné klíče přihlašovacích údajů a pak je nutné vývojářům používat nově vznikající nástroje pro správu tajných kódů, které jsou určeny pro kontejnerové platformy.  Ujistěte se, že vaše řešení obsahuje šifrovaným databázím, šifrování TLS pro data tajné klíče v cestě a minimálními oprávněními [řízení přístupu na základě rolí](../role-based-access-control/overview.md). [Služba Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) je Cloudová služba, která chrání šifrovací klíče a tajné kódy (například certifikáty, připojovací řetězce a hesla) pro kontejnerizované aplikace. Protože tato data jsou citlivé a důležité obchodní informace, zabezpečený přístup k vašemu klíči trezory Recovery Services tak, že jen autorizované aplikace a uživatelé k nim přistupovat.

## <a name="considerations-for-the-container-ecosystem"></a>Důležité informace týkající se ekosystém kontejneru

Následující bezpečnostní opatření, také implementovat a spravovat efektivněji, můžete zabezpečit a chránit vaše ekosystému kontejneru. Tyto míry použít během celého životního cyklu kontejneru, od vývoje až po produkční nasazení a celou řadu platforem, hostitele a orchestrátorů kontejnerů. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Správa ohrožení zabezpečení použijte jako součást vývojového cyklu kontejneru 

Pomocí nástroje Správa efektivní ohrožení zabezpečení v průběhu životního cyklu vývoje kontejneru, zvýšit pravděpodobnost, že vám identifikovat a řešit otázky zabezpečení dřív, než narostou závažnější potíže. 

### <a name="scan-for-vulnerabilities"></a>Kontrola ohrožení zabezpečení 

Nová ohrožení zabezpečení zjištění celou dobu, aby vyhledávání a identifikovat ohrožení zabezpečení akce představuje nepřetržitý proces. Zahrnout v průběhu životního cyklu kontejneru zjišťování ohrožení zabezpečení:

* Jako konečná kontrola ve vývojovém procesu měli byste provést kontrolu ohrožení zabezpečení v kontejnerech před nahráním imagí do veřejného nebo privátního registru. 
* Pokračujte ve vyhledávání imagí kontejnerů v registru pro identifikaci všechny chyby, které nějakým způsobem vynechaly během vývoje a adresu nově zjištěných chyb zabezpečení, které mohou existovat v kód použitý v imagí kontejnerů.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Ohrožení zabezpečení obrázku mapy pro spouštění kontejnerů 

Musíte mít způsob mapování chyby zabezpečení identifikované v imagí kontejneru pro spouštění kontejnerů, takže lze zmírnit nebo vyřešit problémy se zabezpečením.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Ujistěte se, že pouze Image schválené se používají ve vašem prostředí 

Není dostatek změn a volatility v kontejneru ekosystému bez povolení Neznámý kontejnerů s ochranou. Povolit pouze schválené kontejnerové Image. Máte vytvořenou infrastrukturu monitorovat a zabránit používání imagí kontejnerů neschválených nástrojů a procesů. 

Účinný způsob omezení prostoru pro útok a brání vývojářům dělat chyby zabezpečení je řízení toku imagí kontejneru do vývojového prostředí. Například může schválit jeden Linuxová distribuce vytvořená jako základní image, nejlépe Štíhlá (Alpine nebo CoreOS spíše než Ubuntu), chcete-li minimalizovat potenciální útoky na plochu. 

Image podepisování nebo tímto způsobem může poskytnout dodavatelského řetězce, která umožňuje ověření integrity kontejnery. Například Azure Container Registry podporuje Docker [obsahu důvěryhodnosti](https://docs.docker.com/engine/security/trust/content_trust) model, který umožňuje vydavatelům bitové kopie k podepsání imagí, které jsou nahrány do registru a image spotřebitele o přijetí změn pouze podepsané bitové kopie.

### <a name="permit-only-approved-registries"></a>Povolení schválí jen tehdy, registry 

Rozšíření třeba zajistit, že vaše prostředí používá jenom Image schválené je tak, aby povolovala pouze použití schválené container Registry. Bude vyžadovat použití velkých registry kontejnerů schválené omezuje vaše vystavení riziko tím, že omezíte potenciál pro zavedení neznámé chyby nebo problémy se zabezpečením. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zajištění integrity imagí v průběhu životního cyklu 

Součást správy zabezpečení v průběhu životního cyklu kontejneru je k zajištění integrity imagí kontejneru v registru a jsou změněny nebo nasazení do produkčního prostředí. 

* Image obsahující ohrožení zabezpečení, byť méně důležitá, by neměla bude moct spouštět v produkčním prostředí. V ideálním případě by všechny Image nasazené v produkčním prostředí by měla uložit v privátním registru na výběr několik. Zachovejte počet produkčních imagí k zajištění, aby bylo možné je spravovat efektivně.

* Protože je obtížné určit původ softwaru z veřejně dostupných imagí kontejnerů, sestavení Image ze zdroje pro zajištění znalosti původu vrstvy. Když se objeví ohrožení zabezpečení v imagi kontejneru, kterou jste si sami sestavili, můžete rychleji najít řešení. S použitím veřejné image byste museli najít kořen veřejné image a opravit ho nebo získat zabezpečenou image od vydavatele. 

* Ani důkladně zkontrolovaná image nasazená v produkčním prostředí nemusí být aktuální dobu životnosti aplikace. Může docházet k hlášení ohrožení zabezpečení pro vrstvy image, která dříve nebyla známa nebo se objevila až po nasazení do produkčního prostředí. 

  Pravidelně audit imagí nasazených v produkčním prostředí, aby se identifikovaly Image, které jsou zastaralé nebo nějakou dobu nebyly aktualizované. Nasazení modrá zelená metodologie a mechanismy průběžné může použít k aktualizaci imagí kontejnerů bez výpadků. Skenování obrázků s použitím nástrojů popsaných v předchozí části. 

* Použití kanálu průběžné integrace (CI) s integrovaným zabezpečením skenování k vytvoření zabezpečeného imagí a vložit je do privátního registru. Zjišťování ohrožení zabezpečení integrované v řešení průběžné integrace zajišťuje, že se image, které projdou všemi testy, nasdílí do privátního registru, ze kterého se nasazují produkční úlohy. 

  Selhání kanálu průběžné integrace zajistí, že se ohrožené Image nenasdílí do privátního registru, který se používá k nasazování produkčních úloh. Kanál také automatizuje kontrolu, pokud existuje velký počet imagí jejich zabezpečení. Jinak může být ruční auditování imagí za účelem zjišťování ohrožení zabezpečení zdlouhavé a náchylné k chybám. 

### <a name="enforce-least-privileges-in-runtime"></a>Vynutit nejnižších oprávnění v modulu runtime 

Konceptu nejnižších oprávnění je základní z důvodů zabezpečení, které platí také pro kontejnery. Když zneužití ohrožení zabezpečení obvykle poskytuje útočník přístup a oprávnění rovná se u ohrožení zabezpečení aplikace nebo proces. Zajištění, že kontejnery pracovat s nejnižšími oprávněními a nutných k dokončení práce omezuje vaše vystavení k riziku. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Omezení možností útoku na kontejneru tak, že odeberete nepotřebná oprávnění 

Můžete také minimalizovat potenciální prostor pro napadení tak, že odeberete nepoužívané nebo nepotřebné procesů nebo oprávnění z modulu runtime kontejneru. Privilegované kontejnerů spusťte jako uživatel root. Pokud uživatel se zlými úmysly nebo úloha řídicí sekvence v privilegovaných kontejner, kontejner pak spustí jako uživatel root v daném systému.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Seznam povolených souborů a spustitelné soubory, které kontejner může získat přístup nebo spuštění 

Snížení počtu proměnných nebo neznámé prvky pomáhá udržovat stabilní a spolehlivé prostředí. Omezení kontejnery, aby mohli získat přístup nebo spustit pouze schváleny předem nebo přidat na seznam povolených souborů a spustitelné soubory je prověřené metoda omezuje jeho vystavení rizika.  

Je to mnohem jednodušší a spravovat seznam povolených v případě, že je implementován od začátku. Seznam povolených obsahuje míru kontroly a možnosti správy dozvíte, jaké soubory a spustitelné soubory, které jsou požadovány pro správné fungování aplikace. 

Seznam povolených pouze snižuje útoky, ale můžete také poskytují základ pro anomálie a zabránit případy použití. "hlučného souseda" a scénářů užitečných kontejneru. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Vynutit segmentace sítě na spuštěné kontejnery  

Pomáhá chránit před bezpečnostními riziky v jiné podsíti kontejnery v jedné podsíti, Udržovat segmentace sítě (nebo nano segmentace) nebo oddělení mezi spuštěné kontejnery. Údržba segmentace sítě může být také nezbytné pro práci s kontejnery v odvětví, které jsou nutné ke splnění pověření dodržování předpisů.  

Například nástroj partnera [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) poskytuje automatizovaného postupu pro segmentace nano. Aqua monitoruje kontejneru síťových aktivit v modulu runtime. Identifikuje všechny příchozí a odchozí síťová připojení do/z jiných kontejnerů, služby, IP adresy a veřejným Internetem. Segmentace nano se automaticky vytvoří podle monitorovaném provozu. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorování kontejnerů aktivity a přístupu uživatelů 

Stejně jako u jakékoli IT prostředí, měli byste konzistentně sledovat aktivitu a přístupu uživatelů do vašeho kontejneru ekosystému rychle identifikovat všechny škodlivé nebo podezřelé aktivity. Azure nabízí, včetně řešení pro monitorování kontejnerů:

* [Azure Monitor pro kontejnery](../azure-monitor/insights/container-insights-overview.md) ke sledování výkonu vašich úloh nasazených do prostředí Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Azure Monitor pro kontejnery vám poskytne přehled o výkonu shromažďováním paměti a procesoru metriky z řadiče, uzly a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. 

* [Řešení pro monitorování kontejnerů Azure](../azure-monitor/insights/containers.md) umožňuje zobrazit a spravovat další Docker a Windows hostitelích kontejnerů na jednom místě. Příklad:

  * Zobrazte podrobné informace auditu shromáždit, který ukazuje příkazy používá s kontejnery. 
  * Řešení potíží s kontejnery pomocí zobrazení a hledání centralizované protokoly bez nutnosti Chcete-li zobrazit hostitele Docker nebo Windows.  
  * Hledat kontejnery, které mohou být na hostiteli hlučného a využívání nadbytečné prostředky.
  * Zobrazte centralizované procesoru, paměti, úložiště a využití a výkonu informace o síti pro kontejnery.  

  Řešení podporuje orchestrátorů kontejnerů, včetně Docker Swarm, DC/OS, nespravovaný Kubernetes, Service Fabric a Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Aktivita prostředku monitorování kontejneru 

Sledovat vaše aktivita prostředku, jako jsou soubory, sítě a další prostředky, které přistupují k vaše kontejnery. Sledování prostředků aktivitu a využití je užitečný pro sledování výkonu a zabezpečení. 

[Azure Monitor](../azure-monitor/overview.md) umožňuje základní monitorování pro služby Azure tím, že kolekce metriky, protokoly aktivit a diagnostické protokoly. Pomocí protokolu aktivit lze například zjistit, kdy se nové prostředky vytvořily nebo změnily. 

Jsou dostupné metriky, které poskytují statistiky o výkonu různých prostředků a dokonce i operačního systému ve virtuálním počítači. Tato data můžete na webu Azure Portal zobrazit pomocí některého z průzkumníků a na základě těchto metrik můžete vytvářet upozornění. Azure Monitor poskytuje že nejrychlejší metrik profilace (5 minut až 1 minuta), proto byste měli použít pro čas kritické výstrahy a oznámení. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Protokolovat všechny správce přístupu ke kontejneru pro auditování 

Udržujte přesný záznam pro audit správy přístupu k ekosystému kontejneru, registr kontejnerů a imagí kontejneru. Tyto protokoly může být nezbytné pro účely auditování a budou užitečné jako forenzní důkazy od všech incidentů zabezpečení. Můžete použít [řešení pro monitorování kontejnerů Azure](../azure-monitor/insights/containers.md) k dosažení tohoto cíle. 

## <a name="next-steps"></a>Další postup

* Další informace o správě chyb zabezpečení kontejnerů s řešeními od [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) a [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Další informace o [zabezpečení kontejnerů v Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).