---
title: Nasazení Resource Manager a klasický | Microsoft Docs
description: Popisuje rozdíly mezi modelem nasazení Resource Manager a classic (nebo Service Management) modelu nasazení.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: tomfitz
ms.openlocfilehash: 06da24babd470e81bed9c45a32c59ad9cfd153fe
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Azure Resource Manager oproti nasazení classic: pochopení modely nasazení a stav svých prostředků
V tomto článku se dozvíte o Azure Resource Manageru a model nasazení classic. Resource Manager a modely nasazení classic představují dva různé způsoby nasazení a správě řešení Azure. Práce s nimi prostřednictvím dvě rozhraní API sady a nasazené prostředky může obsahovat důležitých rozdílů. Dva modely nejsou vzájemně kompatibilní. Tento článek popisuje tyto rozdíly.

Pro zjednodušení nasazení a správu prostředků, společnost Microsoft doporučuje použít Správce prostředků pro všechny nové prostředky. Pokud je to možné Microsoft doporučuje, znovu nasaďte existujících prostředků prostřednictvím Resource Manager.

Pokud jste nový do Resource Manager, můžete chtít nejprve zkontrolovat přehled terminologie definované v [přehled Azure Resource Manageru](resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Historie nasazení modelů
Azure původně zadat pouze v modelu nasazení classic. V tomto modelu všechny prostředky existovaly nezávisle; došlo k dispozici žádný způsob, jak seskupit související prostředky. Místo toho musíte ručně sledovat prostředky, ke kterým skládá řešení nebo aplikace a nezapomeňte spravovat je v koordinovaný přístup. Nasazení řešení, jste měli k vytvoření každého prostředku jednotlivě prostřednictvím portálu nebo vytvořit skript, který nasadit všechny prostředky ve správném pořadí. Pokud chcete odstranit řešení, jste museli odstranit jednotlivé prostředky. Není snadno můžete použít a aktualizovat zásady řízení přístupu pro související prostředky. Nakonec nelze aplikovat značek k prostředkům a označte je podmínky, které vám pomůžou sledovat vaše prostředky a spravovat fakturace.

V roce 2014 si uvedla Azure Resource Manager, která přidá koncept skupinu prostředků. Skupina prostředků je kontejner pro prostředky, které sdílejí společné životního cyklu. Model nasazení Resource Manager poskytuje několik výhod:

* Můžete nasadit, spravovat a monitorovat všechny služby pro vaše řešení jako se skupinou, nikoli zpracování těchto služeb jednotlivě.
* Můžete opakovaně nasadit řešení v průběhu životního cyklu a mít přitom jistotu, že vaše prostředky jsou nasazeny v konzistentním stavu.
* Řízení přístupu můžete použít na všechny prostředky ve vaší skupině prostředků a tyto zásady budou automaticky použita při přidávání nových prostředků do skupiny prostředků.
* Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.
* JavaScript Object Notation (JSON) můžete použít k definování infrastrukturu pro vaše řešení. Soubor JSON se označuje jako šablony Resource Manageru.
* Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

Pokud jste přidali Resource Manager, všechny prostředky byly zpětně přidány do výchozí skupiny prostředků. Pokud vytvoříte prostředek prostřednictvím teď nasazení classic, prostředku se automaticky vytvoří ve výchozí skupině prostředků pro tuto službu, i když jste nezadali příslušné skupině prostředků v nasazení. Ale že právě existující ve skupině prostředků neznamená, že prostředek byl převeden na modelu Resource Manager.

## <a name="understand-support-for-the-models"></a>Pochopení podporu pro modely
Existují tři scénáře zajímat:

1. Cloudové služby nepodporuje modelu nasazení Resource Manager.
2. Virtuální počítače, účty úložiště a virtuálních sítí podporují modelu nasazení classic i Resource Manager.
3. Všechny ostatní služby Azure podporu správce prostředků.

Pro virtuální počítače, účty úložiště a virtuální sítě Pokud prostředek se vytvořil prostřednictvím nasazení classic, je nutné nadále pracovat s nimi prostřednictvím klasické operace. Resource Manager operations musí používat virtuální počítač, účet úložiště nebo virtuální sítě vytvořený prostřednictvím nasazení Resource Manager. Pokud vaše předplatné obsahuje kombinaci prostředky vytvořené prostřednictvím Resource Manager a nasazení classic, můžete získat tento rozdíl matoucí. Tato kombinace prostředků můžete vytvořit neočekávané výsledky, protože prostředky nepodporují stejné operace.

V některých případech se příkaz Resource Manager můžete načíst informace o zdroji, vytvořené pomocí nasazení classic, nebo můžete při správě například přesun klasický prostředek k jiné skupině prostředků. Ale těchto případech by neměl poskytnout dojem, že podporuje typ operace Resource Manager. Předpokládejme například, že máte skupinu prostředků, která obsahuje virtuální počítač, který byl vytvořen s nasazení classic. Pokud spustíte následující příkaz prostředí PowerShell Resource Manager:

```powershell
Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Vrátí virtuální počítač:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Ale rutinu Resource Manager **Get-AzureRmVM** vrátí pouze virtuální počítače nasazené prostřednictvím Resource Manager. Následující příkaz nevrací virtuálního počítače vytvořena prostřednictvím nasazení classic.

```powershell
Get-AzureRmVM -ResourceGroupName ExampleGroup
```

Pouze prostředky vytvořené pomocí značek podporu správce prostředků. Značky nemůže použít na klasické prostředky.

## <a name="changes-for-compute-network-and-storage"></a>Změny pro výpočty, síť a úložiště
Následující obrázek zobrazuje výpočty, síť a úložiště prostředky nasazené prostřednictvím Resource Manager.

![Správce prostředků – architektura](./media/resource-manager-deployment-model/arm_arch3.png)

Vezměte na vědomí následující vztahy mezi prostředky:

* Všechny prostředky, které existují v rámci skupiny prostředků.
* Virtuální počítač, závisí na konkrétní úložiště účet definovaný v zprostředkovatele prostředku úložiště pro uložení jeho disky v úložišti objektů blob (povinné).
* Virtuální počítač odkazuje na konkrétní síťový adaptér definované v poskytovatele síťových prostředků (povinné) a dostupnosti nastavení definované ve zprostředkovateli výpočetních prostředků (volitelné).
* Síťový adaptér odkazuje na virtuální počítač přiřazenou IP adresu (povinné), podsíť virtuální sítě pro virtuální počítač (povinné) a skupinu zabezpečení sítě (volitelné).
* Podsíť virtuální sítě. odkazuje na skupinu zabezpečení sítě (volitelné).
* Instance služby Vyrovnávání zatížení odkazuje na fond back-end IP adresy, které zahrnují síťový adaptér virtuálního počítače (volitelné) a odkazuje adrese služby Vyrovnávání zatížení veřejných nebo privátních IP (volitelné).

Toto jsou komponenty a jejich vztahů pro nasazení classic:

![Architektura Classic](./media/resource-manager-deployment-model/arm_arch1.png)

Classic řešení pro hostování virtuálního počítače zahrnuje:

* Požadované Cloudová služba, která slouží jako kontejner pro hostování virtuálních počítačů (výpočetní). Virtuální počítače jsou automaticky součástí síťová karta (NIC) a IP adresu přiřadila Azure. Cloudové služby navíc obsahuje instance nástroje pro vyrovnávání zatížení externí, veřejné IP adresy a výchozí koncové body umožnit Vzdálená plocha a Vzdálená prostředí PowerShell provozu pro virtuální počítače se systémem Windows a provoz Secure Shell (SSH) pro virtuální počítače se systémem Linux.
* Účet vyžaduje úložiště, která ukládá virtuální pevné disky pro virtuální počítač, operační systém, včetně dočasné a další datové disky (úložiště).
* Volitelné virtuální sítě, který funguje jako další kontejner, ve kterém můžete vytvořit strukturu podsítěmi a určit podsítě, ve kterém je umístěn virtuální počítač (síť).

Následující tabulka popisuje změny v interakci poskytovatele prostředků výpočty, síť a úložiště:

| Položka | Classic | Resource Manager |
| --- | --- | --- |
| Cloudová služba pro službu Virtual Machines |Cloudová služba byla kontejnerem pro uložení virtuálních počítačů, která vyžadovala dostupnost z platformy a vyrovnávání zatížení. |Cloudová služba už není objektem vyžadovaným pro vytvoření virtuálního počítače pomocí nového modelu. |
| Virtuální sítě |Virtuální síť je volitelný pro virtuální počítač. Pokud zahrnuty, virtuální sítě, nelze nasadit pomocí Správce prostředků. |Virtuální počítač vyžaduje virtuální síť, která byla nasazena s Resource Managerem. |
| Účty úložiště |Virtuální počítač vyžaduje účet úložiště, který ukládá virtuální pevné disky pro operační systém, dočasné a dalších datových disků. |Virtuální počítač vyžaduje účet úložiště pro uložení jeho disky v úložišti objektů blob. |
| Skupiny dostupnosti |Dostupnost pro platformu byla označovaná konfigurací stejného parametru „AvailabilitySetName“ ve službě Virtual Machines. Maximální počet domén selhání byl 2. |Skupina dostupnosti je prostředek vystavený poskytovatelem Microsoft.Compute. Služby Virtual Machines, které vyžadují vysokou dostupnost, musejí být součástí skupiny dostupnosti. Maximální počet domén selhání je teď 3. |
| Skupiny vztahů |Skupiny vztahů byly nezbytné k vytváření služeb Virtual Network. Se zavedením regionálních služeb Virtual Network přestaly být nutné. |Abychom to zjednodušili, koncept skupin vztahů neexistuje v rozhraních API, které se vystavují prostřednictvím správce Azure Resource Manager. |
| Vyrovnávání zatížení |Vytvoření cloudové služby nabízí implicitní nástroj pro vyrovnávání zatížení nasazených služeb Virtual Machines. |Nástroj pro vyrovnávání zatížení je prostředek vystavený poskytovatelem Microsoft.Network. Primární síťové rozhraní služeb Virtual Machines, které potřebuje vyrovnávání zatížení, musí odkazovat na nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení můžou být interní nebo externí. Na instanci služby Vyrovnávání zatížení odkazuje na fond back-end IP adresy, které zahrnují síťový adaptér virtuálního počítače (volitelné) a odkazuje adrese služby Vyrovnávání zatížení veřejných nebo privátních IP (volitelné). |
| Virtuální IP adresa |Cloudové služby získá výchozí VIP (virtuální IP adresy), když je virtuální počítač přidán do cloudové služby. Virtuální IP adresa je adresa přidružená k implicitnímu nástroji pro vyrovnávání zatížení. |Veřejná IP adresa je prostředek vystavený poskytovatelem Microsoft.Network. Veřejná IP adresa může být statická (vyhrazená) nebo dynamická. Dynamické veřejné IP adresy lze přiřadit k nástroji pro vyrovnávání zatížení. Veřejné IP adresy můžete zabezpečit pomocí skupin zabezpečení. |
| Vyhrazená IP adresa |IP adresu můžete v Azure vyhradit a přidružit ji ke cloudové službě, abyste zajistili, že IP adresa zůstane dynamická. |Veřejnou IP adresu můžete vytvořit v režimu statické a nabízí stejné funkce jako rezervovanou IP adresu. |
| Veřejná IP adresa (PIP) na virtuální počítač |Veřejné IP adresy může být také přidružen k virtuálnímu počítači přímo. |Veřejná IP adresa je prostředek vystavený poskytovatelem Microsoft.Network. Veřejná IP adresa může být statická (vyhrazená) nebo dynamická. |
| Koncové body |Vstupní koncové body je třeba konfigurovat na virtuálním počítači, aby se pro určité porty staly otevřeným připojením. Jeden z běžných režimů připojení k virtuálním počítačům se provádí nastavením vstupní koncových bodů. |Příchozí pravidla NAT můžete konfigurovat na nástrojích pro vyrovnávání zatížení, abyste dosáhli stejné možnosti povolování koncových bodů na konkrétních portech za účelem připojení k virtuálním počítačům. |
| Název DNS |Cloudová služba by získala implicitní, globálně jedinečný název DNS. Například: `mycoffeeshop.cloudapp.net`. |Názvy DNS jsou volitelné parametry, které můžete nastavit na prostředku veřejné IP adresy. Plně kvalifikovaný název domény je ve formátu - `<domainlabel>.<region>.cloudapp.azure.com`. |
| Síťová rozhraní |Primární a sekundární síťové rozhraní a jeho vlastnosti byly definované jako síťová konfigurace virtuálního počítače. |Síťové rozhraní je prostředek vystavený poskytovatelem Microsoft.Network. Životní cyklus síťového rozhraní není vázaný na virtuální počítač. Odkazuje na virtuální počítač přiřazenou IP adresu (povinné), podsíť virtuální sítě pro virtuální počítač (povinné) a skupinu zabezpečení sítě (volitelné). |

Další informace o připojení virtuální sítě z různé modely nasazení najdete v tématu [připojení virtuální sítě z různé modely nasazení portálu](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrace z classic do Resource Manager
Pokud jste připravení migrovat prostředky z nasazení classic nasazení Resource Manager, najdete v části:

1. [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](../virtual-machines/windows/migration-classic-resource-manager-deep-dive.md)
2. [Platforma podporovaná migrace z klasického do Azure Resource Manageru prostředků IaaS](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
3. [Migrovat prostředky infrastruktury z klasického do Azure Resource Manageru pomocí prostředí Azure PowerShell](../virtual-machines/windows/migration-classic-resource-manager-ps.md)
4. [Migrovat prostředky infrastruktury z klasického do Azure Resource Manageru pomocí rozhraní příkazového řádku Azure](../virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Můžete vytvořit virtuální počítač pomocí Správce prostředků pro nasazení ve virtuální síti vytvořené pomocí klasického nasazení?**

Tato konfigurace není podporována. Správce prostředků nelze použít k nasazení virtuálního počítače do virtuální sítě, která byla vytvořena pomocí nasazení classic.

**Můžete vytvořit virtuální počítač pomocí Správce prostředků z uživatelského image, která byla vytvořena pomocí modelu nasazení classic?**

Tato konfigurace není podporována. Však můžete zkopírovat soubory virtuálního pevného disku z účtu úložiště, která byla vytvořena pomocí modelu nasazení classic a přidejte je do nový účet vytvořený prostřednictvím Resource Manager.

**Jaký bude dopad na kvótu pro Moje předplatné?**

Kvóty pro virtuální počítače, virtuální sítě a účty úložiště vytvořené pomocí Azure Resource Manager jsou oddělené od dalších kvóty. Každé předplatné získá kvóty pro vytvoření prostředků pomocí nových rozhraní API. Další informace o dodatečných kvótách najdete [tady](../azure-subscription-service-limits.md).

**Můžete nadále používat své automatizované skripty pro zřizování virtuálních počítačů, virtuálních sítí a účty úložiště prostřednictvím rozhraní API Resource Manager?**

Automatizace a skripty, které jste vytvořili pokračovat v práci pro existující virtuální počítače, virtuální sítě vytvořené v režimu Azure Service Management. Skripty je však měly aktualizovat, aby používaly nové schéma pro vytváření stejných prostředků prostřednictvím režimu Resource Manager.

**Kde najdu Příklady šablon Azure Resource Manager?**

Ucelenou sadu úvodních šablon najdete na [rychlých šablonách správce Azure Resource Manager](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Další postup
* Provede procesem vytvoření šablony, která definuje virtuální počítač, účet úložiště a virtuální sítě, najdete v tématu [názorný Průvodce šablonou Resource Manageru](resource-manager-template-walkthrough.md).
* Příkazy pro nasazení šablony najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

