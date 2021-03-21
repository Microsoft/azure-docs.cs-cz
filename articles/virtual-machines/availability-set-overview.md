---
title: Přehled skupin dostupnosti
description: Seznamte se se skupinami dostupnosti v Azure.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c0bbc218723ea75064d9eb6f9c5f8f8c0d18a10b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510489"
---
# <a name="availability-sets-overview"></a>Přehled skupin dostupnosti

Tento článek obsahuje přehled funkcí dostupnosti virtuálních počítačů Azure (VM).

## <a name="what-is-an-availability-set"></a>Co je skupina dostupnosti? 

Skupina dostupnosti je logické seskupení virtuálních počítačů, které umožňují Azure pochopit, jak je sestavená vaše aplikace, aby bylo možné zajistit redundanci a dostupnost. Doporučujeme, aby se v rámci skupiny dostupnosti vytvořily dva nebo víc virtuálních počítačů, které poskytují vysoce dostupné aplikace a splňovaly [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro samotnou skupinu dostupnosti se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače.

## <a name="how-do-availability-sets-work"></a>Jak fungují sady dostupnosti?
Každý virtuální počítač v sadě dostupnosti má základní platformu Azure přiřazenou **aktualizační doménu** a **doménu selhání** . Dané skupině dostupnosti se ve výchozím nastavení přiřadí pět aktualizačních domén (u nasazení podle modelu Resource Manager je pak možné počet aktualizačních domén navýšit až na 20), které uživatel nemůže konfigurovat a které představují skupiny virtuálních počítačů a základního fyzického hardwaru, které lze restartovat současně. Pokud je v rámci jedné skupiny dostupnosti nakonfigurováno více než 5 virtuálních počítačů, šestý virtuální počítač se umístí do stejné aktualizační domény jako první virtuální počítač, sedmý se umístí do stejné aktualizační domény jako druhý atd. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména. Restartovaná aktualizační doména má 30 minut na zotavení, než se zahájí údržba na jiné aktualizační doméně.

Domény selhání definují skupinu virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení se virtuální počítače nakonfigurované v rámci skupiny dostupnosti rozdělují v rámci až tří domén selhání pro nasazení Správce prostředků. Přestože umístění virtuálních počítačů do skupiny dostupnosti neochrání vaši aplikaci před selháním operačního systému nebo selháním spojeným s konkrétní aplikací, omezí se tím dopad potenciálních selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

![Skupiny dostupnosti](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


Virtuální počítače, které používají [Azure Managed Disks](./faq-for-disks.md), odpovídají doménám selhání spravovaných disků (pokud se použije spravovaná skupina dostupnosti). Toto uspořádání zajišťuje, aby všechny spravované disky připojené k virtuálnímu počítači byly umístěné ve stejné doméně selhání spravovaných disků. 

Ve spravované skupině dostupnosti je možné vytvořit jenom virtuální počítače se spravovanými disky. Počet domén selhání spravovaných disků se liší podle oblasti – buď dvě, nebo tři domény selhání na oblast. 

![Spravovaná Skupina dostupnosti](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="next-steps"></a>Další kroky
Teď můžete tyto funkce pro zajištění redundance a dostupnosti začít používat a vytvořit prostředí Azure. Informace o doporučených postupech najdete v tématu věnovaném [osvědčeným postupům pro zajištění dostupnosti v Azure](/azure/architecture/checklist/resiliency-per-service).

