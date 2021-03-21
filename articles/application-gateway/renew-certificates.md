---
title: Obnovení certifikátu Azure Application Gateway
description: Naučte se obnovit certifikát přidružený k naslouchací službě Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622176"
---
# <a name="renew-application-gateway-certificates"></a>Prodloužit platnost Application Gatewaych certifikátů

V určitém okamžiku budete muset prodloužit platnost certifikátů, pokud jste nakonfigurovali aplikační bránu pro šifrování TLS/SSL.

Certifikát přidružený k naslouchacímu procesu můžete obnovit pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure:

## <a name="azure-portal"></a>portál Azure

Pokud chcete obnovit certifikát naslouchacího procesu z portálu, přejděte na naslouchací procesy služby Application Gateway. Vyberte naslouchací proces s certifikátem, který je třeba obnovit, a potom vyberte možnost **obnovit nebo upravit vybraný certifikát**.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="Prodloužit platnost certifikátu":::

Nahrajte nový certifikát PFX, zadejte jeho název, zadejte heslo a pak vyberte **Uložit**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K obnovení certifikátu pomocí Azure PowerShell použijte následující skript:

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

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nakonfigurovat snižování zátěže TLS pomocí Azure Application Gateway, najdete v tématu [Konfigurace snižování zátěže TLS](./create-ssl-portal.md) .