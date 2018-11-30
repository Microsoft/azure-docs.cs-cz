---
title: Nahrát certifikát pro správu služby Azure | Dokumentace Microsoftu
description: Zjistěte, jak nahrát certifikát pro správu služby pro Azure portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 62bc6507e4b56c9df31a561f7677907486b0d562
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421585"
---
# <a name="upload-an-azure-service-management-certificate"></a>Nahrát certifikát pro správu služby Azure
Certifikáty pro správu umožňují ověření pomocí modelu nasazení classic poskytovaný platformou Azure. Mnoho programech a nástrojích (např. Visual Studio nebo pomocí sady SDK Azure) použijte tyto certifikáty k automatizaci konfigurace a nasazení různých služeb Azure. 

> [!WARNING]
> Dej si pozor! Tyto typy certifikátů povolit všem uživatelům, který se ověřuje pomocí nich ke správě předplatného, které jsou přidružené.
>
>

Pokud chcete další informace o Azure certifikáty (včetně vytvoření certifikátu podepsaného svým držitelem), přečtěte si téma [Přehled certifikátů pro Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Můžete také použít [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) k ověření kódu klienta pro účely automatizace.

**Poznámka:** musí být spolupracující správce daného předplatného provádět jakékoli operace v rámci certifikáty pro správu. [Další informace](https://go.microsoft.com/fwlink/?linkid=849300) o tom, jak přidat nebo odebrat Spolusprávce z nového portálu Azure Portal 

## <a name="upload-a-management-certificate"></a>Nahrání certifikátu pro správu
Jakmile budete mít vytvořený certifikát správy, (soubor .cer s pouze veřejný klíč) nahrajte ho do portálu. Je-li certifikát k dispozici na portálu, každý, kdo má odpovídajícího certifikátu (privátní klíč) připojení přes rozhraní API pro správu a přístup k prostředkům pro přidružené předplatné.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **všechny služby** v dolním seznamu služeb Azure, pak vyberte **předplatná** v _Obecné_ skupinu služeb.

    ![Nabídka předplatného](./media/azure-api-management-certs/subscriptions_menu.png)

3. Ujistěte se, že vyberte správné předplatné, které chcete přidružit k certifikátu.     
4. Po výběru správné předplatné, stiskněte klávesu **certifikáty pro správu** v _nastavení_ skupiny.

    ![Nastavení](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Stisknutím klávesy **nahrát** tlačítko.

    ![Nahrát na stránce certifikáty](./media/azure-api-management-certs/certificates_page.png)
6. Vyplňte dialogové okno informace a stiskněte klávesu **nahrát**.

    ![Nastavení](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Další postup
Teď, když máte certifikát pro správu spojené s předplatným, můžete (po instalaci odpovídajícího certifikátu místně) programově připojit k [modelu nasazení classic rozhraní REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) a automatizovat různé prostředky Azure, které jsou spojeny s tímto předplatným.
