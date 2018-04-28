---
title: Azure Glosář - Azure slovník | Microsoft Docs
description: Glosář Azure slouží k pochopení terminologie cloudu na platformě Azure. Tento krátký Azure slovník obsahuje definice pro běžné podmínky cloud pro Azure.
keywords: Azure slovník, terminologie cloudu, Azure glosář, definic termínů, podmínky cloudu
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
ms.openlocfilehash: 84766ba4cf9e844184752bc44d2e0a471b97db27
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glosář Microsoft Azure: slovník terminologie cloudu na platformě Azure

Glosář Microsoft Azure je slovník krátké cloudu terminologie pro platformu Azure. Viz také:

* [Microsoft Azure a Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -služeb definice Azure a jejich protějšky AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Cloudová výpočetní podmínky](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -obecné odvětví cloudu podmínky.

## <a name="account"></a>account
Účet, který se používá pro přístup k a správě předplatného Azure. Ho se často označuje jako účet Azure i když účet může být některý z těchto: stávající pracovní, školní, nebo osobní účet Microsoft, nebo Office 365 uživatelské jméno a heslo. Můžete také vytvořit účet, který chcete spravovat předplatné Azure, když se přihlásíte k [bezplatnou zkušební verzi](https://azure.microsoft.com).  
V tématu [zaregistrujte si předplatné Azure pomocí svého účtu Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) a [účty, které můžete používat k přihlášení](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplikace API
Jiný název pro [aplikaci služby App Service](#app-service-app).

## <a name="app-service-app"></a>Aplikace služby App Service
Výpočetní prostředky, [Azure App Service](app-service/app-service-web-overview.md) poskytuje k hostování webu či webové aplikace, webového rozhraní API, nebo [back-end mobilní aplikace](app-service-mobile/app-service-mobile-value-prop.md). Aplikace služby App Service se také označují jako *App Services*, *webové aplikace*, *aplikace API*, a *mobilní aplikace*.

## <a name="availability-set"></a>Sady dostupnosti.
Kolekce virtuálních počítačů, které se spravují dohromady zajistit redundanci aplikace a spolehlivosti. Použití sady dostupnosti. zajistí, že při událostí do plánované i neplánované údržby bude k dispozici alespoň jeden virtuální počítač.  
V tématu [Správa dostupnosti virtuálních počítačů Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Správa dostupnosti virtuálních počítačů Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Model nasazení Azure classic
Jeden ze dvou [modely nasazení](resource-manager-deployment-model.md) použít k nasazení prostředků v Azure (Azure Resource Manager je nový model). Některé služby Azure podporují jenom modelu nasazení Resource Manager, některé podporují pouze modelu nasazení classic a některé podporovat. Dokumentace pro každou službu Azure určuje modely podporují.

## <a name="cli"></a>Rozhraní příkazového řádku Azure (CLI)
Rozhraní příkazového řádku, který slouží ke správě služeb Azure z Windows, systému macOS a Linux.  Některé služby nebo funkce služby lze spravovat pouze pomocí prostředí PowerShell nebo rozhraní příkazového řádku. V tématu [rozhraní příkazového řádku Azure 2.0](/cli/azure)

## <a name="powershell"></a>Prostředí Azure PowerShell
Rozhraní příkazového řádku pro správu služby Azure přes příkazový řádek z počítačů s Windows. Některé služby nebo funkce služby lze spravovat pouze pomocí prostředí PowerShell nebo rozhraní příkazového řádku.
V tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Model nasazení Azure Resource Manager
Jeden ze dvou [modely nasazení](resource-manager-deployment-model.md) použít k nasazení prostředky v Microsoft Azure (druhá je modelu nasazení classic). Některé služby Azure podporují jenom modelu nasazení Resource Manager, některé podporují pouze modelu nasazení classic a některé podporovat. Dokumentace pro každou službu Azure určuje modely podporují.

## <a name="fault-domain"></a>Doména selhání
Kolekce virtuálních počítačů v nastavení dostupnosti, pravděpodobně neúspěšně ve stejnou dobu. Příkladem je skupinu počítačů v racku, které sdílejí společné přepínač zdroje a sítě power. V Azure jsou virtuální počítače v nastavení dostupnosti automaticky oddělené napříč více domén selhání.  
V tématu [Správa dostupnosti virtuálních počítačů Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [Správa dostupnosti virtuálních počítačů Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>geograficky
Definované hranice pro sídle data, která obvykle obsahuje dvě nebo více oblastí. Hranice může být v rámci nebo za hranicemi national a jsou ovlivněny daň nařízení. Každé geografické má alespoň jedné oblasti. Příkladem oblastech jsou Asie a Tichomoří a Japonsku. Označuje taky jako *geography*.  
V tématu [oblastí Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geografická replikace
Proces automaticky replikace obsah, jako jsou objekty BLOB, tabulek a v rámci pár místní.  
V tématu [aktivní geografickou replikací pro databázi Azure SQL.](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>Bitové kopie
Soubor, který obsahuje operační systém a konfiguraci aplikace, které je možné vytvořit libovolný počet virtuálních počítačů. V Azure existují dva typy obrázků: virtuálních počítačů bitovou kopii a bitovou kopii operačního systému. Image virtuálního počítače obsahuje operační systém a všechny disky připojené k virtuálnímu počítači, když se vytvoří bitovou kopii. Image operačního systému obsahuje pouze zobecněný operačního systému se konfigurace disků bez dat.  
V tématu [vyhledání a vyberte bitových kopií systému Windows virtuálního počítače v Azure pomocí prostředí PowerShell nebo rozhraní příkazového řádku](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Omezení
Počet prostředků, které lze vytvořit nebo srovnávacího testu výkonu, který lze dosáhnout. Omezení jsou obvykle přidružené odběry, služby a nabídky.  
V tématu [předplatného Azure a omezení služby, kvóty a omezení](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení
Prostředek, který rozděluje příchozí komunikaci mezi počítači v síti. Nástroj pro vyrovnávání zatížení v Azure, distribuuje přenosu do virtuálních počítačů, které jsou definované v sadě pro vyrovnávání zatížení. A [nástroj pro vyrovnávání zatížení](load-balancer/load-balancer-overview.md) může být internetového nebo může být vnitřní.  

## <a name="mobile-app"></a>mobile app
Jiný název pro [aplikace App Service](#app-service-app).

## <a name="offer"></a>nabídka
Ceny, kredity a související podmínky týkající se předplatné Azure.  
Najdete v článku [stránce s podrobnostmi o nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portál
Zabezpečený webový portál používat k nasazení a Správa služby Azure.

## <a name="region"></a>oblast
Oblasti v rámci geograficky, která nemá křížové national ohraničení a obsahuje jeden nebo více datových centrech. Ceny, místní služby a nabídka typy jsou zveřejněné na úrovni oblasti. Oblast je obvykle spárován s jinou oblast, což může být až několik set miles rychle. Místní páry slouží jako mechanismus pro zotavení po havárii a scénáře s vysokou dostupností. Také označuje jako *umístění*.  
V tématu [oblastí Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>prostředek
Položka, která je součástí daného řešení Azure. Každá služba Azure umožňuje nasadit různé typy prostředků, například databáze nebo virtuální počítače.   
V tématu [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>skupina prostředků
Kontejner ve Správci prostředků, který obsahuje související prostředky pro aplikaci. Skupina prostředků může zahrnovat všechny prostředky pro aplikaci nebo jenom prostředky, které jsou logicky seskupeny dohromady. To, jakým způsobem se prostředky přidělí do skupin prostředků, můžete rozhodnout na základě toho, co je pro vaši organizaci nejvhodnější.  
V tématu [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Šablony Resource Manageru
Soubor JSON deklarativně definující jeden nebo více prostředků Azure, který definuje závislosti mezi prostředky nasazené. Šablony lze použít k nasazení prostředků konzistentně a opakovaně.  
V tématu [šablon pro tvorbu Azure Resource Manageru](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>poskytovatel prostředků
Služba poskytující prostředky, které můžete nasadit a spravovat prostřednictvím Resource Manageru. Každý poskytovatel prostředků nabízí operace pro práci s nasazenými prostředky. Zprostředkovatelé prostředků lze přistupovat prostřednictvím portálu Azure, Azure PowerShell a několik programovací sady SDK.  
V tématu [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>role
Způsob pro řízení přístupu, kterou lze přiřadit uživatelům, skupinám a službám. Role jsou moct provádět akce, jako je vytvořit, spravovat a přečtěte si prostředků Azure.  
V tématu [RBAC: předdefinovaných rolí](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>Dohoda o úrovni služeb (SLA)
Smlouva, která popisuje závazky společnosti Microsoft pro provozu a připojení. Každá služba Azure má konkrétní SLA.  
V tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>Sdílený přístupový podpis (SAS)
Podpis, který vám umožní udělit omezený přístup k prostředku, bez vystavení klíč účtu. Například [Azure Storage používá SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) udělit klientský přístup k objektům, jako je například objekty BLOB. [IoT Hub používá SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) udělit oprávnění k odesílání telemetrie zařízení.

## <a name="storage-account"></a>účet úložiště
Účet, který poskytuje přístup ke službám Azure Blob, fronty, tabulky a soubor ve službě Azure Storage. Název účtu úložiště definuje jedinečný obor názvů pro datové objekty Azure Storage.  
V tématu [účty Azure storage](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>předplatné
Smlouvy zákazníka se společností Microsoft, umožňující získat služby Azure. Předplatné ceny a podmínky související se řídí nabídku vybrali pro odběr.
V tématu [Microsoft Online Subscription Agreement](https://azure.microsoft.com/support/legal/subscription-agreement/) a [asociování předplatných Azure se službou Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>značka
Indexování pojem, který umožňuje kategorizovat prostředky podle požadavků pro správu nebo fakturaci. Když máte komplexní kolekci prostředků, můžete vizualizovat způsobem, jakým je nejvhodnější značky. Můžete například označit prostředky, které mají v rámci organizace podobnou roli nebo které patří do stejného oddělení.  
V tématu [použití značek k uspořádání prostředků Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>Aktualizace domény
Kolekce virtuálních počítačů v nastavení dostupnosti, které jsou aktualizované ve stejnou dobu. Virtuální počítače ve stejné doméně, aktualizace se během plánované údržby restartují společně. Azure nikdy nerestartuje víc než jednu aktualizační doménu najednou. Také označuje jako upgradovací doméně.  
V tématu [Správa dostupnosti virtuálních počítačů Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Správa dostupnosti virtuálních počítačů Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Virtuální počítač
Implementace softwaru fyzický počítač, který běží operační systém. Na stejném hardwaru, můžete současně spustit více virtuálních počítačů. Virtuální počítače v Azure, jsou k dispozici v mnoha velikostí.  
V tématu [dokumentace virtuální počítače](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>rozšíření virtuálního počítače.
Prostředek, který implementuje chování nebo funkce, které buď další programy fungovat nebo zadejte možnost pro vás k interakci s počítačem, spuštěná. Například můžete použít rozšíření pro virtuální počítač přístup k resetování nebo upravte hodnoty vzdáleného přístupu na virtuálním počítači Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
V tématu [o rozšíření virtuálního počítače a funkcích (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [o rozšíření virtuálního počítače a funkcích (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Virtuální síť
Síť, která poskytuje připojení mezi vašich prostředků Azure, které je izolovaný od všech ostatních klientů Azure. [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) umožňuje vytvořit připojení mezi virtuálními sítěmi a [mezi virtuální sítí a místní sítí](vpn-gateway/vpn-gateway-plan-design.md). Můžete plně řídit bloky IP adres, nastavení DNS, zásady zabezpečení a směrovací tabulky v rámci virtuální sítě.  
V tématu [Přehled virtuálních sítí](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webová aplikace
Jiný název pro [aplikace App Service](#app-service-app).

## <a name="see-also"></a>Další informace najdete v tématech

* [Začínáme s Azure](https://azure.microsoft.com/get-started/)
* [Center prostředků cloudu](https://azure.microsoft.com/resources/)  
* [Azure pro obchodní aplikace](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure ve vašem datovém centru](https://azure.microsoft.com/overview/business-apps-on-azure/)

