---
title: Zabezpečení pro instance kontejnerů
description: Doporučení k zabezpečení irek a tajných kódů pro instance kontejnerů Azure a obecné aspekty zabezpečení pro všechny platformy kontejnerů
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: b5f2c4d9ca80318574e288110fd4ce7f490af00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76260493"
---
# <a name="security-considerations-for-azure-container-instances"></a>Důležité informace o zabezpečení pro instance kontejnerů Azure

Tento článek představuje důležité informace o zabezpečení pro použití Azure Container Instances ke spuštění kontejnerových aplikací. Témata:

> [!div class="checklist"]
> * **Doporučení zabezpečení** pro správu iniciací a tajných klíčů pro instance kontejnerů Azure
> * **Důležité informace pro ekosystém kontejnerů** v průběhu celého životního cyklu kontejneru, pro každou platformu kontejnerů

## <a name="security-recommendations-for-azure-container-instances"></a>Doporučení zabezpečení pro instance kontejnerů Azure

### <a name="use-a-private-registry"></a>Použití soukromého registru

Kontejnery se vytváří z imagí uložených v jednom nebo několika úložištích. Tato úložiště mohou patřit do veřejného registru, jako je [Docker Hub](https://hub.docker.com)nebo do soukromého registru. Příkladem privátního registru je [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), který je možné nainstalovat místně nebo ve virtuálním privátním cloudu. Můžete také použít cloudové služby registru privátního kontejneru, včetně [registru kontejnerů Azure](../container-registry/container-registry-intro.md). 

Veřejně dostupná bitová kopie kontejneru nezaručuje zabezpečení. Image kontejnerů se skládají z více vrstev softwaru a každá softwarová vrstva může mít chyby zabezpečení. Chcete-li snížit hrozbu útoků, měli byste ukládat a načítat bitové kopie z privátního registru, jako je například Azure Container Registry nebo Docker Trusted Registry. Kromě poskytování spravovaného privátního registru podporuje Azure Container Registry [ověřování na principu služby](../container-registry/container-registry-authentication.md) prostřednictvím služby Azure Active Directory pro základní toky ověřování. Toto ověřování zahrnuje přístup založený na rolích pro jen pro čtení (vyžádat), zápis (push) a další oprávnění.

### <a name="monitor-and-scan-container-images"></a>Sledování a skenování obrázků kontejnerů

Využijte řešení pro skenování identifikací kontejnerů v soukromém registru a identifikaci potenciálních chyb zabezpečení. Je důležité pochopit hloubku detekce hrozeb, kterou poskytují různá řešení.

Například Azure Container Registry volitelně [integruje s Azure Security Center](../security-center/azure-container-registry-integration.md) automaticky prohledat všechny ibi Linuxu tlačil do registru. Integrovaný skener Qualys centra Azure Security Center detekuje slabá místa bitových zjištění, klasifikuje je a poskytuje pokyny k nápravě.

Řešení pro monitorování zabezpečení a skenování obrázků, jako je [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) a [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) jsou k dispozici také na Azure Marketplace.  

### <a name="protect-credentials"></a>Ochrana přihlašovacích údajů

Kontejnery se můžou rozprostřít napříč několika clustery a oblastmi Azure. Takže je nutné zabezpečit přihlašovací údaje potřebné pro přihlášení nebo přístup k rozhraní API, jako jsou hesla nebo tokeny. Ujistěte se, že pouze privilegovaní uživatelé mají přístup k těmto kontejnerům v tranzitu a v klidovém stavu. Vyhodnoťte všechna tajné kódy pověření a potom od vývojářů, aby používali nové nástroje pro správu tajných kódů, které jsou určeny pro kontejnerové platformy.  Ujistěte se, že vaše řešení obsahuje šifrované databáze, šifrování TLS pro přenos tajných kódů a [řízení přístupu založené na rolích](../role-based-access-control/overview.md)s nejnižšími oprávněními . [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) je cloudová služba, která chrání šifrovací klíče a tajné klíče (například certifikáty, připojovací řetězce a hesla) pro kontejnerizované aplikace. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, zabezpečte přístup k trezorům klíčů, aby k nim měly přístup pouze oprávněné aplikace a uživatelé.

## <a name="considerations-for-the-container-ecosystem"></a>Důležité informace pro ekosystém kontejnerů

Následující bezpečnostní opatření, která jsou dobře implementována a efektivně spravována, vám mohou pomoci zabezpečit a chránit ekosystém kontejnerů. Tato opatření platí v celém životním cyklu kontejneru, od vývoje až po nasazení v produkčním prostředí a řadu orchestrátorů kontejnerů, hostitelů a platforem. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Použití správy ohrožení zabezpečení jako součást životního cyklu vývoje kontejnerů 

Pomocí efektivní správy chyb zabezpečení v průběhu životního cyklu vývoje kontejnerů můžete zlepšit pravděpodobnost, že identifikujete a vyřešíte problémy se zabezpečením dříve, než se stanou závažnějším problémem. 

### <a name="scan-for-vulnerabilities"></a>Hledání chyb zabezpečení 

Nové chyby zabezpečení jsou objevovány po celou dobu, takže vyhledávání a identifikace chyb zabezpečení je nepřetržitý proces. Začleňte prohledávání chyb zabezpečení v celém životním cyklu kontejneru:

* Jako poslední kontrolu ve vývojovém kanálu byste měli před odesláním bitových kopií do veřejného nebo soukromého registru provést kontrolu chybzabezpečení v kontejnerech. 
* Pokračujte v prohledávacích obrázcích kontejnerů v registru, abyste identifikovali všechny chyby, které byly během vývoje nějakým způsobem vynechány, a vyřešili všechny nově zjištěné chyby zabezpečení, které by mohly existovat v kódu použitém v identifikacích kopiíkontejneru.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapování slabých míst s bitovými kopiemi na spuštěné kontejnery 

Musíte mít prostředky mapování chyb zabezpečení zjištěných v inabech kontejnerů na spuštěné kontejnery, aby bylo možné zmírnit nebo vyřešit problémy se zabezpečením.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Ujistěte se, že ve vašem prostředí jsou používány pouze schválené obrázky 

Je tu dost změn a volatilita v kontejneru ekosystému, aniž by neznámé kontejnery stejně. Povolit pouze schválené image kontejnerů. Měj mety k monitorování a prevenci používání neschválených iobrazek kontejnerů a jejich předcházení. 

Účinným způsobem, jak snížit prostor pro útok a zabránit vývojářům v provádění kritických chyb zabezpečení, je řídit tok iobrazek kontejnerů do vývojového prostředí. Můžete například sankcionovat jednu distribuci Linuxu jako základní bitovou kopii, nejlépe tu, která je štíhlá (Alpine nebo CoreOS spíše než Ubuntu), abyste minimalizovali povrch pro potenciální útoky. 

Podepisování obrázků nebo snímání otisků prstů může poskytnout řetězec úschovy, který vám umožní ověřit integritu kontejnerů. Azure Container Registry například podporuje model [důvěryhodnosti obsahu Dockeru,](https://docs.docker.com/engine/security/trust/content_trust) který umožňuje vydavatelům bitových kopií podepisovat image, které jsou odesílány do registru, a příjemci bitové kopie k vytahování pouze podepsaných bitových kopií.

### <a name="permit-only-approved-registries"></a>Povolit pouze schválené registry 

Rozšíření zajištění, že vaše prostředí používá pouze schválené bitové kopie je povolit pouze použití schválených registrů kontejnerů. Vyžadování použití schválených registrů kontejnerů snižuje vystavení riziku tím, že omezuje možnost zavedení neznámých chyb zabezpečení nebo problémů se zabezpečením. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zajištění integrity obrázků v průběhu celého životního cyklu 

Součástí správy zabezpečení v průběhu celého životního cyklu kontejneru je zajistit integritu iblbitů kontejneru v registru a jak jsou změněny nebo nasazeny do produkčního prostředí. 

* Obrázky s chybami zabezpečení, i menší, by nemělo být povoleno spustit v produkčním prostředí. V ideálním případě by měly být všechny bitové kopie nasazené v produkčním prostředí uloženy v soukromém registru přístupném několika vybraným. Udržujte počet produkčních bitových kopií malý, abyste zajistili, že je lze efektivně spravovat.

* Vzhledem k tomu, že je těžké určit původ softwaru z veřejně dostupné image kontejneru, vytvářejte obrazy ze zdroje, abyste zajistili znalost původu vrstvy. Když se objeví ohrožení zabezpečení v imagi kontejneru, kterou jste si sami sestavili, můžete rychleji najít řešení. S veřejnou image, zákazníci by museli najít kořen veřejné image opravit nebo získat další zabezpečenou bitovou kopii od vydavatele. 

* Důkladně naskenovaná bitová kopie nasazená v produkčním prostředí není zaručena jako aktuální po dobu životnosti aplikace. Může docházet k hlášení ohrožení zabezpečení pro vrstvy image, která dříve nebyla známa nebo se objevila až po nasazení do produkčního prostředí. 

  Pravidelně auditujte bitové kopie nasazené v produkčním prostředí, abyste identifikovali bitové kopie, které jsou zastaralé nebo nebyly nějakou dobu aktualizovány. Můžete použít modrozelené metodiky nasazení a postupné mechanismy upgradu k aktualizaci ibi kontejnerů bez prostojů. Obrázky můžete skenovat pomocí nástrojů popsaných v předchozí části. 

* Pomocí kanálu průběžné integrace (CI) s integrovaným prohledáváním zabezpečení můžete vytvářet zabezpečené bitové kopie a tlačit je do soukromého registru. Zjišťování ohrožení zabezpečení integrované v řešení průběžné integrace zajišťuje, že se image, které projdou všemi testy, nasdílí do privátního registru, ze kterého se nasazují produkční úlohy. 

  Selhání kanálu CI zajišťuje, že ohrožené bitové kopie nejsou nabízeny do soukromého registru, který se používá pro nasazení produkčníúlohy. Také automatizuje skenování zabezpečení obrazu, pokud existuje značný počet obrázků. Jinak může být ruční auditování imagí za účelem zjišťování ohrožení zabezpečení zdlouhavé a náchylné k chybám. 

### <a name="enforce-least-privileges-in-runtime"></a>Vynucení nejnižších oprávnění za běhu 

Koncept nejnižších oprávnění je základní osvědčený postup zabezpečení, který platí také pro kontejnery. Pokud je zneužita chyba zabezpečení, obecně poskytuje útočníkovi přístup a oprávnění, která se rovnají oprávněním ohrožené aplikace nebo procesu. Zajištění toho, aby kontejnery pracovaly s nejnižšími oprávněními a přístupem potřebným k dokončení úlohy, snižuje riziko. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Snižte prostor pro útok na povrch odebráním nepotřebných oprávnění 

Můžete také minimalizovat potenciální prostor pro útok odebráním nepoužívaných nebo nepotřebných procesů nebo oprávnění z běhu kontejneru. Privilegované kontejnery spustit jako root. Pokud uživatel se zlými úmysly nebo úloha unikne v privilegovaném kontejneru, kontejner se pak spustí jako root v tomto systému.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Předběžné schválení souborů a spustitelných souborů, ke kterým má kontejner povolen přístup nebo ke spuštění 

Snížení počtu proměnných nebo neznámých pomáhá udržovat stabilní a spolehlivé prostředí. Omezení kontejnerů, aby mohly přistupovat nebo spouštět pouze předem schválené nebo bezpečné soubory a spustitelné soubory, je osvědčenou metodou omezení vystavení riziku.  

Je to mnohem jednodušší spravovat safelist, když je implementován od začátku. Safelist poskytuje míru řízení a možnosti správy, jak se dozvíte, jaké soubory a spustitelné soubory jsou požadovány pro aplikaci správně fungovat. 

Safelist nejen snižuje povrch útoku, ale může také poskytnout směrný plán pro anomálie a zabránit případy použití "hlučné souseda" a kontejneru breakout scénáře. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Vynucení segmentace sítě u spuštěné kontejnery  

Chcete-li chránit kontejnery v jedné podsíti před bezpečnostními riziky v jiné podsíti, udržovat segmentaci sítě (nebo nanosegmentaci) nebo segregaci mezi spuštěné kontejnery. Udržování segmentace sítě může být také nezbytné pro použití kontejnerů v odvětvích, která jsou nezbytná ke splnění mandátů pro dodržování předpisů.  

Například partnerský nástroj [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) poskytuje automatizovaný přístup k nanosegmentaci. Aqua monitoruje aktivity kontejnerové sítě za běhu. Identifikuje všechna příchozí a odchozí síťová připojení do nebo z jiných kontejnerů, služeb, IP adres a veřejného internetu. Nanosegmentace je automaticky vytvořena na základě sledovaného provozu. 

### <a name="monitor-container-activity-and-user-access"></a>Sledování aktivity kontejneru a přístupu uživatelů 

Stejně jako v jakémkoli it prostředí byste měli důsledně sledovat aktivitu a přístup uživatelů k ekosystému kontejnerů, abyste rychle identifikovali jakoukoli podezřelou nebo škodlivou aktivitu. Azure poskytuje řešení monitorování kontejnerů, včetně:

* [Azure Monitor pro kontejnery](../azure-monitor/insights/container-insights-overview.md) monitoruje výkon vašich úloh nasazených do prostředí Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Azure Monitor pro kontejnery poskytuje viditelnost výkonu shromažďováním metrik paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou dostupné v Kubernetes prostřednictvím rozhraní API metriky. 

* [Řešení Azure Container Monitoring](../azure-monitor/insights/containers.md) vám pomůže zobrazit a spravovat ostatní hostitele kontejnerů Dockeru a Windows na jednom místě. Například:

  * Zobrazení podrobných informací o auditu, které zobrazují příkazy používané s kontejnery. 
  * Poradce při potížích s kontejnery zobrazením a prohledáváním centralizovaných protokolů bez nutnosti vzdáleného zobrazení hostitelů Dockeru nebo Windows.  
  * Najít kontejnery, které mohou být hlučné a spotřebovávají přebytečné prostředky na hostiteli.
  * Zobrazení centralizovaných informací o procesoru, paměti, úložišti a využití sítě a výkonu kontejnerů.  

  Řešení podporuje kontejnerové orchestrátory včetně Docker Swarm, DC/OS, unmanaged Kubernetes, Service Fabric a Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorování aktivity prostředků kontejneru 

Sledujte aktivitu prostředků, jako jsou soubory, síť a další prostředky, ke kterým mají kontejnery přístup. Sledování aktivity prostředků a spotřeby je užitečné jak pro sledování výkonu, tak pro bezpečnostní opatření. 

[Azure Monitor](../azure-monitor/overview.md) umožňuje základní monitorování služeb Azure tím, že umožňuje shromažďování metrik, protokoly aktivit a diagnostické protokoly. Pomocí protokolu aktivit lze například zjistit, kdy se nové prostředky vytvořily nebo změnily. 

  Jsou dostupné metriky, které poskytují statistiky o výkonu různých prostředků a dokonce i operačního systému ve virtuálním počítači. Tato data můžete na webu Azure Portal zobrazit pomocí některého z průzkumníků a na základě těchto metrik můžete vytvářet upozornění. Azure Monitor nabízí nejrychlejší kanál metrik (zkrácení z 5 minut na 1 minutu), takže byste ho měli používat pro upozornění a oznámení, která jsou náročná na rychlou odezvu. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Protokolovat přístup uživatele správy kontejneru pro auditování 

Udržujte přesný auditní záznam přístupu správce k ekosystému kontejnerů, včetně clusteru Kubernetes, registru kontejnerů a iimages kontejnerů. Tyto protokoly mohou být nezbytné pro účely auditu a budou užitečné jako forenzní důkazy po každém bezpečnostním incidentu. Mezi řešení Azure patří:

* [Integrace služby Azure Kubernetes service s Azure Security Center](../security-center/azure-kubernetes-service-integration.md) pro monitorování konfigurace zabezpečení prostředí clusteru a generování doporučení zabezpečení
* [Řešení Azure Container Monitoring](../azure-monitor/insights/containers.md)
* Protokoly prostředků pro [instance kontejnerů Azure](container-instances-log-analytics.md) a [registr kontejnerů Azure](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o používání [Azure Security Center](../security-center/container-security.md) pro detekci hrozeb v reálném čase ve vašich kontejnerizovaných prostředích.

* Přečtěte si další informace o správě slabých míst kontejnerů pomocí řešení [twistlocku](https://www.twistlock.com/solutions/microsoft-azure-container-security/) a [aqua securityu](https://www.aquasec.com/solutions/azure-container-security/).