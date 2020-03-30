---
title: Přidání vlastního certifikátu certifikační autority – Správa rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat vlastní certifikát certifikační autority ve správě rozhraní Azure API.
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
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073609"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Jak přidat vlastní certifikát certifikační autority ve správě rozhraní Azure API

Azure API Management umožňuje instalaci certifikátů certifikační autority do počítače v rámci důvěryhodných kořenových a zprostředkujících úložišť certifikátů. Tato funkce by měla být použita, pokud vaše služby vyžadují vlastní certifikát certifikační autority.

Článek ukazuje, jak spravovat certifikáty certifikační autority instance služby Azure API Management na webu Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Nahrání certifikátu certifikační autority

![Přidání certifikátů certifikační autority](media/api-management-howto-ca-certificates/00.png)

Chcete-li nahrát nový certifikát certifikační autority, postupujte podle následujících kroků. Pokud jste ještě nevytvořili instanci služby Správa rozhraní API, přečtěte si kurz [Vytvoření instance služby Správa rozhraní API](get-started-create-service-instance.md).

1. Přejděte na instanci služby Azure API Management na webu Azure Portal.

2. V nabídce **vyberte certifikáty certifikační** autority.

3. Klikněte na tlačítko **+Přidat**.  

    ![Přidání certifikátů certifikační autority](media/api-management-howto-ca-certificates/01.png)  

4. Vyhledejte certifikát a rozhodněte o úložišti certifikátů. Je potřeba pouze veřejný klíč, takže heslo není vyžadováno.

    ![Přidání certifikátů certifikační autority](media/api-management-howto-ca-certificates/02.png)  

5. Klikněte na **Uložit**. Tato operace může trvat několik minut.

    ![Přidání certifikátů certifikační autority](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Certifikát certifikační autority můžete `New-AzApiManagementSystemCertificate` nahrát pomocí příkazu Powershell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Odstranění klientského certifikátu

Chcete-li certifikát odstranit, klepněte na kontextovou nabídku **...** a vedle certifikátu vyberte **Odstranit.**

![Odstranit certifikáty certifikační autority](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
