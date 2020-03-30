---
title: Nahrání certifikátu správy služeb Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nahrát certifikát správy služeb pro portál Azure.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 4b49a9b391eeca2d2e249b171d99f231bda6fdff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329115"
---
# <a name="upload-an-azure-service-management-certificate"></a>Nahrání certifikátu správy služeb Azure
Certifikáty správy umožňují ověření pomocí klasického modelu nasazení poskytovaného Azure. Mnoho programů a nástrojů (například Visual Studio nebo Azure SDK) používá tyto certifikáty k automatizaci konfigurace a nasazení různých služeb Azure. 

> [!WARNING]
> Dej si pozor! Tyto typy certifikátů umožňují každému, kdo se s nimi ověří, spravovat předplatné, ke kterému je přidružen.
>
>

Pokud chcete získat další informace o certifikátech Azure (včetně vytvoření certifikátu podepsaného svým držitelem), přečtěte si [informace o cloudových službách Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Azure Active [Directory](https://azure.microsoft.com/services/active-directory/) můžete také použít k ověření klientského kódu pro účely automatizace.

**Poznámka:** Chcete-li provádět všechny operace v části Certifikáty správy, musíte být v předplatném spolusprávcem. [Další informace o](https://go.microsoft.com/fwlink/?linkid=849300) přidání nebo odebrání spolusprávců z nového portálu Azure Portal 

## <a name="upload-a-management-certificate"></a>Nahrání certifikátu správy
Jakmile máte vytvořený certifikát pro správu (soubor CER pouze s veřejným klíčem), můžete jej nahrát na portál. Když je certifikát k dispozici na portálu, kdokoli s odpovídajícím certifikátem (soukromý klíč) se může připojit prostřednictvím rozhraní API pro správu a získat přístup k prostředkům pro přidružené předplatné.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Klikněte na **všechny služby** v dolním seznamu služeb Azure a v yberte Předplatná ve skupině _Obecné_ **služby.**

    ![Nabídka Odběr](./media/azure-api-management-certs/subscriptions_menu.png)

3. Nezapomeňte vybrat správné předplatné, které chcete přidružit k certifikátu.     
4. Po výběru správného předplatného stiskněte ve skupině _Nastavení_ **stisknout certifikáty správy.**

    ![Nastavení](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Stiskněte tlačítko **Nahrát.**

    ![Nahrát na stránce certifikátů](./media/azure-api-management-certs/certificates_page.png)
6. Vyplňte informace o dialogu a stiskněte **tlačítko Nahrát**.

    ![Nastavení](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Další kroky
Teď, když máte certifikát pro správu přidružený k předplatnému, můžete se (po místní instalaci odpovídajícího certifikátu) programově připojit ke [klasickému modelu nasazení ROZHRANÍ REST API](/azure/?pivot=sdkstools) a automatizovat různé prostředky Azure, které jsou také přidruženy k tomuto předplatnému.
