---
title: Kurz – Hostování vaší domény a podřízené domény v Azure DNS
description: V tomto kurzu se dozvíte, jak nakonfigurovat službu Azure DNS pro účely hostování zón DNS.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 44f5bf9a28d56e85bae1d50136c50868ec96eb4e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205437"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Kurz: Hostování vaší domény v Azure DNS

Služba Azure DNS umožňuje hostování vaší domény DNS a správu vašich záznamů DNS. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. 

Předpokládejme například, že od registrátora názvů domén zakoupíte doménu contoso.net a potom v Azure DNS vytvoříte zónu s názvem contoso.net. Jako vlastníkovi domény vám registrátor nabídne možnost konfigurovat pro vaši doménu záznamy názvového serveru (tj. záznamy NS). Doménový registrátor uloží tyto záznamy NS v nadřazené zóně, v tomto případě .net. To umožní přesměrování uživatelů internetu po celém světě na vaši doménu ve vaší zóně Azure DNS, když se pokusí o překlad názvů DNS z domény contoso.net.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zóny DNS
> * Načtení seznamu názvových serverů
> * Delegování domény
> * Ověření toho, jestli funguje delegování


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Přihlaste se k portálu Azure.
1. V levém horním rohu vyberte **Vytvořit prostředek** > **Sítě** > **Zóna DNS**. Otevře se stránka **Vytvořit zónu DNS**.

   ![Zóna DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Na stránce **Vytvořit zónu DNS** zadejte následující hodnoty a pak vyberte **Vytvořit**:

   | **Nastavení** | **Hodnota** | **Podrobnosti** |
   |---|---|---|
   |**Název**|[název vaší domény] |Název domény, kterou jste si zakoupili. V tomto kurzu se jako příklad používá doména contoso.net.|
   |**Předplatné**|[Vaše předplatné]|Vyberte předplatné, ve kterém se má zóna vytvořit.|
   |**Skupina prostředků**|**Vytvořit novou:** contosoRG|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný. |
   |**Umístění**|USA – východ||

> [!NOTE]
> Umístění skupiny prostředků nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a nezobrazuje se.

## <a name="retrieve-name-servers"></a>Načtení názvových serverů

Předtím, než budete moci svoji zónu DNS delegovat do Azure DNS, musíte znát názvové servery pro vaši zónu. Azure DNS přiděluje názvové servery z fondu vždy, když je vytvořena zóna.

1. Když máte vytvořenou zónu DNS, na webu Azure Portal v podokně **Oblíbené** vyberte **Všechny prostředky**. Na stránce **Všechny prostředky** vyberte svoji zónu DNS. Pokud předplatné, které jste vybrali, už obsahovalo nějaké prostředky, můžete zadat název své domény do pole **Filtrovat podle názvu** a snadno se tak dostat k bráně aplikace. 

1. Načtěte názvové servery ze stránky zóny DNS. V tomto příkladu má zóna contoso.net přiřazené názvové servery *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* a *ns4-01.azure-dns.info*:

   ![Seznam názvových serverů](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS automaticky vytvoří ve vaší zóně autoritativní záznamy NS pro přiřazené názvové servery.


## <a name="delegate-the-domain"></a>Delegování domény

Teď, když je vytvořena zóna DNS a máte názvové servery, je potřeba aktualizovat nadřazenou doménu s názvovými servery Azure DNS. Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. Na stránce správy DNS vašeho registrátora upravte záznamy NS a nahraďte je názvovými servery Azure DNS.

Při delegování domény do Azure DNS musíte použít názvové servery, které poskytuje Azure DNS. Doporučuje se vždycky použít všechny čtyři názvové servery bez ohledu na název domény. Delegování domény nevyžaduje, aby názvový server měl stejnou doménu nejvyšší úrovně jako vaše doména.

Delegování pomocí názvových serverů ve vaší vlastní zóně, někdy označovaných jako *jednoduché názvové servery*, v současné době není v Azure DNS podporované.

## <a name="verify-that-the-delegation-is-working"></a>Ověření toho, jestli funguje delegování

Po dokončení delegování můžete ověřit jeho funkčnost pomocí nástroje, jako je třeba *nslookup*, který se dotáže na záznam SOA (Start of Authority) pro vaši zónu. Záznam SOA se automaticky vytvoří při vytváření zóny. Po dokončení delegování bude možná potřeba počkat 10 minut nebo i delší dobu, než budete moct úspěšně ověřit jeho funkčnost. Může nějakou dobu trvat, než se změny rozšíří do celého systému DNS.

Nemusíte určit názvové servery Azure DNS. Pokud bylo delegování správně nastaveno, normální proces překladu DNS najde názvové servery automaticky.

Na příkazový řádek zadejte příkaz nslookup podobný tomuto:

```
nslookup -type=SOA contoso.net
```

Tady je příklad odpovědi z předchozího příkazu:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu projít si i následující kurz, můžete si skupinu prostředků **contosoRG** ponechat. V opačném případě skupinu prostředků **contosoRG** odstraňte, aby se odstranily všechny prostředky vytvořené v tomto kurzu. Pokud chcete skupinu prostředků odebrat, klikněte na skupinu prostředků **contosoRG** a potom klikněte na **Odstranit skupinu prostředků**. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili zónu DNS pro svoji doménu a delegovali ji do Azure DNS. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
