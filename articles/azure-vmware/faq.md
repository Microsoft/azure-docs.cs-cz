---
title: Nejčastější dotazy
description: Poskytuje odpovědi na některé běžné dotazy týkající se řešení Azure VMware (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 1649b5649bd18b7ab53f3cc0196d7dff0f6f5b2c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112682"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Nejčastější dotazy týkající se řešení Azure VMware (AVS) Preview

Odpovědi na nejčastější dotazy týkající se řešení Azure VMware (AVS).

## <a name="general"></a>Obecné

**Co je řešení Azure VMware (AVS)?**

Vzhledem k tomu, že podniky sledují strategie pro moderní účely, aby vylepšili flexibilitu, snížili náklady a urychlili inovace, hybridní cloudové platformy se vypracovaly jako klíčové aktivátory digitální transformace zákazníků. Služba AVS kombinuje software definovaného datového centra (SDDC) společnosti VMware s využitím ekosystému Microsoft Azure Global Cloud Service. Řešení AVS je spravované tak, aby splňovalo požadavky na výkon, dostupnost, zabezpečení a dodržování předpisů.

## <a name="avs-service"></a>Služba AVS

**Kde je tato funkci AVS k dispozici ještě dnes?**

Ve verzi Preview je k dispozici v USA – východ v Severní Amerika a v Amsterdam v západní Evropě.

**Můžou úlohy spuštěné v instanci řešení Azure VMware (AVS) využívat nebo integrovat se službami Azure?**

K dispozici jsou všechny služby Azure, které budou dostupné pro zákazníky řešení AVS. Omezení výkonu a dostupnosti pro konkrétní služby se musí řešit na základě případu.

**Používají se stejné nástroje, které teď používám ke správě prostředků privátního cloudu?**

Yes. Azure Portal se používá pro nasazení a řadu operací správy. vCenter a NSX Manager se používají ke správě prostředků vSphere a NSX-T.

**Můžu pomocí místního serveru vCenter spravovat privátní cloud?**

Funkce AVS při spuštění nepodporuje jednotné prostředí pro správu v místních i privátních cloudových prostředích. Clustery privátního cloudu se budou spravovat pomocí vCenter a NSX Manageru v privátním cloudu.

**Můžu používat vRealize Suite spuštěnou místně?** 

Konkrétní integrace a případy použití mohou být vyhodnoceny případ od případu.

**Můžu migrovat virtuální počítače s vSphere z místního prostředí do služby AVS Private cloudy?**

Yes. Migrace virtuálních počítačů a vMotion se dají použít k přesunu virtuálních počítačů do privátního cloudu, pokud jsou splněné standardní systémy přes vCenter [požadavky] [ https://kb.vmware.com/s/article/210695 ].

**Vyžaduje se v místních prostředích konkrétní verze vSphere?**

Vzhledem k tomu, že všechna cloudová prostředí jsou dodávána s HCX, vSphere 5,5 nebo novějším v místních prostředích pro vMotion.

**Jak proces řízení změn vypadá jako?**

Aktualizace provedené v samotné službě budou následovat po standardním procesu správy změn Microsoft Azure. Zákazníci zodpovídají za všechny úlohy správy úloh a přidružené procesy správy změn.

**Jak se to liší od řešení Azure VMware podle CloudSimple?**

Díky novému řešení Azure VMware mají Microsoft a VMware přímé partnerství s poskytovatelem cloudu. Nové řešení je výhradně navržené, sestavené a podporované společností Microsoft a schválené VMware. Tato řešení jsou v souladu s architekturou technologie VMware, která běží na vyhrazené infrastruktuře Azure.

**Pokud jsem stávající zákazník řešení Azure VMware, co mi tato verze Preview znamená?**

Nedošlo k žádné změně stávajícího řešení Azure VMware pomocí CloudSimple. Dál podporujeme řešení v Azure. Řešení Azure VMware je zajištěné naší smlouvou o úrovni služeb [(SLA)](https://aka.ms/CSVMwareSLA). Zákazníci by měli tuto službu i nadále používat pro produkční úlohy; Toto řešení je dostupné na základě [podmínek služby společnosti Microsoft](https://azure.microsoft.com/support/legal/).

**Můžu z řešení Azure VMware migrovat CloudSimple do tohoto nového řešení?**

Ano, řešení Azure VMware podporuje migraci pomocí známých nástrojů VMware, jako je HCX. Pro zákazníky, kteří mají zájem o migraci na nové řešení, prosím Spolupracujte se svým týmem účet Microsoft a prozkoumejte možnosti a dostupnou podporu.



## <a name="compute-network-and-storage"></a>Výpočetní prostředky, sítě a úložiště

**Je k dispozici více než jeden typ hostitele?**

K dispozici je pouze jeden typ hostitele.

**Jaké jsou specifikace procesoru v jednotlivých typech hostitelů?**

Servery mají duální procesory Intel Core 2,3 GHz.

**Kolik paměti je v každém hostiteli?**

Servery mají 576 GB paměti RAM.

**Jaká je kapacita úložiště každého hostitele?**

Každý hostitel ESXi má dvě síti vSAN diskgroups s úrovní kapacity 15,2 TB a 3,2 TB NVMe úrovně mezipaměti (1,6 TB v každé ze všech diskových složek).

**Kolik šířky pásma sítě je k dispozici na každém hostiteli ESXi?**

Hostitelé ESXi podporují šířku pásma připojení až na 25 GB/s.

**Jsou data uložená v úložišti datasíti vsans zašifrovaná v klidovém stavu?**

Ano, všechna síti vSAN data se ve výchozím nastavení šifrují pomocí klíčů uložených v Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hostitelé, clustery a privátní cloudy

**Sdílí se podkladová infrastruktura?**

Ne, hostitelé a clustery privátního cloudu jsou vyhrazené a bezpečně se vymažou před a po použití.

**Jaký je minimální a maximální počet hostitelů na cluster?**

Clustery se můžou škálovat mezi 3 a 16 hostiteli ESXi. Clustery zkušební verze jsou omezené na tři hostitele.

**Můžu škálovat svoje clustery privátního cloudu?**

Ano, clustery se škálují mezi minimálním a maximálním počtem hostitelů ESXi. Clustery zkušební verze jsou omezené na tři hostitele.

**Co jsou zkušební clustery?**

Clustery zkušební verze jsou tři hostitelské clustery, které se používají k měsíčnímu hodnocení privátních cloudů služby AVS.

**Můžu pro zkušební clustery použít špičkové hostitele?**

No. Vysoce koncoví hostitelé ESXi jsou rezervované pro použití v produkčních clusterech.

## <a name="avs-and-vmware-software"></a>Software pro funkci AVS a VMware

**Jaké verze softwaru VMware se používají v privátních cloudech?**

Privátní cloudy používají vSphere 6,7, síti vSAN 6,7, HCX a Version 2,5 NSX-T.  

**Používají privátní cloudy VMware NSX?**

Ano, NSX-T 2,5 se používá pro softwarově definované sítě v privátních cloudech služby AVS.

**Můžu použít VMware NSX-V v privátním cloudu?**

Ne. NSX-T je jediná podporovaná verze NSX.

**NSX se vyžaduje v místních prostředích nebo sítích, které se připojují k privátnímu cloudu.**

Ne, nemusíte používat NSX místně.

**Jaký je plán upgradu a aktualizace softwaru VMware v privátním cloudu?**

Upgrady sady prostředků privátního cloudu jsou hotové, aby software zůstal v rámci jedné verze nejnovější verze softwarové sady od VMware. Verze privátního cloudového softwaru se mohou lišit od nejaktuálnější verze jednotlivých softwarových komponent (ESXi, NSX-T, vCenter, síti vSAN).

**Jak často bude balíček softwaru pro privátní cloud aktualizovaný?**

Software privátního cloudu se upgraduje podle plánu, který sleduje vydání softwarového balíčku od VMware. Váš privátní cloud nevyžaduje pro upgrady výpadky.

## <a name="connectivity"></a>Připojení

**Jaké plánování síťových IP adres je potřeba k začleňování privátních cloudů do místních prostředí?**

Pro nasazení privátního cloudu služby AVS je vyžadován adresní prostor privátní sítě/22. Tento privátní adresní prostor by se neměl překrývat s ostatními virtuálními sítěmi v rámci předplatného nebo s místními sítěmi.
 
**Návody se z místních prostředí připojit k privátnímu cloudu služby AVS?**

Ke službě se můžete připojit jedním ze dvou způsobů: 

- S virtuálním počítačem nebo aplikační bránou nasazenou ve službě Azure Virtual Network, která je peered přes ExpressRoute, do privátního cloudu.
- Prostřednictvím ExpressRoute Global Reach z místního datacentra do okruhu Azure ExpressRoute.

**Návody připojit virtuální počítač úlohy k Internetu nebo koncovému bodu služby Azure?**

V Azure Portal povolte připojení k Internetu pro privátní cloud. Pomocí Správce NSX-T vytvořte směrovač NSX-T T1 a logický přepínač. Pak pomocí vCenter nasadíte virtuální počítač na segment sítě definovaný logickým přepínačem. Tento virtuální počítač bude mít síťový přístup k Internetu a službám Azure.

**Potřebuji omezit přístup z Internetu k virtuálním počítačům v logických sítích v privátním cloudu?**

No. Příchozí síťový provoz z Internetu přímo do privátních cloudů není povolený.

**Je potřeba omezit přístup k Internetu z virtuálních počítačů v logických sítích na Internet?**

Yes. Abyste mohli vytvořit bránu firewall, která omezuje přístup virtuálních počítačů k Internetu, budete muset použít Správce NSX-T.

## <a name="accounts-and-privileges"></a>Účty a oprávnění

**Jaké účty a oprávnění se zobrazí v mém novém privátním cloudu pro funkci AVS?**

K dispozici jsou přihlašovací údaje pro uživatele cloudadmin v vCenter a přístup správce NSX-T Manageru. K dispozici je také skupina CloudAdmin, kterou lze použít k začlenění Azure Active Directory. Další informace najdete v tématu [Koncepty přístupu a identity](concepts-identity.md).

**Může mít správce přístup k hostitelům ESXi?**

Ne, přístup správce k ESXi je omezen na splnění požadavků na zabezpečení řešení.

**Jaká oprávnění a oprávnění budou v vCenter?**

Budete mít CloudAdmin oprávnění skupiny. Další informace najdete v tématu [Koncepty přístupu a identity](concepts-identity.md).

**Jaká oprávnění a oprávnění bude mít správce NSX-T?**

Budete mít úplná oprávnění správce NSX-T a mohli spravovat řízení přístupu na základě rolí, jako byste to měli v místním datovém centru NSX-T. Další informace najdete v tématu [Koncepty přístupu a identity](concepts-identity.md).

> [!NOTE]
> Vytvoří se a nakonfiguruje směrovač T0 v rámci nasazení privátního cloudu. Jakékoli změny tohoto logického směrovače nebo virtuálních počítačů NSX-T hraničních uzlů můžou mít vliv na připojení k vašemu privátnímu cloudu.

## <a name="billing-and-support"></a>Fakturace a podpora

**Jak se mi bude účtovat během verze Preview funkce AVS**

Faktura za službu AVS ve verzi Preview je měsíčně na základě průběžných plateb. Další možnosti budou k dispozici při obecné dostupnosti.

**Jak budou během verze Preview funkce AVS strukturované ceny?**

Obecné otázky týkající se cen najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/azure-vmware) řešení Azure VMware. Ceny verze Preview jsou dostupné na vyžádání, obraťte se prosím na svůj tým účtu nebo podle odkazu na stránce s cenami kontaktujte prodej.

**Kdo podporuje funkci AVS?**

Společnost Microsoft doručuje podporu služby AVS. Poznamenejte si prosím naše pokyny pro verzi Preview a poskytneme vám podporu během 9 až 5 hodin PST v pracovní době od pondělí do pátku. Z [tohoto odkazu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) můžete vyvolat lístek podpory.

**Jaké účty potřebuji k vytvoření privátního cloudu služby AVS?**

Budete potřebovat účet Azure v předplatném Azure.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Jak Návody požádat o zvýšení kvóty hostitele pro řešení Azure VMware?**

Můžete požádat o zvýšení kvóty tím, že [odešlete žádost o podporu](..\azure-portal\supportability\how-to-create-azure-support-request.md). Tým správy kvót vyhodnocuje žádost a schválí ji do tří pracovních dnů.  

> [!IMPORTANT]
> Než budete moct požádat o zvýšení kvóty, nezapomeňte zaregistrovat poskytovatele prostředků **Microsoft. AVS** v Azure Portal.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

1. V Azure Portal v části **pomoc a podpora**vytvořte **novou žádost o podporu** a zadejte pro lístek následující informace:
   - **Typ problému:** Odbornou
   - **Předplatné:** ID vašeho předplatného
   - **Služba:**  Řešení Azure VMware 
   - **Shrnutí:** Zvýšení kvóty
   - **Typ problému:** Problémy se správou kapacity
   - **Podtyp problému:** Požadavek zákazníka na další kvótu/kapacitu hostitele

1. V popisu lístku podpory zadejte na kartě Podrobnosti:
   - Počet dalších uzlů   
   - SKU uzlu
   - Oblast

   > [!NOTE] 
   > Ve výchozím nastavení se udělí minimálně čtyři uzly.

1. Žádost odešlete kliknutím na tlačítko **zkontrolovat + vytvořit** .

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
