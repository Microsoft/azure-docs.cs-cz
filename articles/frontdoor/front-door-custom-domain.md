---
title: Kurz – přidání vlastní domény do konfigurace front-dveří Azure
description: V tomto kurzu se dozvíte, jak ke službě Azure Front Door připojit vlastní doménu.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: duau
ms.openlocfilehash: e153edd807dcb119c34f60dc34e33fed510916bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011519"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Kurz: Přidání vlastní domény do služby Front Door
V tomto kurzu se dozvíte, jak do služby Front Door přidat vlastní doménu. Pokud používáte přední dveře Azure pro doručování aplikací, je potřeba vlastní doména, pokud chcete, aby se váš vlastní název domény zobrazoval v žádosti koncového uživatele. Srozumitelný název domény může být praktický pro vaše zákazníky a užitečný při budování značky.

Po vytvoření front-endu je výchozí hostitel front-end, který je subdoménou `azurefd.net` , zahrnut v adrese URL pro doručování obsahu front-endu z back-endu ve výchozím nastavení (například https: \/ /Contoso-frontend.azurefd.NET/activeusers.htm). Pro usnadnění práce poskytuje Azure Front Door možnost přidružit k výchozímu hostiteli vlastní doménu. Díky této možnosti můžete doručovat obsah na adrese URL s vlastní doménou místo názvu domény vlastněné službou Front Door (například https:\//www.contoso.com/photo.png). 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořit záznam DNS CNAME.
> - Přidružit vlastní doménu ke službě Front Door.
> - Ověřit vlastní doménu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Přední dvířka **nepodporují vlastní** domény s [Punycode](https://en.wikipedia.org/wiki/Punycode) znaky. 

## <a name="prerequisites"></a>Požadavky

* Před dokončením kroků v tomto kurzu musíte nejprve vytvořit službu Front Door. Další informace najdete v tématu [Rychlý start: Vytvoření služby Front Door](quickstart-create-front-door.md).

* Pokud ještě nemáte vlastní doménu, musíte ji nejprve zakoupit u poskytovatele domén. Příklad najdete v tématu [Nákup vlastního názvu domény](../app-service/manage-custom-dns-buy-domain.md).

* Pokud k hostování [domén DNS](../dns/dns-overview.md) používáte Azure, musíte delegovat DNS (Domain Name System) poskytovatele domén na Azure DNS. Další informace najdete v tématu [delegování domény na Azure DNS](../dns/dns-delegate-domain-azure-dns.md). Jinak, pokud vaši doménu DNS spravuje poskytovatel domény, přejděte k části [Vytvoření záznamu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Vytvoření záznamu DNS CNAME

Než budete moct použít vlastní doménu s předními dvířky, musíte nejdřív vytvořit záznam kanonického názvu (CNAME) se svým poskytovatelem domény, aby odkazoval na výchozího hostitele front-endu na front-endu (například contoso.azurefd.net). Záznam CNAME je typem záznamu DNS, který mapuje zdrojový název domény na cílový název domény. U front-bran Azure je názvem zdrojové domény vlastní název domény a cílovým názvem domény je výchozí název hostitele front-dveří. Jakmile se přední dveře ověří, který záznam CNAME vytvoříte, bude provoz adresovaný do zdrojové vlastní domény (například na webové \. contoso.com) směrován do zadaného cílového umístění front-endu na výchozím hostiteli (například contoso-frontend.azurefd.NET). 

Vlastní doménu a její subdoménu lze současně přidružit pouze k jednomu přednímu Dvířku. Můžete ale použít různé subdomény ze stejné vlastní domény pro různé přední dveře pomocí několika záznamů CNAME. Můžete také namapovat vlastní doménu s různými subdoménami na stejné přední dveře.


## <a name="map-the-temporary-afdverify-subdomain"></a>Mapování dočasné subdomény afdverify

Pokud mapujete existující doménu, která je v produkčním prostředí, je potřeba vzít v úvahu zvláštní požadavky. Během registrace vlastní domény na webu Azure Portal může dojít ke krátkému výpadku domény. Abyste se vyhnuli přerušení webového provozu, nejdřív namapujte svoji vlastní doménu na front-endové výchozí hostitele front-endu s poddoménou Azure afdverify a vytvořte dočasné mapování CNAME. Díky tomu mají uživatelé v průběhu mapování DNS přístup k vaší doméně bez přerušení.

Jinak, pokud používáte vlastní doménu poprvé a neprobíhá na ní žádný produkční provoz, můžete vlastní doménu namapovat přímo na službu Front Door. Pokračujte k části [Mapování trvalé vlastní domény](#map-the-permanent-custom-domain).

Vytvoření záznamu CNAME se subdoménou afdverify:

1. Přihlaste se k webu poskytovatele vaší vlastní domény.

2. Vyhledejte stránku pro správu záznamů DNS – projděte si dokumentaci poskytovatele nebo hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name server management** (Správa názvových serverů). 

3. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj                    | Typ  | Cíl                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Zdroj: zadejte název vlastní domény včetně subdomény afdverify v následujícím formátu: afdverify. _&lt; vlastní název &gt; domény_. Například afdverify.www.contoso.com.

    - Typ: Zadejte *CNAME*.

    - Cíl: zadejte výchozího hostitele front-endu front-endu, včetně subdomény afdverify, v následujícím formátu: afdverify. _&lt; název &gt; koncového bodu_. azurefd.NET. Například afdverify.contoso-frontend.azurefd.net.

4. Uložte provedené změny.

Například postup pro registrátora domén GoDaddy je následující:

1. Přihlaste se a vyberte vlastní doménu, kterou chcete použít.

2. V části Domains (Domény) vyberte **Manage All** (Spravovat vše) a pak vyberte **DNS** | **Manage Zones** (Spravovat zóny).

3. Jako **Domain Name** (Název domény) zadejte vlastní doménu a vyberte **Search** (Vyhledat).

4. Na stránce **DNS Management** (Správa DNS) vyberte **Add** (Přidat) a pak v seznamu **Type** (Typ) vyberte **CNAME**.

5. Vyplňte následující pole záznamu CNAME:

    - Type (Typ): Ponechte vybranou hodnotu *CNAME*.

    - Host (Hostitel): Zadejte subdoménu své vlastní domény, kterou chcete použít, včetně názvu subdomény afdverify. Například afdverify.www.

    - Points to (Odkazuje na): Zadejte název výchozího hostitele front-endu služby Front Door včetně názvu subdomény afdverify. Například afdverify.contoso-frontend.azurefd.net. 

    - TTL: ponechte vybranou *jednu hodinu* .

6. Vyberte **Uložit**.
 
    Záznam CNAME se přidá do tabulky záznamů DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Přidružení vlastní domény ke službě Front Door

Po zaregistrování vlastní domény ji můžete přidat do služby Front Door.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte ke službě Front Door obsahující hostitele front-endu, kterého chcete namapovat na vlastní doménu.
    
2. Na stránce **Návrhář služby Front Door** kliknutím na + přidejte vlastní doménu.
    
3. Zadejte **vlastní doménu**. 

4. V poli **Hostitel front-endu** se předvyplní hostitel front-endu, který se použije jako cílová doména vašeho záznamu CNAME a který se odvodí ze služby Front Door: *&lt;výchozí_název_hostitele&gt;*.azurefd.net. Název není možné změnit.

5. Do pole **Vlastní název hostitele** zadejte vlastní doménu (včetně subdomény), kterou chcete použít jako zdrojovou doménu záznamu CNAME. Například webová \. contoso.com nebo CDN.contoso.com. Nepoužívejte název subdomény afdverify.

6. Vyberte **Přidat**.

   Azure ověří, že pro zadaný název vlastní domény existuje záznam CNAME. Pokud je záznam CNAME správný, vaše vlastní doména se ověří.

>[!WARNING]
> **Musíte** zajistit, aby všichni hostitelé front-endu (včetně vlastních domén) ve službě Front Door měli přidružené pravidlo směrování s výchozí cestou (/\*). To znamená, že mezi všemi pravidly směrování musí pro každého front-endového hostitele existovat alespoň jedno pravidlo směrování s definovanou výchozí cestou (/\*). Pokud se vám to nepodaří zajistit, provoz koncových uživatelů se možná nebude směrovat správně.

## <a name="verify-the-custom-domain"></a>Ověření vlastní domény

Po dokončení registrace vlastní domény ověřte, že odkazuje na výchozího hostitele front-endu služby Front Door.
 
V prohlížeči přejděte na adresu souboru s použitím vlastní domény. Například pokud je vaše vlastní doména robotics.contoso.com, adresa URL souboru uloženého v mezipaměti by měla být podobná jako následující adresa URL: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Ověřte, že výsledek je stejný jako při přístupu ke frontě na přední dveře přímo na *&lt; hostiteli &gt;*. azurefd.NET.


## <a name="map-the-permanent-custom-domain"></a>Mapování trvalé vlastní domény

Pokud jste ověřili úspěšné namapování subdomény afdverify na službu Front Door (nebo pokud používáte novou vlastní doménu, která není v produkčním prostředí), můžete pak vlastní doménu namapovat přímo na výchozího hostitele front-endu služby Front Door.

Vytvoření záznamu CNAME pro vlastní doménu:

1. Přihlaste se k webu poskytovatele vaší vlastní domény.

2. Stránku pro správu záznamů DNS najdete v dokumentaci k poskytovateli nebo v části hledání oblastí webu s **názvem domény**, **DNS** nebo **správy názvových serverů**. 

3. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj          | Typ  | Cíl           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - Zdroj: zadejte název vlastní domény (například webová \. contoso.com).

   - Typ: Zadejte *CNAME*.

   - Cíl: Zadejte výchozího hostitele front-endu služby Front Door. Musí být v následujícím formátu:_&lt; hostname &gt;_. azurefd.NET. Například contoso-frontend.azurefd.net.

4. Uložte provedené změny.

5. Pokud jste předtím vytvořili záznam CNAME dočasné subdomény afdverify, odstraňte ho. 

6. Pokud tuto vlastní doménu používáte v produkčním prostředí poprvé, postupujte podle kroků v částech [Přidružení vlastní domény ke službě Front Door](#associate-the-custom-domain-with-your-front-door) a [Ověření vlastní domény](#verify-the-custom-domain).

Například postup pro registrátora domén GoDaddy je následující:

1. Přihlaste se a vyberte vlastní doménu, kterou chcete použít.

2. V části Domains (Domény) vyberte **Manage All** (Spravovat vše) a pak vyberte **DNS** | **Manage Zones** (Spravovat zóny).

3. Jako **Domain Name** (Název domény) zadejte vlastní doménu a vyberte **Search** (Vyhledat).

4. Na stránce **DNS Management** (Správa DNS) vyberte **Add** (Přidat) a pak v seznamu **Type** (Typ) vyberte **CNAME**.

5. Vyplňte pole záznamu CNAME:

    - Type (Typ): Ponechte vybranou hodnotu *CNAME*.

    - Host (Hostitel): Zadejte subdoménu vlastní domény, kterou chcete použít. Například www nebo profile.

    - Points to (Odkazuje na): Zadejte výchozí název hostitele vaší služby Front Door. Například contoso.azurefd.net. 

    - TTL: ponechte vybranou *jednu hodinu* .

6. Vyberte **Uložit**.
 
    Záznam CNAME se přidá do tabulky záznamů DNS.

7. Pokud máte záznam CNAME afdverify, vyberte ikonu tužky vedle něj a pak vyberte ikonu koše.

8. Vyberte **Delete** (Odstranit) a odstraňte záznam CNAME.


## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste do služby Front Door přidali vlastní doménu. Pokud už nechcete mít ke službě Front Door přidruženou vlastní doménu, můžete vlastní doménu odebrat provedením následujících kroků:
 
1. V Návrháři služby Front Door vyberte vlastní doménu, kterou chcete odebrat.

2. V místní nabídce pro vlastní doménu klikněte na Odstranit.  

   Zruší se přidružení vlastní domény ke koncovému bodu.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvořit záznam DNS CNAME.
* Přidružit vlastní doménu ke službě Front Door.
* Ověřit vlastní doménu.

Pokud chcete zjistit, jak povolit protokol HTTPS pro vlastní doménu, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Povolení HTTPS pro vlastní doménu](front-door-custom-domain-https.md)