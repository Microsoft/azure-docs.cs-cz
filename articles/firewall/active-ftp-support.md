---
title: Podpora aktivní služby FTP Azure Firewall
description: Ve výchozím nastavení je aktivní FTP na Azure Firewall zakázaný. Můžete ji povolit pomocí PowerShellu, CLI a šablony ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 2c89eb19025212490d29c97a061da52e779ecf42
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704393"
---
# <a name="azure-firewall-active-ftp-support"></a>Podpora aktivní služby FTP Azure Firewall

S aktivním PROTOKOLem FTP iniciuje server FTP datové připojení k určenému portu dat klienta FTP. Brány firewall v síti na straně klienta standardně blokují externí požadavek na připojení k internímu klientskému portu. Další informace najdete v tématu [aktivní FTP vs. pasivní FTP, konečné vysvětlení](https://slacksite.com/other/ftp.html).

Ve výchozím nastavení je aktivní podpora FTP na Azure Firewall zakázaná a chrání proti útokům na vrácení FTP pomocí `PORT` příkazu FTP. Můžete ale povolit aktivní FTP při nasazení pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo šablony Azure ARM.

> [!NOTE]
> V současné době je aktivní FTP podporováno pouze pro brány firewall nasazené ve virtuální síti. Podpora virtuální sítě WAN se přidá později.

## <a name="azure-powershell"></a>Azure PowerShell

K nasazení pomocí Azure PowerShell použijte `AllowActiveFTP` parametr. Další informace najdete v tématu [Vytvoření brány firewall s povoleným aktivním FTP](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

K nasazení pomocí rozhraní příkazového řádku Azure použijte `--allow-active-ftp` parametr. Další informace najdete v tématu [AZ Network firewall Create](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Šablona ARM (Azure Resource Manageru)

K nasazení pomocí šablony ARM použijte `AdditionalProperties` pole:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Další informace najdete v tématu [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Další kroky

Další informace o nasazení Azure Firewall najdete v tématu [nasazení a konfigurace Azure firewall pomocí Azure PowerShell](deploy-ps.md).