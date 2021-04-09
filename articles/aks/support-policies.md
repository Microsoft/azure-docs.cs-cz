---
title: Zásady podpory pro Azure Kubernetes Service (AKS)
description: Přečtěte si o zásadách podpory Azure Kubernetes Service (AKS), sdílených odpovědnostech a funkcích, které jsou ve verzi Preview (nebo Alpha nebo beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91892706"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zásady podpory pro službu Azure Kubernetes

Tento článek poskytuje podrobné informace o zásadách technické podpory a omezeních pro službu Azure Kubernetes Service (AKS). Článek také podrobně popisuje správu uzlů agentů, součásti spravovaného řízení, komponenty open source a zabezpečení nebo správu oprav.

## <a name="service-updates-and-releases"></a>Aktualizace a vydání služby

* Informace o vydání najdete v [poznámkách k verzi AKS](https://github.com/Azure/AKS/releases).
* Informace o funkcích ve verzi Preview najdete v tématu [funkce AKS ve verzi Preview a související projekty](https://awesomeopensource.com/projects/aks?categoryPage=11).

## <a name="managed-features-in-aks"></a>Spravované funkce v AKS

Základní cloudové komponenty infrastruktura jako služba (IaaS), jako jsou výpočetní nebo síťové komponenty, umožňují přístup k ovládacím prvkům nízké úrovně a možnostem přizpůsobení. Naproti tomu AKS poskytuje nasazení klíč Kubernetes, které vám poskytne společnou sadu konfigurací a možností, které pro cluster potřebujete. Jako AKS uživatel máte omezené možnosti přizpůsobení a nasazení. V systému Exchange se nemusíte starat o clustery Kubernetes a spravovat je přímo.

S AKS získáte plně spravovanou *rovinu řízení*. Rovina ovládacího prvku obsahuje všechny komponenty a služby, které potřebujete k provozování a poskytování clusterů Kubernetes koncovým uživatelům. Všechny součásti Kubernetes jsou spravovány a provozovány společností Microsoft.

Společnost Microsoft spravuje a sleduje následující komponenty prostřednictvím podokna řízení:

* Servery rozhraní API pro Kubelet nebo Kubernetes
* Etcd nebo kompatibilní úložiště klíč-hodnota, které poskytuje technologii QoS (Quality of Service), škálovatelnost a modul runtime.
* Služby DNS (například Kube-DNS nebo CoreDNS)
* Proxy server Kubernetes nebo sítě
* Jakákoli další doplňková nebo systémová součást spuštěná v oboru názvů Kube-System

AKS není řešení typu platforma jako služba (PaaS). Některé součásti, například uzly agentů, mají *sdílenou zodpovědnost*, kde uživatelé musí pomáhat s údržbou clusteru AKS. Vyžaduje se vstup uživatele, například pro použití opravy zabezpečení operačního systému uzlu agenta (OS).

Služby jsou *spravované* v tom smyslu, že se tým Microsoftu a AKS nasazuje, provozuje a zodpovídá za dostupnost a funkčnost služby. Zákazníci nemůžou tyto spravované součásti měnit. Microsoft omezuje přizpůsobení tak, aby se zajistilo jednotné a škálovatelné uživatelské prostředí. Plně přizpůsobitelné řešení najdete v tématu [AKS Engine](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Společná odpovědnost

Po vytvoření clusteru definujete uzly agentů Kubernetes, které vytvoří AKS. Vaše úlohy se spouštějí na těchto uzlech.

Vzhledem k tomu, že uzly agentů spouštějí soukromý kód a ukládají citlivá data, podpora Microsoftu k nim přistupovat jenom tak, že budou jenom velmi omezené. Podpora Microsoftu se nemůže přihlásit, spustit příkazy v nebo zobrazit protokoly pro tyto uzly bez vašeho výslovného svolení nebo asistence.

Jakékoli změny provedené přímo v uzlech agentů pomocí kterékoli z rozhraní API IaaS vykreslí, že cluster nebude možné podporovat. Jakékoli změny provedené v uzlech agentů se musí provádět pomocí Kubernetes nativních mechanismů, jako je `Daemon Sets` .

Podobně i když můžete do clusteru a uzlů přidat libovolná metadata, jako jsou značky a popisky, změna kterékoli ze systémových metadat vygeneruje nepodporovaný cluster.

## <a name="aks-support-coverage"></a>Pokrytí podpory AKS

Společnost Microsoft poskytuje technickou podporu pro následující příklady:

* Připojení ke všem součástem Kubernetes, které poskytuje a podporuje služba Kubernetes, jako je třeba server rozhraní API.
* Správa, doba provozu, technologie QoS a operace Kubernetesch řídicích rovin služeb (například řídicí plocha Kubernetes, Server rozhraní API, etcd a coreDNS).
* Úložiště dat Etcd. Podpora zahrnuje pro plánování havárií a obnovení stavu clusteru automatizované a transparentní zálohy všech etcd dat každých 30 minut. Tyto zálohy nejsou přímo k dispozici pro vás ani pro uživatele. Zajišťují spolehlivost a konzistenci dat. Etcd. vrácení zpět nebo obnovení na vyžádání není podporováno jako funkce.
* Všechny integrační body v ovladači Azure Cloud Provider pro Kubernetes. Mezi ně patří integrace do jiných služeb Azure, jako jsou nástroje pro vyrovnávání zatížení, trvalé svazky nebo sítě (Kubernetes a Azure CNI).
* Otázky nebo problémy týkající se přizpůsobení komponent roviny ovládacího prvku, jako je například server Kubernetes API, etcd a coreDNS.
* Problémy týkající se sítě, jako je Azure CNI, kubenet nebo další problémy s přístupem a funkcemi pro přístup k síti. Problémy by mohly zahrnovat překlad DNS, ztrátu paketů, směrování atd. Microsoft podporuje různé scénáře sítě:
  * Kubenet a Azure CNI pomocí spravovaného virtuální sítě nebo s vlastními podsítěmi (Přineste si vlastní).
  * Připojení k ostatním službám a aplikacím Azure
  * Konfigurace řadičů příchozího přenosu dat a příchozího přenosu dat nebo nástroje pro vyrovnávání zatížení
  * Výkon a latence sítě


> [!NOTE]
> Všechny akce clusteru prováděné Microsoftem a AKS se provádí s souhlasem uživatele v rámci předdefinované Kubernetes role `aks-service` a předdefinované vazby role `aks-service-rolebinding` . Tato role umožňuje AKS řešit a diagnostikovat problémy s clustery, ale nemůže měnit oprávnění ani vytvářet role nebo vazby rolí ani jiné akce s vysokou úrovní oprávnění. Přístup k rolím je povolený jenom v aktivních lístkech podpory s přístupem JIT (just-in-time).

Společnost Microsoft neposkytuje technickou podporu pro následující příklady:

* Dotazy týkající se použití Kubernetes Podpora Microsoftu například neposkytuje Rady k vytváření vlastních řadičů příchozího přenosu dat, použití aplikačních úloh nebo k použití softwarových balíčků nebo nástrojů třetích stran nebo open source.
  > [!NOTE]
  > Podpora Microsoftu může radit na funkce, přizpůsobení a ladění clusteru AKS (například problémy s Kubernetes operací a postupy).
* Projekty Open Source třetích stran, které nejsou poskytovány jako součást plochy ovládacího prvku Kubernetes nebo nasazeny s clustery AKS. Tyto projekty mohou zahrnovat Istio, Helm, zástupné nebo jiné.
  > [!NOTE]
  > Microsoft může poskytovat nejlepší podporu pro open source projekty třetích stran, jako je Helm. Pokud se Open source nástroj od jiného výrobce integruje s Kubernetes poskytovatelem cloudu Azure nebo jinými chybami týkajícími se AKS, Microsoft podporuje v dokumentaci Microsoftu příklady a aplikace.
* Uzavřený zdrojový software třetí strany. Tento software může obsahovat nástroje pro kontrolu zabezpečení a síťová zařízení nebo software.
* Vlastní nastavení sítě, kromě těch, které jsou uvedeny v [dokumentaci k AKS](./index.yml).


## <a name="aks-support-coverage-for-agent-nodes"></a>Pokrytí podpory AKS pro uzly agentů

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Odpovědnosti Microsoftu pro uzly agenta AKS

Microsoft a uživatelé sdílejí zodpovědnost za uzly agenta Kubernetes, kde:

* Základní image operačního systému obsahuje požadované dodatky (například monitorování a síťové agenty).
* Uzly agenta automaticky dostanou opravy operačního systému.
* Problémy s komponentou roviny ovládacího prvku Kubernetes, které běží na uzlech agentů, jsou automaticky opraveny. Mezi tyto komponenty patří:
  * `Kube-proxy`
  * Síťové tunely, které poskytují komunikační cesty k hlavním komponentám Kubernetes
  * `Kubelet`
  * `Moby` nebo `ContainerD`

> [!NOTE]
> Pokud uzel agenta není funkční, AKS může restartovat jednotlivé součásti nebo celý uzel agenta. Tyto operace restartování jsou automatizované a poskytují automatickou nápravu běžných problémů. Pokud chcete získat další informace o mechanismech automatické nápravy, přečtěte si téma [Automatická oprava uzlu](node-auto-repair.md) .

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Zodpovědnosti zákazníků pro uzly agenta AKS

Microsoft nabízí každý týden opravy a nové obrázky pro uzly imagí, ale ve výchozím nastavení je automaticky neopraví. Aby bylo možné zachovat operační systémy uzlu agenta a součásti modulu runtime, měli byste zachovat plán [aktualizace](node-image-upgrade.md) pro běžný uzel nebo ho automatizovat.

Podobně AKS pravidelně uvolňuje nové opravy Kubernetes a dílčí verze. Tyto aktualizace můžou obsahovat vylepšení zabezpečení nebo funkcí Kubernetes. Zodpovídáte za aktualizaci verze Kubernetes vašich clusterů a v souladu se [zásadami verze AKS Kubernetes support](supported-kubernetes-versions.md).

#### <a name="user-customization-of-agent-nodes"></a>Uživatelské přizpůsobení uzlů agentů
> [!NOTE]
> Uzly agenta AKS se zobrazují v Azure Portal jako běžné prostředky Azure IaaS. Tyto virtuální počítače se ale nasadí do vlastní skupiny prostředků Azure (obvykle s předponou MC_ \* ). Základní image operačního systému nemůžete změnit ani žádné přímé přizpůsobení těchto uzlů pomocí rozhraní IaaS API nebo prostředků. Jakékoli vlastní změny, které se neprovádí prostřednictvím rozhraní AKS API, se při upgradu, škálování, aktualizaci nebo restartování neuloží. Neprovádějte změny v uzlech agentů, pokud podpora Microsoftu nesměrujete, abyste provedli změny.

AKS spravuje životní cyklus a operace uzlů agentů vaším jménem – Změna prostředků IaaS přidružených k uzlům agenta není **podporovaná**. Příkladem nepodporované operace je přizpůsobení sady škálování virtuálního počítače v rámci fondu uzlů ruční změnou konfigurace prostřednictvím portálu pro škálování sady virtuálních počítačů nebo rozhraní API.
 
Pro konfigurace nebo balíčky specifické pro úlohy AKS doporučuje použití [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

Použití Kubernetes privilegovaného `daemon sets` a inicializačního kontejneru vám umožní ladit/upravovat nebo instalovat software třetích stran na uzlech agentů clusteru. Příklady takových úprav zahrnují přidání vlastního softwaru pro kontrolu zabezpečení nebo aktualizace nastavení sysctl.

I když se tato cesta doporučuje, pokud se výše uvedené požadavky uplatní, AKS Engineering and Support nemůže pomoct při řešení potíží nebo diagnostikování úprav, které vykreslí uzel nedostupný z důvodu vlastního nasazení `daemon set` .

### <a name="security-issues-and-patching"></a>Problémy se zabezpečením a opravy

Pokud je v jedné nebo více spravovaných součástech AKS zjištěna chyba zabezpečení, tým AKS opraví všechny ovlivněné clustery, aby problém zmírnit. Případně tým poskytne pokyny k upgradu uživatelů.

V případě uzlů agentů ovlivněných chybou zabezpečení vás Microsoft upozorní s podrobnostmi o dopadu a krocích při řešení potíží se zabezpečením (obvykle při upgradu bitové kopie uzlu nebo upgradu opravy clusteru).

### <a name="node-maintenance-and-access"></a>Údržba uzlů a přístup

I když se můžete přihlásit k uzlům agentů a měnit je, tato operace se nedoporučuje, protože změny můžou učinit nepodporu clusteru.

## <a name="network-ports-access-and-nsgs"></a>Síťové porty, přístup a skupin zabezpečení sítě

Skupin zabezpečení sítě můžete přizpůsobit jenom pro vlastní podsítě. Nemůžete přizpůsobit skupin zabezpečení sítě ve spravovaných podsítích nebo na úrovni síťových adaptérů uzlů agentů. AKS má požadavky na výstup pro konkrétní koncové body, pro řízení výstupů a zajištění potřebného připojení, viz [omezení odchozího provozu](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Zastavené nebo nepřidělené clustery

Jak bylo uvedeno dříve, ruční zrušení přidělení všech uzlů clusteru prostřednictvím rozhraní API IaaS/CLI/portálu vykreslí cluster mimo podporu. Jediným podporovaným způsobem, jak zastavit/zrušit přidělení všech uzlů, je [zastavit cluster AKS](start-stop-cluster.md#stop-an-aks-cluster), který zachovává stav clusteru až na 12 měsíců.

Clustery, které jsou zastavené déle než 12 měsíců, již nebudou zachovávat stav. 

Clustery, které jsou nepřidělené mimo rozhraní API AKS, nemají žádné záruky na uchovávání stavů. Řídicí roviny pro clustery v tomto stavu budou archivovány po 30 dnech a odstraněny po uplynutí 12 měsíců.

AKS si vyhrazuje právo na archivaci řídicích rovin, které byly nakonfigurovány podle pokynů k podpoře pro rozšířené tečky, které se rovnají nebo přesahují 30 dnů. AKS udržuje zálohy metadat clusterových etcd a můžou snadno znovu přidělit cluster. Toto přerozdělení můžete iniciovat jakoukoli operací PUT, která cluster přenese zpátky do podpory, jako je například upgrade nebo škálování na aktivní uzly agentů.

Pokud je vaše předplatné pozastavené nebo odstraněné, odstraní se Řídicí rovina a stav vašeho clusteru po 90 dnech.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nepodporované funkce Alpha a beta Kubernetes

AKS podporuje jenom stabilní a beta funkce v rámci nadřazeného Kubernetes projektu. Pokud není uvedeno jinak, nepodporuje AKS žádné funkce alfa, která je k dispozici v nadřazeném projektu Kubernetes.

## <a name="preview-features-or-feature-flags"></a>Funkce náhledu nebo příznaky funkcí

Pro funkce a funkce, které vyžadují rozšířené testování a zpětnou vazbu od uživatelů, vydává společnost Microsoft nové funkce nebo funkce verze Preview za příznakem funkce. Tyto funkce zvažte jako předprodejní nebo beta funkce.

Funkce verze Preview nebo funkce příznaku funkcí nejsou určeny pro produkční prostředí. Probíhající změny v rozhraních API a chování, opravy chyb a další změny můžou vést k nestabilním clusterům a výpadkům.

Funkce ve verzi Public Preview spadají pod podporu "nejlepšího úsilí", protože tyto funkce jsou ve verzi Preview a nejsou určené pro produkční prostředí a jsou podporovány AKS pracovníky technické podpory pouze v pracovní době. Další informace naleznete v tématu:

* [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Naproti chybám a problémům

S ohledem na rychlost vývoje v nadřazeném projektu Kubernetes dojde k chybám invariably. Některé z těchto chyb nelze v systému AKS opravovat ani odpracovat. Místo toho opravy chyb vyžadují pro nadřazené projekty větší opravy (například Kubernetes, operační systémy uzlů nebo agentů a jádro). Pro součásti, které Microsoft vlastní (například poskytovatel cloudu Azure), se AKS a zaměstnanci Azure zavazují opravit problémy v komunitě.

Pokud je problém s technickou podporou rootem z důvodu jedné nebo více chyb typu nadřazený-proud, podpora AKS a technické týmy budou:

* Identifikujte a propojte chyby nadřazeného streamu s případnými podpůrnými podrobnostmi, které vám pomůžou vysvětlit, proč tento problém ovlivňuje váš cluster nebo úlohu. Zákazníci obdrží odkazy na požadovaná úložiště, aby mohli sledovat problémy, a zjistit, kdy bude nová verze poskytovat opravy.
* Poskytněte možná alternativní řešení nebo zmírnění rizik. Pokud se problém dá zmírnit, v úložišti AKS se zaznamená [známý problém](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) . Popsání řešení známých problémů:
  * Problém, včetně odkazů na chyby proti chybě
  * Alternativní řešení a podrobnosti o upgradu nebo jiné trvalosti řešení.
  * Hrubá časová osa pro zařazení problému na základě tempo verze pro odesílání dat.
