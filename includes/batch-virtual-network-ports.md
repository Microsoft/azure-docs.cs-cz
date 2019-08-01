---
title: zahrnout soubor
description: zahrnout soubor
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 07/16/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: c8b25858556538835d6a84bf0d6699f9906f1438
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68322655"
---
### <a name="general-requirements"></a>Obecné požadavky

* Virtuální síť musí být ve stejném předplatném a stejné oblasti jako účet Batch, který použijete k vytvoření fondu.

* Fond využívající virtuální síť může mít maximálně 4 096 uzlů.

* Podsíť zadaná pro fond musí obsahovat dostatek nepřiřazených IP adres pro všechny virtuální počítače, na které fond cílí, jejichž počet je součtem vlastností `targetDedicatedNodes` a `targetLowPriorityNodes` fondu. Pokud podsíť nemá dostatek nepřiřazených IP adres, fond částečně přidělí výpočetní uzly a dojde k chybě změny velikosti. 

* Všechny vlastní servery DNS obsluhující virtuální síť musí být schopné přeložit váš koncový bod služby Azure Storage. Konkrétně musí být možné přeložit adresy URL ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net`. 

Další požadavky na virtuální síť se liší podle toho, jestli je fond Batch v konfiguraci virtuálního počítače nebo konfiguraci služby Cloud Services. Pro nová nasazení fondů do virtuální sítě se doporučuje konfigurace virtuálního počítače.

### <a name="pools-in-the-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

**Podporované virtuální sítě** – Pouze virtuální sítě založené na Azure Resource Manageru

**ID podsítě** – Při zadávání podsítě pomocí rozhraní API služby Batch použijte *identifikátor prostředku* podsítě. Identifikátor podsítě má tento formát:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Oprávnění** – Zkontrolujte, jestli vaše zásady zabezpečení nebo zámky na předplatném nebo skupině prostředků virtuální sítě neomezují oprávnění uživatele spravovat tuto virtuální síť.

**Další síťové prostředky** – Batch automaticky přiděluje další síťové prostředky ve skupině prostředků obsahující virtuální síť. Pro každý 50 vyhrazený uzel (nebo každého 20 uzlů s nízkou prioritou) se dávka přiděluje: 1 skupina zabezpečení sítě (NSG), 1 veřejná IP adresa a 1 Nástroj pro vyrovnávání zatížení. Pro tyto prostředky platí omezení [kvót prostředků](../articles/azure-subscription-service-limits.md) předplatného. V případě velkých fondů možná bude potřeba požádat o navýšení kvóty pro jeden nebo několik z těchto prostředků.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky. Pro fondy v konfiguraci virtuálního počítače přidává Batch skupiny zabezpečení sítě na úrovni síťových rozhraní připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

* Příchozí provoz přes protokol TCP na portech 29876 a 29877 z IP adres role služby Batch. 
* Příchozí provoz přes protokol TCP na portu 22 (uzly s Linuxem) nebo portu 3389 (uzly s Windows) umožňující vzdálený přístup. U některých typů úkolů s více instancemi na platformě Linux (například MPI) budete muset taky u IP adres v podsíti, která obsahuje výpočetní uzly Batch, zapnout provoz protokolu SSH portu 22.
* Odchozí provoz do virtuální sítě na jakémkoli portu.
* Odchozí provoz do internetu na jakémkoli portu.

> [!IMPORTANT]
> Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**.

Nemusíte zadávat skupiny zabezpečení sítě na úrovni podsítě, protože Batch konfiguruje vlastní skupiny zabezpečení sítě. Pokud má však zadaná podsíť přiřazené skupiny zabezpečení sítě (NSG) nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení podle následujících tabulek. Nakonfigurujte příchozí provoz na portu 3389 (Windows) nebo 22 (Linux) pouze v případě, že je třeba povolit vzdálený přístup k virtuálním počítačům fondu z vnějších zdrojů. Virtuální počítače ve fondu budou použitelné i bez této konfigurace. Upozorňujeme, že pokud použijete určité druhy úkolů s více instancemi, jako je MPI, budete muset povolit provoz podsítě virtuální sítě na portu 22 pro Linux.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Značka zdrojové služby | Zdrojové porty | Cíl | Cílové porty | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Není k dispozici | `BatchNodeManagement`[Značka služby](../articles/virtual-network/security-overview.md#service-tags) | * | Any | 29876–29877 | TCP | Allow |
| IP adresy zdroje uživatelů pro vzdálené přístup k výpočetním uzlům a/nebo podsíti výpočetních uzlů pro úlohy s víc instancemi pro Linux, pokud je to potřeba. | Není k dispozici | * | Any | 3389 (Windows), 22 (Linux) | TCP | Allow |

**Odchozí pravidla zabezpečení**

| Source | Zdrojové porty | Cíl | Značka cílové služby | Cílové porty | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Any | * | [Značka služby](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(ve stejné oblasti jako účet Batch a virtuální síť) | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>Fondy v konfigurace služby Cloud Services

**Podporované virtuální sítě** – Pouze virtuální sítě Classic

**ID podsítě** – Při zadávání podsítě pomocí rozhraní API služby Batch použijte *identifikátor prostředku* podsítě. Identifikátor podsítě má tento formát:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Oprávnění** – Instanční objekt `Microsoft Azure Batch` musí mít pro zadanou virtuální síť roli řízení přístupu na základě role `Classic Virtual Machine Contributor`.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky.

Není potřeba zadávat skupinu zabezpečení sítě, protože Batch konfiguruje příchozí komunikaci do uzlů fondu pouze z IP adres služby Batch. Pokud má však zadaná podsíť přiřazené skupiny zabezpečení sítě nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení podle následujících tabulek. Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**.

Pokud potřebujete povolit přístup protokolu RDP k uzlům fondu, nakonfigurujte příchozí provoz na portu 3389 pro Windows. Uzly fondu budou použitelné i bez této konfigurace.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Zdrojové porty | Cíl | Cílové porty | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />Přestože to efektivně vyžaduje „povolit vše“, služba Batch použije pravidlo seznamu ACL na úrovni každého uzlu, které filtruje všechny IP adresy, které nejsou služby Batch. | * | Any | 10100, 20100, 30100 | TCP | Allow |
| Volitelné, aby bylo umožněno přístupu RDP k výpočetním uzlům. | * | Any | 3389 | TCP | Allow |

**Odchozí pravidla zabezpečení**

| Source | Zdrojové porty | Cíl | Cílové porty | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Any | * | Any | 443  | Any | Allow |
