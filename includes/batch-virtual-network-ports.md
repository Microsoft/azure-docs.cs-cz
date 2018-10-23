---
title: zahrnout soubor
description: zahrnout soubor
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 10/05/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9246dea7fa12e5ac9378203e96352e917679525b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312540"
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

**Další síťové prostředky** – Batch automaticky přiděluje další síťové prostředky ve skupině prostředků obsahující virtuální síť. Za každých 50 vyhrazených uzlů (nebo každých 20 uzlů s nízkou prioritou) Batch přidělí: 1 skupinu zabezpečení sítě (NSG), 1 veřejnou IP adresu a 1 nástroj pro vyrovnávání zatížení. Pro tyto prostředky platí omezení [kvót prostředků](../articles/azure-subscription-service-limits.md) předplatného. V případě velkých fondů možná bude potřeba požádat o navýšení kvóty pro jeden nebo několik z těchto prostředků.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky. Pro fondy v konfiguraci virtuálního počítače přidává Batch skupiny zabezpečení sítě na úrovni síťových rozhraní připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

* Příchozí provoz přes protokol TCP na portech 29876 a 29877 z IP adres role služby Batch. 
* Příchozí provoz přes protokol TCP na portu 22 (uzly s Linuxem) nebo portu 3389 (uzly s Windows) umožňující vzdálený přístup.
* Odchozí provoz do virtuální sítě na jakémkoli portu.
* Odchozí provoz do internetu na jakémkoli portu.

> [!IMPORTANT]
> Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**.

Nemusíte zadávat skupiny zabezpečení sítě na úrovni podsítě, protože Batch konfiguruje vlastní skupiny zabezpečení sítě. Pokud má však zadaná podsíť přiřazené skupiny zabezpečení sítě (NSG) nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení podle následujících tabulek. Příchozí provoz na portu 3389 (Windows) nebo 22 (Linux) nakonfigurujte pouze v případě, že potřebujete povolit vzdálený přístup k virtuálním počítačům ve fondu. Virtuální počítače ve fondu budou použitelné i bez této konfigurace.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
Všechny <br /><br />Přestože to efektivně vyžaduje „povolit vše“, služba Batch použije skupinu zabezpečení sítě na úrovni síťového rozhraní na každém virtuálním počítači vytvořeném v rámci konfigurace, která filtruje všechny IP adresy, které nejsou služby Batch. | * | Všechny | 29876–29877 | TCP | Povolit |
| Počítače uživatelů používané pro účely ladění, aby byl možný vzdálený přístup k virtuálnímu počítačům ve fondu. | * | Všechny |  3389 (Windows), 22 (Linux) | TCP | Povolit |

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Značka cílové služby | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
| Všechny | 443 | [Značka služby](../articles/virtual-network/security-overview.md#service-tags) | Úložiště (ve stejné oblasti jako váš účet Batch a virtuální síť)  | Všechny | Povolit |

### <a name="pools-in-the-cloud-services-configuration"></a>Fondy v konfigurace služby Cloud Services

**Podporované virtuální sítě** – Pouze virtuální sítě Classic

**ID podsítě** – Při zadávání podsítě pomocí rozhraní API služby Batch použijte *identifikátor prostředku* podsítě. Identifikátor podsítě má tento formát:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Oprávnění** – Instanční objekt `MicrosoftAzureBatch` musí mít pro zadanou virtuální síť roli řízení přístupu na základě role `Classic Virtual Machine Contributor`.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky.

Není potřeba zadávat skupinu zabezpečení sítě, protože Batch konfiguruje příchozí komunikaci do uzlů fondu pouze z IP adres služby Batch. Pokud má však zadaná podsíť přiřazené skupiny zabezpečení sítě nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení podle následujících tabulek. Pokud je komunikace s výpočetními uzly v zadané podsíti zakázaná skupinou NSG, nastaví služba Batch stav výpočetních uzlů na **nepoužitelné**.

 Příchozí provoz na portu 3389 (Windows) nebo 22 (Linux) nakonfigurujte pouze v případě, že potřebujete povolit vzdálený přístup k uzlům fondu. Uzly fondu budou použitelné i bez této konfigurace.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
Všechny <br /><br />Přestože to efektivně vyžaduje „povolit vše“, služba Batch použije pravidlo seznamu ACL na úrovni každého uzlu, které filtruje všechny IP adresy, které nejsou služby Batch. | * | Všechny | 10100, 20100, 30100 | TCP | Povolit |
| Počítače uživatelů používané pro účely ladění, aby byl možný vzdálený přístup k virtuálnímu počítačům ve fondu. | * | Všechny |  3389 (Windows), 22 (Linux) | TCP | Povolit |

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
| Všechny | * | Všechny | 443  | Všechny | Povolit |