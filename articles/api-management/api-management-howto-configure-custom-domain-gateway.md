---
title: Konfigurace vlastního názvu domény pro vaši bránu Azure API Management pro místní hostování | Microsoft Docs
description: Toto téma popisuje kroky pro konfiguraci vlastního názvu domény pro bránu Azure API Management pro místní hostování.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99490992"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Konfigurace vlastního názvu domény pro bránu s vlastním hostováním

Když zřídíte místní službu [Azure API Management Gateway](self-hosted-gateway-overview.md), není jí přiřazen název hostitele, na kterou bude odkazovat jeho IP adresa. V tomto článku se dozvíte, jak namapovat existující vlastní název DNS (označovaný také jako název hostitele) na samoobslužnou bránu.

## <a name="prerequisites"></a>Požadavky

K provedení kroků popsaných v tomto článku musíte mít:

-   Musíte mít aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Instance API Management. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
- Samoobslužná brána. Další informace najdete v tématu [jak zřídit samoobslužnou bránu](api-management-howto-provision-self-hosted-gateway.md) .
-   Vlastní název domény, který vlastníte vy nebo vaše organizace. Toto téma neposkytuje pokyny k získání vlastního názvu domény.
-   Záznam DNS hostovaný na serveru DNS, který mapuje název vlastní domény na IP adresu samoobslužné brány. Toto téma neposkytuje pokyny k hostování záznamu DNS.
-   Musíte mít platný certifikát s veřejným i privátním klíčem (. PFX). Alternativní název předmětu nebo subjektu (SAN) musí odpovídat názvu domény (to umožňuje, aby instance API Management bezpečně zveřejnila adresy URL přes protokol TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Přidání vlastního certifikátu domény do služby API Management

Přidejte vlastní certifikát domény (. PFX) do instance API Management nebo odkaz na certifikát uložený v Azure Key Vault. Postupujte podle kroků v částech [zabezpečené back-end služby pomocí ověřování klientského certifikátu v Azure API Management](api-management-howto-mutual-certificates.md).

> [!NOTE]
> Pro doménu brány v místním prostředí doporučujeme použít certifikát trezoru klíčů.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Použijte Azure Portal k nastavení vlastního názvu domény pro vaši samoobslužnou bránu.

1. Vyberte **brány** z části **nasazení a infrastruktura**.
2. Vyberte samoobslužnou bránu, pro kterou chcete nakonfigurovat název domény.
3. V části **Nastavení** vyberte **názvy hostitelů** .
4. Vybrat **+ Přidat**
5. Do pole **název** zadejte název prostředku názvu hostitele.
6. Do pole název **hostitele** zadejte název domény.
7. V rozevíracím seznamu **certifikát** vyberte certifikát.
8. Zaškrtněte políčko **vyjednat klientský certifikát** , pokud kterékoli rozhraní API vystavené přes tuto bránu používá ověřování klientským certifikátem.
    > [!WARNING]
    > Toto nastavení sdílí všechny názvy domén, které jsou pro bránu nakonfigurované.
9. Vyberte **Přidat** a přiřaďte vlastní název domény vybrané samoobslužné bráně.

## <a name="next-steps"></a>Další kroky

[Upgrade a škálování služby](upgrade-and-scale.md)
