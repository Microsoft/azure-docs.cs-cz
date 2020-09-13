---
title: Vytvoření front-dveří s použitím přesměrování HTTP na HTTPS pomocí Azure Portal
description: Naučte se, jak vytvořit front-dvířka s přesměrovaným provozem z HTTP na HTTPS pomocí Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: fe2159f0eeb9d01081e6a25e7a88ceff4f1e361c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399686"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Vytvoření front-dveří s použitím přesměrování HTTP na HTTPS pomocí Azure Portal

Pomocí Azure Portal můžete vytvořit [front-dveře](front-door-overview.md) s certifikátem pro ukončení protokolu TLS. Pravidlo směrování se používá k přesměrování provozu HTTP na HTTPS.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření front-dveří s existujícím prostředkem webové aplikace
> * Přidání vlastní domény s certifikátem TLS/SSL 
> * Nastavit přesměrování HTTPS u vlastní domény

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Vytvoření front-dveří s existujícím prostředkem webové aplikace

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure Portal.
3. Vyhledejte **přední dveře** pomocí panelu hledání a jakmile najdete typ prostředku, klikněte na **vytvořit**.
4. Zvolte předplatné a pak buď použijte existující skupinu prostředků, nebo vytvořte novou. Poznámka: umístění, které se požaduje v uživatelském rozhraní, je pouze pro skupinu prostředků. Vaše konfigurace front-dveří se nasadí ve všech [umístěních pop služby Azure front-dveří](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Konfigurace základů pro nové přední dveře](./media/front-door-url-redirect/front-door-create-basics.png)

5. Kliknutím na tlačítko **Další** zadejte kartu konfigurace. Konfigurace pro přední dveře probíhá ve třech krocích – Přidání výchozího hostitele front-endu, přidání back-endy do back-endu a následným vytvořením pravidel směrování pro mapování chování směrování pro hostitele front-endu. 

     ![Návrhář konfigurace front-dveří](./media/front-door-url-redirect/front-door-designer.png)

6. Klikněte na **+** ikonu ' ' na _hostitelích front_ -Endu, abyste vytvořili hostitele front-end, zadejte globálně jedinečný název výchozího hostitele front-endu pro vaše přední dveře ( `\<**name**\>.azurefd.net` ). Kliknutím na tlačítko **Přidat** přejděte k dalšímu kroku.

     ![Přidat hostitele front-endu](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Kliknutím na **+** ikonu ve _fondech back-end_ vytvořte fond back-end. Zadejte název back-end fondu a potom klikněte na**Přidat back-end**.
8. Jako _službu App Service_vyberte typ hostitele back-endu. Vyberte předplatné, ve kterém je vaše webová aplikace hostovaná, a pak vyberte konkrétní webovou aplikaci z rozevíracího seznamu pro **název hostitele back-endu**.
9. Kliknutím na tlačítko **Přidat** uložte back-end a kliknutím na tlačítko **Přidat** znovu uložte konfiguraci fondu back-end.   ![Přidání back-endu do back-endu fondu](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Kliknutím na **+** ikonu v _pravidlech směrování_ vytvoříte trasu. Zadejte název trasy, řekněme, že "HttpToHttpsRedirect" a pak nastavte pole _přijaté protokoly_ na **hodnotu pouze http**. Ujistěte se, že je vybraný příslušný _hostitel front-end_ .  
11. V části _Podrobnosti o trasách_ nastavte _typ trasy_ pro **přesměrování**, zajistěte, aby byl _typ přesměrování_ nastavený na hodnotu **Nalezeno (302)** a _protokol přesměrování_ je nastavený **jenom na https**. 
12. Kliknutím na Přidat uložte pravidlo směrování pro přesměrování HTTP na HTTPS.
     ![Přidat trasu přesměrování HTTP na HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Přidejte další pravidlo směrování pro zpracování provozu HTTPS. Klikněte na **+** přihlašovat _pravidla směrování_ a zadejte název trasy, řekněme, že "DefaultForwardingRoute" a pak nastavte pole _přijaté protokoly_ na **hodnotu pouze https**. Ujistěte se, že je vybraný příslušný _hostitel front-end_ .
14. V části Podrobnosti o trasách nastavte _typ směrování_ , který se má **předat**, ujistěte se, že je vybraný správný back-end fond a že _protokol předávání_ je nastavený **jenom na https**. 
15. Kliknutím na tlačítko Přidat uložte pravidlo směrování pro přesměrování požadavků.
     ![Přidat dopředný postup pro přenosy HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Kliknutím na tlačítko **zkontrolovat + vytvořit** **a vytvořit vytvořte**profil front-dveří. Po vytvoření přejít na prostředek.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Přidání vlastní domény do front-dveří a povolení HTTPS
Následující kroky předvádí, jak můžete přidat vlastní doménu do existujícího prostředku front-dveří a pak povolit přesměrování přes protokol HTTP na HTTPS. 

### <a name="add-a-custom-domain"></a>Přidání vlastní domény

V tomto příkladu přidáte záznam CNAME pro `www` subdoménu (například `www.contosonews.com` ).

#### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Přidejte záznam CNAME pro mapování subdomény k výchozímu hostiteli front-endu ( `<name>.azurefd.net` , kde `<name>` je název vašeho front-endu Profile).

Pro `www.contoso.com` doménu jako příklad přidejte záznam CNAME, který mapuje název `www` na `<name>.azurefd.net` .

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Vlastní doména CNAME do předních dveří](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Zprovoznění vlastní domény na předních dveřích

1. Na kartě přední Návrhář dvířek klikněte na ikonu ' + ' v části hostitelé front-endu a přidejte novou vlastní doménu. 
2. Do pole název vlastního hostitele zadejte plně kvalifikovaný název DNS, třeba `www.contosonews.com` . 
3. Po ověření mapování CNAME z domény na vaše přední dvířka klikněte na **Přidat** a přidejte vlastní doménu.
4. Změny odešlete kliknutím na **Uložit** .

![Nabídka Vlastní domény](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Povolení HTTPS pro vlastní doménu

1. Klikněte na vlastní doménu, kterou jste přidali, a v části **vlastní doména https**změňte stav na **povoleno**.
2. **Typ správy certifikátů** můžete ponechat nastavené na _frontu spravované_ pro bezplatný certifikát, který se udržuje, spravuje a připravuje pomocí front-dveří. Můžete se také rozhodnout použít vlastní certifikát TLS/SSL uložený v Azure Key Vault. V tomto kurzu se předpokládá, že použití spravovaného certifikátu před dveřmi.
![Povolení protokolu HTTPS pro vlastní doménu](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Kliknutím na **aktualizovat** uložte výběr a pak klikněte na **Uložit**.
4. Po několika minutách klikněte na **aktualizovat** a pak znovu klikněte na vlastní doménu. zobrazí se průběh zřizování certifikátu. 

> [!WARNING]
> Povolení protokolu HTTPS pro vlastní doménu může trvat několik minut a také závisí na ověření vlastnictví domény, pokud záznam CNAME není přímo namapovaný na hostitele front-dveří `<name>.azurefd.net` . Přečtěte si další informace o [tom, jak povolit protokol HTTPS pro vlastní doménu](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurace pravidel směrování pro vlastní doménu

1. Klikněte na pravidlo směrování přesměrování vytvořené dříve.
2. Klikněte na rozevírací seznam hostitelé front-end a vyberte vlastní doménu, která bude tuto trasu používat i pro vaši doménu.
3. Klikněte na **Aktualizovat**.
4. Proveďte stejnou operaci pro druhé pravidlo směrování i v případě, že chcete přidat vlastní doménu do trasy pro přesměrování.
5. Změny odešlete kliknutím na **Uložit** .

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
- Přečtěte si další informace o [přesměrování adresy URL na front-dveřích](front-door-url-redirect.md).
