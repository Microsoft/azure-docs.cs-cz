---
title: Glosář Azure – Azure Dictionary | Microsoft Docs
description: Pro pochopení cloudové terminologie na platformě Azure použijte Glosář Azure. Tento krátký slovník Azure poskytuje definice pro běžné cloudové pojmy pro Azure.
keywords: Slovník Azure, cloudová terminologie, Glosář Azure, definice terminologie, cloudové pojmy
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
ms.openlocfilehash: 63adb5d6898fd467a0557aa147e54f814c167e72
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400125"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure Glosář: slovník cloudové terminologie na platformě Azure

Glosář Microsoft Azure je krátký slovník cloudové terminologie pro platformu Azure. Viz také:

* [Microsoft Azure a Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) – definice služeb Azure a jejich protějšků v AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Pojem Cloud Computing](https://azure.microsoft.com/overview/cloud-computing-dictionary/) – obecné oborové cloudové prostředí.

## <a name="account"></a>account
Účet, který se používá pro přístup k předplatnému Azure a jeho správu. Často se označuje jako účet Azure, přestože účet může být některý z těchto: stávající pracovní, školní nebo osobní účet Microsoft. Můžete také vytvořit účet pro správu předplatného Azure při registraci [bezplatné zkušební verze](https://azure.microsoft.com).  
Přečtěte si téma [registrace předplatného Azure s účtem Microsoft 365](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) a [účty, které můžete použít k přihlášení](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplikace API
Jiný název [aplikace App Service](#app-service-app).

## <a name="app-service-app"></a>Aplikace služby App Service
Výpočetní prostředky, které [Azure App Service](app-service/overview.md) poskytují pro hostování webu nebo webové aplikace, webového rozhraní API nebo [back-endu mobilní aplikace](app-service-mobile/app-service-mobile-value-prop.md). Aplikace App Service se také označují jako *App Services*, *webové aplikace*, *aplikace API*a *mobilní aplikace*.

## <a name="availability-set"></a>Skupina dostupnosti
Kolekce virtuálních počítačů, které jsou spravovány společně za účelem zajištění redundance a spolehlivosti aplikací. Použití skupiny dostupnosti zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač.  
Viz [Správa dostupnosti virtuálních počítačů s Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Správa dostupnosti virtuálních počítačů se systémem Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Model nasazení Azure Classic
Jeden ze dvou [modelů nasazení](resource-manager-deployment-model.md) , které se používají k nasazení prostředků v Azure (nový model je Azure Resource Manager). Některé služby Azure podporují jenom model nasazení Správce prostředků, některá podporují jenom klasický model nasazení a některá podporují obojí. Dokumentace ke každé službě Azure určuje, které modely podporují.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Rozhraní příkazového řádku Azure (CLI)
Rozhraní příkazového řádku, které se dá použít ke správě služeb Azure ze systémů Windows, macOS a Linux.  Některé služby nebo funkce služeb se dají spravovat jenom přes PowerShell nebo rozhraní příkazového řádku. Viz [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Rozhraní příkazového řádku pro správu služeb Azure prostřednictvím příkazového řádku z počítačů s Windows. Některé služby nebo funkce služeb se dají spravovat jenom přes PowerShell nebo rozhraní příkazového řádku.
Viz [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Model nasazení Azure Resource Manager
Jeden ze dvou [modelů nasazení](resource-manager-deployment-model.md) , který se používá k nasazení prostředků v Microsoft Azure (druhý je model nasazení Classic). Některé služby Azure podporují jenom model nasazení Správce prostředků, některá podporují jenom klasický model nasazení a některá podporují obojí. Dokumentace ke každé službě Azure určuje, které modely podporují.

## <a name="fault-domain"></a>Doména selhání
Kolekce virtuálních počítačů ve skupině dostupnosti, která může být ve stejnou dobu neúspěšná. Příkladem je skupina počítačů v racku, které sdílejí společný zdroj napájení a síťový přepínač. V Azure se virtuální počítače ve skupině dostupnosti automaticky oddělují mezi několik domén selhání.  
Viz [Správa dostupnosti virtuálních počítačů s Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [Správa dostupnosti virtuálních počítačů se systémem Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .  

## <a name="geo"></a>geograficky
Definovaná hranice pro zasídlí dat, která obvykle obsahuje dvě nebo více oblastí. Hranice mohou být v mezích hranic nebo mimo hranice států a jsou ovlivněny daňovým nařízením. Každé geografické umístění má alespoň jednu oblast. Příklady zeměpisných oblastech jsou Asie a Tichomoří a Japonsko. Označuje se také jako *geografické*.  
Zobrazit [oblasti Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geografická replikace
Proces automatické replikace obsahu, jako jsou objekty blob, tabulky a fronty v rámci regionálního páru.  
Viz [Aktivní geografická replikace pro Azure SQL Database](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Soubor obsahující operační systém a konfiguraci aplikace, které lze použít k vytvoření libovolného počtu virtuálních počítačů. V Azure existují dva typy imagí: image virtuálního počítače a image operačního systému. Image virtuálního počítače zahrnuje operační systém a všechny disky připojené k virtuálnímu počítači, když se vytvoří obrázek. Image operačního systému obsahuje jenom zobecněný operační systém bez konfigurace datových disků.  
Přečtěte si téma [navigace a výběr imagí virtuálních počítačů s Windows v Azure pomocí PowerShellu nebo rozhraní](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) PŘÍKAZového řádku.

## <a name="limits"></a>lhůty
Počet prostředků, které lze vytvořit, nebo srovnávací test výkonu, který lze dosáhnout. Omezení jsou obvykle přidružena k předplatným, službám a nabídkám.  
Viz [limity, kvóty a omezení předplatného a služeb Azure](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Load Balancer
Prostředek, který distribuuje příchozí provoz mezi počítače v síti. V Azure Nástroj pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů definovaných v sadě nástroje pro vyrovnávání zatížení. [Nástroj pro vyrovnávání zatížení](load-balancer/load-balancer-overview.md) může být přístupný na internetu, nebo může být interní.  

## <a name="mobile-app"></a>mobilní aplikace
Jiný název [aplikace App Service](#app-service-app).

## <a name="offer"></a>offer
Ceny, kredity a související podmínky vztahující se k předplatnému Azure.  
Zobrazit [stránku s podrobnostmi nabídky Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portál
Zabezpečený webový portál, který slouží k nasazení a správě služeb Azure.

## <a name="region"></a>oblast
Oblast v geografické oblasti, která nezahrnuje mezinárodní hranice a obsahuje nejméně jedno datacentra. Ceny, regionální služby a typy nabídek se zveřejňují na úrovni oblasti. Oblast je obvykle spárována s jinou oblastí, což může trvat až několik set mil. Regionální páry lze použít jako mechanismus pro zotavení po havárii a pro scénáře s vysokou dostupností. Také se označuje jako *umístění*.  
Zobrazit [oblasti Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>prostředek
Položka, která je součástí vašeho řešení Azure. Každá služba Azure umožňuje nasadit různé typy prostředků, jako jsou databáze nebo virtuální počítače.   
Další informace najdete v tématu [Azure Resource Manager Overview](azure-resource-manager/management/overview.md) .

## <a name="resource-group"></a>skupina prostředků
Kontejner v Správce prostředků, který obsahuje související prostředky pro aplikaci. Skupina prostředků může zahrnovat všechny prostředky pro aplikaci nebo pouze prostředky, které jsou logicky seskupeny dohromady. To, jakým způsobem se prostředky přidělí do skupin prostředků, můžete rozhodnout na základě toho, co je pro vaši organizaci nejvhodnější.  
Další informace najdete v tématu [Azure Resource Manager Overview](azure-resource-manager/management/overview.md) .

## <a name="resource-manager-template"></a><a name="arm-template"></a>Šablona Resource Manageru
Soubor JSON, který deklarativně definuje jeden nebo víc prostředků Azure a definuje závislosti mezi nasazenými prostředky. Šablony lze použít k nasazení prostředků konzistentně a opakovaně.  
Další informace najdete v tématu [vytváření Azure Resource Manager šablon](resource-group-authoring-templates.md) .

## <a name="resource-provider"></a>poskytovatel prostředků
Služba poskytující prostředky, které můžete nasadit a spravovat prostřednictvím Správce prostředků. Každý poskytovatel prostředků nabízí operace pro práci s nasazenými prostředky. Poskytovatelé prostředků jsou k dispozici prostřednictvím Azure Portal, Azure PowerShell a několika programovacích sad SDK.  
Další informace najdete v tématu [Azure Resource Manager Overview](azure-resource-manager/management/overview.md) .

## <a name="role"></a>role
Způsob řízení přístupu, který lze přiřadit uživatelům, skupinám a službám. Role můžou provádět akce, jako je vytváření, Správa a čtení prostředků Azure.  
Viz [RBAC: předdefinované role](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>Smlouva o úrovni služeb (SLA)
Smlouva popisující závazky Microsoftu pro dobu provozu a konektivitu. Každá služba Azure má konkrétní smlouvu SLA.  
Zobrazit [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>sdílený přístupový podpis (SAS)
Signatura, která umožňuje udělit omezený přístup k prostředku bez vystavení klíče účtu. [Azure Storage například používá SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) pro udělení přístupu klienta k objektům, jako jsou objekty blob. [IoT Hub používá SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) k udělení oprávnění zařízením k posílání telemetrie.

## <a name="storage-account"></a>účet úložiště
Účet, který vám poskytne přístup ke službám Azure Blob, Queue, Table a File v Azure Storage. Název účtu úložiště definuje jedinečný obor názvů pro Azure Storage datových objektů.  
Viz [informace o účtech Azure Storage](storage/common/storage-create-storage-account.md) .

## <a name="subscription"></a>předplatné
Smlouvu zákazníka s Microsoftem, která jim umožní získat služby Azure. Ceny předplatného a související podmínky se řídí nabídkou, kterou jste si zvolili pro předplatné.
Přečtěte si téma [Microsoft Online Subscription Agreement](https://azure.microsoft.com/support/legal/subscription-agreement/) a [jak jsou předplatná Azure přidružená k Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>značka
Pojem indexování, který umožňuje kategorizaci prostředků podle vašich požadavků na správu a fakturaci. Pokud máte komplexní kolekci prostředků, můžete použít značky k vizualizaci těchto assetů způsobem, který má největší smysl. Můžete například označit prostředky, které mají v rámci organizace podobnou roli nebo které patří do stejného oddělení.  
Informace najdete v tématu [použití značek k uspořádání prostředků Azure](resource-group-using-tags.md) .

## <a name="update-domain"></a>aktualizovat doménu
Kolekce virtuálních počítačů ve skupině dostupnosti, které jsou aktualizovány ve stejnou dobu. Virtuální počítače ve stejné aktualizační doméně se při plánované údržbě restartují společně. Azure nikdy nerestartuje více než jednu aktualizační doménu současně. Také se označuje jako upgradovací doména.  
Viz [Správa dostupnosti virtuálních počítačů s Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Správa dostupnosti virtuálních počítačů se systémem Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

## <a name="virtual-machine"></a><a name="vm"></a>virtuální počítač
Implementace softwaru fyzického počítače, který používá operační systém. Na stejném hardwaru může běžet více virtuálních počítačů současně. V Azure jsou virtuální počítače dostupné v nejrůznějších velikostech.  
Viz [dokumentace Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>rozšíření virtuálního počítače
Prostředek, který implementuje chování nebo funkce, které pomůžou použít jiné programy, nebo vám umožní pracovat s běžícím počítačem. Můžete například použít rozšíření přístupu virtuálních počítačů k resetování nebo úpravě hodnot vzdáleného přístupu na virtuálním počítači Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Přečtěte si informace [o rozšířeních a funkcích virtuálních počítačů (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [o rozšířeních a funkcích virtuálních počítačů (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

## <a name="virtual-network"></a><a name="vnet"></a>virtuální síť
Síť, která poskytuje připojení mezi prostředky Azure, které jsou izolované od všech ostatních tenantů Azure. [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) umožňuje navázat připojení mezi virtuálními sítěmi a mezi virtuální sítí a místní sítí. V rámci virtuální sítě můžete plně řídit bloky IP adres, nastavení DNS, zásady zabezpečení a směrovací tabulky.  
Další informace najdete v tématu [Virtual Network Overview](virtual-network/virtual-networks-overview.md) .  

## <a name="web-app"></a>Webová aplikace
Jiný název [aplikace App Service](#app-service-app).

## <a name="see-also"></a>Viz také

* [Začínáme s Azure](https://azure.microsoft.com/get-started/)
* [Centrum prostředků cloudu](https://azure.microsoft.com/resources/)  
* [Azure pro vaši obchodní aplikaci](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure ve vašem datovém centru](https://azure.microsoft.com/overview/business-apps-on-azure/)

