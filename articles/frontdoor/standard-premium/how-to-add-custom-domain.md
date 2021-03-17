---
title: Postup přidání vlastní domény do konfigurace služby Azure front-SKU Standard/Premium
description: V tomto kurzu se dozvíte, jak začlenit vlastní doménu do služby Azure front-SKU Standard/Premium.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099101"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Vytvoření vlastní domény na Azure front-SKU Standard/Premium SKU (Preview) pomocí Azure Portal

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Pokud používáte službu Azure front-endu Standard/Premium pro doručování aplikací, je třeba vlastní doména, pokud chcete, aby se váš vlastní název domény zobrazoval v rámci požadavků koncových uživatelů. Srozumitelný název domény může být praktický pro vaše zákazníky a užitečný při budování značky.

Po vytvoření profilu služby Azure front-endu Standard nebo Premium bude mít výchozí hostitel s front-endu subdoménu azurefd.net. Tato subdoména se do adresy URL zahrne, když služba Azure front-Premium Standard nebo Premium doručuje obsah z back-endu ve výchozím nastavení. Například, `https://contoso-frontend.azurefd.net/activeusers.htm`. Pro usnadnění práce poskytuje Azure Front Door možnost přidružit k výchozímu hostiteli vlastní doménu. Pomocí této možnosti dodáte svůj obsah do vlastní domény v adrese URL místo názvu domény vlastněné službou Azure front-Premium (Standard/Premium). Například, https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [**doplňujících podmínek použití pro Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="prerequisites"></a>Požadavky
* Před dokončením kroků v tomto kurzu musíte nejprve vytvořit službu Front Door. Další informace najdete v tématu [rychlý Start: Vytvoření front-dveří Standard/Premium](create-front-door-portal.md).

* Pokud ještě nemáte vlastní doménu, musíte ji nejdřív koupit se zprostředkovatelem domény. Příklad najdete v tématu [Nákup vlastního názvu domény](../../app-service/manage-custom-dns-buy-domain.md).

* Pokud k hostování [domén DNS](../../dns/dns-overview.md)používáte Azure, musíte delegovat DNS (Domain Name System) poskytovatele domény na Azure DNS. Další informace najdete v tématu [delegování domény na Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). V opačném případě, pokud ke zpracování vaší domény DNS používáte poskytovatele domény, musíte doménu ověřit ručně tak, že zadáte výzvy záznamy TXT DNS.

## <a name="add-a-new-custom-domain"></a>Přidat novou vlastní doménu

Část vlastní doména spravovaná doménami na portálu. Vlastní doménu je možné vytvořit a ověřit před přidružením ke koncovému bodu. K vlastní doméně a jejím subdoménám lze současně přidružit pouze jeden koncový bod. Pro různé přední dveře ale můžete použít různé subdomény ze stejné vlastní domény. Můžete také namapovat vlastní domény s různými subdoménami do stejného koncového bodu služby front-dveří.

1. V části nastavení profilu front-dveří Azure vyberte *domény*  a pak klikněte na tlačítko **Přidat doménu** .

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Snímek obrazovky s tlačítkem Přidat doménu na cílové stránce domény":::

1. Zobrazí se stránka **Přidat doménu** , kde můžete zadat informace o vlastní doméně. Můžete zvolit službu DNS spravovanou pomocí Azure, která je doporučená, nebo můžete použít vlastního poskytovatele DNS. Pokud zvolíte DNS spravované v Azure, vyberte existující zónu DNS a pak vyberte vlastní subdoménu nebo vytvořte novou. Pokud používáte jiného poskytovatele DNS, zadejte název vlastní domény ručně. Pokud chcete přidat vlastní doménu, vyberte **Přidat** .

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Snímek obrazovky s přidáním stránky domény":::

    Vytvoří se nová vlastní doména se stavem ověření **odeslání**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Snímek obrazovky s odesláním stavu ověření domény":::

    Počkejte, dokud se stav ověření nezmění na **čekající**. Tato operace může trvat několik minut.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Snímek obrazovky se stavem ověření domény čeká na vyřízení.":::

1. Vyberte stav **čekání** na ověření. Zobrazí se nová stránka s informacemi o záznamu TXT DNS potřebnými k ověření vlastní domény. Záznam TXT má formu `_dnsauth.<your_subdomain>` . Pokud používáte zónu založenou na Azure DNS, vyberte tlačítko **Přidat** a nový záznam TXT se zobrazenou hodnotou záznamu se vytvoří v zóně Azure DNS. Pokud používáte jiného poskytovatele DNS, vytvořte ručně nový záznam TXT `dnsauth.<your_subdomain>` s hodnotou záznamu, jak je znázorněno na stránce.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Snímek obrazovky se stránkou ověření vlastní domény":::

1. Vyberte stav aktualizace. Po ověření domény pomocí záznamu TXT DNS se stav ověření změní na **ověřeno**. Ověření této operace může trvat několik minut.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Snímek obrazovky vlastní domény byl ověřen.":::

1. Zavřete stránku a vraťte se na cílovou stránku seznamu vlastních domén. Stav zřizování vlastní domény by se měl změnit na stav **zřizování** a stav ověření se změní na **schváleno**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Snímek obrazovky se stavem zřizování a schválení.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Přidružení vlastní domény ke koncovému bodu přední dveře

Po ověření vlastní domény ji můžete přidat do koncového bodu služby Azure front-end Standard/Premium.

1. Po ověření vlastní domény ji můžete přidružit ke stávajícímu koncovému bodu služby Azure front-end Standard/Premium a trase. Vyberte odkaz **přidružení koncového bodu** a otevřete stránku **přidružit koncový bod a trasy** . Vyberte koncový bod a trasy, ke kterým se chcete přidružit. Pak vyberte **přidružit**. Po dokončení operace přidružení zavřete stránku.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Snímek stránky přidružit koncový bod a trasy":::

    Stav přidružení koncového bodu by se měl změnit tak, aby odrážel koncový bod, ke kterému je aktuálně přidružená vlastní doména. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Snímek obrazovky s odkazem na přidružení koncového bodu":::

1. Vyberte odkaz stav DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Snímek obrazovky s odkazem na stav DNS":::

1. Zobrazí se stránka **Přidat nebo aktualizovat záznam CNAME** a zobrazí se informace o záznamu CNAME, které musí být poskytnuty před tím, než bude možné začít provozovat tok. Pokud používáte Azure DNS hostované zóny, můžete záznamy CNAME vytvořit tak, že na stránce vyberete tlačítko **Přidat** . Pokud používáte jiného poskytovatele DNS, musíte ručně zadat název a hodnotu záznamu CNAME, jak je znázorněno na stránce.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Snímek obrazovky s přidáním nebo aktualizací záznamu CNAME":::

1. Jakmile se záznam CNAME vytvoří a vlastní doména se přiřadí koncovému bodu služby Azure front-starts, začne se tok provozu.

    > [!NOTE]
    > Pokud je protokol HTTPS povolený, zřizování a šíření certifikátů může trvat několik minut, protože šíření se provádí do všech hraničních umístění. 

## <a name="verify-the-custom-domain"></a>Ověření vlastní domény

Po ověření a přidružení vlastní domény ověřte, že je vlastní doména správně odkazována na váš koncový bod.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Snímek obrazovky s ověřenou a přidruženou vlastní doménou":::

Nakonec ověřte, že se obsah vaší aplikace zpracovává pomocí prohlížeče.

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak povolit protokol HTTPS pro vlastní doménu, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Povolení HTTPS pro vlastní doménu]()
