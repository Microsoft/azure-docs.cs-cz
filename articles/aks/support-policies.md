---
title: Zásady podpory pro Azure Kubernetes Service (AKS)
description: Další informace o zásady poskytování podpory Azure Kubernetes Service (AKS), sdílená odpovědnost a funkce, které jsou ve verzi preview (nebo alfa nebo beta verze).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 0d2c080be727d2ae13d6d9e5274f17cadffbe640
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786460"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zásady podpory pro službu Azure Kubernetes Service

Tento článek obsahuje podrobnosti o zásady poskytování technické podpory a omezení pro službu Azure Kubernetes Service (AKS). Článek podrobně popisuje také správu uzlů pracovního procesu, komponenty roviny spravovatelného ovládacího prvku, open source komponenty třetích stran a správy zabezpečení nebo opravy.

## <a name="service-updates-and-releases"></a>Aktualizace služeb a verzí

* Informace o verzi, naleznete v tématu [poznámky k verzi AKS](https://github.com/Azure/AKS/releases).
* Informace o funkcích ve verzi preview najdete v tématu [AKS ve verzi preview funkce a související projekty](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Spravované funkce ve službě AKS

Základní infrastruktura jako služba (IaaS) cloudové komponenty, jako jsou výpočetní prostředky ani prostředky síťových součástí, uživatelům přístup k nižší úrovně ovládací prvky a možnosti přizpůsobení. Naopak AKS poskytuje kompletní nasazení Kubernetes, který poskytuje zákazníkům společnou sadu konfigurace a možnosti, které potřebují. AKS zákazníci mají omezenou přizpůsobení, nasazení a další možnosti. Tito zákazníci nemusíte starat o nebo spravovat clustery Kubernetes přímo.

Se službou AKS získá plně spravovaná zákazníkem *rovina řízení*. Rovina řízení obsahuje všechny komponenty a služby, které zákazník potřebuje k provozu a koncovým uživatelům poskytovat clustery Kubernetes. Všechny součásti Kubernetes jsou udržované a provozované společností Microsoft.

Microsoft spravuje a monitoruje následující součásti přes ovládací panel:

* Servery Kubelet nebo rozhraní API Kubernetes
* Etcd nebo kompatibilní párů klíč hodnota, poskytování služby (QoS), škálovatelnost a modulu runtime
* Služby DNS (například kube-dns nebo CoreDNS)
* Kubernetes proxy nebo síťový protokol

AKS není plně spravovaný cluster řešení. Mají některé komponenty, jako je například pracovních uzlů, *sdílenou odpovědnost*, ve kterém musí uživatelé pomáhají udržovat clusteru AKS. Uživatelský vstup je potřeba, například použití opravy zabezpečení operačního systému (OS) uzlu pracovního procesu.

Služby jsou *spravované* v tom smyslu, že Microsoftem a týmem AKS nasadí, funguje a je zodpovědný za dostupnost služby a funkce. Zákazníci nelze změnit tyto spravované součásti. Microsoft omezuje přizpůsobení zajistit konzistentní a škálovatelné uživatelské prostředí. Plně přizpůsobitelná řešení, najdete v části [AKS modul](https://github.com/Azure/aks-engine).

> [!NOTE]
> Pracovní uzly AKS se zobrazí na webu Azure Portal jako běžné prostředky Azure IaaS. Ale tyto virtuální počítače se nasazují do vlastní skupinu prostředků Azure (s předponou MC\\*). Je možné změnit pracovních uzlů AKS. Například můžete použít Secure Shell (SSH) Chcete-li změnit pracovních uzlů AKS tak, jak změnit normální virtuální počítače (nelze však změnit základní image operačního systému, a nemusí změny zachovat prostřednictvím aktualizace nebo po restartování), a dalších prostředků Azure můžete připojit k AKS pracovní uzly. Když provedete změny, ale *Vzdálená správa a přizpůsobení,* clusteru AKS se může stát nepoužitelným. Neměňte pracovních uzlů, pokud Microsoft Support přesměruje vám provádět změny.

## <a name="shared-responsibility"></a>Sdílená odpovědnost

Při vytvoření clusteru se zákazník definuje Kubernetes pracovních uzlů, které vytvoří AKS. Na tyto uzly jsou spouštěny úloh zákazníka. Zákazníci vlastníkem a můžete zobrazit nebo upravit pracovní uzly.

Vzhledem k tomu, že uzly clusteru zákazníka spuštění kódu a ukládat citlivá data, Microsoft Support můžete k nim přistupovat pouze omezeným způsobem. Microsoft Support nelze přihlásit, spusťte příkazy v, nebo zobrazit protokoly pro tyto uzly bez oprávnění express zákazníků a pomoc.

Protože pracovní uzly jsou citlivé, společnost Microsoft má velmi pečlivě omezit jejich správu na pozadí. V mnoha případech vašich úloh bude nadále spuštěna i v případě, že Kubernetes hlavní uzly, etcd a Další selhání komponenty spravovaných společností Microsoft. Neuváženě upravenou pracovní uzly můžou způsobit ztrátám dat a úloh a může mít za následek clusteru nepoužitelným.

## <a name="aks-support-coverage"></a>Rozsah podpory AKS

Společnost Microsoft poskytuje technickou podporu pro následující:

* Připojení pro všechny součásti Kubernetes, které služba Kubernetes poskytuje a podporuje, jako je například API server.
* Správa, dobu provozu, technologie QoS a provoz systému Kubernetes ovládat roviny služby (hlavní uzly Kubernetes, rozhraní API serveru, etcd a kube-dns, třeba).
* Etcd. Podpora zahrnuje automatizované transparentní zálohování všech dat etcd každých 30 minut pro obnovení po havárii plánování a cluster stavu. Tyto zálohy nejsou přímo dostupné pro zákazníky a uživatele. Zajišťují spolehlivost dat a konzistence.
* Integrační body se v ovladači poskytovatele cloudu Azure pro Kubernetes. Mezi ně patří integrace do jiných služeb Azure, například nástroje pro vyrovnávání zatížení, trvalé svazků nebo síťový protokol (Kubernetes a Azure CNI).
* Dotazy nebo potíže o přizpůsobení ovládacího prvku roviny komponenty, například serveru Kubernetes API etcd a kube-dns.
* Potíže s problémy se sítí, jako je Azure CNI, kubenet, nebo jiné přístup k síti a funkce. Problémy mohou zahrnovat DNS rozlišení, ztráta paketů, směrování a tak dále. Společnost Microsoft podporuje různé síťové scénáře:
  * Kubenet (basic) a rozšířeného sítě (Azure CNI) v rámci clusteru a související součásti
  * Připojení k dalším službám Azure a aplikace
  * Kontrolery příchozího přenosu dat a konfigurace nástroje pro vyrovnávání příchozího přenosu dat nebo zatížení
  * Výkon sítě a latence

Společnost Microsoft neposkytuje technickou podporu pro následující:

* Dotazy týkající se použití Kubernetes. Například neposkytuje Microsoft Support Rady o tom, jak vytvořit vlastní příchozího přenosu dat kontrolery, pomocí úloh aplikací nebo použít balíčky třetích stran nebo open source softwaru nebo nástroje.
  > [!NOTE]
  > Microsoft Support můžete získat Rady o AKS fungování clusteru, úprav a ladění (pro příklad, problémů s operací Kubernetes a postupy).
* Třetí strany open-source projektů, které nejsou k dispozici jako součást rozhraní Kubernetes rovina řízení nebo nasadit s využitím clusterů AKS. Tyto projekty může zahrnovat Istio, Helm, Envoy nebo ostatním uživatelům.
  > [!NOTE]
  > Microsoft může poskytovat podporu best effort pro open source projekty třetích stran, jako jsou Helm a Kured. Kde je open source nástroj třetí strany se integruje s poskytovateli cloudu Kubernetes Azure nebo jiné chyby specifické pro AKS, společnost Microsoft podporuje příklady a aplikacemi z dokumentace společnosti Microsoft.
* Zavření source softwarem třetích stran. Tento software může obsahovat nástrojů pro vyhledávání zabezpečení a síťové zařízení nebo software.
* Potíže s multicloud nebo kombinovaných sestavení činnostem. Microsoft nepodporuje například problémy související se spouštěním federované multipublic cloudové řešení pro dodavatele.
* Přizpůsobení kromě oprávnění uvedených v seznamu v síti [dokumentaci ke službě AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft podporuje problémy a chyby související se skupinami zabezpečení sítě (Nsg). Například Microsoft Support můžete odpovídat na otázky týkající se selhání NSG k aktualizaci nebo neočekávané NSG nebo zatížení vyrovnávání chování.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS rozsah podpory pro uzly pracovního procesu

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Odpovědnosti Microsoftu pro pracovníka uzlů AKS

Microsoft a Zákazníci se podělí odpovědnost za pracovní uzly Kubernetes kde:

* Základní image operačního systému vyžaduje přidání (třeba monitorování a síťových agentů).
* Pracovní uzly automaticky přijímat oprav operačního systému.
* Problémy s Kubernetes řídit plochy, ve které jsou automaticky opravuje součásti, které běží na pracovních uzlech. Součásti zahrnují následující:
  * Kube-proxy
  * hlavní součásti sítě tunelů, které poskytují komunikační cesty pro Kubernetes
  * kubelet
  * Démon dockeru nebo Moby

> [!NOTE]
> V pracovního uzlu Pokud není v provozu, komponenty rovina řízení AKS týmu může být nutné restartovat uzel celého pracovního procesu. Z důvodu jejich omezený přístup k active úloh zákazníka a data týmu AKS restartování pracovního uzlu jenom v případě, že zákazník, proto ho postoupí problém. Kdykoli je to možné, zabránit ovlivnění aplikace požadované restartování pracuje tým AKS.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Odpovědnosti zákazníka pro pracovníka uzlů AKS

Microsoft nebude automaticky restartuje pracovní uzly určené k použití opravy na úrovni operačního systému. Přestože se dodávají oprav operačního systému k pracovním uzlům *zákazníka* zodpovídá za restartování pracovních uzlů, aby se změny projevily. Sdílené knihovny, procesy démon, jako jsou jednotky SSD hybridní (SSHD) a další komponenty na úrovni systému nebo operačního systému jsou automaticky opravit.

Zákazníci odpovídají za provádění upgrade Kubernetes. Inovace prostřednictvím ovládacího panelu Azure nebo rozhraní příkazového řádku Azure, můžete spustit. To platí pro aktualizace, které obsahují zabezpečení nebo funkce vylepšení Kubernetes.

> [!NOTE]
> Protože je AKS *spravovaná služba*, jeho end cíle zahrnují odebrání odpovědnost za opravy, aktualizace a protokolovat kolekci, aby rozhraní pro správu služeb víc kompletní a bezobslužnou. Jak se zvyšuje kapacitu služby pro správu začátku do konce budoucích verzích může vynechat některé funkce (například restartování uzlu a automatické opravy).

### <a name="security-issues-and-patching"></a>Problémy se zabezpečením a používání dílčích oprav

Pokud se najde chyba zabezpečení v jedné nebo více součástí AKS, tým AKS opraví všechny ovlivněné clustery, jak tyto problémy zmírnit. Alternativně tým bude uživatelům pokyny k upgradu.

Pro uzly pracovního procesu, je k dispozici zabezpečení chyba má vliv, pokud oprava nulovými výpadky tým AKS tuto opravu a upozornit uživatele změny.

Pokud oprava zabezpečení vyžaduje restartování uzlu pracovního procesu, Microsoft upozorní zákazníky tento požadavek. Zákazník zodpovídá za restartování nebo aktualizace až po získání opravy clusteru. Pokud uživatelé se nevztahují opravy podle pokynů AKS, jejich clusteru budou nadále zranitelné vůči potíže se zabezpečením.

### <a name="node-maintenance-and-access"></a>Uzel Údržba a přístup

Pracovní uzly jsou sdílená odpovědnost a vlastněných zákazníky. Z toho důvodu se zákazníci budou moct přihlásit k jejich pracovním uzlům a potenciálně škodlivých změny jako aktualizace jádra a instalace nebo odebrání balíčků.

Pokud zákazníci měnit destruktivní nebo způsobit, že ovládací prvek komponenty roviny přejdou do režimu offline nebo bude funkční, AKS rozpozná tuto chybu a automaticky obnovit do předchozího stavu pracovního uzlu pracovního procesu.

I když zákazníci můžete přihlásit a změnit pracovních uzlů, to se nedoporučují, protože změny můžete vytvořit cluster nepoužitelným.

## <a name="network-ports-access-and-nsgs"></a>Síťové porty, přístupu a skupin zabezpečení sítě

AKS jako spravovaná služba, má specifické požadavky na sítě a připojení. Tyto požadavky jsou méně flexibilní než požadavky pro běžné součásti IaaS. Ve službě AKS operace, jako jsou pravidla NSG blokuje konkrétní port (například pomocí pravidel brány firewall blokující odchozí port 443) přizpůsobení a přidávání na seznam povolených adres URL můžete vytvořit cluster nepoužitelným.

> [!NOTE]
> V současné době AKS neumožňuje úplně zamezit odchozího přenosu dat z vašeho clusteru (například explicitní domény nebo přidávání na seznam povolených portů). Seznam adres URL a portů se mohou změnit bez předchozího upozornění. Aktualizovaný seznam můžete získat tak, že vytvoříte lístek podpory Azure. V seznamu je jenom pro zákazníky, kteří jsou ochotni akceptovat, že jejich dostupnost clusteru může mít vliv na *kdykoli.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nepodporované funkce alfa a beta verze Kubernetes

AKS podporuje pouze stabilní funkce v rámci nadřazeného projektu Kubernetes. Pokud není uvedeno jinak, nepodporuje AKS alfa a beta verze funkce, které jsou k dispozici v nadřazený projekt Kubernetes.

V obou případech alfa nebo beta verze funkce může být nasazeny v než budou všeobecně dostupné:

* Zákazníci splnili s AKS produkt, podporu nebo technické týmy a jste byli požádáni o vyzkoušejte si tyto nové funkce.
* Tyto funkce byly [umožněné příznak funkce](https://github.com/Azure/AKS/blob/master/previews.md). Zákazníci musí explicitně přihlášení k používání těchto funkcí.

## <a name="preview-features-or-feature-flags"></a>Funkce ve verzi Preview nebo příznaky funkcí

Funkce a funkce, které vyžadují delší testování a zpětná vazba uživatelů společnost Microsoft vydává nové funkce ve verzi preview nebo funkce, ovládané příznakem. Vezměte v úvahu tyto funkce jako zkušební nebo beta verze funkce.

Funkce ve verzi Preview nebo funkce příznak funkce nejsou určené pro produkční prostředí. Probíhající změny v rozhraní API a chování, opravy chyb a další změny může způsobit nestabilní clusterů a výpadků.

Funkce ve verzi public preview jsou fall v rámci podpory "co možná nejlepší" a tyto funkce jsou ve verzi preview a není určená pro produkční prostředí podporuje týmy AKS technickou podporu během pracovní doby pouze. Další informace najdete v tématu:

* [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Funkce ve verzi Preview budou účinné od Azure *předplatné* úroveň. V produkčním předplatném neinstalujte funkce ve verzi preview. Na produkčním předplatném funkce ve verzi preview můžete změnit výchozí chování rozhraní API a mít vliv na běžný provoz.

## <a name="upstream-bugs-and-issues"></a>Nadřazený chyb a problémů

Rychlost vývoje podle nadřazený projekt Kubernetes, chyby vždy nastat. Některé z těchto chyb nelze opravit nebo pracoval kolem v rámci systému AKS. Místo toho opravy vyžadují větší opravy na nadřazený projekty (jako je Kubernetes, uzel nebo pracovní operačních systémů a jader). Pro komponenty, které vlastní společnost Microsoft (například poskytovateli cloudu Azure) AKS a Azure pracovníky usilujeme o řešení problémů upstream v komunitě.

Po problém technické podpory se kořenový způsobené nejmíň jeden nadřazený chyby budou týmy podpory a engineering AKS:

* Identifikujte a propojení nadřazeného chyby s všechny podpůrné podrobnosti k pomoci s vysvětlením, proč tento problém se týká úlohy nebo clusteru. Zákazníci získají odkazy na požadované úložiště, abyste mohli sledovat problémy a zobrazit, když nové verze nabídne opravy.
* Zadejte možných alternativních řešení nebo zmírnění rizik. Pokud tento problém můžete zmírnit, [známý problém](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) budou uloženy v úložišti AKS. Vysvětluje, známý problém vyplňování:
  * Problém, včetně odkazů na nadřazený chyby.
  * Alternativní řešení a podrobné informace o upgradu nebo jiné trvalého řešení.
  * Surové časové osy pro tento problém zařazení, založené na nadřazený vydávání verzí.
