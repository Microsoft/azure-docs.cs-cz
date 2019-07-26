---
title: Nahrajte certifikát pro správu služeb Azure | Microsoft Docs
description: Přečtěte si, jak nahrát certifikát správy služby pro Azure Portal.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 5ce96796cbfdcefbaf5568ff199cba6a87f65e05
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359874"
---
# <a name="upload-an-azure-service-management-certificate"></a>Nahrajte certifikát pro správu služeb Azure.
Certifikáty pro správu umožňují ověřování pomocí modelu nasazení Classic, který poskytuje Azure. Mnoho programů a nástrojů (například sadu Visual Studio nebo Azure SDK) tyto certifikáty používá k automatizaci konfigurace a nasazení různých služeb Azure. 

> [!WARNING]
> Dej si pozor! Tyto typy certifikátů umožňují všem, kteří se s nimi ověřují, spravovat předplatné, ke kterému jsou přidružené.
>
>

Pokud chcete získat další informace o certifikátech Azure (včetně vytvoření certifikátu podepsaného svým držitelem), přečtěte si téma [Přehled certifikátů pro Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Můžete také použít [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) k ověřování klientského kódu pro účely automatizace.

**Poznámka:** Musíte být spolusprávcem předplatného, abyste mohli provádět jakékoli operace v rámci certifikátů pro správu. [Další informace](https://go.microsoft.com/fwlink/?linkid=849300) o tom, jak přidat nebo odebrat spolusprávce z nového portálu Azure Portal 

## <a name="upload-a-management-certificate"></a>Odeslat certifikát pro správu
Po vytvoření certifikátu pro správu (soubor. cer jenom s veřejným klíčem) ho můžete nahrát na portál. Když je certifikát k dispozici na portálu, může se kdokoli, kdo má odpovídající certifikát (soukromý klíč), připojit prostřednictvím rozhraní API pro správu a získat přístup k prostředkům přidruženého předplatného.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na **všechny služby** v dolním seznamu služeb Azure a pak  vyberte předplatná ve skupině _Obecné_ služby.

    ![Nabídka odběr](./media/azure-api-management-certs/subscriptions_menu.png)

3. Ujistěte se, že jste vybrali správné předplatné, které chcete přidružit k certifikátu.     
4. Po výběru správného předplatného stiskněte ve skupině _nastavení možnost_ **certifikáty pro správu** .

    ![Nastavení](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Stiskněte tlačítko **nahrát** .

    ![Stránka nahrát na certifikáty](./media/azure-api-management-certs/certificates_page.png)
6. Vyplňte informace dialogového okna a stiskněte **Odeslat**.

    ![Nastavení](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Další postup
Teď, když máte certifikát pro správu, který je přidružený k předplatnému, můžete (po instalaci odpovídajícího certifikátu místně) připojit se k [modelu nasazení classic REST API](/azure/#pivot=sdkstools) a automatizovat různé prostředky Azure. které jsou také přidruženy k tomuto předplatnému.
