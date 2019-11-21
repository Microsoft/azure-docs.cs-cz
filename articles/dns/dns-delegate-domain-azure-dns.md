---
title: 'Tutorial: Host your domain and subdomain - Azure DNS'
description: In this article, learn how to configure Azure DNS to host your DNS zones.
services: dns
author: asudbring
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: allensu
ms.openlocfilehash: 062a5beaec30d510d37af436e00f4d57785245cd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212194"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Kurz: Hostování vaší domény v Azure DNS

Služba Azure DNS umožňuje hostování vaší domény DNS a správu vašich záznamů DNS. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Předpokládejme například, že od registrátora názvů domén zakoupíte doménu contoso.net a potom v Azure DNS vytvoříte zónu s názvem contoso.net. Jako vlastníkovi domény vám registrátor nabídne možnost konfigurovat pro vaši doménu záznamy názvového serveru (tj. záznamy NS). Doménový registrátor uloží tyto záznamy NS v nadřazené zóně, v tomto případě .net. Internet users around the world are then directed to your domain in your Azure DNS zone when they try to resolve DNS records in contoso.net.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Create a DNS zone.
> * Retrieve a list of name servers.
> * Delegate the domain.
> * Verify the delegation is working.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

You must have a domain name available to test with that you can host in Azure DNS . Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

The example domain used for this tutorial is contoso.net, but use your own domain name.

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Go to the [Azure portal](https://portal.azure.com/) to create a DNS zone. Search for and select **DNS zones**.

   ![Zóna DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Select **Create DNS zone**.
1. Na stránce **Vytvořit zónu DNS** zadejte následující hodnoty a pak vyberte **Vytvořit**:

   | **Nastavení** | **Hodnota** | **Podrobnosti** |
   |---|---|---|
   |**Název**|[název vaší domény] |Název domény, kterou jste si zakoupili. V tomto kurzu se jako příklad používá doména contoso.net.|
   |**Předplatné**|[Vaše předplatné]|Vyberte předplatné, ve kterém se má zóna vytvořit.|
   |**Skupina prostředků**|**Vytvořit novou:** contosoRG|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný.<br>Umístění skupiny prostředků nemá žádný vliv na zónu DNS. The DNS zone location is always "global," and isn't shown.|
   |**Umístění**|USA – východ||

## <a name="retrieve-name-servers"></a>Načtení názvových serverů

Předtím, než budete moci svoji zónu DNS delegovat do Azure DNS, musíte znát názvové servery pro vaši zónu. Azure DNS přiděluje názvové servery z fondu vždy, když je vytvořena zóna.

1. Když máte vytvořenou zónu DNS, na webu Azure Portal v podokně **Oblíbené** vyberte **Všechny prostředky**. Na stránce **Všechny prostředky** vyberte svoji zónu DNS. If the subscription that you selected already has several resources in it, you can enter your domain name in the **Filter by name** box to easily access the application gateway. 

1. Načtěte názvové servery ze stránky zóny DNS. V tomto příkladu má zóna contoso.net přiřazené názvové servery *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* a *ns4-01.azure-dns.info*:

   ![Seznam názvových serverů](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS automaticky vytvoří ve vaší zóně autoritativní záznamy NS pro přiřazené názvové servery.

## <a name="delegate-the-domain"></a>Delegování domény

Teď, když je vytvořena zóna DNS a máte názvové servery, je potřeba aktualizovat nadřazenou doménu s názvovými servery Azure DNS. Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. 

1. Na stránce správy DNS vašeho registrátora upravte záznamy NS a nahraďte je názvovými servery Azure DNS.

1. When you delegate a domain to Azure DNS, you must use the name servers that Azure DNS provides. Use all four name servers, regardless of the name of your domain. Domain delegation doesn't require a name server to use the same top-level domain as your domain.

> [!NOTE]
> Až budete kopírovat jednotlivé adresy názvových serverů, nezapomeňte zkopírovat i tečku na konci každé adresy. Tečka na konci znamená konec plně kvalifikovaného názvu domény. Some registrars append the period if the NS name doesn't have it at the end. To be compliant with the DNS RFC, include the trailing period.

Delegations that use name servers in your own zone, sometimes called *vanity name servers*, aren't currently supported in Azure DNS.

## <a name="verify-the-delegation"></a>Verify the delegation

After you complete the delegation, you can verify that it's working by using a tool such as *nslookup* to query the Start of Authority (SOA) record for your zone. Záznam SOA se automaticky vytvoří při vytváření zóny. You might need to wait 10 minutes or more after you complete the delegation, before you can successfully verify that it's working. Může nějakou dobu trvat, než se změny rozšíří do celého systému DNS.

You don't have to specify the Azure DNS name servers. Pokud bylo delegování správně nastaveno, normální proces překladu DNS najde názvové servery automaticky.

1. From a command prompt, enter an nslookup command similar to the following example:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Verify that your response looks similar to the following nslookup output:

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

Pokud máte v plánu projít si i následující kurz, můžete si skupinu prostředků **contosoRG** ponechat. V opačném případě skupinu prostředků **contosoRG** odstraňte, aby se odstranily všechny prostředky vytvořené v tomto kurzu.

- Select the **contosoRG** resource group, and then select **Delete resource group**. 

## <a name="next-steps"></a>Další kroky

In this tutorial, you created a DNS zone for your domain and delegated it to Azure DNS. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)