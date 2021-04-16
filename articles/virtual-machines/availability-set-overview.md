---
title: Přehled skupin dostupnosti
description: Seznamte se se skupinami dostupnosti v Azure.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530548"
---
# <a name="availability-sets-overview"></a>Přehled skupin dostupnosti

Tento článek obsahuje přehled funkcí dostupnosti virtuálních počítačů Azure (VM).

## <a name="what-is-an-availability-set"></a>Co je skupina dostupnosti? 

Skupina dostupnosti je logické seskupení virtuálních počítačů, které umožňují Azure pochopit, jak je sestavená vaše aplikace, aby bylo možné zajistit redundanci a dostupnost. Doporučujeme, aby se v rámci skupiny dostupnosti vytvořily dva nebo víc virtuálních počítačů, které poskytují vysoce dostupné aplikace a splňovaly [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro samotnou skupinu dostupnosti se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače.

## <a name="how-do-availability-sets-work"></a>Jak fungují sady dostupnosti?
Každý virtuální počítač v sadě dostupnosti má základní platformu Azure přiřazenou **aktualizační doménu** a **doménu selhání** . Každá skupina dostupnosti se dá nakonfigurovat až do tří domén selhání a dvaceti aktualizačních domén. Aktualizace domén označují skupiny virtuálních počítačů a základní fyzický hardware, které lze restartovat současně. Pokud je v rámci jedné skupiny dostupnosti nakonfigurováno více než 5 virtuálních počítačů, šestý virtuální počítač se umístí do stejné aktualizační domény jako první virtuální počítač, sedmý se umístí do stejné aktualizační domény jako druhý atd. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména. Restartovaná aktualizační doména má 30 minut na zotavení, než se zahájí údržba na jiné aktualizační doméně.

Domény selhání definují skupinu virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení se virtuální počítače nakonfigurované v rámci skupiny dostupnosti rozdělují napříč až třemi doménami selhání. Přestože umístění virtuálních počítačů do skupiny dostupnosti neochrání vaši aplikaci před selháním operačního systému nebo selháním spojeným s konkrétní aplikací, omezí se tím dopad potenciálních selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagram znázorňující různé výpočetní clustery rozdělené do domén selhání a v rámci těchto domén selhání máme několik aktualizačních domén.":::

Virtuální počítače se taky zarovnají s doménami selhání disku. Toto zarovnání zajišťuje, aby všechny spravované disky připojené k virtuálnímu počítači byly v rámci stejných domén selhání. 

Ve spravované skupině dostupnosti je možné vytvořit jenom virtuální počítače se spravovanými disky. Počet domén selhání spravovaných disků se liší podle oblasti – buď dvě, nebo tři domény selhání na oblast. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagram znázorňující, jakým způsobem jsou domény selhání pro disky a virtuální počítače zarovnané.":::

## <a name="next-steps"></a>Další kroky
Teď můžete tyto funkce pro zajištění redundance a dostupnosti začít používat a vytvořit prostředí Azure. Informace o doporučených postupech najdete v tématu věnovaném [osvědčeným postupům pro zajištění dostupnosti v Azure](/azure/architecture/checklist/resiliency-per-service).

