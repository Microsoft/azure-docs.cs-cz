---
title: Požadavky na zabezpečení pro instance kontejnerů
description: Doporučení pro zabezpečení imagí a tajných klíčů pro Azure Container Instances a obecné požadavky na zabezpečení pro jakoukoli platformu kontejneru
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: cbceeea24501bc9815cb07e1b0a054914ba8e964
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579282"
---
# <a name="security-considerations-for-azure-container-instances"></a>Požadavky na zabezpečení pro Azure Container Instances

Tento článek představuje požadavky na zabezpečení při použití Azure Container Instances ke spouštění kontejnerových aplikací. Témata:

> [!div class="checklist"]
> * **Doporučení zabezpečení** pro správu imagí a tajných kódů pro Azure Container Instances
> * **Předpoklady pro ekosystém kontejneru** v celém životním cyklu kontejneru pro libovolnou platformu kontejneru

Komplexní doporučení, která vám pomůžou zlepšit stav zabezpečení nasazení, najdete v tématu základní informace o [zabezpečení Azure pro Container Instances](security-baseline.md).


## <a name="security-recommendations-for-azure-container-instances"></a>Doporučení zabezpečení pro Azure Container Instances

### <a name="use-a-private-registry"></a>Použití privátního registru

Kontejnery se vytváří z imagí uložených v jednom nebo několika úložištích. Tato úložiště můžou patřit do veřejného registru, jako je [Docker Hub](https://hub.docker.com)nebo do privátního registru. Příkladem privátního registru je [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), který je možné nainstalovat místně nebo ve virtuálním privátním cloudu. Můžete také použít cloudové služby privátních kontejnerů registru, včetně [Azure Container Registry](../container-registry/container-registry-intro.md). 

Veřejně dostupná image kontejneru nezaručuje zabezpečení. Image kontejneru se skládají z více softwarových vrstev a každá vrstva softwaru může mít ohrožení zabezpečení. Aby se snížila hrozba útoků, měli byste ukládat a načítat image z privátního registru, jako je Azure Container Registry nebo Docker Trusted Registry. Kromě poskytování spravovaného privátního registru Azure Container Registry podporuje [ověřování založené na instančních objektech](../container-registry/container-registry-authentication.md) prostřednictvím Azure Active Directory pro toky základního ověřování. Toto ověřování zahrnuje přístup na základě rolí pro čtení (přijetí), zápis (push) a další oprávnění.

### <a name="monitor-and-scan-container-images"></a>Monitorování a skenování imagí kontejneru

Využijte výhod řešení ke skenování imagí kontejnerů v privátním registru a identifikaci potenciálních ohrožení zabezpečení. Je důležité pochopit hloubku detekce hrozeb, kterou poskytují různá řešení.

Azure Container Registry například volitelně [integruje s Azure Security Center](../security-center/defender-for-container-registries-introduction.md) , aby automaticky kontrolovala všechny image Linux vložené do registru. Integrovaný Qualys skener s Azure Security Center detekuje slabá místa v obrazech, klasifikuje je a poskytuje pokyny k nápravě.

K dispozici jsou také řešení pro monitorování zabezpečení a kontrolu imagí, jako je [TwistLock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) a [azurová zabezpečení](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) , a to prostřednictvím Azure Marketplace.  

### <a name="protect-credentials"></a>Ochrana přihlašovacích údajů

Kontejnery se můžou rozdělit mezi několik clusterů a oblastí Azure. Proto je nutné zabezpečit pověření požadovaná pro přihlášení nebo přístup k rozhraní API, jako jsou hesla nebo tokeny. Ujistěte se, že k těmto kontejnerům mají přístup pouze uživatelé s oprávněním k přenosu a v klidovém provozu. Vytvořte inventář všech tajných kódů přihlašovacích údajů a pak vývojářům vyžadovat, aby používali nově vytvořené nástroje pro správu tajných klíčů, které jsou navržené pro kontejnery.  Ujistěte se, že vaše řešení zahrnuje šifrované databáze, šifrování TLS pro data tajných klíčů a minimální oprávnění [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md). [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) je cloudová služba, která chrání šifrovací klíče a tajné kódy (například certifikáty, připojovací řetězce a hesla) pro kontejnerové aplikace. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, zabezpečený přístup k vašim trezorům klíčů, aby k nim měli přístup jenom autorizovaní aplikace a uživatelé.

## <a name="considerations-for-the-container-ecosystem"></a>Předpoklady pro ekosystém kontejnerů

Následující bezpečnostní opatření, která jsou dobře implementované a efektivně spravovaná, vám můžou přispět k zabezpečení a ochraně vašeho ekosystému kontejnerů. Tato opatření platí v celém životním cyklu kontejneru, od vývoje prostřednictvím produkčního nasazení a na celou řadu orchestrací kontejnerů, hostitelů a platforem. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Použití správy ohrožení zabezpečení v rámci životního cyklu vývoje kontejneru 

Díky efektivní správě ohrožení zabezpečení v průběhu životního cyklu vývoje kontejneru vylepšíte lichá, že identifikujete a řešíte bezpečnostní otázky před tím, než se stanou závažnějšími problémy. 

### <a name="scan-for-vulnerabilities"></a>Vyhledat ohrožení zabezpečení 

Neustále se objevují nové chyby zabezpečení, takže vyhledávání a identifikaci chyb zabezpečení je nepřetržitý proces. Zahrňte kontrolu ohrožení zabezpečení v celém životním cyklu kontejneru:

* Jako poslední kontrolu vývojového kanálu byste měli před vložením imagí do veřejného nebo privátního registru provést kontrolu v kontejnerech. 
* Pokračujte v prohledávání imagí kontejnerů v registru tak, aby bylo možné identifikovat případné chyby, které byly při vývoji nějakým způsobem nezjištěny, a řešit nově zjištěná ohrožení zabezpečení, která mohou existovat v kódu používaném v obrázcích kontejneru.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapování slabých kopií obrazu na spuštěné kontejnery 

Je potřeba, abyste nastavili mapování chyb zabezpečení identifikovaných v imagích kontejneru na spouštění kontejnerů, což znamená, že problémy se zabezpečením se dají zmírnit nebo vyřešit.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Zajistěte, aby se ve vašem prostředí používaly jenom schválené image. 

V ekosystému kontejnerů je dostatek změn a nestálosti, aniž by bylo povoleno také použití neznámých kontejnerů. Povolte pouze schválené image kontejneru. Máte k dispozici nástroje a procesy pro monitorování a zabránění použití neschválených imagí kontejneru. 

Účinný způsob, jak omezit plochu pro útok a zabránit vývojářům v provádění kritických chyb zabezpečení, je řízení toku imagí kontejnerů do vývojového prostředí. Můžete například schválit jedno rozdělení Linux jako základní bitovou kopii, pokud jde o jednu z nich (Alpine nebo CoreOS namísto Ubuntu), aby se minimalizovala plocha potenciálních útoků. 

Podepisování obrázku nebo otisky prstů můžou poskytovat řetězec odolnosti, který umožňuje ověřit integritu kontejnerů. Azure Container Registry například podporuje model [vztahu důvěryhodnosti obsahu](https://docs.docker.com/engine/security/trust/content_trust) Docker, který umožňuje vydavatelům imagí podepisovat obrázky, které jsou vloženy do registru, a uživatele s obrázkem, který načte pouze podepsané obrázky.

### <a name="permit-only-approved-registries"></a>Povolit pouze schválené Registry 

Rozšíření zajišťující, že vaše prostředí používá pouze schválené image, je povolit pouze použití schválených registrů kontejnerů. Vyžadování schváleného kontejneru kontejnerů snižuje riziko rizika tím, že omezuje potenciál na zavedení neznámých chyb zabezpečení nebo problémů se zabezpečením. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zajištění integrity imagí v průběhu životního cyklu 

Součástí správy zabezpečení v rámci životního cyklu kontejneru je zajištění integrity imagí kontejneru v registru a jejich změny nebo nasazení do produkčního prostředí. 

* Image s chybami zabezpečení, dokonce i méně závažná, by neměly mít povoleno spouštění v produkčním prostředí. V ideálním případě by měly být všechny image nasazené v produkčním prostředí uložené v privátním registru, který je přístupný pro výběr několika málo. Udržujte si malý počet produkčních imagí, abyste se ujistili, že je možné je efektivně spravovat.

* Vzhledem k tomu, že je obtížné určit původ softwaru z veřejně dostupné image kontejneru, sestavte image ze zdroje, abyste zajistili znalosti původu vrstvy. Když se objeví ohrožení zabezpečení v imagi kontejneru, kterou jste si sami sestavili, můžete rychleji najít řešení. U veřejné image by zákazníci museli najít kořen veřejné image, aby ji opravili nebo získali další zabezpečenou Image od vydavatele. 

* Důkladná naskenovaná image nasazená v produkčním prostředí není zaručená, že se po celou dobu životnosti aplikace bude považovat za neaktuální. Může docházet k hlášení ohrožení zabezpečení pro vrstvy image, která dříve nebyla známa nebo se objevila až po nasazení do produkčního prostředí. 

  Pravidelně auditujte image nasazené v produkčním prostředí, které identifikují image, které jsou zastaralé nebo nejsou v průběhu času aktualizované. K aktualizaci imagí kontejneru bez výpadků můžete použít metodologie nasazení s modrou zelenou a přístupným upgradem. Image můžete kontrolovat pomocí nástrojů popsaných v předchozí části. 

* Pomocí kanálu průběžné integrace (CI) s integrovaným kontrolou zabezpečení Sestavte zabezpečené image a nahrajte je do privátního registru. Zjišťování ohrožení zabezpečení integrované v řešení průběžné integrace zajišťuje, že se image, které projdou všemi testy, nasdílí do privátního registru, ze kterého se nasazují produkční úlohy. 

  Selhání kanálu CI zajišťuje, že se neobsahují ohrožené image do privátního registru, který se používá pro nasazení produkčních úloh. Také automatizuje kontrolu zabezpečení obrazu, pokud je k dispozici velký počet imagí. Jinak může být ruční auditování imagí za účelem zjišťování ohrožení zabezpečení zdlouhavé a náchylné k chybám. 

### <a name="enforce-least-privileges-in-runtime"></a>Vyhovět minimálním oprávněním v modulu runtime 

Princip nejnižších oprávnění je základní osvědčený postup zabezpečení, který platí i pro kontejnery. Pokud dojde k zneužití ohrožení zabezpečení, obecně mu poskytne přístup a oprávnění útočníka se rovnají tomu, že ohrožená aplikace nebo proces. Zajištění toho, aby kontejnery pracovaly s nejnižšími oprávněními a přístup nutný k tomu, aby bylo možné dokončit úlohu, snižuje riziko ohrožení. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Zmenšení prostoru pro útok na kontejner odebráním nepotřebných oprávnění 

Potenciální plochu útoku můžete také minimalizovat odebráním nevyužitých nebo zbytečných procesů nebo oprávnění z modulu runtime kontejneru. Privilegované kontejnery jsou spouštěny jako kořenové. Pokud uživatel se zlými úmysly nebo úlohami řídí v privilegovaném kontejneru, bude kontejner v tomto systému spuštěn jako kořenový.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Předschvalovat soubory a spustitelné soubory, ke kterým má kontejner povolený přístup nebo spustit 

Snížení počtu proměnných nebo neznámých vám pomůže udržet stabilní a spolehlivé prostředí. Omezením kontejnerů tak, aby mohly přistupovat k nebo spustit pouze soubory s předschváleným nebo safelisted soubory a spustitelné soubory, představují prověřenou metodu, která omezuje expozici riziku.  

Je mnohem jednodušší spravovat Safelist, když je implementována od začátku. Safelist poskytuje míru kontroly a správy, protože se dozvíte, jaké soubory a spustitelné soubory jsou nutné, aby aplikace správně fungovala. 

Safelist nejen omezuje plochu pro útok, ale může také poskytovat základní údaje pro anomálie a zamezit tak použití scénářů "hlučného souseda" a užitečných kontejnerů. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Vymáhat segmentaci sítě pro spuštěné kontejnery  

Aby se chránily kontejnery v jedné podsíti před bezpečnostními riziky v jiné podsíti, udržujte segmentaci sítě (nebo nano-segmentace) nebo oddělení mezi běžícími kontejnery. Udržování segmentace sítě může být potřeba také k používání kontejnerů v oborech, které jsou nutné pro splnění požadavků dodržování předpisů.  

Například partnerský nástroj [azurová](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) poskytuje automatizovaný přístup pro segmentaci nano. Azurová sleduje síťové aktivity kontejneru v modulu runtime. Identifikuje všechna příchozí a odchozí síťová připojení k jiným kontejnerům, službám, IP adresám a veřejnému Internetu. Nano – segmentace se automaticky vytvoří na základě monitorovaného provozu. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorovat aktivitu kontejneru a přístup uživatelů 

Stejně jako u jakéhokoli IT prostředí byste měli konzistentně sledovat činnost a uživatelský přístup k ekosystému kontejnerů, abyste mohli rychle identifikovat podezřelé nebo škodlivé aktivity. Azure poskytuje řešení pro monitorování kontejnerů, včetně:

* [Azure monitor for Containers](../azure-monitor/containers/container-insights-overview.md) monitoruje výkon úloh nasazených do prostředí Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Azure Monitor pro kontejnery poskytují přehled o výkonu tím, že shromažďují metriky paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. 

* [Řešení Azure Container monitoring](../azure-monitor/containers/containers.md) vám pomůže zobrazit a spravovat další hostitele kontejnerů Docker a Windows v jednom umístění. Příklad:

  * Zobrazí podrobné informace o auditu, které zobrazují příkazy používané s kontejnery. 
  * Řešení potíží s kontejnery zobrazením a prohledáváním centralizovaných protokolů bez nutnosti vzdáleného zobrazení hostitelů Docker nebo Windows.  
  * Najděte kontejnery, které mohou být v hostiteli vysokou úrovní šumu a využívají nadměrné prostředky.
  * Zobrazit centralizované informace o využití procesoru, paměti, úložiště a sítě a informace o výkonu pro kontejnery.  

  Řešení podporuje orchestrace kontejnerů včetně Docker Swarm, DC/OS, nespravovaných Kubernetes, Service Fabric a Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorování aktivity prostředku kontejneru 

Monitorujte své aktivity prostředků, jako jsou soubory, sítě a další prostředky, ke kterým přistupují vaše kontejnery. Sledování aktivity a spotřeby prostředků je užitečné jak pro monitorování výkonu, tak jako bezpečnostní opatření. 

[Azure monitor](../azure-monitor/overview.md) umožňuje základní monitorování služeb Azure tím, že umožňuje shromažďování metrik, protokolů aktivit a diagnostických protokolů. Pomocí protokolu aktivit lze například zjistit, kdy se nové prostředky vytvořily nebo změnily. 

  Jsou dostupné metriky, které poskytují statistiky o výkonu různých prostředků a dokonce i operačního systému ve virtuálním počítači. Tato data můžete na webu Azure Portal zobrazit pomocí některého z průzkumníků a na základě těchto metrik můžete vytvářet upozornění. Azure Monitor nabízí nejrychlejší kanál metrik (zkrácení z 5 minut na 1 minutu), takže byste ho měli používat pro upozornění a oznámení, která jsou náročná na rychlou odezvu. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Protokolovat všechny přístupy správce kontejneru k auditování 

Udržujte přesnou auditový záznam o přístupu pro správu k ekosystému kontejnerů, včetně clusteru Kubernetes, registru kontejnerů a imagí kontejnerů. Tyto protokoly mohou být nezbytné pro účely auditování a budou užitečné jako forenzní legitimace po jakémkoli incidentu zabezpečení. Mezi řešení Azure patří:

* [Integrace služby Azure Kubernetes s Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) pro monitorování konfigurace zabezpečení prostředí clusteru a generování doporučení zabezpečení
* [Řešení Azure Container monitoring](../azure-monitor/containers/containers.md)
* Protokoly prostředků pro [Azure Container Instances](container-instances-log-analytics.md) a [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Další kroky

* V tématu [základní informace o zabezpečení Azure pro Container Instances](security-baseline.md) najdete komplexní doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

* Přečtěte si další informace o použití [Azure Security Center](../security-center/container-security.md) pro detekci hrozeb v reálném čase ve vašich kontejnerových prostředích.

* Přečtěte si další informace o správě chyb zabezpečení kontejnerů pomocí řešení z [TwistLock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) a [azurová Security](https://www.aquasec.com/solutions/azure-container-security/).