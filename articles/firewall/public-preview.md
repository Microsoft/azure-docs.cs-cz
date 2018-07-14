---
title: Povolení verze preview pro veřejnost Brána Firewall služby Azure
description: Povolení verze preview pro veřejnost Brána Firewall služby Azure pomocí Azure Powershellu
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 263b16a419b5ff20a9b6d62860385f92c2a18f9c
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991155"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Povolení verze preview pro veřejnost Brána Firewall služby Azure

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Povolení s využitím Azure Powershellu

Povolení verze preview pro veřejnost Brána Firewall služby Azure, použijte následující příkazy Azure Powershellu:

```PowerShell
Register-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Trvá až 30 minut, než funkce registraci dokončit. Spuštěním následujících příkazů prostředí Azure PowerShell, můžete zkontrolovat stav registrace:

```PowerShell

Get-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
Po dokončení registrace, spusťte následující příkaz:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Další postup

- [Kurz: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md)

