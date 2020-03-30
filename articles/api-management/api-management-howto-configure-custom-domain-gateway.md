---
title: Konfigurace vlastního názvu domény pro vlastní hostovkou brány azure api managementu | Dokumenty společnosti Microsoft
description: Toto téma popisuje postup konfigurace vlastního názvu domény pro bránu Azure API Management, která se sama hostuje.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335936"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurace názvu vlastní domény

Když zřídíte [bránu pro správu rozhraní Azure API s vlastním hostitelem,](self-hosted-gateway-overview.md) není jí přiřazen název hostitele a musí na ni odkazovat jeho IP adresa. Tento článek ukazuje, jak mapovat existující vlastní název DNS (označovaný také jako název hostitele) samoobslužné brány.

> [!NOTE]
> Funkce brány s vlastním hostitelem je ve verzi Preview. Během předběžné verze je brána s vlastním hostitelem dostupná pouze na úrovních Vývojář a Premium bez dalších poplatků. Úroveň pro vývojáře je omezena na jedno nasazení brány hostované sami sebou.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky popsané v tomto článku, musíte mít:

-   Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Instance správy rozhraní API. Další informace najdete [v tématu Vytvoření instance Azure API Management](get-started-create-service-instance.md).
- Brána s vlastním hostitelem. Další informace naleznete v tématu [Jak zřídit samoobslužnou bránu](api-management-howto-provision-self-hosted-gateway.md)
-   Vlastní název domény, který vlastníte vy nebo vaše organizace. Toto téma neobsahuje pokyny, jak získat vlastní název domény.
-   Záznam DNS hostovaný na serveru DNS, který mapuje název vlastní domény na adresu IP brány hostované samostatně. Toto téma neobsahuje pokyny k hostování záznamu DNS.
-   Musíte mít platný certifikát s veřejným a soukromým klíčem (. PFX). Předmět nebo předmět alternativní název (SAN) musí odpovídat názvu domény (to umožňuje instanci api management bezpečně vystavit adresy URL přes TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Přidání vlastního certifikátu domény do služby API Management

1. V části **Zabezpečení** **vyberte certifikáty.**
2. Vyberte **+ Přidat**.
3. Do pole ID zadejte název zdroje **certifikátu.**
4. Vyberte soubor obsahující certifikát (. PFX) výběrem pole **Certifikát** nebo vedle něj ikonou složky.
5. Do pole **Heslo** zadejte heslo certifikátu.
6. Vyberte **Vytvořit,** chcete-li přidat certifikát do služby api management.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Nastavení vlastního názvu domény pro vlastní hostovku brány Azure Portal použijte portál Azure.

1. V části **Nastavení vyberte brány** v části **Nastavení**.
2. Vyberte bránu s vlastním hostitelem, pro kterou chcete nakonfigurovat název domény.
3. V části **Nastavení**vyberte **Názvy hostitelů** .
4. Vybrat **+ Přidat**
5. Do pole **Název** zadejte název zdroje pro název hostitele.
6. Do pole **Hostname** zadejte název domény.
7. V rozevíracím souboru **Certifikát** vyberte certifikát.
8. Zaškrtněte políčko **Vyjednat klientský certifikát,** pokud některá z souborů API vystavená prostřednictvím této brány používají ověřování klientského certifikátu.
    > [!WARNING]
    > Toto nastavení je sdíleno všemi názvy domén nakonfigurovanými pro bránu.
9. Vyberte **Přidat,** chcete-li přiřadit vlastní název domény vybrané vlastní hostované bráně.

## <a name="next-steps"></a>Další kroky

[Upgrade a škálování služeb](upgrade-and-scale.md)
