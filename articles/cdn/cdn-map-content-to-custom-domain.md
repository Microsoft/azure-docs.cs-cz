---
title: 'Kurz: Přidání vlastní domény do koncového bodu'
titleSuffix: Azure Content Delivery Network
description: Pomocí tohoto kurzu můžete přidat vlastní doménu do koncového bodu Azure Content Delivery Network, aby se název vaší domény zobrazoval v adrese URL.
services: cdn
author: jessie-jyy
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: yuajia
ms.custom: mvc
ms.openlocfilehash: 63ca8cc981f7a192c35f7d1889187ae84773ba08
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307992"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Kurz: Přidání vlastní domény do koncového bodu

V tomto kurzu se dozvíte, jak přidat vlastní doménu do koncového bodu služby Azure Content Delivery Network (CDN). 

Název koncového bodu v profilu CDN je subdoménou azureedge.net. Ve výchozím nastavení je při doručování obsahu zahrnutá doména profilu CDN v rámci adresy URL.

Příklad: **https://contoso.azureedge.net/photo.png**.

Azure CDN poskytuje možnost přidružení vlastní domény ke koncovému bodu CDN. Tato možnost doručuje obsah s vlastní doménou v adrese URL namísto výchozí domény.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořit záznam DNS CNAME.
> - Přidružit vlastní doménu ke koncovému bodu CDN.
> - Ověřit vlastní doménu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Než budete moct dokončit kroky v tomto kurzu, vytvořte profil CDN a aspoň jeden koncový bod CDN. 
    * Další informace najdete v tématu [Rychlý start: Vytvoření profilu a koncového bodu Azure CDN](cdn-create-new-endpoint.md).

* Pokud nemáte vlastní doménu, kupte si ji od poskytovatele domény. 
    * Další informace najdete v tématu [zakoupení vlastního názvu domény](../app-service/manage-custom-dns-buy-domain.md).

* Pokud k hostování [domén DNS](../dns/dns-overview.md)používáte Azure, delegujete svoji vlastní doménu na Azure DNS. 
    * Další informace najdete v tématu [delegování domény na Azure DNS](../dns/dns-delegate-domain-azure-dns.md).

* Pokud ke zpracování domény DNS používáte poskytovatele domény, pokračujte v [vytváření záznamu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Vytvoření záznamu DNS CNAME

Než budete moct použít vlastní doménu s koncovým bodem Azure CDN, musíte nejdřív vytvořit záznam kanonického názvu (CNAME) s Azure DNS nebo vaším poskytovatelem DNS, aby odkazoval na koncový bod CDN. 

Záznam CNAME je záznam DNS, který mapuje název zdrojové domény na název cílové domény. 

V případě Azure CDN je zdrojovým názvem domény vlastní název domény a cílovým názvem domény je název hostitele koncového bodu CDN. 

Po ověření záznamu CNAME Azure CDN směrovat provoz adresovaný do zdrojové vlastní domény na cílový název hostitele koncového bodu CDN.

Vlastní doménu a její subdoménu lze současně přidružit k jednomu koncovému bodu. 

Pro různé subdomény ze stejné vlastní domény pro různé služby Azure použijte více záznamů CNAME.

Můžete namapovat vlastní doménu s různými subdoménami na stejný koncový bod CDN.

> [!NOTE]
> V tomto kurzu se používá typ záznamu CNAME. Pokud používáte typy záznamů nebo AAAA, použijte stejný postup a nahraďte záznam CNAME typem záznamu podle vlastního výběru.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS používá záznamy aliasů pro prostředky Azure v rámci stejného předplatného.

Postup přidání záznamu aliasu pro Azure CDN koncový bod:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V nabídce na levé straně vyberte **všechny prostředky** a pak Azure DNS zónu vlastní domény.

3. V zóně DNS vlastní domény vyberte **+ Sada záznamů**.

4. V části **Přidat sadu záznamů** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název  | Zadejte alias, který chcete použít pro koncový bod CDN. Například **www**. |
    | Typ  | Vyberte **CNAME**. |
    | Sada záznamů aliasů | Vyberte **Ano**. |
    | Typ aliasu | Vyberte **prostředek Azure**. |
    | Zvolit předplatné | Vyberte své předplatné. |
    | Prostředek Azure | Vyberte svůj koncový bod Azure CDN. |

5. Změňte hodnotu **TTL** záznamu na hodnotu.

6. Vyberte **OK**.

# <a name="dns-provider"></a>[**Poskytovatel DNS**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapování dočasné subdomény cdnverify

Pokud mapujete existující doménu, která je v produkčním prostředí, je potřeba vzít v úvahu zvláštní požadavky. 

Při registraci vlastní domény v Azure Portal může dojít k krátké době výpadku domény.

Abyste se vyhnuli přerušení webového provozu, namapujte svoji vlastní doménu na název hostitele koncového bodu CDN s poddoménou Azure **cdnverify** . Tento proces vytvoří dočasné mapování CNAME. 

Díky tomu mají uživatelé v průběhu mapování DNS přístup k vaší doméně bez přerušení. 

Pokud nezáleží na provozním výpadku, můžete přímo namapovat vlastní doménu na koncový bod CDN. Pokračujte a [namapujte trvalou vlastní doménu](#map-the-permanent-custom-domain).

Vytvoření záznamu CNAME se subdoménou cdnverify:

1. Přihlaste se k webu poskytovatele DNS pro vaši vlastní doménu.

2. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj                    | Typ  | Cíl                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Zdroj: zadejte název vlastní domény včetně subdomény cdnverify v následujícím formátu: **cdnverify. \<custom-domain-name>**
        - Příklad: **cdnverify.www.contoso.com**

    - Zadejte: zadejte nebo vyberte **CNAME**.

    - Cíl: zadejte název hostitele koncového bodu CDN včetně subdomény cdnverify v následujícím formátu: **cdnverify. \<endpoint-name> . azureedge.net**. 
        - Příklad: **cdnverify.contoso.azureedge.NET**

3. Uložte provedené změny.

## <a name="map-the-permanent-custom-domain"></a>Mapování trvalé vlastní domény

V této části namapujete trvalou vlastní doménu na koncový bod CDN. 

Vytvoření záznamu CNAME pro vlastní doménu:

1. Přihlaste se k webu poskytovatele vaší vlastní domény.

2. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj          | Typ  | Cíl           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Zdroj: zadejte název vlastní domény.
        - Příklad: **www.contoso.com**

    - Zadejte: zadejte nebo vyberte **CNAME**.

    - Cíl: zadejte název hostitele koncového bodu CDN v následujícím formátu: **\<endpoint-name> . azureedge.NET**. 
        - Příklad: **contoso.azureedge.NET**

3. Uložte provedené změny.

4. Pokud jste předtím vytvořili záznam CNAME dočasné subdomény cdnverify, odstraňte ho. 

5. Pokud tuto vlastní doménu používáte v produkčním prostředí poprvé, postupujte podle kroků pro [přidružení vlastní domény ke koncovému bodu CDN](#associate-the-custom-domain-with-your-cdn-endpoint) a [Ověřte vlastní doménu](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Přidružení vlastní domény ke koncovému bodu CDN

Po zaregistrování vlastní domény ji můžete přidat do svého koncového bodu CDN. 


---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte k profilu CDN obsahujícímu koncový bod, který chcete namapovat na vlastní doménu.
    
2. Na stránce **Profil CDN** vyberte koncový bod CDN, který chcete přidružit k vlastní doméně.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="Výběr koncového bodu CDN" border="true":::
    
3. Vyberte **+ vlastní doména**. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Tlačítko Přidat vlastní doménu" border="true":::

4. V nástroji **Přidat vlastní doménu** je **název hostitele koncového bodu** předem vyplněn a je odvozen z adresy URL koncového bodu CDN: **\<endpoint-hostname>** . azureedge.NET. Název není možné změnit.

5. Do pole **Vlastní název hostitele** zadejte vlastní doménu (včetně subdomény), kterou chcete použít jako zdrojovou doménu záznamu CNAME. 
    1. Například **www.contoso.com** nebo **CDN.contoso.com**. **Nepoužívejte název subdomény cdnverify**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Přidat vlastní doménu" border="true":::

6. Vyberte **Přidat**.

   Azure ověří, že pro zadaný název vlastní domény existuje záznam CNAME. Pokud je záznam CNAME správný, vaše vlastní doména se ověří. 

   Rozšíření nastavení nové vlastní domény do všech hraničních uzlů CDN může chvíli trvat: 
    - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
    - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
    - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Přihlaste se k Azure PowerShell:

```azurepowershell-interactive
    Connect-AzAccount

```
2. K namapování vlastní domény na koncový bod CDN použijte [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) . 

    * Nahraďte **myendpoint8675.azureedge.NET** adresou URL koncového bodu.
    * Nahraďte **myendpoint8675** názvem koncového bodu CDN.
    * Nahraďte **www.contoso.com** vlastním názvem domény.
    * Nahraďte **myCDN** názvem vašeho profilu CDN.
    * Nahraďte **myResourceGroupCDN** názvem vaší skupiny prostředků.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

Azure ověří, že pro zadaný název vlastní domény existuje záznam CNAME. Pokud je záznam CNAME správný, vaše vlastní doména se ověří. 

   Rozšíření nastavení nové vlastní domény do všech hraničních uzlů CDN může chvíli trvat: 

- U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
- V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
- V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut.   


---
## <a name="verify-the-custom-domain"></a>Ověření vlastní domény

Po dokončení registrace vlastní domény ověřte, že vlastní doména odkazuje na koncový bod CDN.
 
1. Ujistěte se, že na koncovém bodu máte veřejný obsah uložený v mezipaměti. Pokud je váš koncový bod CDN například přidružený k účtu úložiště, Azure CDN uloží do mezipaměti obsah veřejného kontejneru. Nastavte kontejner tak, aby povoloval veřejný přístup, který obsahuje alespoň jeden soubor k otestování vlastní domény.

2. V prohlížeči přejděte na adresu souboru s použitím vlastní domény. Pokud je vaše vlastní doména například `www.contoso.com` , adresa URL souboru uloženého v mezipaměti by měla být podobná následující adrese URL: `http://www.contoso.com/my-public-container/my-file.jpg` . Ověřte, že výsledek je stejný jako při přístupu ke koncovému bodu CDN přímo na adrese **\<endpoint-hostname>** . azureedge.NET.

## <a name="clean-up-resources"></a>Vyčištění prostředků

---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal-cleanup)

Pokud už nechcete přidružit svůj koncový bod k vlastní doméně, odeberte vlastní doménu pomocí následujících kroků:
 
1. Přečtěte si poskytovatele DNS, odstraňte záznam CNAME pro vlastní doménu nebo aktualizujte záznam CNAME pro vlastní doménu na koncový bod, který není Azure CDN.

    > [!Important]
    > Aby se zabránilo záznamům DNS v dangling a bezpečnostním rizikům vytvořeným od 9. dubna 2021, Azure CDN vyžaduje odebrání záznamů CNAME Azure CDN koncovým bodům předtím, než je možné prostředky odstranit. Mezi prostředky patří Azure CDN vlastní domény, profily Azure CDN a koncové body nebo skupiny prostředků Azure, které mají povolené Azure CDN vlastních domén.

2. Ve svém profilu CDN vyberte koncový bod s vlastní doménou, kterou chcete odstranit.

3. Na stránce **Koncový bod** v části Vlastní domény klikněte pravým tlačítkem na vlastní doménu, kterou chcete odebrat, a v místní nabídce vyberte **Odstranit**. Vyberte **Ano**.

   Zruší se přidružení vlastní domény ke koncovému bodu.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Pokud už nechcete přidružit svůj koncový bod k vlastní doméně, odeberte vlastní doménu pomocí následujících kroků:

1. Přečtěte si poskytovatele DNS, odstraňte záznam CNAME pro vlastní doménu nebo aktualizujte záznam CNAME pro vlastní doménu na koncový bod, který není Azure CDN.

    > [!Important]
    > Aby se zabránilo záznamům DNS v dangling a bezpečnostním rizikům vytvořeným od 9. dubna 2021, Azure CDN vyžaduje odebrání záznamů CNAME Azure CDN koncovým bodům předtím, než je možné prostředky odstranit. Mezi prostředky patří Azure CDN vlastní domény, profily Azure CDN a koncové body nebo skupiny prostředků Azure, které mají povolené Azure CDN vlastních domén.

2. K odebrání vlastní domény z koncového bodu použijte [příkaz Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) :

    * Nahraďte **myendpoint8675** názvem koncového bodu CDN.
    * Nahraďte **www.contoso.com** vlastním názvem domény.
    * Nahraďte **myCDN** názvem vašeho profilu CDN.
    * Nahraďte **myResourceGroupCDN** názvem vaší skupiny prostředků.

    ```azurepowershell-interactive
        $parameters = @{
            CustomDomainName = 'www.contoso.com'
            EndPointName = 'myendpoint8675'
            ProfileName = 'myCDN'
            ResourceGroupName = 'myResourceGroupCDN'
        }
        Remove-AzCdnCustomDomain @parameters
    ```
---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Vytvořit záznam DNS CNAME.
> - Přidružit vlastní doménu ke koncovému bodu CDN.
> - Ověřit vlastní doménu.

V dalším kurzu se dozvíte, jak pro vlastní doménu Azure CDN nakonfigurovat HTTPS.

> [!div class="nextstepaction"]
> [Kurz: Konfigurace HTTPS pro vlastní doménu Azure CDN](cdn-custom-ssl.md)