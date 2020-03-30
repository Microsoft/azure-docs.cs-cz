---
title: Glosář Azure – slovník Azure | Dokumenty společnosti Microsoft
description: Glosář Azure slouží k pochopení terminologie cloudu na platformě Azure. Tento krátký slovník Azure poskytuje definice běžných cloudových termínů pro Azure.
keywords: Azure slovník, cloudová terminologie, Glosář Azure, definice terminologie, termíny cloudu
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 7c80ba6b15cec08da9bcefea243493a824ef66bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276475"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glosář Microsoft Azure: Slovník cloudové terminologie na platformě Azure

Glosář Microsoft Azure je krátký slovník cloudové terminologie pro platformu Azure. Viz také:

* [Microsoft Azure a Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) – definice služeb Azure a jejich protějšků z AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Podmínky cloud computingu](https://azure.microsoft.com/overview/cloud-computing-dictionary/) – obecné podmínky průmyslového cloudu.

## <a name="account"></a>account
Účet, který se používá k přístupu a správě předplatného Azure. Často se označuje jako účet Azure, i když účet může být libovolný: existující pracovní, školní nebo osobní účet Microsoft nebo uživatelské jméno a heslo Office 365. Můžete si také vytvořit účet pro správu předplatného Azure, když se zaregistrujete k [bezplatné zkušební verzi](https://azure.microsoft.com).  
Viz [Registrace předplatného Azure pomocí účtu Office 365](cost-management-billing/manage/office-365-account-for-azure-subscription.md) a [účtů, které můžete použít k přihlášení](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplikace API
Jiný název [aplikace App Service](#app-service-app).

## <a name="app-service-app"></a>Aplikace služby App Service
Výpočetní prostředky, které [služba Azure App Service](app-service/overview.md) poskytuje pro hostování webu nebo webové aplikace, webového rozhraní API nebo [back-endu mobilní aplikace](app-service-mobile/app-service-mobile-value-prop.md). Aplikace Služby App Service se také označují jako *Služby aplikací*, *webové aplikace*, *aplikace API*a *mobilní aplikace*.

## <a name="availability-set"></a>dostupnost set
Kolekce virtuálních počítačů, které jsou spravovány společně poskytovat redundanci a spolehlivost aplikací. Použití skupiny dostupnosti zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač.  
Viz [Správa dostupnosti virtuálních počítačů s Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Správa [dostupnosti virtuálních počítačů s Linuxem](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klasický model nasazení Azure
Jeden ze dvou [modelů nasazení](resource-manager-deployment-model.md) používaných k nasazení prostředků v Azure (nový model je Azure Resource Manager). Některé služby Azure podporují jenom model nasazení Resource Manageru, některé podporují pouze klasický model nasazení a některé podporují obojí. Dokumentace pro každou službu Azure určuje, které modely podporují.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Rozhraní příkazového řádku Azure (CLI)
Rozhraní příkazového řádku, které lze použít ke správě služeb Azure z Windows, macOS a Linuxu.  Některé služby nebo funkce služby lze spravovat pouze prostřednictvím prostředí PowerShell nebo CLI. Podívejte se na [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Rozhraní příkazového řádku pro správu služeb Azure prostřednictvím příkazového řádku z počítačů s Windows. Některé služby nebo funkce služby lze spravovat pouze prostřednictvím prostředí PowerShell nebo CLI.
Přečtěte [si, jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Model nasazení Azure Resource Manageru
Jeden ze dvou [modelů nasazení](resource-manager-deployment-model.md) používaných k nasazení prostředků v Microsoft Azure (druhý je klasický model nasazení). Některé služby Azure podporují jenom model nasazení Resource Manageru, některé podporují pouze klasický model nasazení a některé podporují obojí. Dokumentace pro každou službu Azure určuje, které modely podporují.

## <a name="fault-domain"></a>doména selhání
Kolekce virtuálních počítačů v množině dostupnosti, které může případně selhat ve stejnou dobu. Příkladem je skupina počítačů v racku, které sdílejí společný zdroj napájení a síťový přepínač. V Azure jsou virtuální počítače v sadě dostupnosti automaticky odděleny mezi více domén ách selhání.  
Viz [Správa dostupnosti virtuálních počítačů s Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo Správa [dostupnosti virtuálních počítačů s Linuxem](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geo
Definovaná hranice pro rezidenci dat, která obvykle obsahuje dvě nebo více oblastí. Hranice mohou být uvnitř nebo za státními hranicemi a jsou ovlivněny daňovou regulací. Každá geo má alespoň jednu oblast. Příklady geos jsou Asie a Tichomoří a Japonsko. Také se nazývá *geografie*.  
Podívejte se na [oblasti Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geografická replikace
Proces automatické replikace obsahu, jako jsou objekty BLOB, tabulky a fronty v rámci místní dvojice.  
Viz [Aktivní geografická replikace pro databázi Azure SQL](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Soubor, který obsahuje konfiguraci operačního systému a aplikace, kterou lze použít k vytvoření libovolného počtu virtuálních počítačů. V Azure existují dva typy bitových kopií: image virtuálního počítače a image operačního systému. Bitová kopie virtuálního počítače obsahuje operační systém a všechny disky připojené k virtuálnímu počítači při vytvoření bitové kopie. Bitová kopie operačního systému obsahuje pouze zobecněný operační systém bez konfigurace datového disku.  
Viz [Navigace a výběr ibi virtuálních strojů Windows v Azure pomocí PowerShellu nebo cli](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Limity
Počet prostředků, které lze vytvořit, nebo výkonnostní měřítko, kterého lze dosáhnout. Limity jsou obvykle spojeny s předplatnými, službami a nabídkami.  
Viz [Omezení předplatného a služeb Azure, kvóty a omezení](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>vyrovnávání zatížení
Prostředek, který distribuuje příchozí provoz mezi počítače v síti. V Azure nástroj pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů definovaných v sadě nástroje pro vyrovnávání zatížení. [Vyrovnávání zatížení](load-balancer/load-balancer-overview.md) může být internet-čelí, nebo to může být interní.  

## <a name="mobile-app"></a>mobilní aplikace
Jiný název aplikace [App Service App](#app-service-app).

## <a name="offer"></a>offer
Ceny, kredity a související podmínky vztahující se k předplatnému Azure.  
Podívejte se na [stránku s podrobnostmi o nabídce Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portál
Zabezpečený webový portál používaný k nasazení a správě služeb Azure.

## <a name="region"></a>oblast
Oblast v rámci zeměpisné oblasti, která nepřekračuje státní hranice a obsahuje jedno nebo více datových center. Ceny, místní služby a typy nabídek jsou vystaveny na úrovni regionu. Oblast je obvykle spárována s jinou oblastí, která může být až několik set mil daleko. Místní páry lze použít jako mechanismus pro zotavení po havárii a scénáře vysoké dostupnosti. Označuje se také jako *umístění*.  
Podívejte se na [oblasti Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>prostředek
Položka, která je součástí vašeho řešení Azure. Každá služba Azure umožňuje nasadit různé typy prostředků, jako jsou databáze nebo virtuální počítače.   
Podívejte se [na přehled Správce prostředků Azure](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>skupina prostředků
Kontejner ve Správci prostředků, který obsahuje související prostředky pro aplikaci. Skupina prostředků může obsahovat všechny prostředky pro aplikaci nebo pouze ty prostředky, které jsou logicky seskupeny. To, jakým způsobem se prostředky přidělí do skupin prostředků, můžete rozhodnout na základě toho, co je pro vaši organizaci nejvhodnější.  
Podívejte se [na přehled Správce prostředků Azure](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Šablona Správce prostředků
Soubor JSON, který deklarativně definuje jeden nebo více prostředků Azure a který definuje závislosti mezi nasazenými prostředky. Šablony lze použít k nasazení prostředků konzistentně a opakovaně.  
Viz [Vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>poskytovatel prostředků
Služba, která poskytuje prostředky, které můžete nasadit a spravovat prostřednictvím Správce prostředků. Každý poskytovatel prostředků nabízí operace pro práci s nasazenými prostředky. K poskytovatelům prostředků se dá přistupovat prostřednictvím portálu Azure, Azure PowerShellu a několika programovacích sad SDK.  
Podívejte se [na přehled Správce prostředků Azure](azure-resource-manager/management/overview.md)

## <a name="role"></a>role
Prostředek pro řízení přístupu, který lze přiřadit uživatelům, skupinám a službám. Role jsou schopné provádět akce, jako je vytváření, správa a čtení prostředků Azure.  
Viz [RBAC: Předdefinované role](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>smlouva o úrovni služeb (SLA)
Smlouva, která popisuje závazky společnosti Microsoft pro dobu připojení a připojení. Každá služba Azure má konkrétní sla.  
Viz [Smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>sdílený přístupový podpis (SAS)
Podpis, který umožňuje udělit omezený přístup k prostředku bez vystavení klíče účtu. Azure Storage například [používá SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) k udělení přístupu klienta k objektům, jako jsou objekty BLOB. [IoT Hub používá SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) udělit zařízení oprávnění k odesílání telemetrie.

## <a name="storage-account"></a>účet úložiště
Účet, který vám umožní přístup k službám Azure Blob, Front, Table a File Services v Azure Storage. Název účtu úložiště definuje jedinečný obor názvů pro datové objekty Azure Storage.  
Informace [o účtech úložiště Azure](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>předplatné
Zákaznická smlouva se společností Microsoft, která jim umožňuje získat služby Azure. Ceny předplatného a související podmínky se řídí nabídkou zvolenou pro předplatné.
Podívejte se [na smlouvu o předplatném Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/) a jak jsou [předplatná Azure přidružená k Azure Active Directory.](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>značka
Termín indexování, který umožňuje kategorizovat prostředky podle vašich požadavků na správu nebo fakturaci. Pokud máte složitou kolekci prostředků, můžete použít značky k vizualizaci těchto datových zdrojů způsobem, který dává největší smysl. Můžete například označit prostředky, které mají v rámci organizace podobnou roli nebo které patří do stejného oddělení.  
Viz [Použití značek k uspořádání prostředků Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>aktualizovat doménu
Kolekce virtuálních počítačů v množině dostupnosti, které jsou aktualizovány ve stejnou dobu. Virtuální počítače ve stejné doméně aktualizace jsou restartovány společně během plánované údržby. Azure nikdy restartuje více než jednu doménu aktualizace najednou. Označuje se také jako doména upgradu.  
Viz [Správa dostupnosti virtuálních počítačů s Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Správa [dostupnosti virtuálních počítačů s Linuxem](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>virtuální stroj
Implementace softwaru fyzického počítače, který spouští operační systém. Na stejném hardwaru může současně běžet více virtuálních počítačů. V Azure jsou virtuální počítače dostupné v různých velikostech.  
Viz [Dokumentace k virtuálním počítačům](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>Rozšíření virtuálního počítače
Prostředek, který implementuje chování nebo funkce, které pomáhají jiným programům pracovat nebo poskytují možnost pracovat se spuštěným počítačem. Například můžete použít rozšíření Přístup u virtuálních počítače k resetování nebo úpravě hodnot vzdáleného přístupu na virtuálním počítači Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Viz [Rozšíření a funkce virtuálních strojů (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo O [rozšířeních a funkcích virtuálních strojů (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>virtuální síť
Síť, která poskytuje připojení mezi prostředky Azure, které jsou izolované od všech ostatních klientů Azure. [Brána Azure VPN](vpn-gateway/vpn-gateway-about-vpngateways.md) umožňuje navázat připojení mezi virtuálními sítěmi a mezi virtuální sítí a místní sítí. Můžete plně řídit bloky IP adres, nastavení DNS, zásady zabezpečení a směrovací tabulky v rámci virtuální sítě.  
Viz [Přehled virtuální sítě](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webová aplikace
Jiný název aplikace [App Service App](#app-service-app).

## <a name="see-also"></a>Viz také

* [Začínáme s Azure](https://azure.microsoft.com/get-started/)
* [Cloudové centrum zdrojů](https://azure.microsoft.com/resources/)  
* [Azure pro vaši obchodní aplikaci](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure ve vašem datovém centru](https://azure.microsoft.com/overview/business-apps-on-azure/)

