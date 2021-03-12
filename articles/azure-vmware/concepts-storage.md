---
title: Koncepty – úložiště
description: Seznamte se s možnostmi úložiště klíčů v privátních cloudech řešení Azure VMware.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a4c34f8767b20de3ca0647e09c5dc9edad3d45fb
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200540"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Koncepty úložiště řešení Azure VMware

Privátní cloudy řešení Azure VMware poskytují nativní prostředí pro úložiště v rámci clusteru s VMware síti vSAN. V úložišti dat síti vSAN se používá všechny místní úložiště z každého hostitele v clusteru a šifrování na základě dat je k dispozici a je ve výchozím nastavení povolené. Prostředky Azure Storage můžete použít k rozšiřování možností úložiště privátních cloudů.

## <a name="vsan-clusters"></a>clustery síti vSAN

Místní úložiště v každém hostiteli clusteru se používá jako součást úložiště dat síti vSAN. Všechny diskgroups používají vrstvu mezipaměti NVMe 1,6 TB s nezpracovanými, na hostiteli a kapacitou založenou na SSD na 15,4 TB. Velikost nezpracované úrovně kapacity clusteru je vytížení na kapacitu hostitele, kolikrát je počet hostitelů. Například čtyři clustery hostitelů poskytnou hrubou kapacitu 61,6 až TB v úrovni kapacity síti vSAN.

Místní úložiště v hostitelích clusteru se používá v síti vSAN úložiště dat v úrovni clusteru. Všechna úložiště dat jsou vytvořena jako součást nasazení privátního cloudu a jsou k dispozici pro okamžité použití. Cloudadmin uživatel a všichni uživatelé ve skupině CloudAdmin mohou spravovat úložiště dat pomocí těchto oprávnění síti vSAN:
- Úložiště dat. AllocateSpace
- Datastore.Browse
- Datastore.Config
- Úložiště dat. DeleteFile
- Úložiště dat. Správa
- Úložiště dat. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Šifrování při neaktivním umístění dat

úložiště dat síti vSAN ve výchozím nastavení používají šifrování neaktivních dat. Řešení šifrování je založené na službě správy klíčů a podporuje operace vCenter pro správu klíčů. Klíče jsou uloženy zašifrované, zabalené Azure Key Vault hlavní klíč. Pokud z jakéhokoli důvodu dojde k odebrání hostitele z clusteru, data v SSD se okamžitě zruší.

## <a name="scaling"></a>Škálování

Kapacita úložiště nativního clusteru se škáluje přidáním hostitelů do clusteru. U clusterů, které používají hostitele AVS36, se nezpracovaná kapacita v rámci clusteru zvyšuje o 15,4 TB s každým přidaným hostitelem. Přidání hostitelů do clusteru trvá přibližně 10 minut. Pokyny k škálování clusterů najdete v [kurzu škálování privátního cloudu][tutorial-scale-private-cloud].

## <a name="azure-storage-integration"></a>Integrace úložiště Azure

Služby Azure Storage můžete používat na úlohách běžících ve vašem privátním cloudu. Služba Azure Storage zahrnuje účty úložiště, Table Storage a Blob Storage. Připojení úloh ke službám Azure Storage neprojde internetem. Toto připojení poskytuje vyšší úroveň zabezpečení a umožňuje ve svých úlohách privátního cloudu používat služby Azure Storage založené na smlouvě SLA.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s koncepty úložiště řešení Azure VMware, si možná budete chtít přečíst:

- [Koncepce identity privátního cloudu](concepts-identity.md).
- [vSphere řízení přístupu na základě role pro řešení Azure VMware](concepts-role-based-access-control.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
