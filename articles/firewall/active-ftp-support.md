---
title: Podpora aktivní služby FTP Azure Firewall
description: Ve výchozím nastavení je aktivní FTP na Azure Firewall zakázaný. Můžete ji povolit pomocí PowerShellu, CLI a šablony ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864464"
---
# <a name="azure-firewall-active-ftp-support"></a>Podpora aktivní služby FTP Azure Firewall

S aktivním PROTOKOLem FTP iniciuje server FTP datové připojení k určenému portu dat klienta FTP. Brány firewall v síti na straně klienta standardně blokují externí požadavek na připojení k internímu klientskému portu. Další informace najdete v tématu [aktivní FTP vs. pasivní FTP, konečné vysvětlení](https://slacksite.com/other/ftp.html).

Ve výchozím nastavení je aktivní podpora FTP na Azure Firewall zakázaná a chrání proti útokům na vrácení FTP pomocí `PORT` příkazu FTP. Můžete ale povolit aktivní FTP při nasazení pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo šablony Azure ARM.

Aby bylo možné podporovat protokol FTP pro aktivní režim, je nutné otevřít následující porty TCP:

- Port 21 serveru FTP z libovolného místa (iniciuje klient připojení)
- Port 21 serveru FTP na porty > 1023 (Server reaguje na port ovládacího prvku klienta)
- Port 20 serveru FTP na porty > 1023 na klientech (server iniciuje datové připojení k portu dat klienta)
- Port 20 serveru FTP z portů > 1023 na klientech (klient odesílá potvrzení do datového portu serveru)

## <a name="azure-powershell"></a>Azure PowerShell

K nasazení pomocí Azure PowerShell použijte `AllowActiveFTP` parametr. Další informace najdete v tématu [Vytvoření brány firewall s povoleným aktivním FTP](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

K nasazení pomocí rozhraní příkazového řádku Azure použijte `--allow-active-ftp` parametr. Další informace najdete v tématu [AZ Network firewall Create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

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