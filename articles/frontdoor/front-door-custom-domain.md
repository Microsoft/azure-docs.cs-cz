---
title: Kurz – Přidání vlastní domény do konfigurace služby Azure Front Door | Microsoft Docs
description: V tomto kurzu se dozvíte, jak ke službě Azure Front Door připojit vlastní doménu.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0e1c5e4c3e4b40fd04ca9d48aba9b1e5194d4261
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330921"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Kurz: Přidání vlastní domény do služby Front Door
V tomto kurzu se dozvíte, jak do služby Front Door přidat vlastní doménu. Pokud k doručování aplikací používáte službu Azure Front Door Service a chcete, aby se v požadavcích koncových uživatelů zobrazoval název vaší vlastní domény, potřebujete vlastní doménu. Srozumitelný název domény může být praktický pro vaše zákazníky a užitečný při budování značky.

Po vytvoření služby Front Door se do adresy URL pro doručování obsahu služby Front Door z back-endu ve výchozím nastavení zahrne výchozí hostitel front-endu, což je subdoména `azurefd.net` (například https:\//contoso.azurefd.net/activeusers.htm). Pro usnadnění práce poskytuje Azure Front Door možnost přidružit k výchozímu hostiteli vlastní doménu. Díky této možnosti můžete doručovat obsah na adrese URL s vlastní doménou místo názvu domény vlastněné službou Front Door (například https:\//www.contoso.com/photo.png). 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořit záznam DNS CNAME.
> - Přidružit vlastní doménu ke službě Front Door.
> - Ověřit vlastní doménu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto kurzu musíte nejprve vytvořit službu Front Door. Další informace najdete v tématu [rychlý start: Vytvoření branou](quickstart-create-front-door.md).

Pokud ještě nemáte vlastní doménu, musíte ji nejprve zakoupit u poskytovatele domén. Příklad najdete v tématu [Nákup vlastního názvu domény](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Pokud k hostování [domén DNS](https://docs.microsoft.com/azure/dns/dns-overview) používáte Azure, musíte delegovat DNS (Domain Name System) poskytovatele domén na Azure DNS. Další informace najdete v tématu [Delegování domény do DNS Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Jinak, pokud vaši doménu DNS spravuje poskytovatel domény, přejděte k části [Vytvoření záznamu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Vytvoření záznamu DNS CNAME

Předtím, než budete moct použít vlastní doménu s vaší branou, je nutné nejprve vytvořit záznam kanonického názvu (CNAME) s vašeho poskytovatele domény tak, aby odkazovala na váš branou výchozí front-endu hostitele (třeba contoso.azurefd.net). Záznam CNAME je typem záznamu DNS, který mapuje zdrojový název domény na cílový název domény. V případě služby Azure Front Door Service je zdrojovým názvem domény vlastní název domény a cílovým názvem domény je výchozí název hostitele služby Front Door. Jakmile služba Front Door ověří vytvořený záznam CNAME, bude se provoz určený do zdrojové vlastní domény (například www.contoso.com) směrovat do výchozího hostitele front-endu zadané cílové služby Front Door (například contoso.azurefd.net). 

Vlastní doména a její subdoména můžou současně být přidružené pouze k jedné službě Front Door. Pro různé služby Front Door však můžete použít různé subdomény stejné vlastní domény, a to pomocí několika záznamů CNAME. Na stejnou službu Front Door můžete také namapovat vlastní doménu s různými subdoménami.


## <a name="map-the-temporary-afdverify-sub-domain"></a>Mapování dočasné subdomény afdverify

Pokud mapujete existující doménu, která je v produkčním prostředí, je potřeba vzít v úvahu zvláštní požadavky. Během registrace vlastní domény na webu Azure Portal může dojít ke krátkému výpadku domény. Abyste se vyhnuli přerušení webového provozu, namapujte vlastní doménu nejprve na výchozího hostitele front-endu služby Front Door se subdoménou Azure afdverify a vytvořte tak dočasné mapování CNAME. Díky tomu mají uživatelé v průběhu mapování DNS přístup k vaší doméně bez přerušení.

Jinak, pokud používáte vlastní doménu poprvé a neprobíhá na ní žádný produkční provoz, můžete vlastní doménu namapovat přímo na službu Front Door. Pokračujte k části [Mapování trvalé vlastní domény](#map-the-permanent-custom-domain).

Vytvoření záznamu CNAME se subdoménou afdverify:

1. Přihlaste se k webu poskytovatele vaší vlastní domény.

2. Vyhledejte stránku pro správu záznamů DNS – projděte si dokumentaci poskytovatele nebo hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name server management** (Správa názvových serverů). 

3. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj                    | Typ  | Cíl                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - Zdroj: Zadejte název vaší vlastní domény včetně subdomény afdverify v následujícím formátu: afdverify.  _&lt;vlastního názvu domény&gt;_. Například afdverify.www.contoso.com.

    - Zadejte: Zadejte *CNAME*.

    - Cíl: Zadejte výchozí branou front-endu hostitele, včetně subdomény afdverify v následujícím formátu: afdverify.  _&lt;název koncového bodu&gt;_. azurefd.net. Například afdverify.contoso.azurefd.net.

4. Uložte provedené změny.

Například postup pro registrátora domén GoDaddy je následující:

1. Přihlaste se a vyberte vlastní doménu, kterou chcete použít.

2. V části Domains (Domény) vyberte **Manage All** (Spravovat vše) a pak vyberte **DNS** | **Manage Zones** (Spravovat zóny).

3. Jako **Domain Name** (Název domény) zadejte vlastní doménu a vyberte **Search** (Vyhledat).

4. Na stránce **DNS Management** (Správa DNS) vyberte **Add** (Přidat) a pak v seznamu **Type** (Typ) vyberte **CNAME**.

5. Vyplňte následující pole záznamu CNAME:

    - Zadejte: Ponechte *CNAME* vybrané.

    - Hostitel: Zadejte subdoménu vlastní domény k použití, včetně názvu subdomény afdverify. Například afdverify.www.

    - Points to: Zadejte název hostitele vašeho výchozího branou front-endu hostitele, včetně názvu subdomény afdverify. Například afdverify.contoso.azurefd.net. 

    - TTL: Ponechte *1 hodina* vybrané.

6. Vyberte **Uložit**.
 
    Záznam CNAME se přidá do tabulky záznamů DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Přidružení vlastní domény ke službě Front Door

Po zaregistrování vlastní domény ji můžete přidat do služby Front Door.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte ke službě Front Door obsahující hostitele front-endu, kterého chcete namapovat na vlastní doménu.
    
2. Na stránce **Návrhář služby Front Door** kliknutím na + přidejte vlastní doménu.
    
3. Zadejte **vlastní doménu**. 

4. V poli **Hostitel front-endu** se předvyplní hostitel front-endu, který se použije jako cílová doména vašeho záznamu CNAME a který se odvodí ze služby Front Door: *&lt;výchozí_název_hostitele&gt;*.azurefd.net. Název není možné změnit.

5. Do pole **Vlastní název hostitele** zadejte vlastní doménu (včetně subdomény), kterou chcete použít jako zdrojovou doménu záznamu CNAME. Například www.contoso.com nebo cdn.contoso.com. Nepoužívejte název subdomény afdverify.

6. Vyberte **Přidat**.

   Azure ověří, že pro zadaný název vlastní domény existuje záznam CNAME. Pokud je záznam CNAME správný, vaše vlastní doména se ověří.

>[!WARNING]
> **Musíte** zajistit, aby všichni hostitelé front-endu (včetně vlastních domén) ve službě Front Door měli přidružené pravidlo směrování s výchozí cestou (/\*). To znamená, že napříč všemi směrovacími tabulkami musí pro každého hostitele front-endu existovat alespoň jedno pravidlo směrování s definovanou výchozí cestou (/\*). Pokud se vám to nepodaří zajistit, provoz koncových uživatelů se možná nebude směrovat správně.

## <a name="verify-the-custom-domain"></a>Ověření vlastní domény

Po dokončení registrace vlastní domény ověřte, že odkazuje na výchozího hostitele front-endu služby Front Door.
 
V prohlížeči přejděte na adresu souboru s použitím vlastní domény. Například pokud je vaše vlastní doména robotics.contoso.com, adresa URL souboru uloženého v mezipaměti by měla být podobná jako následující adresa URL: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Ověřte, že výsledek je stejný jako v případě přímého přístupu ke službě Front Door na adrese *&lt;hostitel_služby_Front_Door&gt;*.azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Mapování trvalé vlastní domény

Pokud jste ověřili úspěšné namapování subdomény afdverify na službu Front Door (nebo pokud používáte novou vlastní doménu, která není v produkčním prostředí), můžete pak vlastní doménu namapovat přímo na výchozího hostitele front-endu služby Front Door.

Vytvoření záznamu CNAME pro vlastní doménu:

1. Přihlaste se k webu poskytovatele vaší vlastní domény.

2. Vyhledejte stránku pro správu záznamů DNS – projděte si dokumentaci poskytovatele nebo hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name Server Management** (Správa názvových serverů). 

3. Vytvořte položku záznamu CNAME pro svou vlastní doménu a vyplňte pole podle následující tabulky (názvy polí se můžou lišit):

    | Zdroj          | Typ  | Cíl           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azurefd.net |

    - Zdroj: Zadejte vlastní název domény (například www.contoso.com).

    - Zadejte: Zadejte *CNAME*.

    - Cíl: Zadejte hostitele výchozí branou front-endu. Musí mít následující formát: _&lt;název_hostitele&gt;_.azurefd.net. Například contoso.azurefd.net.

4. Uložte provedené změny.

5. Pokud jste předtím vytvořili záznam CNAME dočasné subdomény afdverify, odstraňte ho. 

6. Pokud tuto vlastní doménu používáte v produkčním prostředí poprvé, postupujte podle kroků v částech [Přidružení vlastní domény ke službě Front Door](#associate-the-custom-domain-with-your-front-door) a [Ověření vlastní domény](#verify-the-custom-domain).

Například postup pro registrátora domén GoDaddy je následující:

1. Přihlaste se a vyberte vlastní doménu, kterou chcete použít.

2. V části Domains (Domény) vyberte **Manage All** (Spravovat vše) a pak vyberte **DNS** | **Manage Zones** (Spravovat zóny).

3. Jako **Domain Name** (Název domény) zadejte vlastní doménu a vyberte **Search** (Vyhledat).

4. Na stránce **DNS Management** (Správa DNS) vyberte **Add** (Přidat) a pak v seznamu **Type** (Typ) vyberte **CNAME**.

5. Vyplňte pole záznamu CNAME:

    - Zadejte: Ponechte *CNAME* vybrané.

    - Hostitel: Zadejte subdoménu vlastní domény k použití. Například www nebo profile.

    - Points to: Zadejte výchozí název hostitele vašeho branou. Například contoso.azurefd.net. 

    - TTL: Ponechte *1 hodina* vybrané.

6. Vyberte **Uložit**.
 
    Záznam CNAME se přidá do tabulky záznamů DNS.

7. Pokud máte záznam CNAME afdverify, vyberte ikonu tužky vedle něj a pak vyberte ikonu koše.

8. Vyberte **Delete** (Odstranit) a odstraňte záznam CNAME.


## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste do služby Front Door přidali vlastní doménu. Pokud už nechcete mít ke službě Front Door přidruženou vlastní doménu, můžete vlastní doménu odebrat provedením následujících kroků:
 
1. V Návrháři služby Front Door vyberte vlastní doménu, kterou chcete odebrat.

2. V místní nabídce pro vlastní doménu klikněte na Odstranit.  

   Zruší se přidružení vlastní domény ke koncovému bodu.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Vytvořit záznam DNS CNAME.
> - Přidružit vlastní doménu ke službě Front Door.
> - Ověřit vlastní doménu.