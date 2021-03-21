---
title: Připojení k účtu zabezpečeného úložiště z vašeho pracovního prostoru Azure synapse
description: V tomto článku se dozvíte, jak se připojit k účtu zabezpečeného úložiště z vašeho pracovního prostoru Azure synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674310"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Připojení k zabezpečenému účtu Azure Storage z pracovního prostoru synapse

V tomto článku se dozvíte, jak se připojit k zabezpečenému účtu Azure Storage z vašeho pracovního prostoru Azure synapse. Účet služby Azure Storage můžete propojit s pracovním prostorem synapse při vytváření pracovního prostoru. Po vytvoření pracovního prostoru můžete propojit další účty úložiště.


## <a name="secured-azure-storage-accounts"></a>Zabezpečené účty úložiště Azure
Azure Storage poskytuje vrstvený model zabezpečení, který umožňuje zabezpečení a řízení přístupu k účtům úložiště. Můžete nakonfigurovat pravidla brány firewall protokolu IP pro udělení provozu z vybraných veřejných rozsahů IP adres k vašemu účtu úložiště. Můžete taky nakonfigurovat Síťová pravidla, která budou udělovat přenosy z vybraných virtuálních sítí přístup k vašemu účtu úložiště. Můžete kombinovat pravidla brány firewall protokolu IP, která umožňují přístup z vybraných rozsahů IP adres a síťových pravidel, která udělují přístup z vybraných virtuálních sítí ve stejném účtu úložiště. Tato pravidla se vztahují na veřejný koncový bod účtu úložiště. Nepotřebujete žádná pravidla přístupu, abyste mohli povolit provoz ze spravovaných privátních koncových bodů vytvořených v pracovním prostoru do účtu úložiště. Pravidla brány firewall pro úložiště je možné použít pro existující účty úložiště nebo pro nové účty úložiště při jejich vytváření. Další informace o zabezpečení účtu úložiště najdete [tady](../../storage/common/storage-network-security.md).

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse pracovní prostory a virtuální sítě
Když vytváříte pracovní prostor synapse, můžete se rozhodnout, že se k němu má přidružit spravovaná virtuální síť. Pokud při vytváření pracovního prostoru nepovolíte spravovanou virtuální síť, váš pracovní prostor je ve sdílené virtuální síti společně s jinými synapse pracovními prostory, ke kterým není přidružena spravovaná virtuální síť. Pokud jste při vytváření pracovního prostoru povolili spravovanou virtuální síť, je váš pracovní prostor přidružený k vyhrazené virtuální síti spravované službou Azure synapse. Tyto virtuální sítě se ve vašem zákaznickém předplatném nevytvoří. Proto nebude možné udělit provoz z těchto virtuálních sítí přístup k účtu zabezpečeného úložiště pomocí síťových pravidel popsaných výše.  

## <a name="access-a-secured-storage-account"></a>Přístup k zabezpečenému účtu úložiště
Synapse pracuje na sítích, které nemůžou být zahrnuté do vašich síťových pravidel. Aby bylo možné povolit přístup z vašeho pracovního prostoru do zabezpečeného účtu úložiště, je třeba provést následující:

* Vytvořte pracovní prostor Azure synapse s přidruženou spravovanou virtuální sítí a z něj z něj vytvořte spravované privátní koncové body.
* Udělte vašemu pracovnímu prostoru Azure synapse přístup k vašemu účtu zabezpečeného úložiště jako k důvěryhodné službě Azure. Jako důvěryhodná služba pak Azure synapse použije silné ověřování k zabezpečenému připojení k vašemu účtu úložiště.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Vytvoření pracovního prostoru synapse se spravovanou virtuální sítí a vytvoření spravovaných privátních koncových bodů pro váš účet úložiště
Pomocí [těchto kroků](./synapse-workspace-managed-vnet.md) můžete vytvořit pracovní prostor synapse, ke kterému je přidružená spravovaná virtuální síť. Po vytvoření pracovního prostoru s přidruženou spravovanou virtuální sítí můžete na účet zabezpečeného úložiště vytvořit spravovaný privátní koncový bod podle kroků uvedených [tady](./how-to-create-managed-private-endpoints.md). 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Udělte vašemu pracovnímu prostoru Azure synapse přístup k vašemu účtu zabezpečeného úložiště jako k důvěryhodné službě Azure.
Analytické možnosti, jako je vyhrazený fond SQL a fond SQL bez serveru, používají víceklientské infrastruktury, která není nasazená do spravované virtuální sítě. Aby provoz z těchto možností měl přístup k zabezpečenému účtu úložiště, musíte nakonfigurovat přístup k vašemu účtu úložiště na základě spravované identity přiřazené systémem v pracovním prostoru podle následujících kroků.

V Azure Portal přejděte na účet zabezpečeného úložiště. V levém navigačním podokně vyberte **síť** . V části **instance prostředků** jako **typ prostředku** vyberte *Microsoft. synapse/pracovní prostory* a jako **název instance** zadejte název svého pracovního prostoru. Vyberte **Uložit**.

![Konfigurace sítě účtu úložiště.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Teď byste měli mít přístup k účtu zabezpečeného úložiště z pracovního prostoru.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [virtuální síti spravovaného pracovního prostoru](./synapse-workspace-managed-vnet.md).

Přečtěte si další informace o [spravovaných soukromých koncových bodech](./synapse-workspace-managed-private-endpoints.md).