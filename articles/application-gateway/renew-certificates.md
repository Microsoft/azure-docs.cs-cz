---
title: Obnovte certifikát Azure Application Gateway
description: Zjistěte, jak obnovit certifikát přidružený naslouchací proces aplikace brány.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598262"
---
# <a name="renew-application-gateway-certificates"></a>Obnovení certifikátů Application Gateway

V určitém okamžiku budete muset obnovit certifikáty, pokud jste nakonfigurovali svojí aplikační brány pro šifrování SSL.

Můžete obnovit certifikát přidružený naslouchací proces buď na portálu Azure, Azure PowerShell nebo rozhraní příkazového řádku Azure:

## <a name="azure-portal"></a>Azure Portal

Chcete-li obnovit certifikát naslouchací proces z portálu, přejděte na naslouchací procesy vaší aplikace brány. Klikněte na tlačítko naslouchací proces, který má certifikát, který je potřeba obnovit a potom klikněte na **obnovit nebo upravit vybraný certifikát**.

![Obnovení certifikátu](media/renew-certificate/ssl-cert.png)

Nahrajte nový certifikát PFX, zadejte jeho název, zadejte heslo a pak klikněte na tlačítko **Uložit**.

## <a name="azure-powershell"></a>Azure PowerShell

Při obnovení certifikátu pomocí Azure PowerShell, použijte následující rutinu:

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
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

Další postup konfigurace snižování zátěže protokolu SSL s Azure Application Gateway najdete v tématu [konfigurovat přesměrování zpracování SSL](application-gateway-ssl-portal.md)
