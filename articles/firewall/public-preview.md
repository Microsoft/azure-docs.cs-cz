---
title: Povolení verze preview pro veřejnost Brána Firewall služby Azure
description: Povolení verze preview pro veřejnost Brána Firewall služby Azure pomocí Azure Powershellu
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193060"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Povolení verze preview pro veřejnost Brána Firewall služby Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Povolení s využitím Azure Powershellu

Povolení verze preview pro veřejnost Brána Firewall služby Azure, použijte následující příkazy Azure Powershellu:

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Trvá až 30 minut, než funkce registraci dokončit. Spuštěním následujících příkazů prostředí Azure PowerShell, můžete zkontrolovat stav registrace:

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
Po dokončení registrace, spusťte následující příkaz:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Další postup

- [Kurz: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md)

