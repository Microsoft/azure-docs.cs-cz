---
title: Zásady podpory pro službu Azure Kubernetes Service (AKS)
description: Další informace o zásadách podpory služby Azure Kubernetes Service (AKS), sdílené odpovědnosti a funkcích, které jsou ve verzi preview (nebo alfa nebo beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593576"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zásady podpory pro službu Azure Kubernetes

Tento článek obsahuje podrobnosti o zásadách technické podpory a omezení pro službu Azure Kubernetes Service (AKS). Článek také podrobně popisuje správu uzlů pracovníka, součásti spravované řídicí roviny, součásti s otevřeným zdrojovým kódem jiných výrobců a správu zabezpečení nebo oprav.

## <a name="service-updates-and-releases"></a>Aktualizace a vydání služeb

* Informace o verzi naleznete [v poznámkách k verzi AKS](https://github.com/Azure/AKS/releases).
* Informace o funkcích ve verzi Preview naleznete v tématu [Funkce náhledu AKS a související projekty](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Spravované funkce v AKS

Základní infrastruktura jako součásti cloudu (IaaS), jako jsou výpočetní nebo síťové součásti, poskytují uživatelům přístup k ovládacím prvkům nižší úrovně a možnostem přizpůsobení. Naproti tomu AKS poskytuje nasazení Kubernetes na klíč, které poskytuje zákazníkům společnou sadu konfigurací a funkcí, které potřebují. Zákazníci AKS mají omezené možnosti přizpůsobení, nasazení a další možnosti. Tito zákazníci se nemusí starat o clustery Kubernetes ani je spravovat přímo.

S AKS zákazník získá plně spravované *řídicí roviny*. Řídicí rovina obsahuje všechny součásti a služby, které zákazník potřebuje k provozu a poskytuje clustery Kubernetes koncovým uživatelům. Všechny komponenty Kubernetes jsou udržovány a provozovány společností Microsoft.

Společnost Microsoft spravuje a monitoruje následující součásti prostřednictvím ovládacího podokna:

* Kubelet nebo Kubernetes API servery
* Etcd nebo kompatibilní úložiště hodnot klíčů, které poskytuje kvalitu služeb (QoS), škálovatelnost a runtime
* Služby DNS (například kube-dns nebo CoreDNS)
* Kubernetes proxy nebo sítě

AKS není zcela spravované řešení clusteru. Některé součásti, například pracovní uzly, mají *sdílenou odpovědnost*, kde uživatelé musí pomoci udržovat cluster AKS. Vstup uživatele je vyžadován například k použití opravy zabezpečení operačního systému pracovního uzlu (OS).

Služby jsou *spravovány* v tom smyslu, že Microsoft a tým AKS nasazuje, provozuje a je zodpovědný za dostupnost a funkce služby. Zákazníci nemohou tyto spravované součásti měnit. Společnost Microsoft omezuje přizpůsobení, aby zajistila konzistentní a škálovatelné uživatelské prostředí. Plně přizpůsobitelné řešení naleznete v tématu [AKS Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Pracovní uzly AKS se zobrazují na webu Azure Portal jako běžné prostředky Azure IaaS. Ale tyto virtuální počítače se nasazují do vlastní skupiny prostředků Azure (s předponou MC\\*). Je možné změnit pracovní uzly AKS. Pomocí zabezpečeného prostředí (SSH) můžete například změnit pracovní uzly AKS způsobem změny normálních virtuálních počítačů (nemůžete však změnit základní bitovou kopii operačního systému a změny nemusí přetrvávat prostřednictvím aktualizace nebo restartování) a můžete k AKS připojit další prostředky Azure a připojit k AKS další prostředky Azure pracovníu uzlů. Ale když provedete změny *mimo správu pásma a přizpůsobení,* cluster AKS může být neúnosné. Vyhněte se změně pracovních uzlů, pokud vás podpora společnosti Microsoft nenasměruje k provádění změn.

Vydávání nepodporovaných operací, jak je definováno výše, jako je například mimo pásmo deallocation všech uzlů agenta, způsobí, že cluster není podporován. AKS si vyhrazuje právo archivovat řídicí roviny, které byly nakonfigurovány mimo pokyny podpory po delší dobu rovnou 30 dnům. AKS udržuje zálohy metadat clusteru etcd a může snadno přerozdělit clusteru. Toto přerozdělení může být iniciováno libovolnou operací PUT, která vrátí cluster do podpory, jako je například upgrade nebo škálování na aktivní uzly agentů.

## <a name="shared-responsibility"></a>Společná odpovědnost

Při vytvoření clusteru zákazník definuje pracovní uzly Kubernetes, které vytvoří AKS. Úlohy zákazníků jsou spouštěny na těchto uzlech. Zákazníci vlastní a mohou zobrazit nebo upravit pracovní uzly.

Vzhledem k tomu, že uzly clusteru zákazníka spouštějí soukromý kód a ukládají citlivá data, může k nim podpora společnosti Microsoft přistupovat pouze omezeným způsobem. Podpora společnosti Microsoft se nemůže přihlásit, spouštět příkazy nebo zobrazit protokoly pro tyto uzly bez výslovného oprávnění nebo pomoci zákazníka.

Vzhledem k tomu, že pracovní uzly jsou citlivé, společnost Microsoft velmi dbá na omezení správy na pozadí. V mnoha případech bude vaše úloha nadále spuštěna i v případě, že hlavní uzly Kubernetes atdd a další součásti spravované společností Microsoft nezdaří. Nedbale upravené pracovní uzly mohou způsobit ztráty dat a úloh a může způsobit, že cluster je nepodporující.

## <a name="aks-support-coverage"></a>Pokrytí podpory AKS

Společnost Microsoft poskytuje technickou podporu pro následující:

* Připojení ke všem komponentám Kubernetes, které služba Kubernetes poskytuje a podporuje, jako je například server rozhraní API.
* Správa, provozuna, QoS a provoz služeb Kubernetes řízení roviny (Kubernetes master uzly, API server, atdd, a kube-dns, například).
* Etcd. Podpora zahrnuje automatické, transparentní zálohování všech dat etcd každých 30 minut pro plánování katastrof a obnovení stavu clusteru. Tyto zálohy nejsou přímo k dispozici zákazníkům nebo uživatelům. Zajišťují spolehlivost a konzistenci dat.
* Všechny integrační body v ovladači poskytovatele cloudu Azure pro Kubernetes. Patří mezi ně integrace do jiných služeb Azure, jako jsou nástroje pro vyrovnávání zatížení, trvalé svazky nebo sítě (Kubernetes a Azure CNI).
* Otázky nebo problémy týkající se přizpůsobení komponent roviny ovládacího prvku, jako je například server Api Kubernetes, etcd a kube-dns.
* Problémy týkající se sítí, jako je Azure CNI, kubenet nebo jiné problémy s přístupem k síti a funkce. Problémy mohou zahrnovat překlad DNS, ztrátu paketů, směrování a tak dále. Společnost Microsoft podporuje různé síťové scénáře:
  * Kubenet (základní) a pokročilé sítě (Azure CNI) v rámci clusteru a přidružené součásti
  * Připojení k dalším službám a aplikacím Azure
  * Řadiče příchozích dat a konfigurace ingress nebo load balancer
  * Výkon a latence sítě

Společnost Microsoft neposkytuje technickou podporu pro následující:

* Otázky o tom, jak používat Kubernetes. Podpora společnosti Microsoft například neposkytuje rady, jak vytvořit vlastní řadiče příchozího přenosu dat, používat úlohy aplikací nebo použít softwarové balíčky nebo nástroje třetích stran nebo softwaru s otevřeným zdrojovým kódem.
  > [!NOTE]
  > Podpora společnosti Microsoft může poradit ohledně funkcí clusteru AKS, přizpůsobení a ladění (například problémy a postupy operací Kubernetes).
* Projekty s otevřeným zdrojovým kódem jiných výrobců, které nejsou k dispozici jako součást řídicí roviny Kubernetes nebo nasazeny s clustery AKS. Tyto projekty mohou zahrnovat Istio, Helm, vyslance nebo jiné.
  > [!NOTE]
  > Společnost Microsoft může poskytovat podporu s maximálním úsilím pro projekty s otevřeným zdrojovým kódem třetích stran, jako je Helm a Kured. V případě, že se nástroj s otevřeným zdrojovým kódem od jiného výrobce integruje s poskytovatelem cloudu Kubernetes Azure nebo jinými chybami specifickými pro AKS, podporuje Microsoft příklady a aplikace z dokumentace microsoftu.
* Software pro uzavřený zdroj třetích stran. Tento software může zahrnovat bezpečnostní skenovací nástroje a síťová zařízení nebo software.
* Problémy s multicloud nebo multivendor build-outs. Společnost Microsoft například nepodporuje problémy související se spuštěním federovaného řešení víceveřejných cloudových dodavatelů.
* Jiná nastavení sítě než uvedená v [dokumentaci k systému AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Společnost Microsoft podporuje problémy a chyby související se skupinami zabezpečení sítě (NSGs). Podpora společnosti Microsoft může například odpovědět na otázky týkající se selhání aktualizace skupiny zabezpečení zabezpečení nebo neočekávaného chování skupiny zabezpečení nebo vyrovnávání zatížení.

## <a name="aks-support-coverage-for-worker-nodes"></a>Pokrytí podpory AKS pro pracovní uzly

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Odpovědnosti společnosti Microsoft pro pracovní uzly AKS

Microsoft a zákazníci sdílejí odpovědnost za pracovní uzly Kubernetes, kde:

* Základní bitová kopie operačního systému vyžaduje dodatky (například monitorování a síťové agenty).
* Pracovní uzly přijímat opravy operačního spoje automaticky.
* Problémy s součástmi řídicí roviny řízení Kubernetes, které běží na pracovních uzlech, jsou automaticky opraveny. Součásti zahrnují následující:
  * Kube-proxy
  * Síťové tunely, které poskytují komunikační cesty k hlavním součástem Kubernetes
  * Kubelet
  * Docker nebo Moby daemon

> [!NOTE]
> Na pracovní uzel, pokud součást roviny ovládacího prvku není funkční, tým AKS může být nutné restartovat jednotlivé součásti nebo celý pracovní uzel. Tyto operace restartování jsou automatizované a poskytují automatickou nápravu běžných problémů. Tyto restartování dochází pouze na úrovni _uzlu_ a nikoli clusteru, pokud není nouzová údržba nebo výpadek.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Odpovědnosti zákazníků pro pracovní uzly AKS

Společnost Microsoft automaticky nerestartuje pracovní uzly, aby použila opravy na úrovni operačního systému. Přestože opravy operačního systému jsou doručovány do pracovních uzlů, *zákazník* je zodpovědný za restartování pracovních uzlů použít změny. Sdílené knihovny, daemony, jako je ssd hybridní jednotka (SSHD) a další součásti na úrovni systému nebo operačního systému jsou automaticky opraveny.

Zákazníci jsou zodpovědní za provádění upgradů společnosti Kubernetes. Můžou spouštět upgrady prostřednictvím ovládacího panelu Azure nebo v příkazovém příkazovém příkazovém příkazu Azure. To platí pro aktualizace, které obsahují vylepšení zabezpečení nebo funkcí kubernetes.

> [!NOTE]
> Vzhledem k tomu, že AKS je *spravovaná služba*, její konečné cíle zahrnují odebrání odpovědnosti za opravy, aktualizace a shromažďování protokolů, aby byla správa služeb úplnější a praktická. Jak se zvyšuje kapacita služby pro správu od konce, budoucí verze mohou vynechat některé funkce (například restartování uzlu a automatické opravy).

### <a name="security-issues-and-patching"></a>Problémy se zabezpečením a opravy

Pokud je nalezena chyba zabezpečení v jedné nebo více součástech AKS, tým AKS opraví všechny ovlivněné clustery, aby se problém zmírnil. Případně tým poskytne uživatelům pokyny k upgradu.

Pro pracovní uzly, které má vliv na chybu zabezpečení, pokud je k dispozici oprava s nulovými prostoji, tým AKS použije tuto opravu a upozorní uživatele na změnu.

Pokud oprava zabezpečení vyžaduje restartování pracovního uzlu, společnost Microsoft na tento požadavek upozorní zákazníky. Zákazník je zodpovědný za restartování nebo aktualizaci získat opravu clusteru. Pokud uživatelé nepoužijí opravy podle pokynů AKS, jejich cluster bude i nadále zranitelný vůči problému se zabezpečením.

### <a name="node-maintenance-and-access"></a>Údržba uzlů a přístup

Pracovní uzly jsou sdílenou odpovědností a jsou vlastněny zákazníky. Z tohoto důvodu mají zákazníci možnost přihlásit se ke svým pracovním uzlům a provádět potenciálně škodlivé změny, jako jsou aktualizace jádra a instalace nebo odebrání balíčků.

Pokud zákazníci provést destruktivní změny nebo způsobit součásti roviny ovládacího prvku přejít do offline nebo se stanou nefunkční, AKS zjistí tuto chybu a automaticky obnovit pracovní uzel do předchozího pracovního stavu.

Přestože zákazníci mohou přihlásit a změnit pracovní uzly, to se nedoporučuje, protože změny mohou způsobit, že cluster je nepodporující.

## <a name="network-ports-access-and-nsgs"></a>Síťové porty, přístup a sítě nsg

Jako spravovaná služba má AKS specifické požadavky na síť a připojení. Tyto požadavky jsou méně flexibilní než požadavky na běžné součásti IaaS. V AKS operace, jako je přizpůsobení pravidel skupiny nsg, blokování konkrétního portu (například pomocí pravidel brány firewall, které blokují odchozí port 443) a adresy URL seznamu povolených adres mohou způsobit, že váš cluster bude nepodporující.

> [!NOTE]
> V současné době AKS neumožňuje zcela uzamknout odchozí provoz z clusteru. Chcete-li řídit seznam adres URL a portů, které může cluster použít pro odchozí provoz, viz [omezení odchozího přenosu](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nepodporované funkce alfa a beta Kubernetes

AKS podporuje pouze stabilní funkce v rámci projektu Upstream Kubernetes. Pokud není dokumentováno jinak, AKS nepodporuje alfa a beta funkce, které jsou k dispozici v projektu Kubernetes.

Ve dvou scénářích mohou být funkce alfa nebo beta zavedeny dříve, než jsou obecně dostupné:

* Zákazníci se setkali s týmy produktů, podpory nebo inženýrských týmů AKS a byli požádáni, aby vyzkoušeli tyto nové funkce.
* Tyto funkce byly [povoleny příznakem funkce](https://github.com/Azure/AKS/blob/master/previews.md). Zákazníci se musí explicitně přihlásit k používání těchto funkcí.

## <a name="preview-features-or-feature-flags"></a>Náhled funkcí nebo příznaků funkcí

Pro funkce a funkce, které vyžadují rozšířené testování a zpětnou vazbu od uživatelů, společnost Microsoft vydává nové funkce náhledu nebo funkce za příznakem funkce. Zvažte tyto funkce jako předběžné verze nebo beta funkce.

Funkce náhledu nebo funkce příznaku nejsou určeny pro produkční prostředí. Probíhající změny v api a chování, opravy chyb a další změny může mít za následek nestabilní clustery a prostoje.

Funkce ve verzi Public Preview spadají pod podporu "nejlepšího úsilí", protože tyto funkce jsou ve verzi preview a nejsou určeny pro produkční prostředí a jsou podporovány týmy technické podpory AKS pouze během pracovní doby. Další informace naleznete na adrese:

* [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Funkce náhledu se projeví na úrovni *předplatného* Azure. Neinstalujte funkce náhledu do produkčního předplatného. V produkčním předplatném mohou funkce náhledu změnit výchozí chování rozhraní API a ovlivnit běžné operace.

## <a name="upstream-bugs-and-issues"></a>Protiproudu chyby a problémy

Vzhledem k rychlosti vývoje v projektu Kubernetes proti proudu neustále vznikají chyby. Některé z těchto chyb nelze opravit nebo opracovávat v rámci systému AKS. Místo toho opravy chyb vyžadují větší opravy upstream projektů (například Kubernetes, uzel nebo pracovní operační systémy a jádra). U komponent, které Microsoft vlastní (například poskytovatele cloudu Azure), se pracovníci AKS a Azure zavázali k řešení problémů v komunitě.

Pokud je problém technické podpory způsoben kořenem jedné nebo více chyb proti proudu, týmy podpory AKS a inženýrských týmů:

* Identifikujte a propojte protiproudové chyby s podpůrnými podrobnostmi, které vám pomohou vysvětlit, proč tento problém ovlivňuje váš cluster nebo pracovní vytížení. Zákazníci obdrží odkazy na požadované repozitáře, aby mohli sledovat problémy a zjistit, kdy nová verze poskytne opravy.
* Poskytují potenciální řešení nebo skutečnosti snižující závažnost rizika. Pokud lze problém zmírnit, bude v úložišti AKS podán [známý problém.](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) Podání známého problému vysvětluje:
  * Problém, včetně odkazů na upstream chyby.
  * Řešení a podrobnosti o upgradu nebo jiné trvalost řešení.
  * Hrubé časové osy pro zařazení problému na základě kadence předcházejícího vydání.
