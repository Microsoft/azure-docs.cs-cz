---
title: 'Kurz: vytvoření podřízených zón DNS Azure'
titleSuffix: Azure DNS
description: Kurz týkající se vytváření podřízených zón DNS v Azure Portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: d805fb926131e047572887758b10506e4edd5944
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960763"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Kurz: vytvoření nové podřízené zóny DNS

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Přihlašujete se k webu Azure Portal.
> * Vytvoření podřízené zóny DNS prostřednictvím nové zóny DNS.
> * Vytvoření podřízené zóny DNS prostřednictvím nadřazené zóny DNS.
> * Ověřuje se delegování NS pro novou podřízenou zónu DNS.



## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným.  Pokud účet nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Stávající zóna nadřazených Azure DNS.  

Pro účely tohoto kurzu použijeme contoso.com jako nadřazenou zónu a subdomain.contoso.com jako název podřízené domény.  Nahraďte *contoso.com* nadřazeným názvem domény a *subdoménou* s vaší podřízenou doménou.  Pokud jste nadřazenou zónu DNS nevytvořili, přečtěte si téma Postup [vytvoření zóny DNS pomocí Azure Portal](./dns-getstarted-portal.md#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.
Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet.

Existují dva způsoby, jak můžete vytvořit podřízenou zónu DNS.
1.  Prostřednictvím stránky portálu vytvořit zónu DNS.
1.  Prostřednictvím konfigurační stránky nadřazené zóny DNS.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Vytvoření podřízené zóny DNS prostřednictvím vytvoření zóny DNS

V tomto kroku vytvoříme novou podřízenou zónu DNS s názvem **subdomain.contoso.com** a delegujete ji na existující nadřazenou zónu DNS **contoso.com**. Zónu DNS vytvoříte pomocí karet na stránce **vytvořit ZÓNU DNS** .
1.  V nabídce webu Azure Portal nebo na **domovské** stránce vyberte **Vytvořit prostředek**. Zobrazí se **nové** okno.
1.  Vyberte **sítě**, vyberte **zónu DNS** a pak vyberte **Přidat** tlačítko.

1.  Na kartě **základy** zadejte nebo vyberte následující hodnoty:
    * **Předplatné**: vyberte předplatné, ve kterém se má zóna vytvořit.
    * **Skupina prostředků**: zadejte svou stávající skupinu prostředků, nebo můžete vytvořit novou tak, že vyberete **vytvořit novou**, zadáte *MyResourceGroup* a pak vyberete **OK**. Název skupiny prostředků musí být v rámci předplatného Azure jedinečný.
    * Zaškrtněte toto políčko: **Tato zóna je podřízenou položkou stávající zóny, která je již hostována v Azure DNS**
    * **Předplatné nadřazené zóny**: v tomto rozevíracím seznamu vyhledejte nebo vyberte název předplatného, pod kterým se vytvořila nadřazená zóna DNS *contoso.com* .
    * **Nadřazená zóna**: na panelu hledání zadejte *contoso.com* , aby se načetla v rozevíracím seznamu. Po načtení vyberte *contoso.com* z rozevíracího seznamu.
    * **Název:** Pro tento příklad kurzu zadejte *subdoménu* . Všimněte si, že váš nadřazený název zóny DNS *contoso.com* se při výběru nadřazené zóny z výše uvedeného kroku automaticky přidá jako přípona názvu.

1. Vyberte **Další: Zkontrolovat a vytvořit**.
1. Na kartě **Revize + vytvořit** zkontrolujte souhrn, opravte chyby ověřování a potom vyberte **vytvořit**.
Vytvoření zóny může trvat několik minut.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Snímek obrazovky se stránkou vytvořit zónu DNS." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Stránka s přehledem vytvoření podřízené zóny DNS prostřednictvím nadřazené zóny DNS
Můžete také vytvořit novou podřízenou zónu DNS a delegovat ji do nadřazené zóny DNS pomocí tlačítka pro **podřízenou zónu** ze stránky přehled nadřazené zóny. Pomocí tohoto tlačítka se automaticky naplní nadřazené parametry pro podřízenou zónu automaticky. 

1.  V Azure Portal v části **všechny prostředky** otevřete zónu DNS *contoso.com* ve skupině prostředků **MyResourceGroup** . Můžete zadat *contoso.com* do pole **filtrovat podle názvu** , aby bylo snazší ho najít.
1.  Na stránce Přehled zóny DNS vyberte tlačítko **+ podřízená zóna** .

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Tlačítko pro podřízenou zónu obrazovky" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Pak se otevře stránka vytvořit zónu DNS. Možnost podřízené zóny je již zaškrtnuta a na této stránce je již vyplněno předplatné nadřazené zóny a nadřazená zóna.
1.  Pro tento příklad kurzu zadejte název jako *podřízený* . Všimněte si, že nadřazený název zóny DNS contoso.com je automaticky přidán jako předpona názvu.
1.  Vyberte **Další: značky** a potom **Další: zkontrolovat + vytvořit**.
1.  Na kartě **Revize + vytvořit** zkontrolujte souhrn, opravte chyby ověřování a potom vyberte **vytvořit**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Snímek obrazovky pro vybranou podřízenou zónu" border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>Ověřit podřízenou zónu DNS
Teď, když máte vytvořenou novou podřízenou zónu DNS *subdomain.contoso.com* . Chcete-li ověřit, že delegování proběhlo správně, je třeba zkontrolovat záznamy názvový server (NS) pro vaši podřízenou zónu v nadřazené zóně, jak je popsáno níže.  

**Načtěte názvové servery podřízené zóny DNS:**

1.  V Azure Portal v části **všechny prostředky** otevřete zónu DNS *subdomain.contoso.com* ve skupině prostředků **MyResourceGroup** . Můžete zadat *subdomain.contoso.com* do pole **filtrovat podle názvu** , aby bylo snazší ho najít.
1.  Načtěte názvové servery ze stránky přehled zóny DNS. V tomto příkladu se k zóně contoso.com přiřadily názvové servery *ns1-08.Azure-DNS.com, NS2-08.Azure-DNS.NET, NS3-08.Azure-DNS.org* a *NS4-08.Azure-DNS.info*:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Snímek obrazovky názvové servery podřízené zóny" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Ověřte záznam NS v nadřazené zóně DNS:**

Nyní v tomto kroku přejdete na nadřazenou zónu DNS *contoso.com* a zkontrolujeme, že se vytvořila položka sady záznamů NS pro podřízené zóny názvové servery.

1. V Azure Portal v části **všechny prostředky** otevřete zónu DNS contoso.com ve skupině prostředků **MyResourceGroup** . Můžete zadat contoso.com do pole **filtrovat podle názvu** , aby bylo snazší ho najít.
1.  Na stránce Přehled zón DNS *contoso.com* vyhledejte sady záznamů.
1.  Zjistíte, že sada záznamů typu NS a subdoménou Name je už v nadřazené zóně DNS vytvořená. Ověřte hodnoty pro tuto sadu záznamů, které se podobají názvový server seznamu, který jste načetli z podřízené zóny DNS v předchozím kroku.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Snímek obrazovky názvové servery ověření podřízené zóny" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Vyčištění prostředků
Když už nepotřebujete prostředky, které jste vytvořili v tomto kurzu, odstraňte je odstraněním skupiny prostředků **MyResourceGroup** . Otevřete skupinu prostředků **MyResourceGroup** a vyberte **Odstranit skupinu prostředků**.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Scénáře privátních zón Azure DNS](private-dns-scenarios.md)