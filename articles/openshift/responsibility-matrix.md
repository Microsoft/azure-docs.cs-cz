---
title: Tabulka přiřazení zodpovědnosti Azure Red Hat OpenShift
description: Přečtěte si o vlastnictví zodpovědnosti za provoz clusteru Azure Red Hat OpenShift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI, RACI, podpora
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537015"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Přehled odpovědností pro Azure Red Hat OpenShift

Tento dokument popisuje zodpovědnosti společnosti Microsoft, Red Hat a zákazníků pro clustery Azure Red Hat OpenShift. Další informace o Azure Red Hat OpenShift a jejích součástech najdete v tématu definice služby Azure Red Hat OpenShift.

I když vám Microsoft a Red Hat spravuje službu Azure Red Hat OpenShift, jsou akcie akcií zodpovědností za funkčnost jejich clusteru. I když jsou clustery Azure Red Hat OpenShift hostované v prostředcích Azure v zákaznických předplatných Azure, ke kterým se dostanete vzdáleně. Základní platforma a zabezpečení dat jsou vlastněné Microsoftem a Red Hat.

## <a name="overview"></a>Přehled
<table>
  <tr>
   <td><strong>Partner</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Správa incidentů a operací</a></strong>
   </td>
   <td><strong><a href="#change-management">Správa změn</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Správa identit a přístupu</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Dodržování předpisů pro zabezpečení a předpisy</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Zákaznická data</a>
   </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Zákaznické aplikace</a>
   </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Vývojářské služby </a>
   </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
   <td>Zákazník </td>
  </tr>
  <tr>
   <td>Monitorování platformy </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>protokolování </td>
   <td>Microsoft a Red Hat </td>
   <td>Shared </td>
   <td>Shared </td>
   <td>Shared </td>
  </tr>
  <tr>
   <td>Aplikační síť </td>
   <td>Shared </td>
   <td>Shared </td>
   <td>Shared </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>Sítě clusteru </td>
   <td>Microsoft a Red Hat </td>
   <td>Shared </td>
   <td>Shared </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>Virtuální síť </td>
   <td>Shared </td>
   <td>Shared </td>
   <td>Shared </td>
   <td>Shared </td>
  </tr>
  <tr>
   <td>Řízení uzlů roviny </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>Pracovní uzly </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>Verze clusteru </td>
   <td>Microsoft a Red Hat </td>
   <td>Shared </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>Správa kapacit </td>
   <td>Microsoft a Red Hat </td>
   <td>Shared </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>Virtuální úložiště </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
  </tr>
  <tr>
   <td>Fyzická infrastruktura a zabezpečení </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
   <td>Microsoft a Red Hat </td>
  </tr>
</table>


Tabulka 1. Odpovědnosti podle prostředku


## <a name="tasks-for-shared-responsibilities-by-area"></a>Úkoly pro sdílené odpovědnosti podle oblasti 

### <a name="incident-and-operations-management"></a>Správa incidentů a operací 

Zodpovědnost za sledování a údržbu clusteru Azure Red Hat OpenShift pro zákazníky a Microsoft a Red Hat Share. Zákazník je zodpovědný za incidenty a správu provozu [zákaznických dat](#customer-data-and-applications) a všech vlastních sítí, které zákazník mohl nakonfigurovat.

<table>
  <tr>
   <td><strong>Partner</strong>
   </td>
   <td><strong>Odpovědnosti Microsoft a Red Hat</strong>
   </td>
   <td><strong>Odpovědnosti zákazníků</strong>
   </td>
  </tr>
  <tr>
   <td>Aplikační síť </td>
   <td>
<ul>

<li>Sledujte cloudové nástroje pro vyrovnávání zatížení a nativní službu OpenShift router a odpovězte na upozornění.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitoruje stav koncových bodů služby Vyrovnávání zatížení.

<li>Monitoruje stav tras aplikace a koncových bodů za nimi.

<li>Oznamte výpadky Microsoftu a Red Hat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuální síť
   </td>
   <td>
<ul>

<li>Monitorujte cloudové nástroje pro vyrovnávání zatížení, podsítě a cloudové komponenty Azure, které jsou nezbytné pro výchozí síťové platformy, a reagovat na výstrahy.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorujte síťový provoz, který je volitelně nakonfigurovaný prostřednictvím připojení VNet na virtuální síť, připojení k síti VPN nebo připojení privátního propojení, a to kvůli potenciálním problémům nebo bezpečnostním hrozbám.
</li>
</ul>
   </td>
  </tr>
</table>


Tabulka 2. Sdílené odpovědnosti pro správu incidentů a operací


### <a name="change-management"></a>Správa změn

Společnosti Microsoft a Red Hat jsou zodpovědné za povolení změn Clusterové infrastruktury a služeb, které zákazník bude kontrolovat, a udržování verzí dostupných pro hlavní uzly, služby infrastruktury a pracovní uzly. Zákazník zodpovídá za inicializaci změn infrastruktury a instalaci a údržbu volitelných služeb a síťových konfigurací v clusteru a také všechny změny zákaznických dat a zákaznických aplikací.


<table>
  <tr>
   <td><strong>Partner</strong>
   </td>
   <td><strong>Odpovědnosti Microsoft a Red Hat</strong>
   </td>
   <td><strong>Odpovědnosti zákazníků</strong>
   </td>
  </tr>
  <tr>
   <td>protokolování </td>
   <td>
<ul>

<li>Centrálně agregovat a monitorovat protokoly auditu platformy.

<li>Poskytněte dokumentaci pro zákazníka, aby se povolilo protokolování aplikací pomocí Log Analytics přes Azure Monitor kontejnerů.

<li>Poskytněte protokoly auditu na žádost zákazníka.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Nainstalujte do clusteru volitelný výchozí operátor protokolování aplikace.

<li>Nainstalujte, nakonfigurujte a udržujte všechna volitelná řešení protokolování aplikací, jako je například protokolování kontejnerů na postranového vozíku nebo aplikace protokolování třetích stran.

<li>Optimalizuje velikost a četnost aplikačních protokolů vytvářených aplikacemi zákazníka, pokud mají vliv na stabilitu clusteru.

<li>Vyžádejte si protokoly auditu platformy prostřednictvím případu podpory pro prohledání konkrétních incidentů.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Aplikační síť
   </td>
   <td>
<ul>

<li>Nastavení nástrojů pro vyrovnávání zatížení veřejných cloudů

<li>Nastavte službu Native OpenShift Router Service. Poskytněte možnost nastavit směrovač jako soukromou a přidat až jeden další horizontálních oddílů směrovače.

<li>Nainstalujte, nakonfigurujte a udržujte součásti OpenShift SDN pro výchozí provoz interního pod.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Konfigurace nevýchozích oprávnění sítě v sítích pro projekt a pod, pod příchozím a odchozím výstupem pomocí objektů NetworkPolicy

<li>Vyžádá a nakonfiguruje další nástroje pro vyrovnávání zatížení služeb pro konkrétní služby.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sítě clusteru
   </td>
   <td>
<ul>

<li>Nastavte součásti pro správu clusterů, jako jsou například veřejné nebo soukromé koncové body služby a nezbytné integrace s virtuálními síťovými součástmi.

<li>Nastavení interních síťových komponent vyžadovaných pro interní komunikaci clusteru mezi pracovními uzly a hlavními uzly.
</li>
</ul>
   </td>
   <td>
<ul>

<li>V případě potřeby zadejte volitelné nevýchozí rozsahy IP adres pro CIDR strojové směrování, CIDR služby a směrování CIDR, pokud je to potřeba, prostřednictvím Správce clusteru OpenShift při zřizování clusteru.

<li>Vyžádejte si, aby koncový bod služby API byl při vytváření clusteru nebo po vytvoření clusteru prostřednictvím rozhraní příkazového řádku Azure vydaný jako veřejný nebo soukromý.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuální síť
   </td>
   <td>
<ul>

<li>Nastavte a nakonfigurujte součásti virtuální sítě, které jsou potřeba ke zřízení clusteru, včetně virtuálního privátního cloudu, podsítí, nástrojů pro vyrovnávání zatížení, bran Internetu a bran NAT atd.

<li>Poskytněte zákazníkům možnost spravovat připojení VPN s místními prostředky, připojením VNet k virtuální síti a připojením privátního propojení podle požadavků prostřednictvím Správce clusteru OpenShift.

<li>Umožněte zákazníkům vytvářet a nasazovat nástroje pro vyrovnávání zatížení veřejných cloudů pro použití s nástroji pro vyrovnávání zatížení služeb.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Nastavte a udržujte volitelné síťové součásti veřejného cloudu, jako je třeba připojení typu VNet-to-VNet, připojení k síti VPN nebo připojení k privátnímu propojení.

<li>Vyžádá a nakonfiguruje další nástroje pro vyrovnávání zatížení služeb pro konkrétní služby.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Verze clusteru
   </td>
   <td>
<ul>

<li>Naplánování plánu a stavu upgradů pro menší verze a verze údržby

<li>Publikovat protokol změn a poznámky k verzi pro upgrady s menšími a údržbou
</li>
</ul>
   </td>
   <td>
<ul>

<li>Iniciovat upgrade clusteru

<li>Testování zákaznických aplikací v menších verzích a údržbě, aby se zajistila kompatibilita
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Správa kapacit
   </td>
   <td>
<ul>

<li>Monitorování využití řídicí plochy (hlavní uzly)

<li>Škálování a změna velikosti uzlů řídicí plochy pro udržení kvality služby

<li>Monitorujte využívání zákaznických prostředků, včetně kapacity sítě, úložiště a výpočetní kapacity. Kde nejsou povoleny funkce automatického škálování u všech změn požadovaných pro prostředky clusteru (např. nové výpočetní uzly pro škálování, další úložiště atd.)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Pomocí poskytovaných ovládacích prvků cluster OpenShift přidejte nebo odeberte další pracovní uzly podle potřeby.

<li>Odpovězte na oznámení Microsoftu a Red Hat týkající se požadavků na prostředky clusteru.
</li>
</ul>
   </td>
  </tr>
</table>


Tabulka 3. Sdílené odpovědnosti pro správu změn


### <a name="identity-and-access-management"></a>Správa identit a přístupu 

Správa identit a přístupu zahrnuje všechny zodpovědnosti za zajištění toho, aby měli přístup k prostředkům clusteru, aplikace a infrastruktury jenom řádným jednotlivcům. To zahrnuje úlohy, jako je například poskytování mechanismů řízení přístupu, ověřování, autorizace a Správa přístupu k prostředkům.


<table>
  <tr>
   <td><strong>Partner</strong>
   </td>
   <td><strong>Odpovědnosti Microsoft a Red Hat</strong>
   </td>
   <td><strong>Odpovědnosti zákazníků</strong>
   </td>
  </tr>
  <tr>
   <td>protokolování </td>
   <td>
<ul>

<li>Dodržování standardů řízených interních procesů přístupu založených na průmyslových standardech pro protokoly auditu platformy.

<li>Poskytněte nativní možnosti RBAC OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Nakonfigurujte OpenShift RBAC pro řízení přístupu k projektům a rozšíření protokolů aplikace projektu.

<li>Pro řešení pro protokolování třetích stran nebo vlastní aplikace je zákazník zodpovědný za správu přístupu.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Aplikační síť
   </td>
   <td>
<ul>

<li>Poskytněte nativní možnosti RBAC OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Nakonfigurujte OpenShift RBAC pro řízení přístupu ke konfiguraci směrování podle potřeby.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sítě clusteru
   </td>
   <td>
<ul>

<li>Poskytněte nativní možnosti RBAC OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Spravujte členství v organizaci Red Hat v organizaci Red Hat.

<li>Spravovat správce organizace pro organizaci Red Hat pro udělení přístupu Správci clusteru OpenShift.

<li>Nakonfigurujte OpenShift RBAC pro řízení přístupu ke konfiguraci směrování podle potřeby.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuální síť
   </td>
   <td>
<ul>

<li>Poskytněte řízení přístupu zákazníkům prostřednictvím Správce clusteru OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Spravujte volitelný přístup uživatelů k součástem veřejného cloudu prostřednictvím Správce clusteru OpenShift.
</li>
</ul>
   </td>
  </tr>
</table>


Tabulka 4. Sdílené odpovědnosti pro správu identit a přístupu


### <a name="security-and-regulation-compliance"></a>Dodržování předpisů pro zabezpečení a předpisy 

Zabezpečení a dodržování předpisů zahrnuje veškeré zodpovědnosti a kontroly, které zajistí dodržování příslušných zákonů, zásad a předpisů.


<table>
  <tr>
   <td><strong>Partner</strong>
   </td>
   <td><strong>Odpovědnosti Microsoft a Red Hat</strong>
   </td>
   <td><strong>Odpovědnosti zákazníků</strong>
   </td>
  </tr>
  <tr>
   <td>protokolování </td>
   <td>
<ul>

<li>Odešlete protokoly auditu clusteru do Microsoft a Red Hat SIEM k analýze událostí zabezpečení. Uchovávání protokolů auditu po určenou dobu pro podporu forenzní analýzy.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Analyzujte protokoly aplikací pro události zabezpečení. Odešlete protokoly aplikací do externího koncového bodu prostřednictvím protokolování kontejnerů na postranového vozíku nebo aplikací pro protokolování třetích stran, pokud je potřeba delší dobu uchování, než je výchozí zásobník protokolování.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuální síť
   </td>
   <td>
<ul>

<li>Monitorujte virtuální síťové součásti pro potenciální problémy a bezpečnostní hrozby.

<li>Další možnosti monitorování a ochrany vám povyužívají další veřejné nástroje Microsoft a Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorovat volitelné nakonfigurované součásti virtuální sítě pro potenciální problémy a bezpečnostní hrozby.

<li>Podle potřeby nakonfigurujte všechna nezbytná pravidla brány firewall nebo ochrany datového centra.
</li>
</ul>
   </td>
  </tr>
</table>


Tabulka 5. Sdílené odpovědnosti za dodržování předpisů pro zabezpečení a předpisy


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Zodpovědnost za zákazníky při používání Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Zákaznická data a aplikace

Zákazník je zodpovědný za aplikace, úlohy a data, která nasazují do Azure Red Hat OpenShift. Microsoft a Red Hat ale poskytují různé nástroje, které zákazníkům pomůžou spravovat data a aplikace na platformě.


<table>
  <tr>
   <td><strong>Partner</strong>
   </td>
   <td><strong>Jak vám Microsoft a Red Hat pomůže</strong>
   </td>
   <td><strong>Odpovědnosti zákazníků</strong>
   </td>
  </tr>
  <tr>
   <td>Zákaznická data </td>
   <td>
<ul>

<li>Udržujte standardy na úrovni platformy pro šifrování dat podle definice oboru zabezpečení a dodržování předpisů. 

<li>Poskytněte OpenShift komponenty, které vám pomůžou spravovat data aplikací, jako jsou tajné klíče.

<li>Umožněte integraci s datovými službami třetích stran (například Azure SQL) k ukládání a správě dat mimo cluster a/nebo Microsoft a Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Udržujte odpovědnost za všechna zákaznická data uložená na platformě a způsob, jakým zákaznické aplikace spotřebovávají a zveřejňují tato data.

<li>Šifrování Etcd
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Zákaznické aplikace
   </td>
   <td>
<ul>

<li>Zřizování clusterů s nainstalovanými komponentami OpenShift, aby zákazníci měli přístup k rozhraním API OpenShift a Kubernetes k nasazení a správě kontejnerových aplikací.

<li>Poskytněte přístup k rozhraním API OpenShift, která může zákazník použít k nastavení operátorů pro přidání komunit, třetích stran, Microsoft a Red Hat a Red Hat Services do clusteru. 

<li>Poskytněte třídy úložiště a moduly plug-in, aby podporovaly trvalé svazky pro použití s aplikacemi pro zákazníky.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Udržujte odpovědnost za aplikace, data a jejich úplný životní cyklus pro zákazníky a třetí strany.

<li>Pokud zákazník do clusteru přidá červené Hat, komunitu, třetí stranu, své vlastní nebo jiné služby, a to pomocí operátorů nebo externích imagí, je zákazník zodpovědný za tyto služby a pro práci s příslušným poskytovatelem (včetně Red Hat) při řešení problémů.

<li>Pomocí poskytovaných nástrojů a funkcí <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">proveďte konfiguraci a nasazení</a>; <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">Udržujte si aktuální data</a>; <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">nastavení požadavků a omezení prostředků</a>; <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">Velikost clusteru, aby měl dostatek prostředků ke spouštění aplikací</a>; <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">nastavit oprávnění</a>; integrace s jinými službami; <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">Spravujte všechny datové proudy nebo šablony imagí, které nasadí zákazník</a>; <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">externě obsluhuje</a>; uložení, zálohování a obnovení dat; a jinak Spravujte své vysoce dostupné a odolné úlohy.

<li>Udržovat zodpovědnost za monitorování aplikací běžících na Azure Red Hat OpenShift; včetně instalace a operačního softwaru pro shromažďování metrik a vytváření výstrah.
</li>
</ul>
   </td>
  </tr>
</table>


Tabulka 7 Zodpovědnost zákazníků na zákaznická data, zákaznické aplikace a služby
