---
title: Posílení zabezpečení v hostitelích virtuálních počítačů s AKS
description: Další informace o posílení zabezpečení v operačním systému hostitele virtuálních počítačů AKS
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b7552fc083c5ed340dc54c2a31160b0c8b4bd076
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420903"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Posílení zabezpečení pro hostitelský operační systém uzlu agenta AKS

Služba Azure Kubernetes Service (AKS) je zabezpečená služba, která vyhovuje standardům SOC, ISO, PCI DSS a HIPAA. Tento článek popisuje posílení zabezpečení použité pro hostitele virtuálních počítačů s AKS. Další informace o zabezpečení AKS najdete v tématu [koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

> [!Note]
> Tento dokument je omezený na agenty Linux pouze v AKS.

Clustery AKS se nasazují v hostitelských virtuálních počítačích, které spouštějí zabezpečený operační systém, který se využívá pro kontejnery běžící na AKS. Tento hostitelský operační systém je založený na imagi **Ubuntu 16.04. LTS** s dalšími možnostmi posílení zabezpečení a použitými optimalizacemi (viz podrobnosti o posílení zabezpečení).

Cílem hostitelského operačního systému s posíleným zabezpečením je snížit úroveň oblasti útoku a optimalizovat pro nasazení kontejnerů zabezpečeným způsobem.

> [!Important]
> Nezabezpečený operační systém pro zabezpečení nezahrnuje CI srovnávací testy. I když dojde k překrytí pomocí srovnávacích testů CIS, cíl nedodržuje CIS. Cílem posílení zabezpečení operačního systému hostitele je sblížení s úrovní zabezpečení v souladu s vlastními standardy zabezpečení interního hostitele Microsoftu.

## <a name="security-hardening-features"></a>Funkce posílení zabezpečení

* AKS poskytuje standardně optimalizovaný hostitelský operační systém. Neexistuje možnost vybrat jiný operační systém.

* Azure na AKS hostitelích virtuálních počítačů aplikuje každodenní opravy (včetně oprav zabezpečení). Některé z těchto oprav budou vyžadovat restart, i když jiné nebudou. Zodpovídáte za plánování restartování hostitele virtuálních počítačů AKS podle potřeby. Pokyny k automatizaci oprav AKS najdete v tématu věnovaném [opravám AKS uzlů](https://docs.microsoft.com/azure/aks/node-updates-kured).

## <a name="what-is-configured"></a>Co je nakonfigurované

| SLUŽBY  | Popis auditu|
|---|---|
| 1.1.1.1 |Zajistěte, aby připojení systémů souborů CramFS bylo zakázané.|
| 1.1.1.2 |Zajistěte, aby připojení systémů souborů freevxfs bylo zakázané.|
| 1.1.1.3 |Zajistěte, aby připojení systémů souborů JFFS2 bylo zakázané.|
| 1.1.1.4 |Zajistěte, aby připojení systémů souborů HFS bylo zakázané.|
| 1.1.1.5 |Zajistěte, aby připojení systémů souborů HFS Plus bylo zakázané.|
|1.4.3 |Ujistěte se, že je vyžadováno ověřování pro režim jednoho uživatele. |
|1.7.1.2 |Ujistěte se, že je správně nakonfigurované upozornění na místní přihlášení. |
|1.7.1.3 |Ujistěte se, že je správně nakonfigurované upozornění na vzdálené přihlášení. |
|1.7.1.5 |Zajistěte, aby byla nakonfigurovaná oprávnění pro/etc/issue. |
|1.7.1.6 |Zajistěte, aby byla nakonfigurovaná oprávnění pro/etc/issue.NET. |
|2.1.5 |Ujistěte se, že--streaming-připojení-nečinné – časový limit není nastavený na 0. |
|3.1.2 |Zajistěte, aby odesílání přesměrování paketů bylo zakázané. |
|3.2.1 |Zajistěte, aby se nepřijaly zdrojové směrované balíčky. |
|3.2.2 |Ujistěte se, že přesměrování protokolu ICMP nejsou přijata. |
|3.2.3 |Ujistěte se, že zabezpečené přesměrování protokolu ICMP nejsou přijímány. |
|3.2.4 |Zajistěte, aby byly zaznamenány podezřelé pakety. |
|3.3.1 |Zajistěte, aby inzerování směrovače IPv6 nebyla přijata. |
|3.5.1 |Ujistěte se, že je DCCP zakázané. |
|bodu |Ujistěte se, že je SCTP zakázané. |
|3.5.3 |Ujistěte se, že je služba RDS zakázaná. |
|3.5.4 |Ujistěte se, že je TIPC zakázané. |
|4.2.1.2 |Ujistěte se, že je protokolování nakonfigurované. |
|5.1.2 |Zajistěte, aby byla nakonfigurovaná oprávnění pro/etc/crontab. |
|5.2.4 |Zajistěte, aby bylo předávání X11 SSH zakázané. |
|5.2.5 |Ujistěte se, že je MaxAuthTries SSH nastavený na 4 nebo míň. |
|5.2.8 |Zajistěte, aby bylo zablokované přihlašovací jméno SSH |
|5.2.10 |Zajistěte, aby PermitUserEnvironment SSH bylo zakázané. |
|5.2.11 |Zajistěte, aby se používaly jenom schválené maximální algoritmy. |
|5.2.12 |Zajistěte, aby byl nakonfigurovaný časový limit nečinnosti SSH. |
|5.2.13 |Ujistěte se, že je LoginGraceTime SSH nastavený na jednu minutu nebo míň. |
|5.2.15 |Zajistěte, aby byl nakonfigurovaný výstražný banner SSH. |
|5.3.1 |Ujistěte se, že jsou nakonfigurované požadavky na vytváření hesel. |
|5.4.1.1 |Zajistěte, aby vypršení platnosti hesla bylo 90 dní nebo méně. |
|5.4.1.4 |Zajistěte, aby byl neaktivní zámek hesla nastaven na 30 dní nebo méně. |
|5.4.4 |Zajistěte, aby výchozí uživatel umask byl 027 nebo více omezující. |
|5,6 |Zajistěte, aby byl omezený přístup k příkazu su|

## <a name="additional-notes"></a>Další poznámky
 
* Aby bylo možné dále omezit prostor pro útoky, byly některé zbytečné ovladače modulu jádra v operačním systému zakázané.

* SYSTÉM s posíleným zabezpečením se sestavuje a udržuje konkrétně pro AKS a není podporovaný mimo platformu AKS.

## <a name="next-steps"></a>Další kroky  

Další informace o zabezpečení AKS najdete v následujících článcích: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Požadavky na zabezpečení AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Osvědčené postupy pro AKS](https://docs.microsoft.com/azure/aks/best-practices)
