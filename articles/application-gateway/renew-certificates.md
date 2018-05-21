---
title: Obnovte certifikát Azure Application Gateway
description: Zjistěte, jak obnovit certifikát přidružený naslouchací proces aplikace brány.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="renew-application-gateway-certificates"></a>Obnovení certifikátů Application Gateway

V určitém okamžiku budete muset obnovit certifikáty, pokud jste nakonfigurovali svojí aplikační brány pro šifrování SSL.

Můžete obnovit certifikát přidružený naslouchací proces pomocí portálu Azure nebo Azure PowerShell:

## <a name="azure-portal"></a>Azure Portal

Chcete-li obnovit certifikát naslouchací proces z portálu, přejděte na naslouchací procesy vaší aplikace brány. Klikněte na tlačítko naslouchací proces, který má certifikát, který je potřeba obnovit a potom klikněte na **obnovit nebo upravit vybraný certifikát**.

![Obnovení certifikátu](media/renew-certificate/ssl-cert.png)

Nahrajte nový certifikát PFX, zadejte jeho název, zadejte heslo a pak klikněte na tlačítko **Uložit**.

## <a name="azure-powershell"></a>Azure PowerShell

Při obnovení certifikátu pomocí Azure PowerShell, použijte následující rutinu:

```PowerShell
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

## <a name="next-steps"></a>Další postup

Další postup konfigurace snižování zátěže protokolu SSL s Azure Application Gateway najdete v tématu [konfigurovat přesměrování zpracování SSL](application-gateway-ssl-portal.md)
