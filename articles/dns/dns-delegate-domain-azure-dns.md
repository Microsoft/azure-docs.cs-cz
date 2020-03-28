---
title: 'Kurz: Hostování domény a subdomény – Azure DNS'
description: V tomto článku najdete informace o konfiguraci Azure DNS pro hostování zón DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 8f29a2bbe0eb392927dd111b13e2260111ddd18e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238969"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Kurz: Hostování vaší domény v Azure DNS

Služba Azure DNS umožňuje hostování vaší domény DNS a správu vašich záznamů DNS. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Předpokládejme například, že od registrátora názvů domén zakoupíte doménu contoso.net a potom v Azure DNS vytvoříte zónu s názvem contoso.net. Jako vlastníkovi domény vám registrátor nabídne možnost konfigurovat pro vaši doménu záznamy názvového serveru (tj. záznamy NS). Doménový registrátor uloží tyto záznamy NS v nadřazené zóně, v tomto případě .net. Uživatelé internetu po celém světě jsou pak přesměrováni do vaší domény v zóně Azure DNS, když se pokusí vyřešit záznamy DNS v contoso.net.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte zónu DNS.
> * Načíst seznam názvových serverů.
> * Delegujte doménu.
> * Ověřte, zda delegování funguje.


Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K testování, které můžete hostovat ve službě Azure DNS, musíte mít k dispozici název domény. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

Ukázková doména použitá pro tento kurz je contoso.net, ale použijte vlastní název domény.

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Přejděte na [portál Azure](https://portal.azure.com/) a vytvořte zónu DNS. Vyhledejte a vyberte **zóny DNS**.

   ![Zóna DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Vyberte **Vytvořit zónu DNS**.
1. Na stránce **Vytvořit zónu DNS** zadejte následující hodnoty a pak vyberte **Vytvořit**:

   | **Nastavení** | **Hodnotu** | **Podrobnosti** |
   |---|---|---|
   |**Název**|[název vaší domény] |Název domény, kterou jste si zakoupili. V tomto kurzu se jako příklad používá doména contoso.net.|
   |**Předplatné**|[Vaše předplatné]|Vyberte předplatné, ve kterém se má zóna vytvořit.|
   |**Skupina prostředků**|**Vytvořit novou:** contosoRG|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný.<br>Umístění skupiny prostředků nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy "globální" a není zobrazeno.|
   |**Umístění**|USA – východ||

## <a name="retrieve-name-servers"></a>Načtení názvových serverů

Předtím, než budete moci svoji zónu DNS delegovat do Azure DNS, musíte znát názvové servery pro vaši zónu. Azure DNS přiděluje názvové servery z fondu vždy, když je vytvořena zóna.

1. Když máte vytvořenou zónu DNS, na webu Azure Portal v podokně **Oblíbené** vyberte **Všechny prostředky**. Na stránce **Všechny prostředky** vyberte svoji zónu DNS. Pokud předplatné, které jste vybrali, již obsahuje několik prostředků, můžete zadat název domény do pole **Filtr podle názvu,** abyste mohli snadno přistupovat k bráně aplikace. 

1. Načtěte názvové servery ze stránky zóny DNS. V tomto příkladu byla contoso.net zóny přiřazena názvová servery *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org*a *ns4-01.azure-dns.info*:

   ![Seznam názvových serverů](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS automaticky vytvoří ve vaší zóně autoritativní záznamy NS pro přiřazené názvové servery.

## <a name="delegate-the-domain"></a>Delegování domény

Teď, když je vytvořena zóna DNS a máte názvové servery, je potřeba aktualizovat nadřazenou doménu s názvovými servery Azure DNS. Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. 

1. Na stránce správy DNS vašeho registrátora upravte záznamy NS a nahraďte je názvovými servery Azure DNS.

1. Když delegujete doménu na Azure DNS, musíte použít názvové servery, které Azure DNS poskytuje. Používejte všechny čtyři názvové servery bez ohledu na název vaší domény. Delegování domény nevyžaduje, aby názvový server používal stejnou doménu nejvyšší úrovně jako vaše doména.

> [!NOTE]
> Až budete kopírovat jednotlivé adresy názvových serverů, nezapomeňte zkopírovat i tečku na konci každé adresy. Tečka na konci znamená konec plně kvalifikovaného názvu domény. Někteří registrátoři připojit období, pokud název NS nemá na konci. Chcete-li být kompatibilní s rfc DNS, zahrňte koncové období.

Delegace, které používají názvové servery ve vaší vlastní zóně, někdy nazývané *názvové servery marnosti*, nejsou aktuálně podporovány v Azure DNS.

## <a name="verify-the-delegation"></a>Ověření delegování

Po dokončení delegování můžete ověřit, zda funguje, pomocí nástroje, jako je *například nslookup,* k dotazu na záznam SOA (Start of Authority) pro vaši zónu. Záznam SOA se automaticky vytvoří při vytváření zóny. Možná budete muset počkat 10 minut nebo více po dokončení delegování, než budete moci úspěšně ověřit, že to funguje. Může nějakou dobu trvat, než se změny rozšíří do celého systému DNS.

Není nutné zadat názvové servery Azure DNS. Pokud bylo delegování správně nastaveno, normální proces překladu DNS najde názvové servery automaticky.

1. Z příkazového řádku zadejte příkaz nslookup podobný následujícímu příkladu:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Ověřte, zda vaše odpověď vypadá podobně jako následující výstup nslookup:

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

- Vyberte skupinu prostředků **contosoRG** a pak vyberte **Odstranit skupinu prostředků**. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pro svou doménu vytvořili zónu DNS a delegovali ji na Azure DNS. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)