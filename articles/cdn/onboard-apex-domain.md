---
title: Připojení kořenové nebo vrcholné domény ke stávajícímu koncovému bodu Azure CDN – Azure Portal
description: Naučte se, jak připojit kořenovou nebo vrcholovou doménu k existujícímu koncovému bodu Azure CDN pomocí Azure Portal.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370130"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Připojení kořenové nebo vrcholné domény ke stávajícímu koncovému bodu Azure CDN

Azure CDN používá záznamy CNAME k ověření vlastnictví domény pro připojování vlastních domén. CDN nevystavuje IP adresu front-endu přidruženou k vašemu profilu CDN. Pokud je váš záměr připojit se k Azure CDN, nemůžete mapovat svoji doménu vrcholu na IP adresu.

Protokol DNS brání přiřazení záznamů CNAME ve vrcholu zóny. Pokud je vaše doména například, `contoso.com` můžete vytvořit záznamy CNAME pro `somelabel.contoso.com` , ale nemůžete vytvořit záznam CNAME pro `contoso.com` sebe samé. Toto omezení představuje problém pro vlastníky aplikace, kteří mají aplikace s vyrovnáváním zatížení za Azure CDN. Vzhledem k tomu, že použití profilu CDN vyžaduje vytvoření záznamu CNAME, není možné nasměrovat profil CDN ze vrcholu zóny.

Tento problém lze vyřešit pomocí záznamů aliasů v Azure DNS. Na rozdíl od záznamů CNAME se záznamy aliasů vytvářejí ve vrcholu zóny. Vlastníci aplikací můžou použít tuto položku k nasměrování záznamu vrcholu zóny na profil CDN, který má veřejné koncové body. Vlastníci aplikace odkazují na stejný profil CDN, který se používá pro všechny ostatní domény v rámci zóny DNS. Například `contoso.com` a `www.contoso.com` může odkazovat na stejný profil CDN. 

Mapování vrcholu nebo kořenové domény na váš profil CDN vyžaduje sloučení CNAME nebo DNS dohledávání. Mechanismus, ve kterém poskytovatel DNS rekurzivně vyřeší záznam CNAME, dokud nezíská IP adresu. Tato funkce je podporována Azure DNS pro koncové body CDN. 

> [!NOTE]
> Existují i další poskytovatelé DNS, kteří podporují sloučení CNAME nebo dohledávání DNS, ale Azure CDN doporučuje použití Azure DNS pro zákazníky pro hostování svých domén.

Můžete použít Azure Portal k připojení vrcholu domény do sítě CDN a povolení protokolu HTTPS tím, že ho přidružíte k certifikátu pro ukončení TLS. Domény vrcholu se také označují jako kořenové nebo holé domény.

## <a name="create-an-alias-record-for-zone-apex"></a>Vytvoří záznam aliasu pro vrchol zóny.

1. Otevřete konfiguraci **Azure DNS** pro doménu, která se má připojit.

2. Vyberte **+ Sada záznamů**.

3. V části **Přidat sadu záznamů** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ------|
    | Název | Zadejte **@** . |
    | Typ | Vyberte **.** |
    | Sada záznamů aliasů | Vyberte **Ano**. |
    | Typ aliasu | Vyberte **prostředek Azure**. |
    | Zvolit předplatné | Vyberte své předplatné. |
    | Prostředek Azure | Vyberte koncový bod CDN. |

4. Zadejte hodnotu **TTL**.

5. Kliknutím na **tlačítko OK** odešlete změny.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Záznam aliasu pro vrchol zóny":::

6. Výše uvedený krok vytvoří záznam vrcholu zóny ukazující na prostředek CDN. **Cdnverify** mapování záznamů CNAME se používá ke zprovoznění domény v profilu CDN.
    1. Příklad: **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Připojení vlastní domény k CDN

Po zaregistrování vlastní domény ji můžete přidat do svého koncového bodu CDN. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte k profilu CDN obsahujícímu koncový bod, který chcete namapovat na vlastní doménu.
    
2. Na stránce **Profil CDN** vyberte koncový bod CDN, který chcete přidružit k vlastní doméně.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="Výběr koncového bodu CDN" border="true":::
    
3. Vyberte **+ vlastní doména**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Tlačítko Přidat vlastní doménu" border="true":::

4. V nástroji **Přidat vlastní doménu** je **název hostitele koncového bodu** předem vyplněn a je odvozen z adresy URL koncového bodu CDN: **\<endpoint-hostname>** . azureedge.NET. Název není možné změnit.

5. Pro **vlastní název hostitele** zadejte svou vlastní kořenovou nebo vrcholovou doménu, kterou chcete použít jako zdrojovou doménu záznamu CNAME. 
    1. Například **contoso.com**. **Nepoužívejte název subdomény cdnverify**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Přidat vlastní doménu" border="true":::

6. Vyberte **Přidat**.

   Azure ověří, že pro zadaný název vlastní domény existuje záznam CNAME. Pokud je záznam CNAME správný, vaše vlastní doména se ověří. 

   Rozšíření nastavení nové vlastní domény do všech hraničních uzlů CDN může chvíli trvat: 
    - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
    - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
    - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut.   

## <a name="enable-https-on-your-custom-domain"></a>Povolení HTTPS pro vlastní doménu

Další informace o povolení protokolu HTTPS ve vlastní doméně pro Azure CDN najdete v tématu [kurz: konfigurace https pro Azure CDN vlastní doménu.](cdn-custom-ssl.md)

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvořit koncový bod CDN](cdn-create-new-endpoint.md).
