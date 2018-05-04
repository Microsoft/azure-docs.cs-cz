---
title: Nahrát na server certifikát služby Azure Service Management | Microsoft Docs
description: Naučte se nahrát na server certifikát služby správy pro portál Azure.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: be548a8e823d4c7109951183886764738a66ecea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="upload-an-azure-service-management-certificate"></a>Nahrajte certifikát pro správu služby Azure
Certifikáty pro správu umožňují ověření pomocí modelu nasazení classic poskytovaný platformou Azure. Mnoho programy a nástroje (například Visual Studio nebo sadu Azure SDK) použít tyto certifikáty k automatizaci konfigurace a nasazení různých služeb Azure. 

> [!WARNING]
> Buďte opatrní! Tyto typy certifikátů, povolí všem uživatelům, kteří se mají spravovat předplatné, které jsou přidruženy ověřuje.
>
>

Pokud vás zajímají další informace o Azure certifikáty (včetně vytváření certifikát podepsaný svým držitelem), najdete v části [Přehled certifikátů pro Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Můžete také použít [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) k ověření kódu klienta pro účely automatizace.

**Poznámka:** musí být v předplatném spolusprávce provádět žádné operace v rámci certifikáty pro správu. [Další informace](https://go.microsoft.com/fwlink/?linkid=849300) o tom, jak přidat nebo odebrat Spolusprávců z nového portálu Azure 

## <a name="upload-a-management-certificate"></a>Nahrání certifikátu pro správu
Jakmile je certifikát správy vytvořený, (soubor .cer s pouze veřejný klíč) nahrajte ho do portálu. Je-li certifikát k dispozici na portálu, každý, kdo má odpovídající certifikátu (privátní klíč) můžete připojit přes rozhraní API pro správu a přístup k prostředkům pro přidružené předplatné.

1. Přihlaste se k portálu [Azure Portal](http://portal.azure.com).
2. Klikněte na tlačítko **všechny služby** v seznamu dolní služby Azure, pak vyberte **odběry** v _Obecné_ skupinu služeb.

    ![Předplatné nabídky](./media/azure-api-management-certs/subscriptions_menu.png)

3. Ujistěte se, že vyberte správné předplatné, který chcete přidružit k certifikátu.     
4. Po výběru správné předplatné, stiskněte klávesu **certifikáty pro správu** v _nastavení_ skupiny.

    ![Nastavení](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Stiskněte **nahrát** tlačítko.

    ![Nahrát na stránce certifikáty](./media/azure-api-management-certs/certificates_page.png)
6. Vyplňte informace dialogové okno a stiskněte klávesu **nahrát**.

    ![Nastavení](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Další postup
Teď, když máte certifikát pro správu přidružené předplatné, můžete (po instalaci odpovídající certifikátu místně) prostřednictvím kódu programu připojit k [modelu nasazení classic REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) a automatizovat různé prostředky Azure, které jsou také přidružené tomuto předplatnému.
