---
title: Pracovat s nastavením brány firewall úložiště
description: Nastavení brány firewall sítě účtu úložiště může způsobit selhání při vytváření cíle Azure Blob Storage v mezipaměti HPC Azure. Tento článek obsahuje alternativní řešení pro omezení, dokud nebude zavedena Oprava softwaru.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.author: v-erkel
ms.openlocfilehash: 6916c79e9110a88beff65d487fac72441382c2f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092366"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Jak obejít nastavení brány firewall účtu úložiště objektů blob

Konkrétní nastavení, které se používá v bránách firewall účtu úložiště, může způsobit, že se vytváření cílů služby Blob Storage nezdaří. Tým Azure HPC cache pracuje na opravě softwaru tohoto problému, ale můžete ho obejít podle pokynů v tomto článku.

Nastavení brány firewall, které umožňuje přístup jenom z vybraných sítí, může zabránit tomu, aby mezipaměť vytvořila cíl úložiště objektů BLOB. Tato konfigurace se nachází na stránce nastavení **brány firewall a virtuální sítě** pro účet úložiště.

Problémem je to, že služba Cache používá skrytou virtuální síť služby, která je oddělená od zákaznických prostředí. Tato síť není možné explicitně autorizovat pro přístup k vašemu účtu úložiště.

Když vytvoříte cíl úložiště objektů blob, služba cache Service tuto síť používá ke kontrole, jestli je kontejner prázdný. Pokud brána firewall nepovoluje přístup ze skryté sítě, tato operace se nezdařila a vytvoření cíle úložiště se nezdařilo.

Pokud chcete tento problém obejít, dočasně změňte nastavení brány firewall při vytváření cíle úložiště:

1. Přejděte na stránku **brány firewall účtu úložiště a virtuální sítě** a změňte nastavení "Povolte přístup z" na **všechny sítě**.
1. Vytvořte cíl služby Blob Storage ve vaší mezipaměti HPC Azure.
1. Po úspěšném vytvoření cíle úložiště změňte nastavení brány firewall účtu zpátky na **vybrané sítě**.

Mezipaměť prostředí Azure HPC nepoužívá k přístupu k cíli úložiště virtuální síť služby.

Pro pomoc s tímto řešením se [obraťte na službu a podporu společnosti Microsoft](hpc-cache-support-ticket.md).
