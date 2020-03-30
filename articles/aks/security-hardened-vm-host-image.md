---
title: Posílení zabezpečení u hostitelů virtuálních strojů AKS
description: Informace o posílení zabezpečení v hostitelském hostiteli virtuálního operačního systému AKS
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594376"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Posílení zabezpečení u hostitelů virtuálních strojů AKS 

Azure Kubernetes Service (AKS) je zabezpečená služba kompatibilní se standardy SOC, ISO, PCI DSS a HIPAA. Tento článek popisuje posílení zabezpečení aplikované na hostitele virtuálních strojů AKS. Další informace o zabezpečení AKS najdete v [tématu koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes Service (AKS).](https://docs.microsoft.com/azure/aks/concepts-security)

Clustery AKS se nasazují na hostitelské virtuální počítače, na kterých běží operační systém optimalizovaný pro zabezpečení. Tento hostitelský operační systém je v současné době založen na bitové kopii Ubuntu 16.04.LTS se sadou dalších kroků posílení zabezpečení (viz Podrobnosti o posílení zabezpečení).   

Cílem zabezpečení tvrzené hostitelského operačního systému je snížit plochu útoku a umožnit nasazení kontejnerů v bezpečným způsobem. 

> [!Important]
> Zabezpečení tvrzené OS není CIS benchmarked. I když existují překrývání s referenčními hodnotami CIS, cílem není být v souladu s CIS. Cílem posílení zabezpečení hostitelského operačního systému je sblížení na úrovni zabezpečení, která je v souladu s vlastními interními standardy zabezpečení hostitele společnosti Microsoft. 

## <a name="security-hardening-features"></a>Funkce posílení zabezpečení 

* AKS poskytuje ve výchozím nastavení hostitelský operační systém optimalizovaný pro zabezpečení. Neexistuje žádná aktuální možnost vybrat alternativní operační systém. 

* Azure aplikuje denní opravy (včetně oprav zabezpečení) na hostitele virtuálních strojů AKS. Některé z těchto oprav budou vyžadovat restartování, zatímco jiné nebudou. Jste zodpovědní za plánování restartování hostitele virtuálního počítače AKS podle potřeby. Pokyny k automatizaci oprav AKS naleznete v [tématu oprava uzlů AKS](https://docs.microsoft.com/azure/aks/node-updates-kured).

Níže je uveden přehled práce zpevnění obrázků, která je implementována v AKS-Engine k výrobě zabezpečení optimalizovaného hostitelského operačního systému. Práce byla implementována [v tomto projektu GitHub](https://github.com/Azure/aks-engine/projects/7).  

AKS-Engine v současné době nepropaguje ani nedodržuje žádný konkrétní bezpečnostní standard, ale ID auditu CIS (Center for Internet Security) jsou poskytována pro pohodlí, pokud je to možné. 

## <a name="whats-configured"></a>Co je nakonfigurováno?

| Cis  | Popis auditu| 
|---|---|
| 1.1.1.1 |Zajistěte, aby montáž souborových systémů cramfs byla zakázána|
| 1.1.1.2 |Zajistěte, aby byla montáž souborových systémů freevxfs zakázána|
| 1.1.1.3 |Ujistěte se, že montáž souborových systémů JFFS2 je zakázána|
| 1.1.1.4 |Zajistěte, aby byla montáž souborových systémů HFS zakázána|
| 1.1.1.5 |Zajistěte, aby byla montáž souborových systémů HFS Plus zakázána|
|1.4.3 |Zajištění vyžadování ověřování pro režim jednoho uživatele |
|1.7.1.2 |Ujistěte se, že je správně nakonfigurován místní varovný banner pro přihlášení. |
|1.7.1.3 |Ujistěte se, že je správně nakonfigurován varovný banner pro vzdálené přihlášení |
|1.7.1.5 |Ujistěte se, že jsou nakonfigurována oprávnění k /etc/issue |
|1.7.1.6 |Ujistěte se, že jsou nakonfigurována oprávnění na /etc/issue.net |
|2.1.5 |Ujistěte se, že --streaming-connection-idle-timeout není nastaven na 0 |
|3.1.2 |Zajištění zakázání odesílání přesměrování paketů |
|3.2.1 |Ujistěte se, že zdrojové směrované balíčky nejsou přijaty |
|3.2.2 |Ujistěte se, že přesměrování ICMP nejsou přijata |
|3.2.3 |Ujistěte se, že nejsou přijata zabezpečená přesměrování ICMP |
|3.2.4 |Ujistěte se, že jsou protokolovány podezřelé pakety |
|3.3.1 |Zajistěte, aby nebyly akceptovány reklamy směrovače IPv6 |
|3.5.1 |Ujistěte se, že je dccp zakázán. |
|3.5.2 |Ujistěte se, že je sctp zakázáno |
|3.5.3 |Ujistěte se, že rds je zakázáno |
|3.5.4 |Ujistěte se, že je tipc zakázán |
|4.2.1.2 |Ujistěte se, že je protokolování nakonfigurováno |
|5.1.2 |Ujistěte se, že jsou nakonfigurována oprávnění na /etc/crontab |
|5.2.4 |Ujistěte se, že je zakázáno předávání SSH X11 |
|5.2.5 |Ujistěte se, že je SSH MaxAuthTries nastavenna na 4 nebo méně |
|5.2.8 |Ujistěte se, že je zakázáno přihlášení kořenového adresáře SSH |
|5.2.10 |Ujistěte se, že je zakázáno prostředí SSH PermitUserEnvironment |
|5.2.11 |Ujistěte se, že jsou použity pouze schválené algoritmy MAX |
|5.2.12 |Ujistěte se, že je nakonfigurován časový limit nečinnosti SSH |
|5.2.13 |Ujistěte se, že je SSH LoginGraceTime nastaven na jednu minutu nebo méně |
|5.2.15 |Ujistěte se, že je nakonfigurován výstražný banner SSH |
|5.3.1 |Ujistěte se, že jsou nakonfigurovány požadavky na vytvoření hesla |
|5.4.1.1 |Ujistěte se, že vypršení platnosti hesla je 90 dní nebo méně |
|5.4.1.4 |Ujistěte se, že neaktivní zámek hesla je 30 dní nebo méně |
|5.4.4 |Ujistěte se, že výchozí uživatel umask je 027 nebo více omezující |
|5.6 |Zajistěte, aby byl přístup k příkazu su omezen|

## <a name="additional-notes"></a>Další poznámky
 
* Pro další snížení oblasti útoku byly některé zbytečné ovladače modulů jádra v osu zakázány. 

* Zabezpečení tvrzené OS není podporovánmimo platformu AKS. 

## <a name="next-steps"></a>Další kroky  

Další informace o zabezpečení AKS naleznete v následujících článcích: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Důležité informace o zabezpečení AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Doporučené postupy AKS](https://docs.microsoft.com/azure/aks/best-practices)
