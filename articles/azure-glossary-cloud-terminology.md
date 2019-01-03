---
title: Glosář Azure – Azure slovníku | Dokumentace Microsoftu
description: Glosář Azure slouží k pochopení terminologie cloudu na platformě Azure. Tento krátký Azure slovník obsahuje definice pro společné termíny cloud pro Azure.
keywords: Slovník Azure, terminologie cloud, Glosář Azure, terminologie definice, podmínky pro cloud
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
ms.openlocfilehash: 748a9c0c583d4e1af9626527a440b19900898b87
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600297"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glosář Microsoft Azure: Slovník terminologie cloudu na platformě Azure

Glosář Microsoft Azure je krátký slovník terminologie cloud pro platformu Azure. Viz také:

* [Microsoft Azure a Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) – definice škálu služeb Azure a jejich protějšky v AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Termíny cloud computingu](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -obecných oborových termínech cloudu.

## <a name="account"></a>account
Účet, který se používá k přístupu a správě předplatného Azure. To se často označuje jako účet Azure i účet může být některé z těchto: existující pracovní, školní nebo osobní účet Microsoft, nebo Office 365 uživatelské jméno a heslo. Můžete také vytvořit účet, který chcete spravovat předplatné Azure, v případě, že můžete zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com).  
Zobrazit [registraci předplatného Azure pomocí účtu Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) a [účty, které můžete použít k přihlášení](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplikace API
Jiný název pro [aplikace služby App Service](#app-service-app).

## <a name="app-service-app"></a>Aplikace služby App Service
Výpočetní prostředky, které [služby Azure App Service](app-service/overview.md) poskytuje k hostování webu či webové aplikace, webové rozhraní API, nebo [back-endu mobilní aplikace](app-service-mobile/app-service-mobile-value-prop.md). Aplikace služby App Service se také označují jako *App Services*, *webové aplikace*, *API apps*, a *mobilní aplikace*.

## <a name="availability-set"></a>Skupina dostupnosti
Kolekce virtuálních počítačů, které se spravují dohromady pro zajištění redundance aplikace a spolehlivost. Použití skupiny dostupnosti zajišťuje, že během události plánované i neplánované údržby je k dispozici alespoň jeden virtuální počítač.  
Zobrazit [Správa dostupnosti virtuálních počítačů Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Správa dostupnosti virtuálních počítačů s Linuxem](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Model nasazení Azure classic
Jeden ze dvou [modely nasazení](resource-manager-deployment-model.md) použít k nasazení prostředků v Azure (Azure Resource Manageru je nový model). Některé služby Azure podporují jenom modelu nasazení Resource Manager, některé podporují pouze v modelu nasazení classic a některé podporovat. V dokumentaci pro jednotlivé služby Azure určuje modely podporují.

## <a name="cli"></a>Rozhraní příkazového řádku Azure (CLI)
Rozhraní příkazového řádku, který slouží ke správě služeb Azure z Windows, macOS a Linuxu.  Některé služby nebo funkce služby je možné spravovat jenom prostřednictvím Powershellu nebo rozhraní příkazového řádku. Zobrazit [rozhraní příkazového řádku Azure](/cli/azure)

## <a name="powershell"></a>Prostředí Azure PowerShell
Rozhraní příkazového řádku ke správě služeb Azure přes příkazový řádek z počítačů s Windows. Některé služby nebo funkce služby je možné spravovat jenom prostřednictvím Powershellu nebo rozhraní příkazového řádku.
Zobrazit [instalace a konfigurace Azure Powershellu](/powershell/azure/overview)

## <a name="arm-model"></a>Model nasazení Azure Resource Manageru
Jeden ze dvou [modely nasazení](resource-manager-deployment-model.md) použít k nasazení prostředků v Microsoft Azure (druhý je modelu nasazení classic). Některé služby Azure podporují jenom modelu nasazení Resource Manager, některé podporují pouze v modelu nasazení classic a některé podporovat. V dokumentaci pro jednotlivé služby Azure určuje modely podporují.

## <a name="fault-domain"></a>Doména selhání
Kolekce virtuálních počítačů ve skupině dostupnosti, která pravděpodobně nepůjde ve stejnou dobu. Příkladem je skupina počítačů do racku, které sdílejí společný power přepínač zdroje a sítě. V Azure jsou automaticky oddělené virtuální počítače ve skupině dostupnosti napříč několika doménami selhání.  
Zobrazit [Správa dostupnosti virtuálních počítačů Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [Správa dostupnosti virtuálních počítačů s Linuxem](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geograficky
Definovaných hranic rezidenci dat, který obvykle obsahuje dvě nebo víc oblastech. Hranice může být v rámci nebo mimo národní ohraničení a jsou ovlivněny nařízení daně. Každé geografické má aspoň jednu oblast. Příklady zeměpisných oblastech: Asie a Tichomoří a Japonsko. Zkratka *Geografie*.  
Zobrazit [oblastí Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>Geografická replikace
Proces automaticky replikuje obsah, jako jsou objekty BLOB, tabulky a fronty v páru oblastí.  
Zobrazit [aktivní geografické replikace pro Azure SQL Database](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Soubor, který obsahuje operační systém a konfigurace aplikace, které lze vytvořit libovolný počet virtuálních počítačů. V Azure existují dva typy obrázků: Image virtuálního počítače a image operačního systému. Image virtuálního počítače obsahuje operační systém a všechny disky připojené k virtuálnímu počítači, když se vytvoří bitovou kopii. Image operačního systému obsahuje pouze zobecněný operační systém s diskovou konfigurací, žádná data.  
Zobrazit [vyhledání a výběr imagí virtuálních počítačů Windows v Azure pomocí Powershellu nebo rozhraní příkazového řádku](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>omezení
Počet prostředků, které je možné vytvořit nebo srovnávacího testu výkonu, který jde dosáhnout. Omezení jsou obvykle přidruženy k předplatných, služeb a nabídky.  
Zobrazit [předplatného Azure a limity, kvóty a omezení](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení
Prostředek, který distribuuje příchozí provoz mezi počítači v síti. V Azure nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů, které jsou definovány v sadě nástroje pro vyrovnávání zatížení. A [nástroj pro vyrovnávání zatížení](load-balancer/load-balancer-overview.md) může být přístupem k Internetu, nebo může být vnitřní.  

## <a name="mobile-app"></a>mobile app
Jiný název pro [aplikace služby App Service](#app-service-app).

## <a name="offer"></a>nabídka
Na ceny, kredity a související podmínky platné pro předplatné Azure.  
Zobrazit [stránce s podrobnostmi nabídky Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portál
Zabezpečený webový portál používá k nasazení a správy služeb Azure.

## <a name="region"></a>oblast
Oblasti v rámci zeměpisné oblasti, která neobsahuje křížové národní ohraničení a obsahuje jeden nebo více datových centrech. Ceny, regionálních služeb a typy nabídek jsou přístupné na úrovni oblasti. Oblast je obvykle spárovaná s jinou oblastí, které můžou být až několik stovek mil okamžitě. Oblastní páry slouží jako mechanismus pro zotavení po havárii a scénáře s vysokou dostupností. Označuje také jako *umístění*.  
Zobrazit [oblastí Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>prostředek
Položka, která je součástí vašeho řešení Azure. Jednotlivé služby Azure můžete nasadit různé typy prostředků, jako jsou databáze nebo virtuální počítače.   
Zobrazit [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>skupina prostředků
Kontejner ve službě Správce prostředků, který obsahuje související prostředky pro aplikaci. Skupina prostředků může zahrnovat všechny prostředky pro aplikaci nebo jenom prostředky, které jsou logicky seskupeny dohromady. To, jakým způsobem se prostředky přidělí do skupin prostředků, můžete rozhodnout na základě toho, co je pro vaši organizaci nejvhodnější.  
Zobrazit [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Šablona Resource Manageru
Soubor JSON, který deklarativně definuje jeden nebo více prostředků Azure a, který definuje závislosti mezi nasazenými prostředky. Šablony lze použít k nasazení prostředků konzistentně a opakovaně.  
Zobrazit [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>Poskytovatel prostředků
Služba poskytující prostředky, které můžete nasadit a spravovat prostřednictvím Resource Manageru. Každý poskytovatel prostředků nabízí operace pro práci s nasazenými prostředky. Poskytovatelé prostředků je možný prostřednictvím webu Azure portal, prostředí Azure PowerShell a několik programovacích sad SDK.  
Zobrazit [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>role
Prostředky pro řízení přístupu, který je možné přiřadit uživatelům, skupinám a službám. Role budou moct provádět akce, jako je vytvářet, spravovat a přečtěte si o prostředky Azure.  
Zobrazit [RBAC: Předdefinované role](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>Smlouva o úrovni služeb (SLA)
Smlouva, která popisuje závazky společnosti Microsoft týkající se dostupnosti a konektivity. Jednotlivé služby Azure má konkrétní smlouvy SLA.  
Zobrazit [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>sdílený přístupový podpis (SAS)
Podpis, který vám umožní udělit omezený přístup k prostředku, bez vystavení klíč účtu. Například [služby Azure Storage pomocí SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) k udělení přístupu klientů k objekty, jako jsou objekty BLOB. [IoT Hub pomocí SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) udělit oprávnění k odesílání telemetrie zařízení.

## <a name="storage-account"></a>účet úložiště
Účet, který poskytuje přístup ke službám Azure Blob, fronty, tabulky a souboru ve službě Azure Storage. Definuje název účtu úložiště jedinečný obor názvů pro datové objekty Azure Storage.  
Zobrazit [účty Azure storage](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>předplatné
Na základě smlouvy se společností Microsoft, který umožňuje získat služeb Azure. Ceník předplatného a jejími podmínkami se řídí nabídky vybrána pro předplatné.
Zobrazit [Microsoft Online Subscription Agreement](https://azure.microsoft.com/support/legal/subscription-agreement/) a [předplatné Azure propojeno se službou Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>značka
Indexování termín, který umožňuje kategorizovat prostředky podle požadavků na správu nebo fakturaci. Když máte komplexní kolekci prostředků, můžete použít značky ve způsobu, jakým je nejvhodnější tyto assety vizualizovat. Můžete například označit prostředky, které mají v rámci organizace podobnou roli nebo které patří do stejného oddělení.  
Zobrazit [použití značek k uspořádání prostředků Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>Aktualizační doména
Kolekce virtuálních počítačů ve skupině dostupnosti, které jsou aktualizovány ve stejnou dobu. Virtuální počítače ve stejné aktualizační domény se během plánované údržby restartují společně. Azure nikdy nerestartuje víc než jednu aktualizační doménu najednou. Také označuje jako jednu upgradovací doménu.  
Zobrazit [Správa dostupnosti virtuálních počítačů Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Správa dostupnosti virtuálních počítačů s Linuxem](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Virtuální počítač
Implementace software fyzický počítač, na kterém běží operační systém. Několik virtuálních počítačů můžete spustit současně na stejném hardwaru. V Azure jsou k dispozici v různých velikostí virtuálních počítačů.  
Zobrazit [dokumentace k virtuálním počítačům](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>rozšíření virtuálního počítače.
Prostředek, který implementuje chování nebo funkce, které buď pomáhají ostatní programy fungovat nebo poskytnout možnost interakce s spuštěný počítač. Například můžete použít rozšíření přístupu virtuálních počítačů na resetovat nebo změnit hodnoty vzdáleného přístupu na virtuálním počítači Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Zobrazit [funkce (Windows) a rozšíření virtuálního počítače](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [funkce (Linux) a rozšíření virtuálního počítače](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Virtuální sítě
Síť, která poskytuje připojení mezi prostředky Azure, které je izolovaná od všech ostatních tenantů Azure. [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) umožňuje vytvořit připojení mezi virtuálními sítěmi a [mezi virtuální sítí a místní sítí](vpn-gateway/vpn-gateway-plan-design.md). Vám plnou kontrolu nad bloky IP adres, nastavení DNS, zásady zabezpečení a směrovacích tabulek v rámci virtuální sítě.  
Zobrazit [Přehled služby Virtual Network](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webová aplikace
Jiný název pro [aplikace služby App Service](#app-service-app).

## <a name="see-also"></a>Další informace najdete v tématech

* [Začínáme s Azure](https://azure.microsoft.com/get-started/)
* [Centrum prostředků cloudu](https://azure.microsoft.com/resources/)  
* [Azure pro obchodní aplikace](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure ve vašem datovém centru](https://azure.microsoft.com/overview/business-apps-on-azure/)

