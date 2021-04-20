---
title: 'Kurz: Hostování vaší domény a subdomény – Azure DNS'
description: V tomto kurzu se naučíte konfigurovat Azure DNS pro hostování zón DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: c9c0568eb4d8a7403fc29f34a4c4e9f6e0fadecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738859"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Kurz: Hostování vaší domény v Azure DNS

Služba Azure DNS umožňuje hostování vaší domény DNS a správu vašich záznamů DNS. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Předpokládejme, že doménu koupíte `contoso.net` z registrátora názvu domény a potom vytvoříte zónu s názvem `contoso.net` v Azure DNS. Vzhledem k tomu, že jste vlastníkem domény, váš registrátor vám nabídne možnost konfigurovat záznamy názvového serveru (NS) pro vaši doménu. Registrátor ukládá záznamy NS v nadřazené zóně .NET. Uživatelé internetu po celém světě se pak přesměrují do vaší domény v Azure DNS zóně při pokusu o překlad záznamů DNS v contoso.net.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte zónu DNS.
> * Načtěte seznam názvových serverů.
> * Delegování domény
> * Ověřte, zda delegování funguje.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Musíte mít k dispozici název domény pro testování, který můžete hostovat v Azure DNS. Musíte mít úplnou kontrolu nad touto doménou. Úplná kontrola zahrnuje možnost nastavit pro doménu záznamy názvového serveru (NS).

V tomto příkladu odkazujeme na nadřazenou doménu a `contoso.net` .

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Pro vytvoření zóny DNS použijte [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **zóny DNS**.

   ![Zóna DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Vyberte **vytvořit ZÓNU DNS**.

1. Na stránce **vytvořit ZÓNU DNS** zadejte následující hodnoty a pak vyberte **vytvořit**. Například, `contoso.net`.

   > [!NOTE] 
   > Pokud je nově vytvořená zóna podřízenou zónou (např. nadřazená zóna = `contoso.net` podřízená zóna = `child.contoso.net` ), přečtěte si náš [kurz vytvoření nové podřízené zóny DNS](./tutorial-public-dns-zones-child.md) .

    | **Nastavení** | **Hodnota** | **Podrobnosti** |
    |--|--|--|
    | **Skupina prostředků**    | ContosoRG | Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci předplatného, který jste vybrali, jedinečný. Umístění skupiny prostředků nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a není zobrazeno. |
    | **Podřízená zóna**        | ponechat nezaškrtnutou | Vzhledem k tomu, že tato **zóna není** [podřízenou zónou](./tutorial-public-dns-zones-child.md) , měli byste toto políčko nechat nezaškrtnuté. |
    | **Název**              | `contoso.net` | Pole pro název nadřazené zóny      |
    | **Umístění**          | East US | Toto pole je založené na umístění, které jste vybrali jako součást vytváření skupiny prostředků.  |
    

## <a name="retrieve-name-servers"></a>Načtení názvových serverů

Předtím, než budete moci svoji zónu DNS delegovat do Azure DNS, musíte znát názvové servery pro vaši zónu. Azure DNS poskytuje názvové servery z fondu pokaždé, když se vytvoří zóna.

1. Když máte vytvořenou zónu DNS, na webu Azure Portal v podokně **Oblíbené** vyberte **Všechny prostředky**. Na stránce **Všechny prostředky** vyberte svoji zónu DNS. Pokud předplatné, které jste vybrali, již obsahuje několik prostředků, můžete zadat název domény do pole **filtrovat podle názvu** , abyste mohli snadno získat přístup k aplikační bráně. 

1. Načtěte názvové servery ze stránky zóny DNS. V tomto příkladu `contoso.net` byla zóně přiřazeny názvové servery `ns1-01.azure-dns.com` , `ns2-01.azure-dns.net` * `ns3-01.azure-dns.org` a `ns4-01.azure-dns.info` :

   ![Seznam názvových serverů](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS automaticky vytvoří ve vaší zóně autoritativní záznamy NS pro přiřazené názvové servery.

## <a name="delegate-the-domain"></a>Delegování domény

Jakmile se zóna DNS vytvoří a máte názvové servery, bude nutné aktualizovat nadřazenou doménu pomocí názvových serverů Azure DNS. Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. 

1. Na stránce správy DNS vašeho registrátora upravte záznamy NS a nahraďte je názvovými servery Azure DNS.

1. Při delegování domény na Azure DNS je nutné použít názvové servery, které Azure DNS poskytuje. Používejte všechny čtyři názvové servery bez ohledu na název vaší domény. Delegování domény nevyžaduje názvové servery, aby používaly stejnou doménu nejvyšší úrovně jako vaše doména.

> [!NOTE]
> Až budete kopírovat jednotlivé adresy názvových serverů, nezapomeňte zkopírovat i tečku na konci každé adresy. Tečka na konci znamená konec plně kvalifikovaného názvu domény. Některé registrátory přidávají období, pokud název NS nemá na konci. Chcete-li být v souladu se specifikací RFC DNS, uveďte koncové období.

Delegování používající názvové servery ve vaší vlastní zóně, někdy označované jako *individuální názvové servery*, se v současnosti v Azure DNS nepodporují.

## <a name="verify-the-delegation"></a>Ověření delegování

Po dokončení delegování můžete ověřit, že funguje pomocí nástroje, jako je například *nslookup* , pro dotazování záznamu Start of Authority (SOA) pro vaši zónu. Záznam SOA se automaticky vytvoří při vytváření zóny. Možná budete muset po dokončení delegování počkat aspoň 10 minut, než budete moct úspěšně ověřit, jestli funguje. Může nějakou dobu trvat, než se změny rozšíří do celého systému DNS.

Nemusíte zadávat Azure DNS názvové servery. Pokud bylo delegování správně nastaveno, normální proces překladu DNS najde názvové servery automaticky.

1. Z příkazového řádku zadejte příkaz nslookup podobný následujícímu příkladu:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Ověřte, že vaše odpověď vypadá podobně jako v následujícím výstupu nástroje Nslookup:

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

Vyberte skupinu prostředků **contosoRG** a pak vyberte **Odstranit skupinu prostředků**. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili zónu DNS pro vaši doménu a delegovani ji na Azure DNS. Další informace o Azure DNS a webových aplikacích získáte v kurzu o webových aplikacích.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
