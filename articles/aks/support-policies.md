---
title: Zásady podpory pro Azure Kubernetes Service (AKS)
description: Přečtěte si o zásadách podpory Azure Kubernetes Service (AKS), sdílených odpovědnostech a funkcích, které jsou ve verzi Preview (nebo Alpha nebo beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: ec58f8df5507fd9c52950e880c062e6cad964b7a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106981"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zásady podpory pro službu Azure Kubernetes

Tento článek poskytuje podrobné informace o zásadách technické podpory a omezeních pro službu Azure Kubernetes Service (AKS). Článek také podrobně popisuje správu pracovních uzlů, součásti spravovaného ovládacího prvku, komponenty Open Source třetích stran a zabezpečení nebo správu oprav.

## <a name="service-updates-and-releases"></a>Aktualizace a vydání služby

* Informace o vydání najdete v [poznámkách k verzi AKS](https://github.com/Azure/AKS/releases).
* Informace o funkcích ve verzi Preview najdete v tématu [funkce AKS ve verzi Preview a související projekty](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Spravované funkce v AKS

Základní cloudové komponenty infrastruktura jako služba (IaaS), jako jsou výpočetní nebo síťové komponenty, poskytují uživatelům přístup k ovládacím prvkům nízké úrovně a možnostem přizpůsobení. Naproti tomu AKS poskytuje nasazení klíč Kubernetes, které zákazníkům poskytuje běžnou sadu konfigurací a schopností, které potřebují. AKS zákazníci mají omezené možnosti přizpůsobení, nasazení a dalších možností. Tito zákazníci si nemuseli dělat starosti ani spravovat clustery Kubernetes přímo.

S AKS zákazník získá plně spravovanou *rovinu řízení*. Rovina ovládacího prvku obsahuje všechny součásti a služby, které zákazník potřebuje k provozu a poskytování clusterů Kubernetes koncovým uživatelům. Všechny součásti Kubernetes jsou spravovány a provozovány společností Microsoft.

Společnost Microsoft spravuje a sleduje následující komponenty prostřednictvím podokna řízení:

* Servery rozhraní API pro Kubelet nebo Kubernetes
* Etcd nebo kompatibilní úložiště klíč-hodnota, které poskytuje technologii QoS (Quality of Service), škálovatelnost a modul runtime.
* Služby DNS (například Kube-DNS nebo CoreDNS)
* Proxy server Kubernetes nebo sítě

AKS není zcela spravované řešení clusteru. Některé součásti, například pracovní uzly, mají *sdílenou zodpovědnost*, kde uživatelé musí pomáhat s údržbou clusteru AKS. Uživatelský vstup je vyžadován, například pro použití opravy zabezpečení operačního systému pracovního uzlu (OS).

Služby jsou *spravované* v tom smyslu, že se tým Microsoftu a AKS nasazuje, provozuje a zodpovídá za dostupnost a funkčnost služby. Zákazníci nemůžou tyto spravované součásti měnit. Microsoft omezuje přizpůsobení tak, aby se zajistilo jednotné a škálovatelné uživatelské prostředí. Plně přizpůsobitelné řešení najdete v tématu [AKS Engine](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Společná odpovědnost

Po vytvoření clusteru definuje zákazník pracovní uzly Kubernetes, které AKS vytvoří. Na těchto uzlech se spouštějí úlohy zákazníka. Vlastníci a můžou zobrazit nebo upravit pracovní uzly.

Vzhledem k tomu, že uzly clusterů zákazníka spouštějí soukromý kód a ukládají citlivá data, podpora Microsoftu k nim přistupovat jenom tak, že jsou jenom omezené. Podpora Microsoftu se nemůže přihlásit, spustit příkazy v nebo zobrazit protokoly pro tyto uzly bez výslovného svolení nebo asistence zákazníka.

Vzhledem k tomu, že jsou pracovní uzly citlivé, společnost Microsoft má skvělou péči o omezení správy na pozadí. V mnoha případech se vaše úlohy i nadále spouštějí i v případě, že se Kubernetes hlavní uzly, etcd a další komponenty spravované společností Microsoft selžou. Carelessly změněné pracovní uzly můžou způsobit ztráty dat a zatížení a může cluster vykreslovat nepodporovatelné.

## <a name="aks-support-coverage"></a>Pokrytí podpory AKS

Společnost Microsoft poskytuje technickou podporu pro následující:

> [!NOTE]
> Všechny akce clusteru prováděné Microsoftem a AKS se provádí s souhlasem uživatele v rámci předdefinované Kubernetes role `aks-service` a předdefinované vazby role `aks-service-rolebinding` . Tato role umožňuje AKS řešit a diagnostikovat problémy s clustery, ale nemůže měnit oprávnění ani vytvářet role nebo vazby rolí ani jiné akce s vysokou úrovní oprávnění. Přístup k rolím je povolený jenom v aktivních lístkech podpory s přístupem JIT (just-in-time).

* Připojení ke všem součástem Kubernetes, které poskytuje a podporuje služba Kubernetes, jako je třeba server rozhraní API.
* Správa, doba provozu, technologie QoS a operace Kubernetesch řídicích rovin služeb (například hlavní uzly Kubernetes, Server rozhraní API, etcd a Kube-DNS).
* Etcd. Podpora zahrnuje pro plánování havárií a obnovení stavu clusteru automatizované a transparentní zálohy všech etcd dat každých 30 minut. Tyto zálohy nejsou přímo dostupné pro zákazníky nebo uživatele. Zajišťují spolehlivost a konzistenci dat. Etcd. vrácení zpět nebo obnovení na vyžádání není podporováno jako funkce.
* Všechny integrační body v ovladači Azure Cloud Provider pro Kubernetes. Mezi ně patří integrace do jiných služeb Azure, jako jsou nástroje pro vyrovnávání zatížení, trvalé svazky nebo sítě (Kubernetes a Azure CNI).
* Otázky nebo problémy týkající se přizpůsobení komponent roviny ovládacího prvku, jako je například server Kubernetes API, etcd a Kube-DNS.
* Problémy týkající se sítě, jako je Azure CNI, kubenet nebo další problémy s přístupem a funkcemi pro přístup k síti. Problémy by mohly zahrnovat překlad DNS, ztrátu paketů, směrování atd. Microsoft podporuje různé scénáře sítě:
  * Kubenet (základní) a pokročilé sítě (Azure CNI) v rámci clusteru a přidružených součástí
  * Připojení k ostatním službám a aplikacím Azure
  * Konfigurace řadičů příchozího přenosu dat a příchozího přenosu dat nebo nástroje pro vyrovnávání zatížení
  * Výkon a latence sítě

Společnost Microsoft neposkytuje technickou podporu pro následující:

* Dotazy týkající se použití Kubernetes Podpora Microsoftu například neposkytuje Rady k vytváření vlastních řadičů příchozího přenosu dat, použití aplikačních úloh nebo k použití softwarových balíčků nebo nástrojů třetích stran nebo open source.
  > [!NOTE]
  > Podpora Microsoftu může radit na funkce, přizpůsobení a ladění clusteru AKS (například problémy s Kubernetes operací a postupy).
* Projekty Open Source třetích stran, které nejsou poskytovány jako součást plochy ovládacího prvku Kubernetes nebo nasazeny s clustery AKS. Tyto projekty mohou zahrnovat Istio, Helm, zástupné nebo jiné.
  > [!NOTE]
  > Microsoft může poskytovat nejlepší podporu pro open source projekty třetích stran, jako jsou Helm a Kured. Pokud se Open source nástroj od jiného výrobce integruje s Kubernetes poskytovatelem cloudu Azure nebo jinými chybami týkajícími se AKS, Microsoft podporuje v dokumentaci Microsoftu příklady a aplikace.
* Uzavřený zdrojový software třetí strany. Tento software může obsahovat nástroje pro kontrolu zabezpečení a síťová zařízení nebo software.
* Problémy týkající se více než cloudových nebo více dodavatelů sestavení Microsoft například nepodporuje problémy související se spouštěním řešení pro Cloud ve federaci s více veřejnými cloudy.
* Vlastní nastavení sítě, kromě těch, která jsou uvedena v [dokumentaci k AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Společnost Microsoft podporuje problémy a chyby související se skupinami zabezpečení sítě (skupin zabezpečení sítě). Podpora Microsoftu například může zodpovědět otázky týkající se selhání aktualizace nebo neočekávaného NSG nebo nástroje pro vyrovnávání zatížení.

## <a name="aks-support-coverage-for-worker-nodes"></a>Pokrytí podpory AKS pro pracovní uzly

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Odpovědnosti Microsoftu pro pracovní uzly AKS

Microsoft a zákazníci mají odpovědnost za pracovní uzly Kubernetes, kde:

* Základní image operačního systému obsahuje požadované dodatky (například monitorování a síťové agenty).
* Pracovní uzly automaticky dostanou opravy operačního systému.
* Problémy s komponentou roviny ovládacího prvku Kubernetes, které běží na pracovních uzlech, jsou automaticky opraveny. Součástí jsou tyto součásti:
  * Kube – proxy
  * Síťové tunely, které poskytují komunikační cesty k hlavním komponentám Kubernetes
  * Kubelet
  * Docker nebo Moby démon

> [!NOTE]
> Pokud je v pracovním uzlu nefunkční komponenta roviny ovládacího prvku, může tým AKS restartovat jednotlivé součásti nebo celý pracovní uzel. Tyto operace restartování jsou automatizované a poskytují automatickou nápravu běžných problémů. Tato restartování se vyskytují jenom na úrovni _uzlu_ a ne v clusteru, pokud nedochází k nouzové údržbě nebo výpadku.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Odpovědnosti zákazníků pro pracovní uzly AKS

Společnost Microsoft neprovede automatické restartování pracovních uzlů, aby bylo možné použít opravy na úrovni operačního systému. I když jsou opravy operačního systému doručovány do pracovních uzlů, *Zákazník* zodpovídá za restartování pracovních uzlů, aby změny projevily. Sdílené knihovny, démoni, jako je například hybridní jednotka SSD (SSHD) a další komponenty na úrovni systému nebo operačního systému, jsou automaticky opraveny.

Zákazníci zodpovídají za provádění upgradů Kubernetes. Upgrady můžou provádět upgrady přes ovládací panel Azure nebo Azure CLI. To platí pro aktualizace, které obsahují vylepšení zabezpečení nebo funkcí Kubernetes.

#### <a name="user-customization-of-worker-nodes"></a>Uživatelské přizpůsobení pracovních uzlů
> [!NOTE]
> Pracovní uzly AKS se zobrazují v Azure Portal jako běžné prostředky Azure IaaS. Tyto virtuální počítače se ale nasadí do vlastní skupiny prostředků Azure (s předponou MC \\ *). Je možné rozšířit AKS pracovní uzly ze svých základních konfigurací. Například můžete pomocí Secure Shell (SSH) změnit pracovní uzly AKS způsobem, jakým měníte normální virtuální počítače. Základní image operačního systému ale nemůžete změnit. Jakékoli vlastní změny se nemusí uchovávat při upgradu, škálování, aktualizaci nebo restartování. Změny *mimo pásmo a mimo rozsah rozhraní AKS API* **způsobí, že**se cluster AKS stane nepodporovaným. Vyhněte se měnícím se uzlům pracovních procesů, pokud podpora Microsoftu vás neprovede změny.

Vydávají se nepodporované operace, jak je definováno výše, jako je například vzdálené zrušení přidělení všech uzlů agentů, vykreslí cluster nepodporovaný. AKS si vyhrazuje právo na archivaci řídicích rovin, které byly nakonfigurovány podle pokynů k podpoře pro rozšířené tečky, které se rovnají nebo přesahují 30 dnů. AKS udržuje zálohy metadat clusterových etcd a můžou snadno znovu přidělit cluster. Toto přerozdělení můžete iniciovat jakoukoli operací PUT, která cluster přenese zpátky do podpory, jako je například upgrade nebo škálování na aktivní uzly agentů.

AKS spravuje životní cyklus a operace pracovních uzlů jménem zákazníků – Změna prostředků IaaS přidružených k pracovním uzlům **se nepodporuje.** Příkladem nepodporované operace je přizpůsobení sady škálování virtuálního počítače fondu uzlů ručním změnou konfigurací v VMSS prostřednictvím portálu VMSS nebo rozhraní VMSS API.
 
V případě konfigurací specifických pro úlohy nebo balíčků doporučuje AKS používat [Kubernetes daemonsets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

Použití Kubernetes privilegovaných daemonsets a inicializačních kontejnerů umožňuje zákazníkům ladit/upravovat nebo instalovat software třetích stran na uzlech pracovních procesů clusteru. Příklady takových úprav zahrnují přidání vlastního softwaru pro kontrolu zabezpečení nebo aktualizace nastavení sysctl.

I když se jedná o doporučený postup, pokud se výše uvedené požadavky uplatní, AKS Engineering and Support nemůže pomoct při řešení potíží nebo diagnostikování přerušených/nefunkčních úprav nebo těch, které vykreslí uzel nedostupným z důvodu daemonset nasazeného zákazníka.

> [!NOTE]
> AKS jako *spravovaná služba* má koncové cíle, jako je například odebrání zodpovědnosti za opravy, aktualizace a shromažďování protokolů, aby byla správa služeb úplnější a byla vypnuta. Vzhledem k tomu, že se zvyšuje kapacita služby pro komplexní správu, můžou budoucí verze vynechat některé funkce (například restartování uzlu a automatické opravy).

### <a name="security-issues-and-patching"></a>Problémy se zabezpečením a opravy

Pokud je v jedné nebo více součástech AKS zjištěna chyba zabezpečení, tým AKS opraví všechny ovlivněné clustery, aby problém zmírnit. Případně tým poskytne pokyny k upgradu uživatelů.

V případě pracovních uzlů, které mají vliv na chybu zabezpečení, se v případě, že je k dispozici oprava s nulovou dobou výpadku, AKS tým použije tuto opravu a upozorní uživatele na změnu.

Pokud oprava zabezpečení vyžaduje restartování uzlu pracovního procesu, společnost Microsoft bude informovat zákazníky tohoto požadavku. Zákazník zodpovídá za restartování nebo aktualizaci, aby získal opravu clusteru. Pokud uživatelé nepoužijí opravy podle pokynů AKS, jejich cluster bude i nadále ohrožen problémem se zabezpečením.

### <a name="node-maintenance-and-access"></a>Údržba uzlů a přístup

Pracovní uzly jsou sdílenou zodpovědností a jsou vlastněné zákazníky. Z tohoto důvodu se zákazníci můžou přihlásit ke svým pracovním uzlům a dělat potenciálně škodlivé změny, jako jsou například aktualizace jádra a instalace nebo odebrání balíčků.

Pokud si zákazníci vznikne destruktivní změny nebo způsobí, že se komponenty roviny řízení vrátí do režimu offline nebo nebudou fungovat, AKS detekuje tuto chybu a automaticky obnoví pracovní uzel do předchozího funkčního stavu.

I když se zákazníci můžou přihlásit k pracovním uzlům a měnit je, nedoporučuje se to udělat, protože změny můžou cluster nepodporovat.

## <a name="network-ports-access-and-nsgs"></a>Síťové porty, přístup a skupin zabezpečení sítě

AKS má jako spravované služby určité požadavky na síť a připojení. Tyto požadavky jsou pro normální komponenty IaaS méně flexibilní než požadavky. V AKS operace, jako je přizpůsobení pravidel NSG, blokování konkrétního portu (například pomocí pravidel brány firewall, které blokují odchozí port 443) a povolených adres URL může být váš cluster nepodporují.

> [!NOTE]
> AKS v současné době neumožňuje úplně uzamknout výstupní provoz z vašeho clusteru. Pokud chcete řídit seznam adres URL a portů, které může váš cluster používat pro odchozí přenosy, přečtěte si téma omezení odchozího [provozu](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nepodporované funkce Alpha a beta Kubernetes

AKS podporuje pouze stabilní funkce v rámci nadřazeného Kubernetes projektu. Pokud není uvedeno jinak, nepodporuje AKS funkce Alpha a beta, které jsou k dispozici v nadřazeném projektu Kubernetes.

Ve dvou scénářích může být funkce Alpha nebo beta zavedena před tím, než je všeobecně dostupná:

* Zákazníci splnili AKS produkt, podporu nebo technické týmy a byli požádáni o vyzkoušení těchto nových funkcí.
* Tyto funkce jsou [povolené příznakem funkce](https://github.com/Azure/AKS/blob/master/previews.md). Zákazníci musí explicitně vyjádřit výslovný souhlas s používáním těchto funkcí.

## <a name="preview-features-or-feature-flags"></a>Funkce náhledu nebo příznaky funkcí

Pro funkce a funkce, které vyžadují rozšířené testování a zpětnou vazbu od uživatelů, vydává společnost Microsoft nové funkce nebo funkce verze Preview za příznakem funkce. Tyto funkce zvažte jako předprodejní nebo beta funkce.

Funkce verze Preview nebo funkce příznaku funkcí nejsou určeny pro produkční prostředí. Probíhající změny v rozhraních API a chování, opravy chyb a další změny můžou vést k nestabilním clusterům a výpadkům.

Funkce ve verzi Public Preview spadají pod podporu "nejlepšího úsilí", protože tyto funkce jsou ve verzi Preview a nejsou určené pro produkční prostředí a jsou podporovány AKS pracovníky technické podpory pouze v pracovní době. Další informace najdete v těchto tématech:

* [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Funkce ve verzi Preview se projeví na úrovni *předplatného* Azure. Neinstalujte funkce verze Preview v produkčním předplatném. V produkčním předplatném můžou funkce ve verzi Preview změnit výchozí chování rozhraní API a ovlivnit běžné operace.

## <a name="upstream-bugs-and-issues"></a>Naproti chybám a problémům

S ohledem na rychlost vývoje v nadřazeném projektu Kubernetes dojde k chybám invariably. Některé z těchto chyb nelze v systému AKS opravovat ani odpracovat. Místo toho opravy chyb vyžadují pro nadřazené projekty větší opravy (například Kubernetes, uzel nebo pracovní operační systémy a jádra). Pro součásti, které Microsoft vlastní (například poskytovatel cloudu Azure), se AKS a zaměstnanci Azure zavazují opravit problémy v komunitě.

Pokud je problém s technickou podporou rootem z důvodu jedné nebo více chyb typu nadřazený-proud, podpora AKS a technické týmy budou:

* Identifikujte a propojte chyby nadřazeného streamu s případnými podpůrnými podrobnostmi, které vám pomůžou vysvětlit, proč tento problém ovlivňuje váš cluster nebo úlohu. Zákazníci obdrží odkazy na požadovaná úložiště, aby mohli sledovat problémy, a zjistit, kdy bude nová verze poskytovat opravy.
* Poskytněte možná alternativní řešení nebo zmírnění rizik. Pokud se problém dá zmírnit, v úložišti AKS se zaznamená [známý problém](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) . Popsání řešení známých problémů:
  * Problém, včetně odkazů na chyby proti chybě
  * Alternativní řešení a podrobnosti o upgradu nebo jiné trvalosti řešení.
  * Hrubá časová osa pro zařazení problému na základě tempo verze pro odesílání dat.
