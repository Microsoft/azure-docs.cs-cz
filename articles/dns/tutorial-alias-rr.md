---
title: Kurz – Vytvoření záznamu aliasu Azure DNS odkazujícího na záznam prostředku v zóně
description: V tomto kurzu se dozvíte, jak nakonfigurovat záznam aliasu Azure DNS tak, aby odkazoval na záznam prostředku v rámci zóny.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990837"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Kurz: Vytvoření záznamu aliasu odkazujícího na záznam prostředku zóny

Záznamy aliasů můžou odkazovat na jiné sady záznamů stejného typu. Můžete mít například sadu záznamů DNS CNAME nastavenou jako alias pro jinou sadu záznamů CNAME stejného typu. To je užitečné v případě, že chcete mít nějaké sady záznamů nastavené jako aliasy a jiné tak, aby se nechovaly jako aliasy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření záznamu aliasu pro záznam prostředku v zóně
> * Test záznamu aliasu


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Musíte mít k dispozici název domény, kterou můžete hostovat v Azure DNS a použít k testování. Nad touto doménou musíte mít úplnou kontrolu, včetně možnosti nastavit pro ni záznamy názvového serveru (NS).

Pokyny k hostování domény v Azure DNS najdete v [kurzu hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Vytvoření záznamu aliasu

Vytvořte záznam aliasu, který odkazuje na záznam prostředku v zóně.

### <a name="create-the-target-resource-record"></a>Vytvoření záznamu cílového prostředku
1. Kliknutím na zónu Azure DNS ji otevřete.
2. Klikněte na **Sada záznamů**.
3. Do textového pole **Název** zadejte **server**.
4. Jako **Typ** vyberte **A**.
5. Do textového pole **IP ADRESA** zadejte **10.10.10.10**.
6. Klikněte na **OK**.

### <a name="create-the-alias-record"></a>Vytvoření záznamu aliasu
1. Kliknutím na zónu Azure DNS ji otevřete.
2. Klikněte na **Sada záznamů**.
3. Do textového pole **Název** zadejte **test**.
4. Jako **Typ** vyberte **A**.
5. V zaškrtávacím políčku **Sada záznamů aliasů** klikněte na **Ano** a vyberte možnost **Sada záznamů zóny**.
6. V části **Sada záznamů zóny** vyberte záznam **server**.
7. Klikněte na **OK**.

## <a name="test-the-alias-record"></a>Test záznamu aliasu

1. Spusťte svůj oblíbený nástroj nslookup. Jednou z možností je přejít na adresu [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Nastavte typ dotazu na záznamy A a vyhledejte **test.\<název_vaší_domény\>**. Jako odpověď byste měli dostat adresu **10.10.10.10**.
3. Na webu Azure Portal změňte záznam A **server** na hodnotu **10.11.11.11**.
4. Počkejte několik minut a pak znovu použijte nástroj nslookup pro záznam **test**.
5. Jako odpověď byste měli dostat adresu **10.11.11.11**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, můžete odstranit záznamy prostředků **server** a **test** ve vaší zóně.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili záznam aliasu odkazující na záznam prostředku v rámci zóny. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
