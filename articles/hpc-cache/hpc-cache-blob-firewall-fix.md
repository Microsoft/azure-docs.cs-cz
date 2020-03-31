---
title: Řešení nastavení brány firewall úložiště
description: Nastavení síťové brány firewall účtu úložiště může způsobit selhání při vytváření cíle úložiště objektů blob Azure v mezipaměti Azure HPC. Tento článek poskytuje řešení omezení, dokud není zavedena oprava softwaru.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174405"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Jak obejít nastavení brány firewall účtu úložiště objektů blob

Konkrétní nastavení používané v bráně firewall účtu úložiště může způsobit, že vytvoření cíle úložiště objektů Blob se nezdaří. Tým mezipaměti HPC Azure pracuje na opravě softwaru pro tento problém, ale můžete jej obejít podle pokynů v tomto článku.

Nastavení brány firewall, které umožňuje přístup pouze z "vybraných sítí", může zabránit mezipaměti ve vytváření cíle úložiště objektů Blob. Tato konfigurace je na stránce **Nastavení brány firewall a virtuálních sítí** účtu úložiště.

Problém je, že služba mezipaměti používá virtuální síť skryté služby, která je oddělená od prostředí zákazníka. Není možné explicitně autorizovat tuto síť pro přístup k účtu úložiště.

Když vytvoříte cíl úložiště objektů Blob, služba mezipaměti používá tuto síť ke kontrole, zda je kontejner prázdný. Pokud brána firewall neumožňuje přístup ze skryté sítě, kontrola se nezdaří a vytvoření cíle úložiště se nezdaří.

Chcete-li tento problém vyřešit, dočasně změňte nastavení brány firewall při vytváření cíle úložiště:

1. Přejděte na stránku **Firewally a virtuálních sítí** účtu úložiště a změňte nastavení "Povolit přístup ze všech **sítí"** na všechny sítě .
1. Vytvořte cíl úložiště objektů blob ve vaší mezipaměti Azure HPC.
1. Po úspěšném vytvoření cíle úložiště změňte nastavení brány firewall účtu zpět na **Vybrané sítě**.

Azure HPC Cache nepoužívá virtuální síť služby pro přístup k cíli dokončeného úložiště.

Chcete-li pomoci s tímto zástupným řešením, [obraťte se na služby a podporu společnosti Microsoft](hpc-cache-support-ticket.md).
