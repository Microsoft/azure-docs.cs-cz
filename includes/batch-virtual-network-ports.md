---
title: zahrnout soubor
description: zahrnout soubor
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: dc08dcded6418208751edbffcb5d263db059ec01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80657479"
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

**Další síťové prostředky** – Batch automaticky přiděluje další síťové prostředky ve skupině prostředků obsahující virtuální síť.

> [!IMPORTANT]
>Za každých 50 vyhrazených uzlů (nebo každých 20 uzlů s nízkou prioritou) Batch přidělí: jednu skupinu zabezpečení sítě (NSG), jednu veřejnou IP adresu a jeden nástroj pro vyrovnávání zatížení. Pro tyto prostředky platí omezení [kvót prostředků](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) předplatného. V případě velkých fondů možná bude potřeba požádat o navýšení kvóty pro jeden nebo několik z těchto prostředků.

#### <a name="network-security-groups-batch-default"></a>Skupiny zabezpečení sítě: Výchozí hodnota služby Batch

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky podle potřeb vaší úlohy. Pro fondy v konfiguraci virtuálního počítače přidává Batch skupiny zabezpečení sítě na úrovni síťových rozhraní připojených k výpočetním uzlům. U těchto skupin zabezpečení sítě jsou nakonfigurovaná následující další pravidla:

* Příchozí provoz přes protokol TCP na portech 29876 a 29877 z IP adres služby Batch, které odpovídají značce služby `BatchNodeManagement`.
* Příchozí provoz přes protokol TCP na portu 22 (uzly s Linuxem) nebo portu 3389 (uzly s Windows) umožňující vzdálený přístup. U některých typů úkolů s více instancemi v Linuxu (jako je MPI) bude potřeba povolit také provoz přes protokol SSH na portu 22 pro IP adresy v podsíti, která obsahuje výpočetní uzly služby Batch. Toto je možné zablokovat pravidly NSG na úrovni podsítě (viz níže).
* Odchozí provoz do virtuální sítě na jakémkoli portu. Toto je možné upravit pravidly NSG na úrovni podsítě (viz níže).
* Odchozí provoz do internetu na jakémkoli portu. Toto je možné upravit pravidly NSG na úrovni podsítě (viz níže).

> [!IMPORTANT]
> Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**. Kromě toho by žádné prostředky vytvořené službou Batch neměly mít zámky prostředků, jinak může dojít k tomu, že v důsledku uživatelem zahájených akcí, jako je odstranění fondu, nedojde k vyčištění prostředků.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Skupiny zabezpečení sítě: Zadání pravidel na úrovni podsítě

Nemusíte zadávat skupiny zabezpečení sítě na úrovni podsítě virtuální sítě, protože služba Batch konfiguruje vlastní skupiny zabezpečení sítě (viz výše). Pokud máte k podsíti, ve které jsou nasazené výpočetní uzly služby Batch, přidruženou skupinu zabezpečení sítě nebo pokud chcete použít vlastní pravidla NSG a přepsat výchozí nastavení, musíte u této skupiny zabezpečení sítě nakonfigurovat alespoň příchozí a odchozí pravidla zabezpečení, jak je uvedeno v následujících tabulkách.

Příchozí provoz na portu 3389 (Windows) nebo 22 (Linux) nakonfigurujte pouze v případě, že potřebujete povolit vzdálený přístup k výpočetním uzlům z externích zdrojů. Pokud potřebujete zajistit podporu úkolů s více instancemi a některými moduly runtime MPI, možná bude potřeba v Linuxu povolit pravidla pro port 22. Výpočetní uzly ve fondu budou použitelné i bez povolení provozu na těchto portech.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Značka zdrojové služby | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- | --- |
| – | [Značka služby](../articles/virtual-network/security-overview.md#service-tags) `BatchNodeManagement` (pokud používáte místní variantu ve stejné oblasti jako váš účet Batch) | * | Všechny | 29876–29877 | TCP | Povolit |
| Zdrojové IP adresy uživatelů pro vzdálený přístup k výpočetním uzlům nebo podsíti výpočetních uzlů pro úlohy Linuxu s více instancemi, pokud je to potřeba | – | * | Všechny | 3389 (Windows), 22 (Linux) | TCP | Povolit |

> [!WARNING]
> IP adresy služby Batch se můžou v průběhu času měnit. Proto se důrazně doporučuje používat pro pravidla NSG značku služby `BatchNodeManagement` (nebo regionální variantu). Nedoporučuje se do pravidel NSG přímo doplnit IP adresy služby Batch.

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Značka cílové služby | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- | --- |
| Všechny | * | [Značka služby](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (pokud používáte místní variantu ve stejné oblasti jako váš účet Batch) | 443 | TCP | Povolit |

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

Pokud potřebujete povolit přístup k uzlům fondu přes protokol RDP, nakonfigurujte pro Windows příchozí provoz na portu 3389. Uzly fondu budou použitelné i bez této konfigurace.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
Všechny <br /><br />Přestože to efektivně vyžaduje „povolit vše“, služba Batch použije pravidlo seznamu ACL na úrovni každého uzlu, které filtruje všechny IP adresy, které nejsou služby Batch. | * | Všechny | 10100, 20100, 30100 | TCP | Povolit |
| Volitelné povolení přístupu k výpočetním uzlům přes protokol RDP | * | Všechny | 3389 | TCP | Povolit |

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
| Všechny | * | Všechny | 443  | Všechny | Povolit |
