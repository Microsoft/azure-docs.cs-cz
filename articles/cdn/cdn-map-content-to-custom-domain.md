---
title: Kurz – Přidání vlastní domény do koncového bodu Azure CDN | Microsoft Docs
description: V tomto kurzu namapujete obsah koncového bodu Azure CDN na vlastní doménu.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: d20dca9a1248cfb2915ebbc456fba80aadcf81f5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196500"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Kurz: Přidání vlastní domény do koncového bodu Azure CDN
V tomto kurzu se dozvíte, jak přidat vlastní doménu do koncového bodu služby Azure Content Delivery Network (CDN). Pokud k doručování obsahu používáte síť CDN a chcete, aby se v adrese URL sítě CDN zobrazoval název vaší vlastní domény, potřebujete vlastní doménu. Srozumitelný název domény může být praktický pro vaše zákazníky a užitečný při budování značky. 

Ve výchozím nastavení se po vytvoření koncového bodu CDN ve vašem profilu vloží název koncového bodu, což je subdoména domény azureedge.net, do adresy URL pro doručování obsahu CDN (například https:\//contoso.azureedge.net/photo.png). Pro usnadnění práce poskytuje Azure CDN možnost přidružit ke koncovému bodu CDN vlastní doménu. Díky této možnosti můžete doručovat obsah na adrese URL s vlastní doménou místo názvu koncového bodu (například https:\//www.contoso.com/photo.png). 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořit záznam DNS CNAME.
> - Přidružit vlastní doménu ke koncovému bodu CDN.
> - Ověřit vlastní doménu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto kurzu musíte nejprve vytvořit profil CDN a alespoň jeden koncový bod CDN. Další informace najdete v tématu [Rychlý start: Vytvoření profilu a koncového bodu Azure CDN](cdn-create-new-endpoint.md).

Pokud ještě nemáte vlastní doménu, musíte ji nejprve zakoupit u poskytovatele domén. Příklad najdete v tématu [Nákup vlastního názvu domény](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app).

Pokud k hostování [domén DNS](https://docs.microsoft.com/azure/dns/dns-overview) používáte Azure, musíte delegovat DNS (Domain Name System) poskytovatele domén na Azure DNS. Další informace najdete v tématu [Delegování domény do DNS Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Jinak, pokud vaši doménu DNS spravuje poskytovatel domény, přejděte k části [Vytvoření záznamu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Vytvoření záznamu DNS CNAME

Než budete moct použít vlastní doménu u koncového bodu Azure CDN, musíte nejprve u svého poskytovatele domény vytvořit záznam kanonického názvu (CNAME) odkazující na váš koncový bod CDN. Záznam CNAME je typem záznamu DNS, který mapuje zdrojový název domény na cílový název domény. V případě Azure CDN je zdrojovým názvem domény vlastní název domény a cílovým názvem domény je název hostitele koncového bodu CDN. Jakmile Azure CDN ověří vytvořený záznam CNAME, bude se provoz určený do zdrojové vlastní domény (například www.contoso.com) směrovat na zadaný název hostitele cílového koncového bodu CDN (například contoso.azureedge.net). 

K vlastní doméně a její subdoméně může současně být přidružený pouze jeden koncový bod. Pro různé koncové body služeb Azure však můžete použít různé subdomény stejné vlastní domény, a to pomocí několika záznamů CNAME. Na stejný koncový bod CDN můžete také namapovat vlastní doménu s různými subdoménami.


## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapování dočasné subdomény cdnverify

Pokud mapujete existující doménu, která je v produkčním prostředí, je potřeba vzít v úvahu zvláštní požadavky. Během registrace vlastní domény na webu Azure Portal může dojít ke krátkému výpadku domény. Abyste se vyhnuli přerušení webového provozu, namapujte vlastní doménu nejprve na název hostitele koncového bodu CDN se subdoménou Azure cdnverify a vytvořte tak dočasné mapování CNAME. Díky tomu mají uživatelé v průběhu mapování DNS přístup k vaší doméně bez přerušení. 

Jinak, pokud používáte vlastní doménu poprvé a neprobíhá na ní žádný produkční provoz, můžete vlastní doménu namapovat přímo na koncový bod CDN. Pokračujte k části [Mapování trvalé vlastní domény](#map-permanent-custom-domain).

Vytvoření záznamu CNAME se subdoménou cdnverify:

1. Přihlaste se k webu poskytovatele vaší vlastní domény.

2. Vyhledejte stránku pro správu záznamů DNS – projděte si dokumentaci poskytovatele nebo hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name server management** (Správa názvových serverů). 

3. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj                    | Typ  | Cíl                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Zdroj: Zadejte název vlastní domény včetně subdomény cdnverify v následujícím formátu: cdnverify._&lt;název_vlastní_domény&gt;. Například cdnverify.www.contoso.com.

    - Typ: Zadejte *CNAME*.

    - Cíl: Zadejte název hostitele koncového bodu CDN včetně subdomény cdnverify v následujícím formátu: cdnverify._&lt;název_koncového_bodu&gt;_.azureedge.net. Například cdnverify.contoso.azureedge.net.

4. Uložte provedené změny.

Například postup pro registrátora domén GoDaddy je následující:

1. Přihlaste se a vyberte vlastní doménu, kterou chcete použít.

2. V části Domains (Domény) vyberte **Manage All** (Spravovat vše) a pak vyberte **DNS** | **Manage Zones** (Spravovat zóny).

3. Jako **Domain Name** (Název domény) zadejte vlastní doménu a vyberte **Search** (Vyhledat).

4. Na stránce **DNS Management** (Správa DNS) vyberte **Add** (Přidat) a pak v seznamu **Type** (Typ) vyberte **CNAME**.

5. Vyplňte následující pole záznamu CNAME:

    ![Záznam CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Type (Typ): Ponechte vybranou hodnotu *CNAME*.

    - Host (Hostitel): Zadejte subdoménu své vlastní domény, kterou chcete použít, včetně názvu subdomény cdnverify. Například cdnverify.www.

    - Points to (Odkazuje na): Zadejte název hostitele vašeho koncového bodu CDN včetně názvu subdomény cdnverify. Například cdnverify.contoso.azureedge.net. 

    - TTL: Ponechte vybranou hodnotu *1 Hour* (1 hodina).

6. Vyberte **Uložit**.
 
    Záznam CNAME se přidá do tabulky záznamů DNS.

    ![Tabulka záznamů DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Přidružení vlastní domény ke koncovému bodu CDN

Po zaregistrování vlastní domény ji můžete přidat do svého koncového bodu CDN. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte do profilu CDN obsahujícího koncový bod, který chcete namapovat na vlastní doménu.
    
2. Na stránce **Profil CDN** vyberte koncový bod CDN, který chcete přidružit k vlastní doméně.

   Otevře se stránka **Koncový bod**.
    
3. Vyberte **Vlastní doména**. 

   ![Tlačítko Vlastní doména CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   Otevře se stránka **Přidat vlastní doménu**.

4. V poli **Název hostitele koncového bodu** je už uveden název hostitele koncového bodu, který se má použít jako cílová doména záznamu CNAME. Je odvozen z adresy URL cílového bodu CDN: *&lt;název hostitele koncového bodu&gt;*.azureedge.net. Název není možné změnit.

5. Do pole **Vlastní název hostitele** zadejte vlastní doménu (včetně subdomény), kterou chcete použít jako zdrojovou doménu záznamu CNAME. Například www.contoso.com nebo cdn.contoso.com. Nepoužívejte název subdomény cdnverify.

   ![Dialogové okno vlastní domény CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Vyberte **Přidat**.

   Azure ověří, že pro zadaný název vlastní domény existuje záznam CNAME. Pokud je záznam CNAME správný, vaše vlastní doména se ověří. 

   Rozšíření nastavení nové vlastní domény do všech hraničních uzlů CDN může chvíli trvat: 
    - U profilů **Azure CDN Standard od Microsoftu** šíření trvá většinou 10 minut. 
    - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
    - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut.   


## <a name="verify-the-custom-domain"></a>Ověření vlastní domény

Po dokončení registrace vlastní domény ověřte, že odkazuje na váš koncový bod CDN.
 
1. Ujistěte se, že na koncovém bodu máte veřejný obsah uložený v mezipaměti. Pokud je váš koncový bod CDN například přidružený k účtu úložiště, Azure CDN uloží do mezipaměti obsah veřejného kontejneru. Pokud chcete vlastní doménu otestovat, ověřte, že je váš kontejner nastavený tak, aby povoloval veřejný přístup, a že obsahuje alespoň jeden soubor.

2. V prohlížeči přejděte na adresu souboru s použitím vlastní domény. Například pokud je vaše vlastní doména cdn.contoso.com, adresa URL souboru uloženého v mezipaměti by měla být podobná jako následující adresa URL: http:\//cdn.contoso.com/my-public-container/my-file.jpg. Ověřte, že výsledek je stejný jako v případě přímého přístupu ke koncovému bodu CDN na adrese *&lt;název hostitele koncového bodu&gt;*.azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Mapování trvalé vlastní domény

Pokud jste ověřili úspěšné namapování subdomény cdnverify na váš koncový bod (nebo pokud používáte novou vlastní doménu, která není v produkčním prostředí), můžete pak vlastní doménu namapovat přímo na název hostitele koncového bodu CDN.

Vytvoření záznamu CNAME pro vlastní doménu:

1. Přihlaste se k webu poskytovatele vaší vlastní domény.

2. Vyhledejte stránku pro správu záznamů DNS – projděte si dokumentaci poskytovatele nebo hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name Server Management** (Správa názvových serverů). 

3. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj          | Typ  | Cíl           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Zdroj: Zadejte název vlastní domény (například www.contoso.com).

    - Typ: Zadejte *CNAME*.

    - Cíl: Zadejte název hostitele koncového bodu CDN. Musí mít následující formát: _&lt;název_koncového_bodu&gt;_.azureedge.net. Například contoso.azureedge.net.

4. Uložte provedené změny.

5. Pokud jste předtím vytvořili záznam CNAME dočasné subdomény cdnverify, odstraňte ho. 

6. Pokud tuto vlastní doménu používáte v produkčním prostředí poprvé, postupujte podle kroků v částech [Přidružení vlastní domény ke koncovému bodu CDN](#associate-the-custom-domain-with-your-cdn-endpoint) a [Ověření vlastní domény](#verify-the-custom-domain).

Například postup pro registrátora domén GoDaddy je následující:

1. Přihlaste se a vyberte vlastní doménu, kterou chcete použít.

2. V části Domains (Domény) vyberte **Manage All** (Spravovat vše) a pak vyberte **DNS** | **Manage Zones** (Spravovat zóny).

3. Jako **Domain Name** (Název domény) zadejte vlastní doménu a vyberte **Search** (Vyhledat).

4. Na stránce **DNS Management** (Správa DNS) vyberte **Add** (Přidat) a pak v seznamu **Type** (Typ) vyberte **CNAME**.

5. Vyplňte pole záznamu CNAME:

    ![Záznam CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Type (Typ): Ponechte vybranou hodnotu *CNAME*.

    - Host (Hostitel): Zadejte subdoménu vlastní domény, kterou chcete použít. Například www nebo cdn.

    - Points to (Odkazuje na): Zadejte název hostitele vašeho koncového bodu CDN. Například contoso.azureedge.net. 

    - TTL: Ponechte vybranou hodnotu *1 Hour* (1 hodina).

6. Vyberte **Uložit**.
 
    Záznam CNAME se přidá do tabulky záznamů DNS.

    ![Tabulka záznamů DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Pokud máte záznam CNAME cdnverify, vyberte ikonu tužky vedle něj a pak vyberte ikonu koše.

8. Vyberte **Delete** (Odstranit) a odstraňte záznam CNAME.


## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste přidali vlastní doménu do koncového bodu CDN. Pokud už nechcete mít ke koncovému bodu přidruženou vlastní doménu, můžete vlastní doménu odebrat provedením následujících kroků:
 
1. Ve svém profilu CDN vyberte koncový bod s vlastní doménou, kterou chcete odstranit.

2. Na stránce **Koncový bod** v části Vlastní domény klikněte pravým tlačítkem na vlastní doménu, kterou chcete odebrat, a v místní nabídce vyberte **Odstranit**.  

   Zruší se přidružení vlastní domény ke koncovému bodu.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Vytvořit záznam DNS CNAME.
> - Přidružit vlastní doménu ke koncovému bodu CDN.
> - Ověřit vlastní doménu.

V dalším kurzu se dozvíte, jak pro vlastní doménu Azure CDN nakonfigurovat HTTPS.

> [!div class="nextstepaction"]
> [Kurz: Konfigurace HTTPS pro vlastní doménu Azure CDN](cdn-custom-ssl.md)


