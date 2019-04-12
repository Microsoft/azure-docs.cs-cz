---
title: Zásady podpory Azure Kubernetes Service (AKS)
description: Další informace o zásady poskytování podpory Azure Kubernetes Service (AKS), sdílená odpovědnost, verze Preview/Alpha/beta verze funkce.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502170"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Zásady podpory Azure Kubernetes Service (AKS)

Tento článek obsahuje podrobnosti o AKS technickou podporu zásad omezení, a podrobnosti, včetně správy uzlu pracovního procesu, spravované řízení roviny komponenty, open source komponenty třetích stran a zabezpečení / Správa oprav.

## <a name="service-updates--releases"></a>Aktualizace služeb a vydané verze

* Informace o verzi, najdete v článku [poznámky k verzi AKS][1]
* Funkce ve verzi public preview, najdete v části [funkce AKS ve verzi Preview a související projekty][2]

## <a name="what-is-managed"></a>Co je "spravované"

Na rozdíl od základní IaaS cloudové komponenty jako jsou výpočetní prostředky ani prostředky sítě, které zpřístupňují nízké úrovně ovládací prvky a možnosti vlastního nastavení pro uživatele využívají, poskytuje služby AKS nasazení Kubernetes klíč, který představuje společnou sadu konfigurací a funkce potřebné pro Kubernetes. Zákazníci, kteří používají tato služba mít omezený počet přizpůsobení, nasazení a další možnosti. To také znamená, že zákazníci nemusíte starat nebo přímé správě clusterů Kubernetes.

Se službou AKS získá plně spravovaná zákazníkem **rovina řízení** – kde rovina řízení obsahuje všechny komponenty a služby nezbytné k provozu a koncovým uživatelům poskytovat clustery Kubernetes. Všechny součásti Kubernetes jsou udržované a provozované společností Microsoft.

S managed **rovina řízení** následující součásti jsou spravovaná a sledování od Microsoftu:

* Kubelet / Server (servery) rozhraní Kubernetes API
* Etcd nebo kompatibilní úložiště klíč/hodnota – včetně kvality služeb, škálovatelnost a modulu runtime
* Služby DNS (například kube-dns / CoreDNS)
* Kubernetes Proxy/sítě

AKS není 100 % managed **clusteru** řešení. Některé součásti (například pracovní uzly) mají určité **sdílených odpovědností** případy, kdy akce pro zachování clusteru AKS vyžadovat zásah uživatele. Například pracovní uzel použití opravy zabezpečení operačního systému.

 **Spravované**, znamená, že Microsoftem a týmem AKS nasadí, funguje a je zodpovědný za dostupnost a funkce z těchto služeb. **Zákazníci nelze změnit tyto součásti**. Přizpůsobení je omezená na zajistit konzistentní a škálovatelné uživatelské prostředí. Plně přizpůsobitelná řešení, najdete v části [AKS-Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Je důležité pochopit, pracovních uzlů Azure Kubernetes Service se zobrazí na webu Azure Portal jako běžné prostředky Azure IaaS, i když jsou tyto virtuální počítače nasazeny do vlastní skupiny prostředků Azure (s předponou MC\\*). Uživatel může změnit, SSH do nich stejně jako normální virtuální počítače (nelze však změnit základní image operačního systému, a nemusí změny zachovat prostřednictvím aktualizace nebo po restartování), a můžete k nim připojit dalších prostředků Azure nebo je jinak upravit. **Ale to z Vzdálená správa a přizpůsobení znamená, že samotný cluster AKS se může stát nepoužitelným. Pokud podle Microsoft Support se vyhněte změnou pracovního uzlu.**

## <a name="what-is-shared-responsibility"></a>Co je sdílená odpovědnost

Při vytváření clusteru AKS vytvoří počet pracovních uzlů Kubernetes definované zákazníkem. Tyto uzly, jak je uvedeno jsou, ve kterých se spouští úloh zákazníka. Zákazníci vlastníkem a můžete zobrazit nebo upravit tyto uzly pracovního procesu.

Protože tyto uzly jsou provádění kódu a ukládat citlivá data, má podporu Microsoftu **omezený přístup** zákazníkovi všechny uzly clusteru. Odborné pomoci společnosti Microsoft nelze přihlásit, spusťte příkazy nebo zobrazit protokoly pro tyto uzly bez oprávnění express zákazníka a/nebo pomoc spustit příkazy ladění podle na tým podpory.

Z důvodu citlivosti těchto pracovních uzlů společnost Microsoft má velmi pečlivě omezit správy 'na pozadí' z těchto uzlů. I v případě, že Kubernetes hlavní uzly, etcd a další součásti selhání (spravovaný microsoftem) vašich úloh bude nadále spuštěna v mnoha případech. Pokud pracovní uzly se upraví bez péči, může způsobit ztrátu dat a/nebo úlohy a následek, že cluster nepoužitelným.

## <a name="azure-kubernetes-service-support-coverage"></a>Azure Kubernetes Service podpora je poskytována

**Společnost Microsoft poskytuje technickou podporu pro následující:**

* Připojení pro všechny komponenty Kubernetes poskytuje a podporuje Kubernetes service (jako je například API server)
* Správa, dobu provozu, technologie QoS a provoz systému Kubernetes řídit rovinou služeb (Kubernetes hlavní uzly, rozhraní API serveru etcd, kube-dns pro příklad.
* Podpora Etcd zahrnuje automatizované transparentní zálohování všech dat etcd každých 30 minut pro obnovení po havárii plánování a cluster stavu. Tyto zálohy nejsou k dispozici přímo pro zákazníky a uživatele a lze zajistit spolehlivost dat a konzistence
* Žádné integraci okamžiky ovladač poskytovatele cloudu Azure pro Kubernetes, jako je integrace v rámci jiné službě Azure službami, jako je nástroje pro vyrovnávání zatížení, trvalé svazky sítě (Kubernetes a Azure CNI)
* Dotazy nebo problémy kolem přizpůsobení ovládacího prvku roviny komponenty, například serveru Kubernetes API etcd a kube-dns.
* Problémy s o tématům práce v síti, jako je například Azure CNI, Kubenet nebo jinou síť přístup a funkce problémy (DNS rozlišení, ztráta paketů, směrování a tak dále).
  * Podporované síťové scénáře zahrnují Kubenet (Basic) a rozšířeného sítě (Azure CNI) v rámci clusteru a související součásti, připojení k dalším službám Azure a aplikace. Kromě toho řadiče příchozího přenosu dat a konfiguraci služby Vyrovnávání zatížení s/příchozího přenosu dat, výkon sítě a latence jsou podporovány společností Microsoft.

**Společnost Microsoft neposkytuje technickou podporu pro následující:**

* Poradce / použijte "Postup" Kubernetes otázky, například jak vytvořit vlastní příchozího přenosu dat řadiče, dotazy úlohy aplikace, a balíčky třetích stran/OSS nebo nástroje jsou mimo rozsah.
  * Poradní lístky pro AKS clusteru funkci, přizpůsobení, – například Kubernetes operací ladění problémů/postupy-tos jsou v rámci oboru.
* Třetí strany open-source projektů není zadaný jako součást rozhraní Kubernetes rovina řízení nebo nasadit s využitím clusterů AKS, jako je například Istio, Helm, Envoy a dalších.
  * Pro open source projekty třetích stran, jako jsou Helm a Kured, nejvhodnější podporou poskytuje příklady a aplikace, které jsou k dispozici v dokumentaci společnosti Microsoft a kde open source nástroje třetí strany se integruje s poskytovatelem cloudu Kubernetes Azure nebo jiné chyby specifické pro AKS.
* Zavření source softwarem třetích stran – to může zahrnovat bezpečnostní kontrola nástroje, síťové zařízení nebo software.
* Problémy "multicloudové" nebo více dodavatele sestavení nasazení se nepodporují, například spuštění dodavatele řešení veřejného cloudu s více federativní není podporováno.
* Přizpůsobení konkrétní sítě, než ty, které zdokumentované v oficiálním [dokumentaci ke službě AKS][3].
  > [!NOTE]
  > Problémy a chyby související skupiny zabezpečení sítě je podporována. Například můžete podporu odpovědi na otázky kolem selhání aktualizace skupiny zabezpečení sítě nebo neočekávané chování skupiny zabezpečení sítě nebo nástroje pro vyrovnávání zatížení.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Azure Kubernetes Service podpora je poskytována (počet uzlů pracovního procesu)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Odpovědnosti Microsoftu pro služby Azure Kubernetes Service pracovní uzly

Jak je uvedeno v `shared responsibility` části Kubernetes pracovní uzly spadají do modelu společné odpovědnosti, kde:

* Nabízí základní image operačního systému požadované přidání (třeba monitorování a síťových agentů)
* Automatické distribuce oprav operačního systému k pracovním uzlům
* Automatické nápravy potíží s Kubernetes řídit roviny komponent spuštěných na pracovních uzlech, například:
  * kube-proxy
  * hlavní součásti sítě tunelů, které poskytují komunikační cesty pro Kubernetes
  * kubelet
  * Démon dockeru/moby

> [!NOTE]
> Pokud komponenty rovina řízení je nefunkční na pracovním uzlu, AKS týmu muset restartovat uzel celého pracovního procesu k vyřešení daného problému. Toto je provedeno jménem uživatele a nelze provést, pokud není eskalací zákazníka (termín s přístupem k zákazníkům aktivní úlohy a data). Pokaždé, když bude možné pracovníky AKS práci, abyste provedli požadované restartování, vliv na jiné aplikace.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Odpovědnosti zákazníka pro uzly pracovního procesu Azure Kubernetes Service

**Microsoft nepodporuje:**

- Automatické restartování pracovních uzlů pro operační systém úroveň opravy projevily **(v současné době najdete níže).**

Opravy operačního systému jsou poskytována k pracovním uzlům, i když je **odpovědnosti zákazníka** restartování pracovních uzlů, aby se změny projevily. Tato automatickým opravám zahrnuje sdílené knihovny, procesy démon, jako je například SSHD a další úrovně komponenty systému/OS.

Pro upgrade Kubernetes **zákazníci odpovídají za provedení upgradu** prostřednictvím Azure ovládacích panelech nebo rozhraní příkazového řádku Azure. To platí pro aktualizace obsahující zabezpečení nebo funkce vylepšení Kubernetes.

> [!NOTE]
> Jak je AKS `managed service` naše cíle ukončení služby zahrnují odebrání odpovědnost za opravy, aktualizace, shromažďování protokolů a informace k němu další služby plně spravované a bezobslužnou. Tyto položky (restartování uzlu, automatické opravy) může být odebrán v budoucích verzích rostoucím naše možnosti pro koncové správy.

### <a name="security-issues-and-patching"></a>Problémy se zabezpečením a používání dílčích oprav

V některých případech (například CVEs) Chyba zabezpečení najdete v jednom nebo více součástí služby AKS. V takových případech bude AKS opravy všechny ovlivněné clustery, jak tyto problémy zmírnit, pokud je to možné, nebo uživatelům poskytnout pokyny k upgradu.

Pro pracovní uzly neměly vliv tyto bezpečnostní riziko Pokud je k dispozici nulovými výpadky opravy problému, tým AKS použít opravu a upozornit uživatele změny.

Pokud oprava zabezpečení vyžaduje restartování uzlu pracovního procesu, Microsoft upozorní zákazníky tento požadavek a zákazník zodpovídá provést restartování nebo aktualizace s cílem získat potřebné opravy pro jejich clusteru. Pokud uživatelé se nevztahují opravy podle pokynů AKS, jejich clusteru bude i nadále zranitelné vůči problém(y).

### <a name="node-maintenance-and-access"></a>Uzel Údržba a přístup

Protože pracovní uzly jsou sdílená odpovědnost a pod vlastnictví zákazníkům zákazníkům Přihlaste se do těchto pracovních procesů a provádět potenciálně škodlivé změny, např. aktualizace jádra odebrání balíčků a nainstalovat nové balíčky.

Pokud zákazníci provést destruktivní nebo akce, které aktivují součásti rovina řízení k přechodu do režimu offline nebo jinak budou funkční, službě AKS rozpozná tuto chybu a provádět automatickou nápravu obnovení uzlu pracovního procesu k předchozí pracovní stav.

Přestože Zákazníci můžete přihlásit a alter pracovních uzlů, je *nedoporučuje* vzhledem k tomu, že váš cluster může být nepoužitelným.

## <a name="network-ports-access-and-network-security-groups"></a>Síťové porty, přístup a skupiny zabezpečení sítě

AKS jako spravovaná služba, má specifické požadavky na sítě a připojení. Tyto požadavky jsou méně flexibilní než normální komponenty IaaS. Na rozdíl od jiných komponent IaaS může vykreslit určité operace (například přizpůsobení pravidel skupin zabezpečení sítě, blokování konkrétní port, adresu URL na seznam povolených, a tak dále) nepoužitelným clusteru (např. pravidla brány firewall blokující odchozí port 443).

> [!NOTE]
> Zcela omezovat se jenom výchozí přenos dat (například explicitní domény/port na seznam povolených) z clusteru není podporovaný scénář AKS v tuto chvíli. Seznam adres URL a portů podléhají změnám bez upozornění a můžete poskytované prostřednictvím lístek podpory Azure. Jenom pro zákazníky, kteří jsou ochotni akceptovat, který je zadaný seznam *dostupnost clusteru může mít vliv na kdykoli.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Systém Alpha/beta verze Kubernetes funkcí (nepodporuje se)

AKS podporuje pouze stabilní funkce v rámci nadřazeného projektu Kubernetes. Systém Alpha/beta verze funkce dostupné v upstreamový Kubernetes nejsou podporovány, pokud jinak předávají a uvádí.

Existují dva případy, kdy alfa nebo beta verze funkce může být vrácena před GA:

* Zákazníci splnili s AKS produkt, podporu nebo technické týmy a jste byli požádáni explicitně vyzkoušet tyto nové funkce.
* Tyto funkce byly [povolit prostřednictvím příznak funkce] [ 2] (explicitní vyjádřit výslovný souhlas)

## <a name="preview-features--feature-flags"></a>Funkce ve verzi Preview / příznaků funkcí

Pro funkce, které vyžadují delší testování, community a zpětné vazby uživatelů společnost Microsoft vydá nové funkce ve verzi preview nebo funkce, ovládané příznakem. Tyto funkce by měl být předběžné verze / Beta a jsou přístupné a poskytuje tak uživatelům příležitost vyzkoušet si tyto nové funkce.

Ale tato verze preview / příznak funkce funkce nejsou určené k použití v produkčním prostředí – rozhraní API, změna chování, opravy chyb a další změny provádět, který může způsobit nestabilní clusterů a výpadků. Podpora pro účely těchto funkcí je omezena na hlášení chyb a problémů. Než povolíte tyto funkce na produkční systémy nebo předplatných.

> [!NOTE]
> Povolení funkce ve verzi preview se projeví při Azure **předplatné** úroveň. Neinstalujte funkce ve verzi preview na produkční předplatného, jak ho můžete změnit výchozí chování rozhraní API vliv na běžný provoz.

## <a name="upstream-bugs-and-issues"></a>Nadřazený chyb a problémů

Rychlost vývoje podle nadřazený projekt Kubernetes, jsou vždy chyby, které nejde opravit nebo pracoval kolem v rámci systému AKS a místo toho vyžadují větší opravy na nadřazený projekty (jako je Kubernetes, uzlu/Worker operační systémy a jader). Pro komponenty, které vlastníme, (například poskytovateli cloudu Azure) pracovníci AKS/Azure usilujeme o opravy problému upstream v komunitě.

V případech, kde je problém technické podpory kořenové způsobila na jeden nebo více chyb nadřazený podporují AKS a engineering provede následující položky:

* Identifikujte a propojení nadřazeného chyby s všechny podpůrné podrobnosti o tom, proč to má vliv na váš clusteru a/nebo úlohy. Zákazníci budou zobrazovat odkazy na požadované úložiště/problémy, podívejte se na problémy a podívejte se, když nové vydané verze Kubernetes/další zahrne fix(es)
* Potenciální dokumentovat nebo zmírnění rizik: V některých případech může být problém – v takovém případě je to možné "[známý problém](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)" bude zaznamenaná v úložišti AKS, který vysvětluje:
  * Problém a odkaz na nadřazený chyby
  * Alternativní řešení a podrobnosti o upgradu nebo jiné trvalost řešení
  * Hrubý časové osy pro zahrnutí založené na nadřazený vydávání verzí

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
