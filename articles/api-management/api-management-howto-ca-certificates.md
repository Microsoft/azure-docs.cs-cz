---
title: Přidání vlastního certifikátu certifikační autority – Azure API Management | Microsoft Docs
description: Přečtěte si, jak přidat vlastní certifikát certifikační autority do Azure API Management. Můžete také zobrazit pokyny k odstranění certifikátu.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51719f23302bfaa036f99e88fcd440d97ca3bc02
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817805"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Postup přidání vlastního certifikátu certifikační autority do Azure API Management

Azure API Management umožňuje instalovat certifikáty certifikační autority v počítači do úložiště důvěryhodných kořenových certifikátů a zprostředkujících certifikátů. Tato funkce by měla být použita, pokud vaše služby vyžadují vlastní certifikát certifikační autority.

V tomto článku se dozvíte, jak spravovat certifikáty certifikační autority instance služby Azure API Management v Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Nahrání certifikátu certifikační autority

![Přidání certifikátů certifikační autority](media/api-management-howto-ca-certificates/00.png)

Pomocí následujícího postupu Nahrajte nový certifikát certifikační autority. Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si kurz [vytvoření instance služby API Management](get-started-create-service-instance.md).

1. V Azure Portal přejděte do své instance služby Azure API Management.

2. Z nabídky vyberte **certifikáty certifikační autority** .

3. Klikněte na tlačítko **+Přidat**.  

    ![Snímek obrazovky, který zobrazuje tlačítko + Přidat pro přidání certifikátu certifikační autority](media/api-management-howto-ca-certificates/01.png)  

4. Vyhledejte certifikát a rozhodněte se v úložišti certifikátů. Je potřeba jenom veřejný klíč, takže se heslo nepožaduje.

    ![Snímek obrazovky, který ukazuje, jak vyhledat certifikát.](media/api-management-howto-ca-certificates/02.png)  

5. Klikněte na **Uložit**. Tato operace může trvat několik minut.

    ![Snímek obrazovky, který ukazuje, jak certifikát Uložit.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Certifikát certifikační autority můžete nahrát pomocí `New-AzApiManagementSystemCertificate` příkazu PowerShellu.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Odstranění klientského certifikátu

Pokud chcete certifikát odstranit, klikněte na místní nabídka **...** a vyberte **Odstranit** vedle certifikátu.

![Odstranění certifikátů certifikační autority](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
