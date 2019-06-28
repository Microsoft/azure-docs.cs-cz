---
title: Vytvoření přední dveře s protokolem HTTP na HTTPS přesměrování pomocí webu Azure portal
description: Zjistěte, jak vytvořit přední dveře s přesměrované přenosy z HTTP na HTTPS pomocí webu Azure portal.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: a07b19c49630cc925e719aaa1d46476a1edc58f5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333285"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Vytvoření přední dveře s protokolem HTTP na HTTPS přesměrování pomocí webu Azure portal

Na webu Azure portal můžete použít k vytvoření [branou](front-door-overview.md) certifikátem pro ukončení protokolu SSL. Pravidlo směrování se používá k přesměrování provozu HTTP na HTTPS.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření přední dveře s existujícím prostředkem webové aplikace
> * Přidání vlastní domény s certifikátem SSL 
> * Nastavení přesměrování protokolu HTTPS pro vlastní doménu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Vytvoření přední dveře s existujícím prostředkem webové aplikace

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure Portal.
3. Vyhledejte **branou** pomocí vyhledávání panelu a po nalezení typu prostředku klikněte na tlačítko **vytvořit**.
4. Zvolte předplatné a pak použijte existující skupinu prostředků nebo vytvořte novou. Všimněte si, umístění v uživatelském rozhraní zobrazí výzva je pouze skupiny prostředků. Konfigurace branou se nasadí ve všech [POP v Azure přední dveře](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Konfigurace základy pro nový vstupní brána](./media/front-door-url-redirect/front-door-create-basics.png)

5. Klikněte na tlačítko **Další** zadat na kartě konfigurace. Probíhá konfigurace pro branou ve třech krocích – přidání výchozího hostitele front-endu, přidání back-endů v back-endového fondu a pak vytvořit pravidla směrování pro mapování směrování chování pro hostitele front-endu. 

     ![Návrháře konfigurace branou](./media/front-door-url-redirect/front-door-designer.png)

6. Klikněte na tlačítko " **+** " na ikonu _hostitele front-endu_ Pokud chcete vytvořit hostitele front-endu, zadejte globálně jedinečný název výchozího hostitele front-endu pro vaše branou (`\<**name**\>.azurefd.net`). Klikněte na tlačítko **přidat** pokračujte k dalšímu kroku.

     ![Přidání hostitele front-endu](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Klikněte na tlačítko " **+** " na ikonu _back-endové fondy_ k vytvoření fondu back-endu. Zadejte název back-endový fond a potom klikněte na tlačítko "**přidat back-endu**".
8. Vyberte typ hostitele back-end jako _služby App service_. Vyberte předplatné, které je hostitelem webové aplikace a pak vyberte konkrétní webové aplikace z rozevíracího seznamu pro **název hostitele back-endu**.
9. Klikněte na tlačítko **přidat** uložte back-endu a klikněte na **přidat** znovu pro uložení konfigurace back-endový fond.   ![Přidat back-endu do back-endový fond](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Klikněte " **+** " na ikonu _pravidla směrování_ má být vytvořena trasa. Zadejte název pro tuto trasu, Řekněme, že "HttpToHttpsRedirect" a pak nastavte _přijato protokoly_ pole **HTTP pouze**. Ujistěte se, že odpovídající _hostitele front-endu_ zaškrtnuto.  
11. Na _podrobnosti postupu_ nastavte _typ trasy_ k **přesměrování**, ujistěte se, že _přesměrování typu_ je nastavena na  **Nalezeno (302)** a _přesměrování protokolu_ je nastavena na **pouze HTTPS**. 
12. Klikněte na Přidat a uložit pravidlo směrování pro protokol HTTP, přesměrování protokolu HTTPS.
     ![Přidat protokolu HTTP na HTTPS přesměrování trasu](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Přidáte jiné pravidlo směrování pro zpracování přenosy HTTPS. Klikněte na tlačítko " **+** " přihlášení _pravidla směrování_ a zadejte název pro tuto trasu, Řekněme, že "DefaultForwardingRoute" a pak nastavte _přijato protokoly_ pole k **HTTPS pouze**. Ujistěte se, že odpovídající _hostitele front-endu_ zaškrtnuto.
14. V části Podrobnosti postupu nastavte _typ trasy_ k **vpřed**, ujistěte se, že je vybrané správné back-endového fondu a _předávání protokolu_ je nastavena na  **Pouze HTTPS**. 
15. Klikněte na tlačítko Přidat se uložit pravidlo směrování pro přesměrování požadavku.
     ![Přidání přesměrování trasu pro komunikaci přes protokol HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Klikněte na tlačítko **revize + vytvořit** a potom **vytvořit**, při vytváření profilu branou. Přejdete k prostředku po vytvoření.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Přidání vlastní domény na váš branou a povolení protokolu HTTPS na něm
Následující kroky prezentovat, jak můžete přidat vlastní doménu na existující prostředek branou a pak povolte HTTP na HTTPS přesměrování na něj. 

### <a name="add-a-custom-domain"></a>Přidání vlastní domény

V tomto příkladu přidáte záznam CNAME pro `www` subdoménu (například `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Přidejte záznam CNAME pro mapování subdomény na hostitele front-endu vašeho branou výchozí (`<name>.azurefd.net`, kde `<name>` je název profilu branou).

Pro `www.contoso.com` domény, jako například přidejte záznam CNAME, který mapuje název `www` k `<name>.azurefd.net`.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Vlastní domény CNAME na přední dveře](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Připojit vlastní domény na přední dveře

1. Na kartě branou návrháře klikněte na '+' ikonu na hostitelích front-endu části přidáte novou vlastní doménu. 
2. Do pole název vlastního hostitele, zadejte plně kvalifikovaný vlastní název DNS příklad `www.contosonews.com`. 
3. Jakmile ověříte mapování CNAME z domény do vašeho branou, klikněte na **přidat** přidat vlastní doménu.
4. Klikněte na tlačítko **Uložit** odeslat provedené změny.

![Nabídka Vlastní domény](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Povolení HTTPS pro vlastní doménu

1. Klikněte na tlačítko pro vlastní doménu, která byla přidána a v části **HTTPS vlastní domény**, změňte stav na **povoleno**.
2. Můžete nechat **typ správy certifikátu** nastavena na _branou spravované_ bezplatné certifikát udržuje, spravovat a autorotated podle branou. Můžete také použít vlastní vlastního certifikátu SSL uloženého s Azure Key Vault. Tento kurz předpokládá, že použití branou spravovaný certifikát.
![Povolení HTTPS pro vlastní doménu](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Klikněte na **aktualizace** uložte výběr a potom klikněte na **Uložit**.
4. Klikněte na tlačítko **aktualizovat** za pár minut a pak klikněte na vlastní doménu znovu, abyste viděli průběh zřizování certifikátů. 

> [!WARNING]
> Povolení HTTPS pro vlastní domény může trvat několik minut a také závisí na ověření vlastnictví domény, když branou hostitele není záznam CNAME namapuje přímo `<name>.azurefd.net`. Další informace o [jak povolit HTTPS pro vlastní doménu](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurace pravidel směrování pro vlastní doménu

1. Kliknutím na pravidlo směrování pro přesměrování vytvořili dříve.
2. Klikněte na rozevírací seznam pro hostitele front-endu a vyberte vaši vlastní doménu, chcete-li použít tuto trasu pro vaši doménu také.
3. Klikněte na tlačítko **aktualizace**.
4. To znamená, proveďte stejnou operaci i další směrování pravidla pro předávání trasu k přidání vlastní domény.
5. Klikněte na tlačítko **Uložit** změny potvrďte.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
- Další informace o [adresu URL pro přesměrování na branou](front-door-url-redirect.md).
