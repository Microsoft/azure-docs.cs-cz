---
title: Kurz – Hostování vaší domény a podřízené domény v Azure DNS
description: V tomto kurzu se dozvíte, jak nakonfigurovat službu Azure DNS pro účely hostování zón DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 10/30/2018
ms.author: victorh
ms.openlocfilehash: a952eb679810f36008425ae5daacc4261db50c77
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999608"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Kurz: Hostování vaší domény v Azure DNS

Služba Azure DNS umožňuje hostování vaší domény DNS a správu vašich záznamů DNS. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. 

Předpokládejme například, že od registrátora názvů domén zakoupíte doménu contoso.net a potom v Azure DNS vytvoříte zónu s názvem contoso.net. Jako vlastníkovi domény vám registrátor nabídne možnost konfigurovat pro vaši doménu záznamy názvového serveru (tj. záznamy NS). Doménový registrátor uloží tyto záznamy NS v nadřazené zóně, v tomto případě .net. Internet uživatelům na celém světě jsou pak přesměrováni na vaši doménu v zóně Azure DNS, vaše při pokusu o překlad záznamů DNS v contoso.net.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zóny DNS.
> * Načte seznam názvové servery.
> * Delegování domény.
> * Ověřte, že funguje delegování.


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
   |**Skupina prostředků**|**Vytvořit novou:** contosoRG|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný.<br>Umístění skupiny prostředků nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy "globální" a se nezobrazí.|
   |**Umístění**|USA – východ||

## <a name="retrieve-name-servers"></a>Načtení názvových serverů

Předtím, než budete moci svoji zónu DNS delegovat do Azure DNS, musíte znát názvové servery pro vaši zónu. Azure DNS přiděluje názvové servery z fondu vždy, když je vytvořena zóna.

1. Když máte vytvořenou zónu DNS, na webu Azure Portal v podokně **Oblíbené** vyberte **Všechny prostředky**. Na stránce **Všechny prostředky** vyberte svoji zónu DNS. Pokud předplatné, které jste vybrali, již v sobě obsahuje několik prostředků, můžete zadat název domény v **filtrovat podle názvu** pole snadno přístup k službě application gateway. 

1. Načtěte názvové servery ze stránky zóny DNS. V tomto příkladu má zóna contoso.net přiřazené názvové servery *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* a *ns4-01.azure-dns.info*:

   ![Seznam názvových serverů](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS automaticky vytvoří ve vaší zóně autoritativní záznamy NS pro přiřazené názvové servery.

## <a name="delegate-the-domain"></a>Delegování domény

Teď, když je vytvořena zóna DNS a máte názvové servery, je potřeba aktualizovat nadřazenou doménu s názvovými servery Azure DNS. Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. 

1. Na stránce správy DNS vašeho registrátora upravte záznamy NS a nahraďte je názvovými servery Azure DNS.

1. Při delegování domény do Azure DNS musíte použít názvové servery, které poskytuje Azure DNS. Použijte všechny čtyři názvové servery, bez ohledu na název vaší domény. Delegování domény nevyžaduje, aby názvový server, který se použije jako vaši doménu stejnou doménu nejvyšší úrovně.

> [!NOTE]
> Až budete kopírovat jednotlivé adresy názvových serverů, nezapomeňte zkopírovat i tečku na konci každé adresy. Tečka na konci znamená konec plně kvalifikovaného názvu domény. Některé registrátorů připojit období, pokud název NS ho nemá na konci. Chcete-li být kompatibilní s DNS RFC, zahrňte koncové tečky.

Delegování pomocí názvových serverů ve vaší vlastní zóně, někdy nazývá *jednoduché názvové servery*, nejsou aktuálně podporované v Azure DNS.

## <a name="verify-the-delegation"></a>Ověřte, delegování

Po dokončení delegování můžete ověřit, že funguje, pomocí nástroje, jako například *nslookup* se dotáže na záznam Start of Authority (SOA) pro vaši zónu. Záznam SOA se automaticky vytvoří při vytváření zóny. Možná budete muset Počkejte 10 minut nebo více po dokončení delegování, než budete moci úspěšně ověřit, že je funkční. Může nějakou dobu trvat, než se změny rozšíří do celého systému DNS.

Nemusíte určit názvové servery Azure DNS. Pokud bylo delegování správně nastaveno, normální proces překladu DNS najde názvové servery automaticky.

1. Z příkazového řádku zadejte příkaz nslookup podobně jako v následujícím příkladu:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Ověřte, že vaše odpověď vypadá podobně jako následující výstupní nslookup:

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

- Vyberte **contosoRG** prostředku, skupiny a pak vyberte **odstranit skupinu prostředků**. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili jste zónu DNS pro vaši doménu a delegovat do Azure DNS. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)