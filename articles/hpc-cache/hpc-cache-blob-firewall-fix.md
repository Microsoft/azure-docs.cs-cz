---
title: Pracovat s nastavením brány firewall úložiště
description: Nastavení brány firewall sítě účtu úložiště může způsobit selhání při vytváření cíle Azure Blob Storage v mezipaměti HPC Azure. Tento článek obsahuje alternativní řešení pro omezení, dokud nebude zavedena Oprava softwaru.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 45a7169330b11e98a8618b08205217212414ca5d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258924"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Jak obejít nastavení brány firewall účtu úložiště objektů blob

Konkrétní nastavení, které se používá v bránách firewall účtu úložiště, může způsobit, že se vytváření cílů služby Blob Storage nezdaří. Tým Azure HPC cache pracuje na opravě softwaru tohoto problému, ale můžete ho obejít podle pokynů v tomto článku.

Nastavení brány firewall, které umožňuje přístup pouze z vybraných sítí, může zabránit vytvoření nebo úpravě cíle úložiště objektů BLOB. Tato konfigurace se nachází na stránce nastavení **brány firewall a virtuální sítě** pro účet úložiště. (Tento problém se nevztahuje na cíle úložiště ADLS-NFS.)

Problémem je to, že služba Cache používá skrytou virtuální síť služby, která je oddělená od zákaznických prostředí. Tato síť není možné explicitně autorizovat pro přístup k vašemu účtu úložiště.

Když vytvoříte cíl úložiště objektů blob, služba cache Service tuto síť používá ke kontrole, jestli je kontejner prázdný. Pokud brána firewall nepovoluje přístup ze skryté sítě, tato operace se nezdařila a vytvoření cíle úložiště se nezdařilo.

Brána firewall taky může blokovat změny v cestách k cílovým oborům názvů služby Blob Storage.

Pokud chcete tento problém obejít, dočasně změňte nastavení brány firewall při vytváření cíle úložiště:

1. Přejděte na stránku **brány firewall účtu úložiště a virtuální sítě** a změňte nastavení "Povolte přístup z" na **všechny sítě**.
1. Vytvořte cíl služby Blob Storage ve vaší mezipaměti HPC Azure.
1. Vytvořte cestu k oboru názvů cíle úložiště.
1. Po úspěšném vytvoření cíle a cesty úložiště změňte nastavení brány firewall účtu zpátky na **vybrané sítě**.

Mezipaměť prostředí Azure HPC nepoužívá k přístupu k cíli úložiště virtuální síť služby.

Pro pomoc s tímto řešením se [obraťte na službu a podporu společnosti Microsoft](hpc-cache-support-ticket.md).
