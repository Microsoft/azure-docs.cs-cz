---
title: 'Rychlý Start: vytvoření profilu služby Azure front-end Standard/Premium – Azure Portal'
description: V tomto rychlém startu se dozvíte, jak používat službu Azure front-Standard/Premium pro vaše vysoce dostupné a vysoce výkonné globální webové aplikace pomocí Azure Portal.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 18956948a32a79c1435bf4fc1554b09480c9010c
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100798"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Rychlý Start: vytvoření profilu služby Azure front-end Standard/Premium – Azure Portal

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Prohlédněte si [dokumentaci ke službě Azure front dveří](../front-door-overview.md).

V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit profil front-Premium pro Azure. Profil front-Premium pro Azure můžete vytvořit prostřednictvím *rychlého vytvoření* se základní konfigurací nebo prostřednictvím *vlastního vytváření* s pokročilejšími konfiguracemi. Pomocí *vlastního vytvoření* nasadíte dvě Web Apps. V dalším kroku vytvoříte profil služby Azure front-Premium Standard/Premium pomocí těchto dvou Web Apps jako svého původu. Pak ověříte připojení k vašemu Web Apps pomocí hostitelského hostitele Azure front-endu Standard/Premium.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Vytvořit profil předních dveří – rychlé vytvoření

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Z domovské stránky nebo nabídky Azure vyberte **+ vytvořit prostředek**. Vyhledat *Standard/Premium pro frontu (Preview)* Potom vyberte **Vytvořit**.

1. Na stránce **Porovnat nabídky** vyberte **rychle vytvořit**. Pak vyberte **pokračovat a vytvořte si přední dveře**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Snímek obrazovky s porovnáním nabídek":::

1. Na stránce **vytvořit profil front dveří** zadejte nebo vyberte následující nastavení.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Snímek obrazovky s úvodní stránkou pro rychlé vytvoření":::    

    | Nastavení | Hodnota |
    | --- | --- |
    | **Předplatné**  | Vyberte své předplatné. |
    | **Skupina prostředků**  | Vyberte **vytvořit nový** a do textového pole zadejte *Contoso-AppService* .|
    | **Název** | Zadejte název svého profilu. V tomto příkladu se používá **Contoso-AFD-quickcreate**. |
    | **Úroveň** | Vyberte položku SKU úrovně Standard nebo Premium. Standardní SKU je optimalizováno pro doručování obsahu. SKU úrovně Premium se staví na standardní SKU a zaměřuje se na zabezpečení. Viz [porovnání vrstev](tier-comparison.md).  |
    | **Název koncového bodu** | Zadejte globálně jedinečný název koncového bodu. |
    | **Typ zdroje** | Vyberte typ prostředku pro zdroj. V tomto příkladu vybereme službu App Service jako zdroj, který má povolený privátní odkaz. |
    | **Název počátečního hostitele** | Zadejte název hostitele pro váš původ. |
    | **Povolit privátní propojení** | Pokud chcete mít privátní připojení mezi předními dvířky Azure a vaším zdrojem. Další podrobnosti najdete v tématu doprovodné materiály k [privátním odkazům](concept-private-link.md) a [Povolení privátního odkazu](how-to-enable-private-link.md).
    | **Ukládání do mezipaměti** | Zaškrtněte políčko, pokud chcete obsah do mezipaměti přiblížit uživatelům globálně pomocí hraničních zařízení služby Azure front-end a sítě Microsoftu. |
    | **Zásady WAF** | Pokud chcete povolit tuto funkci, vyberte **vytvořit novou** nebo vybrat existující zásadu WAF z rozevíracího seznamu. |

    > [!NOTE]
    > Při vytváření profilu služby front-Premium pro Azure je nutné vybrat počátek ze stejného předplatného, ve kterém je vytvořena přední dvířka.

1. Vyberte **zkontrolovat + vytvořit** a načtěte si profil front-dveří v provozu.

   > [!NOTE]
   > Může trvat několik minut, než se konfigurace rozšíří na všechny body POP hraničních zařízení.

1. Pak kliknutím na **vytvořit** zařiďte nasazený a běžící profil front-dveří.

1. Pokud jste povolili privátní propojení, v tomto příkladu přejdete k původnímu umístění (App Service). Vyberte **sítě**  >  **konfigurace privátního propojení**. Pak vyberte nevyřízenou žádost z předních dveří Azure a klikněte na schválit. Po několika sekundách budete mít k dispozici zabezpečený přístup přes přední dveře Azure.

## <a name="create-front-door-profile---custom-create"></a>Vytvořit profil předních dveří – vlastní vytvoření

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Vytvoření webové aplikace se dvěma instancemi jako původní

Pokud už máte nakonfigurované zdrojové nebo zdrojové skupiny, přejděte k části Vytvoření verze front-dveří Standard/Premium (Preview) pro vaši aplikaci.

V tomto příkladu vytvoříme webovou aplikaci se dvěma instancemi, které běží v různých oblastech Azure. Instance webové aplikace běží v režimu *aktivní/aktivní* , takže jedna z nich může přijímat přenosy. Tato konfigurace se liší od *aktivní/úsporné* konfigurace, kdy jedna funguje jako převzetí služeb při selhání.

Pokud ještě nemáte webovou aplikaci, použijte následující postup a nastavte ukázkovou webovou aplikaci.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **WebApp**.

1. Na kartě **základy** stránky **vytvořit webovou aplikaci** zadejte nebo vyberte následující informace.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | **Předplatné**               | Vyberte své předplatné. |
    | **Skupina prostředků**       | Vyberte **vytvořit nový** a do textového pole zadejte *FrontDoorQS_rg1* .|
    | **Název**                   | Zadejte jedinečný **název** vaší webové aplikace. V tomto příkladu se používá *WebAppContoso-001*. |
    | **Publikovat** | Vyberte **Kód**. |
    | **Zásobník modulu runtime**         | Vyberte **.NET Core 2,1 (LTS)**. |
    | **Operační systém**          | Vyberte možnost **Windows**. |
    | **Oblast**           | Vyberte **střed USA**. |
    | **Plán Windows** | Vyberte **vytvořit nový** a do textového pole zadejte *myAppServicePlanCentralUS* . |
    | **SKU a velikost** | Vyberte **Standard S1 100 celkem ACU, 1,75-GB paměti**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Rychlé vytvoření skladové položky Premium pro front-in Azure Portal":::

1. Vyberte **zkontrolovat + vytvořit**, zkontrolujte souhrn a pak vyberte **vytvořit**. Nasazení do nástroje může trvat několik minut.

Po dokončení nasazení vytvořte druhou webovou aplikaci. Použijte stejné nastavení jako výše, s výjimkou následujících nastavení:

| Nastavení          | Hodnota     |
| ---              | ---  |
| **Skupina prostředků**   | Vyberte **vytvořit novou** a zadejte *FrontDoorQS_rg2*. |
| **Název**             | Zadejte jedinečný název vaší webové aplikace, v tomto příkladu *WebAppContoso-002*.  |
| **Oblast**           | Jiné oblasti, v tomto příkladu *střed USA – jih* |
| **Plán App Service**  >  **Plán Windows**         | Vyberte **nové** a zadejte *myAppServicePlanSouthCentralUS* a pak vyberte **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Vytvoření front-dveří Standard/Premium (Preview) pro vaši aplikaci

Pokud chcete směrovat uživatelský provoz na základě nejnižší latence mezi dvěma servery Web Apps, nakonfigurujte přední dveře Azure na úrovni Standard/Premium (Preview). Také Zabezpečte přední dveře pomocí brány firewall webových aplikací. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
 
1. Z domovské stránky nebo nabídky Azure vyberte **+ vytvořit prostředek**. Vyhledat *Standard/Premium pro frontu (Preview)* Potom vyberte **Vytvořit**.

1. Na stránce **Porovnat nabídky** vyberte **vlastní vytvořit**. Pak vyberte **pokračovat a vytvořte si přední dveře**.

1. Na kartě **základy**   Zadejte nebo vyberte následující informace a pak vyberte **Další: tajný klíč**. 

    | Nastavení | Hodnota |
    | --- | --- |
    | **Předplatné** | Vyberte své předplatné. |
    | **Skupina prostředků** | Vyberte **vytvořit nový** a do textového pole zadejte *FrontDoorQS_rg0* . |
    | **Umístění skupiny prostředků** | Vyberte **východní USA** |
    | **Název profilu** | Zadejte jedinečný název v tomto předplatném **WebApp-contoso-AFD** |
    | **Úroveň** | Vyberte **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Vytvořit profil front-dveří":::

1. *Volitelné*: **tajné klíče** Pokud plánujete použití spravovaných certifikátů, je tento krok nepovinný. Pokud máte v Azure existující Key Vault, kterou plánujete použít k převedení vlastního certifikátu na vlastní doménu, vyberte **Přidat certifikát**. Po vytvoření můžete také přidat certifikát do prostředí pro správu.

    > [!NOTE]
    > Musíte mít správné oprávnění k přidání certifikátu z Azure Key Vault jako uživatel. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Snímek obrazovky s přidáním tajného klíče do vlastního vytvoření":::

1. Na kartě **koncový bod** vyberte **přidat koncový bod** a zadejte globálně jedinečný název koncového bodu. Po dokončení prostředí pro vytváření můžete vytvořit více koncových bodů v profilu služby Azure front-end Standard/Premium. V tomto příkladu se používá *Contoso-Endu*. Ponechte časový limit odpovědi na původ (v sekundách) a stav jako výchozí. Vyberte **Přidat** a přidejte koncový bod.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Snímek obrazovky s přidáním koncového bodu":::

1. Dále přidejte skupinu zdrojů, která obsahuje vaše dvě webové aplikace. Výběrem **+ Přidat**   otevřete stránku **Přidat původní skupinu** . Jako název zadejte *myOrignGroup* a pak vyberte **+ Přidat počátek**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Snímek obrazovky s přidáním zdrojové skupiny":::

1. Na stránce **Přidat zdroj**   Zadejte nebo vyberte následující informace. Pak vyberte **Přidat**.

    | Nastavení | Hodnota |
    | --- | --- |
    | **Název** | Zadejte **WebApp1** |
    | **Typ zdroje** | Vybrat **služby App Services** |
    | **Název hostitele** | Vyberte `WebAppContoso-001.azurewebsites.net` |
    | **Hlavička počátečního hostitele** | Vyberte `WebAppContoso-001.azurewebsites.net` |
    | **Další pole** | Ponechte všechna ostatní pole jako výchozí. |

    > [!NOTE]
    > Při vytváření profilu služby front-Premium pro Azure je nutné vybrat původ ze stejného předplatného, ve kterém je vytvořená přední dvířka Azure Standard/Premium.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Snímek obrazovky s přidáním dalších zdrojů":::

1. Zopakováním kroku 8 přidejte druhý původ webapp002. `webappcontoso-002.azurewebsite.net`Jako **název hostitele zadejte počátek** a **záhlaví původ hostitele**.

1. Na stránce **přidat zdrojovou skupinu** se zobrazí dva zdroje, ve výchozím nastavení ponechte všechna ostatní pole.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Snímek obrazovky s přidáním stránky zdrojové skupiny":::

1. Pak přidejte trasu pro mapování koncového bodu front-end do skupiny původní. Tato trasa přesměruje požadavky od koncového bodu do myOriginGroup. Pro konfiguraci směrování vyberte **+ Přidat** na trase.  

1. Na stránce **Přidat trasu** zadejte nebo vyberte následující informace. Pak vyberte **Přidat**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Přidat trasu bez ukládání do mezipaměti":::

    | Nastavení | Hodnota |
    | --- | --- |
    | **Název** | Zadejte **MyRoute** |    
    | **Doména** | Vyberte `contoso-frontend.z01.azurefd.net` | 
    | **Název hostitele** | Vyberte `WebAppContoso-001.azurewebsites.net` |
    | **Vzorce, které se mají spárovat** | Ponechte jako výchozí. |
    | **Přijaté protokoly** | Ponechte jako výchozí. | 
    | **Přesměrování** | Ponechte výchozí hodnotu pro **přesměrování všech přenosů na používání protokolu HTTPS**. | 
    | **Skupina původu** | Vyberte **MyOriginGroup**. | 
    | **Cesta ke zdroji** | Ponechte jako výchozí. | 
    | **Protokol předávání** | Vyberte **odpovídající příchozí požadavek**. | 
    | **Ukládání do mezipaměti** | V tomto rychlém startu nechejte nezaškrtnuto. Pokud chcete mít obsah uložený v mezipaměti pro okraje, zaškrtněte políčko **Povolit ukládání do mezipaměti**. |
    | **Pravidla** | Ponechte jako výchozí. Po vytvoření profilu front-dveří můžete vytvořit vlastní pravidla a použít je na trasy. |  
       
    >[!WARNING]
    > **Zajistěte** , aby existovala trasa pro každý koncový bod. Absence trasy může způsobit selhání koncového bodu.

1. V dalším kroku vyberte **+ Přidat** na zabezpečení a přidejte zásady WAF. Vyberte **Přidat** nové a udělte své zásadě jedinečný název. Zaškrtněte políčko pro **Přidání ochrany bot**. Vyberte koncový bod v **doménách** a pak vyberte **Přidat**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="Přidat zásady WAF":::

1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**. Bude trvat několik minut, než se konfigurace rozšíří na všechny body POP hraničních zařízení. Teď máte svůj první profil front-dveří a koncový bod.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Zkontrolovat vlastní vytvoření":::

## <a name="verify-azure-front-door"></a>Ověřit přední dveře Azure

Když vytvoříte profil služby Azure front-end Standard/Premium, může trvat několik minut, než se konfigurace nasadí globálně. Po dokončení budete mít přístup k hostiteli front-endu, který jste vytvořili. V prohlížeči, přejít na `contoso-frontend.z01.azurefd.net` . Vaše žádost bude automaticky směrována na nejbližší server ze zadaných serverů ve skupině původu.

Pokud jste v tomto rychlém startu vytvořili tyto aplikace, zobrazí se stránka s informacemi.

Pokud chcete otestovat rychlé globální převzetí služeb při selhání, použijte následující postup:

1. Otevřete prohlížeč, jak je popsáno výše, a přejít na adresu front-endu: `contoso-frontend.azurefd.net` .

1. V Azure Portal vyhledejte a vyberte *App Services*. Přejděte dolů a najděte jednu z vašich webových aplikací, **WebAppContoso-001** v tomto příkladu.

1. Vyberte webovou aplikaci a vyberte možnost **zastavit** a **Ano** pro ověření.

1. Aktualizujte si stránku v prohlížeči. Měla by se zobrazit stejná informační stránka.

   >[!TIP]
   >Pro tyto akce existuje trochu zpoždění. Možná budete muset aktualizovat znovu.

1. Najděte jinou webovou aplikaci a zastavte ji také.

1. Aktualizujte si stránku v prohlížeči. Tentokrát by se měla zobrazit chybová zpráva.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Obě instance webové aplikace se zastavily.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, můžete odebrat všechny položky, které jste vytvořili. Odstraněním skupiny prostředků se odstraní také její obsah. Pokud nemáte v úmyslu používat tato přední dvířka, měli byste odebrat prostředky, abyste se vyhnuli zbytečným poplatkům.

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků** nebo v nabídce Azure Portal vyberte **skupiny prostředků** .

1. Vyfiltrujte nebo přejděte dolů a najděte skupinu prostředků, například **FrontDoorQS_rg0**.

1. Vyberte skupinu prostředků a pak vyberte **Odstranit skupinu prostředků**.

   >[!WARNING]
   >Tato akce je irreversable.

1. Zadejte název skupiny prostředků, který chcete ověřit, a pak vyberte **Odstranit**.

Opakujte postup pro ostatní dvě skupiny.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a Naučte se, jak přidat vlastní doménu do front-dveří.
> [!div class="nextstepaction"]
> [Přidání vlastní domény](how-to-add-custom-domain.md)
