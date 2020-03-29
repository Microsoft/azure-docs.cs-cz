---
title: Obnovení certifikátu Azure Application Gateway
description: Přečtěte si, jak obnovit certifikát přidružený k naslouchací proces brány aplikace.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278000"
---
# <a name="renew-application-gateway-certificates"></a>Obnovení certifikátů aplikační brány

V určitém okamžiku budete muset obnovit certifikáty, pokud jste nakonfigurovali aplikační bránu pro šifrování SSL.

Certifikát přidružený k naslouchací proces můžete obnovit pomocí portálu Azure, Azure PowerShellu nebo azure cli:

## <a name="azure-portal"></a>portál Azure

Chcete-li obnovit certifikát posluchače z portálu, přejděte na posluchače aplikační brány. Klepněte na naslouchací proces s certifikátem, který je třeba obnovit, a potom klepněte na tlačítko **Obnovit nebo upravit vybraný certifikát**.

![Obnovit certifikát](media/renew-certificate/ssl-cert.png)

Nahrajte nový certifikát PFX, pojmenujte jej, zadejte heslo a klikněte na **Uložit**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li obnovit certifikát pomocí Azure PowerShellu, použijte následující skript:

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

Informace o konfiguraci snižování zátěže SSL pomocí brány aplikace Azure najdete v [tématu Konfigurace snižování zátěže protokolu SSL.](application-gateway-ssl-portal.md)
