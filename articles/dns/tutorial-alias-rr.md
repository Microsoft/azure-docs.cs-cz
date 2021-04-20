---
title: 'Kurz: vytvoření záznamu aliasu pro odkazování na záznam prostředku v zóně'
titleSuffix: Azure DNS
description: V tomto kurzu se dozvíte, jak nakonfigurovat záznam aliasu Azure DNS tak, aby odkazoval na záznam prostředku v rámci zóny.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: c20f079fdbccdf003d96a1b2683060c2cd6d0e5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737365"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Kurz: Vytvoření záznamu aliasu odkazujícího na záznam prostředku zóny

Záznamy aliasů můžou odkazovat na jiné sady záznamů stejného typu. Můžete mít například sadu záznamů DNS CNAME nastavenou jako alias pro jinou sadu záznamů CNAME stejného typu. Tato možnosti je užitečná v případě, že chcete mít některé sady záznamů nastavené jako aliasy a jiné tak, aby se nechovaly jako aliasy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření záznamu aliasu pro záznam prostředku v zóně
> * Test záznamu aliasu


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Vytvoření záznamu aliasu

Vytvořte záznam aliasu, který odkazuje na záznam prostředku v zóně.

### <a name="create-the-target-resource-record"></a>Vytvoření záznamu cílového prostředku
1. Výběrem zóny Azure DNS ji otevřete.
2. Vyberte **Sada záznamů**.
3. Do textového pole **Název** zadejte **server**.
4. Jako **Typ** vyberte **A**.
5. Do textového pole **IP ADRESA** zadejte **10.10.10.10**.
6. Vyberte **OK**.

### <a name="create-the-alias-record"></a>Vytvoření záznamu aliasu
1. Výběrem zóny Azure DNS ji otevřete.
2. Vyberte **Sada záznamů**.
3. Do textového pole **Název** zadejte **test**.
4. Jako **Typ** vyberte **A**.
5. V zaškrtávacím políčku **Sada záznamů aliasů** vyberte **Ano**. Pak vyberte možnost **Sada záznamů zóny**.
6. V části **Sada záznamů zóny** vyberte záznam **server**.
7. Vyberte **OK**.

## <a name="test-the-alias-record"></a>Test záznamu aliasu

1. Spusťte svůj oblíbený nástroj nslookup. Jednou z možností je procházení [https://network-tools.com/nslook](https://network-tools.com/nslook) .
2. Nastavte typ dotazu pro záznamy a vyhledejte **test. \<your domain name\>**. Odpověď je **10.10.10.10**.
3. Na webu Azure Portal změňte záznam A **server** na hodnotu **10.11.11.11**.
4. Počkejte několik minut a pak znovu použijte nástroj nslookup pro záznam **test**. Odpověď je **10.11.11.11**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené pro účely tohoto kurzu nepotřebujete, odstraňte ve své zóně záznamy prostředků **server** a **test**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili záznam aliasu odkazující na záznam prostředku v rámci zóny. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
