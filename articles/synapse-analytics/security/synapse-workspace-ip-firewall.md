---
title: Konfigurace pravidel brány firewall protokolu IP
description: Článek, který vás učí, abyste nakonfigurovali pravidla brány firewall protokolu IP v Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b937dad6c3c8f5a5773ca7779493b41c905307b1
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226501"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Pravidla brány firewall protokolu IP pro Azure synapse Analytics

Tento článek vysvětluje pravidla brány firewall protokolu IP a naučíte se, jak je nakonfigurovat ve službě Azure synapse Analytics.

## <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP

Pravidla firewallu protokolu IP udělují nebo odepírají přístup k pracovnímu prostoru Synapse v závislosti na zdrojové IP adrese každého požadavku. Pro svůj pracovní prostor můžete nakonfigurovat pravidla firewallu protokolu IP. Pravidla brány firewall protokolu IP nakonfigurovaná na úrovni pracovního prostoru se vztahují na všechny veřejné koncové body pracovního prostoru (vyhrazené fondy SQL, fond SQL bez serveru a vývoj).

## <a name="create-and-manage-ip-firewall-rules"></a>Vytváření a správa pravidel firewallu protokolu IP

Existují dva způsoby, jak pravidla brány firewall protokolu IP přidat do pracovního prostoru synapse. Pokud chcete do svého pracovního prostoru přidat bránu firewall protokolu IP, vyberte **zabezpečení + sítě** a zaškrtněte políčko během vytváření pracovního prostoru vytvořit **připojení ze všech IP adres** .

![Snímek obrazovky, který zvýrazní tlačítko zabezpečení a sítě.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure Portal konfiguraci IP adres pracovního prostoru synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Po vytvoření pracovního prostoru můžete také přidat pravidla firewallu protokolu IP do pracovního prostoru synapse. V části **zabezpečení** z Azure Portal vyberte **brány firewall** . Pokud chcete přidat nové pravidlo brány firewall protokolu IP, zadejte jeho název, počáteční IP adresu a koncovou IP adresu. Po dokončení vyberte **Uložit**.

![Konfigurace IP adres pracovních prostorů Azure synapse v Azure Portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Připojení ke službě Synapse z vlastní sítě

K pracovnímu prostoru synapse se můžete připojit pomocí synapse studia. Můžete také použít SQL Server Management Studio (SSMS) pro připojení k prostředkům SQL (vyhrazené fondy SQL a fond SQL bez serveru) ve vašem pracovním prostoru.

Ujistěte se, že brána firewall v síti a místní počítač umožňují odchozí komunikaci na portech TCP 80, 443 a 1443 pro synapse Studio.

Také je potřeba pro synapse Studio zapnout odchozí komunikaci na portu UDP 53. Pokud se chcete připojit pomocí nástrojů, jako je SSMS a Power BI, musíte na portu TCP 1433 zapnout odchozí komunikaci.

Zásada připojení SQL je nastavená na *výchozí* hodnotu pro pracovní prostor. Můžete si přečíst další informace o IP adresách a portech, které by klienti měli v [tomto](../../azure-sql/database/connectivity-architecture.md#connection-policy)případě umožňovat odchozí komunikaci.




## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](../quickstart-create-workspace.md)

Vytvoření pracovního prostoru Azure synapse pomocí [spravovaného pracovního prostoru Virtual Network](./synapse-workspace-managed-vnet.md)