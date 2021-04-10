---
title: zahrnout soubor
description: zahrnout soubor
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 02/16/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: baf146bdd89d45c5d7e1ed359822a35d383b7b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103561906"
---
### <a name="general-requirements"></a>Obecné požadavky

* Virtuální síť musí být ve stejném předplatném a stejné oblasti jako účet Batch, který použijete k vytvoření fondu.

* Podsíť zadaná pro fond musí obsahovat dostatek nepřiřazených IP adres pro všechny virtuální počítače, na které fond cílí, jejichž počet je součtem vlastností `targetDedicatedNodes` a `targetLowPriorityNodes` fondu. Pokud podsíť nemá dostatek nepřiřazených IP adres, fond částečně přidělí výpočetní uzly a dojde k chybě změny velikosti.

* Všechny vlastní servery DNS obsluhující virtuální síť musí být schopné přeložit váš koncový bod služby Azure Storage. Konkrétně musí být možné přeložit adresy URL ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net`.

* Ve stejné virtuální síti nebo ve stejné podsíti se dá vytvořit víc fondů (Pokud má dostatečný adresní prostor). Jeden fond nemůže existovat v rámci více virtuální sítě nebo podsítí.

Další požadavky na virtuální síť se liší podle toho, jestli je fond Batch v konfiguraci virtuálního počítače nebo konfiguraci služby Cloud Services. Pro nová nasazení fondů do virtuální sítě se doporučuje konfigurace virtuálního počítače.

### <a name="pools-in-the-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

**Podporované virtuální sítě** – Pouze virtuální sítě založené na Azure Resource Manageru

**ID podsítě** – Při zadávání podsítě pomocí rozhraní API služby Batch použijte *identifikátor prostředku* podsítě. Identifikátor podsítě má tento formát:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**Oprávnění** – Zkontrolujte, jestli vaše zásady zabezpečení nebo zámky na předplatném nebo skupině prostředků virtuální sítě neomezují oprávnění uživatele spravovat tuto virtuální síť.

**Další síťové prostředky** – Batch automaticky vytvoří další síťové prostředky ve skupině prostředků, která obsahuje virtuální síť.

> [!IMPORTANT]
> Pro každý 100 vyhrazený uzel nebo uzly s nízkou prioritou vytvoří služba Batch: jednu skupinu zabezpečení sítě (NSG), jednu veřejnou IP adresu a jeden nástroj pro vyrovnávání zatížení. Pro tyto prostředky platí omezení [kvót prostředků](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) předplatného. V případě velkých fondů možná bude potřeba požádat o navýšení kvóty pro jeden nebo několik z těchto prostředků.

#### <a name="network-security-groups-batch-default"></a>Skupiny zabezpečení sítě: Výchozí hodnota služby Batch

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky podle potřeb vaší úlohy. Pro fondy v konfiguraci virtuálního počítače přidává Batch skupiny zabezpečení sítě na úrovni síťových rozhraní připojených k výpočetním uzlům. U těchto skupin zabezpečení sítě jsou nakonfigurovaná následující další pravidla:

* Příchozí provoz přes protokol TCP na portech 29876 a 29877 z IP adres služby Batch, které odpovídají značce služby `BatchNodeManagement`.
* Příchozí provoz přes protokol TCP na portu 22 (uzly s Linuxem) nebo portu 3389 (uzly s Windows) umožňující vzdálený přístup. U některých typů úkolů s více instancemi v Linuxu (jako je MPI) bude potřeba povolit také provoz přes protokol SSH na portu 22 pro IP adresy v podsíti, která obsahuje výpočetní uzly služby Batch. Toto je možné zablokovat pravidly NSG na úrovni podsítě (viz níže).
* Odchozí provoz do virtuální sítě na jakémkoli portu. Toto je možné upravit pravidly NSG na úrovni podsítě (viz níže).
* Odchozí provoz do internetu na jakémkoli portu. Toto je možné upravit pravidly NSG na úrovni podsítě (viz níže).

> [!IMPORTANT]
> Buďte opatrní při úpravách nebo přidávání příchozích nebo odchozích pravidel v skupin zabezpečení sítěě nakonfigurovaném pro dávkovou práci. Pokud je komunikace s výpočetními uzly v zadané podsíti zamítnutá NSG, služba Batch nastaví stav výpočetních uzlů na **nepoužitelné**. Kromě toho by se neměly žádné zámky prostředků použít pro všechny prostředky vytvořené službou Batch, protože to může zabránit vyčištění prostředků v důsledku akcí iniciované uživatelem, jako je například odstranění fondu.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Skupiny zabezpečení sítě: Zadání pravidel na úrovni podsítě

Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě virtuální sítě, protože Batch konfiguruje vlastní skupin zabezpečení sítě (viz výše). Pokud máte NSG přidruženou k podsíti, ve které jsou nasazené dávkové výpočetní uzly, nebo pokud chcete použít vlastní pravidla NSG pro přepsání použitých výchozích nastavení, musíte tento NSG nakonfigurovat alespoň na pravidla zabezpečení příchozích a odchozích připojení uvedená v následujících tabulkách.

Příchozí provoz na portu 3389 (Windows) nebo 22 (Linux) nakonfigurujte pouze v případě, že potřebujete povolit vzdálený přístup k výpočetním uzlům z externích zdrojů. Pokud potřebujete zajistit podporu úkolů s více instancemi a některými moduly runtime MPI, možná bude potřeba v Linuxu povolit pravidla pro port 22. Výpočetní uzly ve fondu budou použitelné i bez povolení provozu na těchto portech.

> [!WARNING]
> IP adresy služby Batch se můžou v průběhu času měnit. Proto důrazně doporučujeme, abyste `BatchNodeManagement` pro pravidla NSG, která jsou uvedena v následujících tabulkách, používali značku služby (nebo místní varianta). Vyhněte se naplnění pravidel NSG s konkrétními IP adresami služby Batch.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Značka zdrojové služby | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- | --- |
| – | `BatchNodeManagement`[značka služby](../articles/virtual-network/network-security-groups-overview.md#service-tags) (při použití regionální varianty ve stejné oblasti jako účet Batch) | * | Všechny | 29876–29877 | TCP | Povolit |
| Zdrojové IP adresy uživatelů pro vzdálený přístup k výpočetním uzlům nebo podsíti výpočetních uzlů pro úlohy Linuxu s více instancemi, pokud je to potřeba | – | * | Všechny | 3389 (Windows), 22 (Linux) | TCP | Povolit |

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Značka cílové služby | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- | --- |
| Všechny | * | [Značka služby](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `Storage` (pokud používáte místní variantu ve stejné oblasti jako váš účet Batch) | 443 | TCP | Povolit |
| Všechny | * | [Značka služby](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `BatchNodeManagement` (pokud používáte místní variantu ve stejné oblasti jako váš účet Batch) | 443 | TCP | Povolit |

`BatchNodeManagement`Pro kontaktování služby Batch z výpočetních uzlů, jako jsou úlohy Správce úloh, se vyžaduje odchozí připojení.

### <a name="pools-in-the-cloud-services-configuration"></a>Fondy v konfigurace služby Cloud Services

> [!WARNING]
> Fondy konfigurace cloudové služby jsou zastaralé. Místo toho prosím použijte fondy konfigurací virtuálních počítačů.

**Podporované virtuální sítě** – Pouze virtuální sítě Classic

**ID podsítě** – Při zadávání podsítě pomocí rozhraní API služby Batch použijte *identifikátor prostředku* podsítě. Identifikátor podsítě má tento formát:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**Oprávnění** – `Microsoft Azure Batch` instanční objekt musí mít `Classic Virtual Machine Contributor` pro zadanou virtuální síť roli Azure.

#### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Podsíť musí umožňovat příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy na výpočetních uzlech, a odchozí komunikaci kvůli komunikaci se službou Azure Storage nebo jinými prostředky.

Není potřeba zadávat skupinu zabezpečení sítě, protože Batch konfiguruje příchozí komunikaci do uzlů fondu pouze z IP adres služby Batch. Pokud má však zadaná podsíť přiřazené skupiny zabezpečení sítě nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení podle následujících tabulek. Pokud je komunikace s výpočetními uzly v zadané podsíti zamítnutá NSG, služba Batch nastaví stav výpočetních uzlů na **nepoužitelné**.

Pokud potřebujete povolit přístup k uzlům fondu přes protokol RDP, nakonfigurujte pro Windows příchozí provoz na portu 3389. Tento postup není nutný, pokud se uzly fondu dají použít.

**Příchozí pravidla zabezpečení**

| Zdrojové IP adresy | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
Všechny <br /><br />Přestože to efektivně vyžaduje „povolit vše“, služba Batch použije pravidlo seznamu ACL na úrovni každého uzlu, které filtruje všechny IP adresy, které nejsou služby Batch. | * | Všechny | 10100, 20100, 30100 | TCP | Povolit |
| Volitelné povolení přístupu k výpočetním uzlům přes protokol RDP | * | Všechny | 3389 | TCP | Povolit |

**Odchozí pravidla zabezpečení**

| Zdroj | Zdrojové porty | Cíl | Cílové porty | Protocol (Protokol) | Akce |
| --- | --- | --- | --- | --- | --- |
| Všechny | * | Všechny | 443  | Všechny | Povolit |
