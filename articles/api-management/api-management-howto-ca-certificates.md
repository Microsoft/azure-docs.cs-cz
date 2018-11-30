---
title: Přidat vlastní certifikát certifikační Autority – Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak přidat vlastní certifikát certifikační Autority ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: b05713fd8acb49b25dba04781c1049c9f05115fb
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444682"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Jak přidat vlastní certifikát certifikační Autority ve službě Azure API Management

Azure API Management umožňuje nainstalovat počítač v rámci důvěryhodných kořenových a zprostředkujících certifikátů úložišť certifikátů certifikační Autority. Tato funkce by měla použít, pokud vaše služby vyžadovat vlastní certifikát certifikační Autority.

Tento článek ukazuje, jak spravovat certifikáty certifikační Autority instance služby Azure API Management na webu Azure Portal.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Nahrajte certifikát certifikační Autority

![Přidat certifikáty certifikační Autority](media/api-management-howto-ca-certificates/00.png)

Postupujte podle následujících kroků, abyste nahrát nový certifikát certifikační Autority. Pokud jste instanci služby API Management ještě nevytvořili, projděte si kurz [vytvoření instance služby API Management](get-started-create-service-instance.md).

1. Přejděte k vaší instanci služby Azure API Management na webu Azure Portal.

2. Vyberte **certifikáty certifikační Autority** z nabídky.

3. Klikněte na tlačítko **+ přidat** tlačítko.  

    ![Přidat certifikáty certifikační Autority](media/api-management-howto-ca-certificates/01.png)  

4. Vyhledejte certifikát a rozhodnout o úložišti certifikátů. Pouze veřejný klíč je potřeba, aby nebylo třeba heslo.

    ![Přidat certifikáty certifikační Autority](media/api-management-howto-ca-certificates/02.png)  

5. Klikněte na **Uložit**. Tato operace může trvat několik minut.

    ![Přidat certifikáty certifikační Autority](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Můžete nahrát pomocí certifikátu certifikační Autority `New-AzureRmApiManagementSystemCertificate` příkaz prostředí Powershell.

## <a name="step1a"> </a>Odstranit klientský certifikát

Pokud chcete odstranit certifikát, klikněte na tlačítko kontextové nabídky **...**  a vyberte **odstranit** vedle certifikátu.

![Odstranit certifikáty certifikační Autority](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a