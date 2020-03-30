---
title: Vytvoření předních dveří s přesměrováním HTTP na HTTPS pomocí portálu Azure
description: Zjistěte, jak vytvořit přední dveře s přesměrovanou návštěvností z PROTOKOLU HTTP na protokol HTTPS pomocí portálu Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: b7385ef27cd17705f2c86b6f57d4780511b6935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246853"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Vytvoření předních dveří s přesměrováním HTTP na HTTPS pomocí portálu Azure

Portál Azure můžete použít k vytvoření [přednídveře](front-door-overview.md) s certifikátem pro ukončení SSL. Pravidlo směrování se používá k přesměrování přenosu HTTP na protokol HTTPS.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření předních dveří s existujícím prostředkem webové aplikace
> * Přidání vlastní domény s certifikátem SSL 
> * Instalační přesměrování HTTPS na vlastní doménu

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Vytvoření předních dveří s existujícím prostředkem webové aplikace

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure Portal.
3. Vyhledejte **přední dveře** pomocí vyhledávacího panelu a jakmile najdete typ prostředku, klepněte na tlačítko **Vytvořit**.
4. Zvolte předplatné a pak použijte existující skupinu prostředků nebo vytvořte novou. Všimněte si, že umístění dotazované v ui je pouze pro skupinu prostředků. Konfigurace předních dveří se nasadí ve všech [umístěních POP azure front door](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Konfigurace základů pro nové přední dveře](./media/front-door-url-redirect/front-door-create-basics.png)

5. Klepnutím na tlačítko **Další** přejděte na kartu konfigurace. Konfigurace pro front door se děje ve třech krocích – přidání výchozího hostitele front-endu, přidání back-endů do back-endového fondu a následné vytvoření pravidel směrování pro mapování chování směrování pro hostitele front-endu. 

     ![Návrhář konfigurace předních dveří](./media/front-door-url-redirect/front-door-designer.png)

6. Kliknutím na**+** ikonu ' na _hostitelích frontendu_ vytvořte front-endového hostitele, zadejte globálně jedinečný`\<**name**\>.azurefd.net`název výchozího hostitele front-endu pro front-door ( ). Chcete-li pokračovat dalším krokem, klepněte na tlačítko **Přidat.**

     ![Přidání front-endu hostitele](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Kliknutím na**+** ikonu ' ' v _back-endových fondech_ vytvořte back-endový fond. Zadejte název back-endového fondu a klikněte na tlačítko '**Přidat back-end**'.
8. Vyberte typ hostitele back-endu jako _službu aplikace_. Vyberte předplatné, kde je vaše webová aplikace hostovaná, a pak vyberte konkrétní webovou aplikaci z rozevíracího portálu **Backend název hostitele**.
9. Klepnutím na **tlačítko Přidat** uložte back-end a dalším klepnutím na tlačítko **Přidat** uložte konfiguraci back-endového fondu.   ![Přidání back-endu do back-endu fondu](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Chcete-li**+** vytvořit trasu, klepněte na ikonu ' v _pravidlech směrování._ Zadejte název trasy, řekněte HttpToHttpsRedirect a nastavte pole _Přijaté protokoly_ na **"pouze http"**. Ujistěte se, že je vybrán příslušný _front-endhostitele._  
11. V části _Podrobnosti trasy_ nastavte _typ trasy_ na **přesměrování**, ujistěte se, že _typ přesměrování_ je nastaven na **nalezeno (302)** a _protokol Přesměrování_ je nastaven pouze na **protokol HTTPS**. 
12. Kliknutím na Přidat uložte pravidlo směrování pro http na přesměrování HTTPS.
     ![Přidání protokolu HTTP do přesměrování https](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Přidejte další pravidlo směrování pro zpracování provozu HTTPS. Klepněte**+** na tlačítko ' ' podepsat _na pravidla směrování_ a zadejte název trasy, řekněme 'DefaultForwardingRoute', a nastavte pole Přijaté _protokoly_ na **'pouze HTTPS'**. Ujistěte se, že je vybrán příslušný _front-endhostitele._
14. V části Podrobnosti trasy nastavte _typ trasy_ **dopředu**, ujistěte se, že je vybrán pravý back-endový fond a protokol _pro předávání_ je nastaven pouze na **protokol HTTPS**. 
15. Kliknutím na Přidat uložte pravidlo směrování pro předávání požadavků.
     ![Přidání dopředné trasy pro přenos https](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Klikněte na **Revize + vytvořit** a potom **vytvořit**a vytvořte profil předních dveří. Po vytvoření přejděte na zdroj.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Přidání vlastní domény do předních dveří a povolení protokolu HTTPS
Následující kroky ukazují, jak můžete přidat vlastní doménu na existující prostředek front door a pak povolit HTTP na https přesměrování na něm. 

### <a name="add-a-custom-domain"></a>Přidání vlastní domény

V tomto příkladu přidáte záznam CNAME pro subdoménu `www` (například ). `www.contosonews.com`

#### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME

Přidejte záznam CNAME pro mapování subdomény na výchozího front-endového hostitele předních dveří (`<name>.azurefd.net`, kde `<name>` je název vašeho profilu předních dveří).

Pro `www.contoso.com` doménu, jako příklad, přidejte záznam CNAME, který mapuje název `www` na `<name>.azurefd.net`.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![CNAME vlastní doména na přední dveře](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Na palubě vlastní domény na předních dveřích

1. Na kartě Návrhář předních dveří klikněte na ikonu +v části Frontend hosts a přidejte novou vlastní doménu. 
2. Do pole vlastní název hostitele zadejte plně `www.contosonews.com`kvalifikovaný vlastní název DNS, například . 
3. Po ověření mapování CNAME z domény do předních dveří klikněte na **Přidat** a přidejte vlastní doménu.
4. Kliknutím na **Uložit** odešlete změny.

![Nabídka Vlastní domény](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Povolení protokolu HTTPS ve vlastní doméně

1. Klikněte na vlastní doménu, která byla přidána, a v části **Vlastní doména HTTPS**, změňte stav na **Povoleno**.
2. Typ správy **certifikátu** můžete ponechat nastavený na _front door spravovaný_ pro bezplatný certifikát udržovaný, spravovaný a automaticky otočený předními dveřmi. Můžete také použít vlastní certifikát SSL uložený v trezoru klíčů Azure. Tento kurz předpokládá, že použití certifikátu spravovaného předních dveří.
![Povolení protokolu HTTPS pro vlastní doménu](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Kliknutím na **Aktualizovat** uložte výběr a klepněte na tlačítko **Uložit**.
4. Po několika minutách klikněte na **Aktualizovat** a potom znovu klikněte na vlastní doménu, abyste viděli průběh zřizování certifikátů. 

> [!WARNING]
> Povolení protokolu HTTPS pro vlastní doménu může trvat několik minut a také závisí na ověření `<name>.azurefd.net`vlastnictví domény, pokud CNAME není přímo mapována na hostitele front door . Přečtěte si další informace o [povolení protokolu HTTPS pro vlastní doménu](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurace pravidel směrování pro vlastní doménu

1. Klikněte na pravidlo přesměrování směrování vytvořené dříve.
2. Klikněte na rozevírací seznam pro hostitele front-endu a vyberte vlastní doménu, která použije tuto trasu i pro vaši doménu.
3. Klikněte na **Aktualizovat**.
4. Proveďte stejnou operaci pro jiné pravidlo směrování, které je také pro trasu předávání přidat vlastní doménu.
5. Kliknutím na **Uložit** odešlete změny.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
- Další informace o [přesměrování adres URL na předních dveřích](front-door-url-redirect.md).
