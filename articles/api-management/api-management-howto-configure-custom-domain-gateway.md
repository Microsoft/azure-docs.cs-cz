---
title: Konfigurace vlastního názvu domény pro vaši bránu Azure API Management pro místní hostování | Microsoft Docs
description: Toto téma popisuje kroky pro konfiguraci vlastního názvu domény pro bránu Azure API Management pro místní hostování.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 0894203be4867e305c8e15467a2a867b9bfdc727
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506804"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurace názvu vlastní domény

Když zřídíte místní službu [Azure API Management Gateway](self-hosted-gateway-overview.md) , není přiřazen název hostitele a musí na něj odkazovat jeho IP adresa. V tomto článku se dozvíte, jak namapovat stávající vlastní název DNS (označovaný také jako název hostitele) pro samostatnou hostovanou bránu.

## <a name="prerequisites"></a>Požadavky

K provedení kroků popsaných v tomto článku musíte mít:

-   Musíte mít aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Instance API Management. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
- Samoobslužná brána. Další informace najdete v tématu [jak zřídit samoobslužnou bránu](api-management-howto-provision-self-hosted-gateway.md) .
-   Vlastní název domény, který vlastníte vy nebo vaše organizace. Toto téma neposkytuje pokyny k získání vlastního názvu domény.
-   Záznam DNS hostovaný na serveru DNS, který mapuje název vlastní domény na IP adresu samoobslužné brány. Toto téma neposkytuje pokyny k hostování záznamu DNS.
-   Musíte mít platný certifikát s veřejným i privátním klíčem (. PFX). Alternativní název předmětu nebo subjektu (SAN) musí odpovídat názvu domény (to umožňuje API Management instance bezpečně vystavit adresy URL přes protokol TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Přidání vlastního certifikátu domény do služby API Management

1. V části **zabezpečení**vyberte **certifikáty** .
2. Vyberte **+ Přidat**.
3. Zadejte název prostředku pro certifikát do pole **ID** .
4. Vyberte soubor, který obsahuje certifikát (. PFX) tak, že vyberete pole **certifikátu** nebo ikonu složky vedle něho.
5. Do pole **heslo** zadejte heslo pro certifikát.
6. Vyberte **vytvořit** a přidejte certifikát do služby API Management.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Použijte Azure Portal k nastavení vlastního názvu domény pro vaši samoobslužnou bránu.

1. Vyberte **brány** z části **nasazení a infrastruktura**.
2. Vyberte samoobslužnou bránu, pro kterou chcete nakonfigurovat název domény.
3. V části **Nastavení**vyberte **názvy hostitelů** .
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
