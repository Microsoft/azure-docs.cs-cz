---
title: Prodloužit platnost certifikátu Azure Application Gateway
description: Zjistěte, jak obnovit certifikát přidružený naslouchací proces application gateway.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314513"
---
# <a name="renew-application-gateway-certificates"></a>Prodloužit platnost certifikátů aplikační brány

V určitém okamžiku budete potřebovat k obnovení certifikátů, pokud jste nakonfigurovali vaše brána application gateway pro šifrování SSL.

Můžete obnovit certifikát přidružený naslouchací proces buď na webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure:

## <a name="azure-portal"></a>portál Azure

Prodloužit platnost certifikátu naslouchací proces z portálu, přejděte na naslouchací procesy pro brány vaší aplikace. Klikněte na naslouchací proces, který se má certifikát, který je potřeba obnovit a potom klikněte na **obnovit nebo upravit vybraný certifikát**.

![Prodloužit platnost certifikátu](media/renew-certificate/ssl-cert.png)

Nahrát nový certifikát PFX, pojmenujte ho, zadejte heslo a potom klikněte na tlačítko **Uložit**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K obnovení certifikátu pomocí prostředí Azure PowerShell, použijte tento skript:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Další postup

Další konfigurace přesměrování zpracování SSL pomocí Azure Application Gateway najdete v tématu [konfigurovat přesměrování zpracování SSL](application-gateway-ssl-portal.md)
