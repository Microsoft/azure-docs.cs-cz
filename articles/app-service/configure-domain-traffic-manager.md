---
title: Nakonfigurujte názvy DNS pomocí Traffic Manager
description: Přečtěte si, jak nakonfigurovat vlastní doménu pro Azure App Service aplikaci, která se integruje s Traffic Manager pro vyrovnávání zatížení.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 2910ea3f896ba3920126737965ca9c9dbabcfeb3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709100"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurace vlastního názvu domény v Azure App Service s integrací Traffic Manager

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Cloud Services najdete v tématu [Konfigurace vlastního názvu domény pro cloudovou službu Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Když použijete [Azure Traffic Manager](../traffic-manager/index.yml) k vyrovnávání zatížení provozu do [Azure App Service](overview.md), App Service aplikaci lze přistupovat pomocí **\<traffic-manager-endpoint> . trafficmanager.NET**. Pomocí aplikace App Service můžete přiřadit vlastní název domény, jako je například webová \. contoso.com, aby bylo možné poskytnout uživatelům lépe rozpoznatelný název domény.

V tomto článku se dozvíte, jak nakonfigurovat vlastní název domény pomocí aplikace App Service, která je integrovaná s [Traffic Manager](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Při konfiguraci názvu domény pomocí koncového bodu Traffic Manager jsou podporovány pouze záznamy [CNAME](https://en.wikipedia.org/wiki/CNAME_record) . Vzhledem k tomu, že se záznamy nepodporují, není podporované i mapování kořenových domén, jako je contoso.com.
> 

## <a name="prepare-the-app"></a>Příprava aplikace

Pokud chcete namapovat vlastní název DNS na aplikaci, která je integrovaná s Azure Traffic Manager, musí být [plán App Service](https://azure.microsoft.com/pricing/details/app-service/) webové aplikace na úrovni **Standard** nebo vyšší. V tomto kroku se ujistíte, že je aplikace App Service na podporované cenové úrovni.

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**.

Na stránce **App Services** vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/select-app.png)

V levém navigačním panelu na stránce aplikace vyberte **škálovat nahoru (App Service plán)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Zkontrolujte, jestli je aplikace na úrovni **Standard** nebo vyšší (kterákoli úroveň v **produkční** nebo **izolované** kategorii). Pokud ano, zavřete stránku **horizontálního navýšení kapacity** a přejděte k [části Vytvoření mapování CNAME](#create-the-cname-mapping).

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Pokud potřebujete navýšit horizontální navýšení kapacity vaší aplikace, vyberte některou z cenových úrovní v kategorii **Výroba** . Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na **Použít**.

## <a name="create-traffic-manager-endpoint"></a>Vytvořit Traffic Manager koncový bod

Podle kroků v části [Přidání nebo odstranění koncových bodů](../traffic-manager/traffic-manager-manage-endpoints.md)přidejte do svého profilu Traffic Manager svou aplikaci App Service jako koncový bod.

Když je vaše aplikace App Service v podporované cenové úrovni, zobrazí se v seznamu dostupných App Service cílů při přidávání koncového bodu. Pokud vaše aplikace není v seznamu uvedena, [Ověřte cenovou úroveň vaší aplikace](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Vytvoření mapování CNAME
> [!NOTE]
> Pokud chcete nakonfigurovat [doménu App Service, kterou jste zakoupili](manage-custom-dns-buy-domain.md), přeskočte tuto část a přejděte na [Povolit vlastní doménu](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

I když se konkrétní poskytovatelé domény liší, namapujete  se z [nekořenového názvu domény](#what-about-root-domains) (například **www.contoso.com**) Traffic Manager *na* název domény (**contoso.trafficmanager.NET**), který je integrovaný do vaší aplikace. 

> [!NOTE]
> Pokud se záznam už používá a vy k němu potřebujete na něj navazovat vazby, můžete vytvořit další záznam CNAME. Pokud třeba chcete do aplikace bez přerušení navazovat vazby **webové \. contoso.com** , vytvořte záznam CNAME z **awverify. www** do **contoso.trafficmanager.NET**. Pak můžete do aplikace přidat "www \. contoso.com", aniž byste museli měnit "www" záznam CNAME. Další informace najdete v tématu [migrace aktivního názvu DNS na Azure App Service](manage-custom-dns-migrate-domain.md).

Až dokončíte přidávání nebo úpravu záznamů DNS u svého poskytovatele domény, uložte změny.

### <a name="what-about-root-domains"></a>Co jsou kořenové domény?

Vzhledem k tomu, že Traffic Manager podporuje pouze vlastní mapování domén se záznamy CNAME a protože standardy DNS nepodporují záznamy CNAME pro mapování kořenových domén (například **contoso.com**), Traffic Manager nepodporuje mapování na kořenové domény. Pokud chcete tento problém obejít, použijte přesměrování adresy URL z na úrovni aplikace. V ASP.NET Core například můžete použít [přepis adresy URL](/aspnet/core/fundamentals/url-rewriting). Pak použijte Traffic Manager k vyrovnávání zatížení subdomény (**www.contoso.com**). Další možností je [vytvořit záznam aliasu pro název domény vrchol, který bude odkazovat na profil Traffic Manager Azure](../dns/tutorial-alias-tm.md). Příklad: contoso.com. Místo používání přesměrování služby můžete Azure DNS nakonfigurovat tak, aby odkazovaly na Traffic Manager profil přímo z vaší zóny. 

U scénářů s vysokou dostupností můžete instalaci DNS vyrovnávání zatížení implementovat bez Traffic Manager vytvořením několika *záznamů* , které ukazují z kořenové domény na IP adresu každé kopie aplikace. Pak [namapujte stejnou kořenovou doménu na všechny kopie aplikace](app-service-web-tutorial-custom-domain.md#map-an-a-record). Vzhledem k tomu, že stejný název domény nejde namapovat na dvě různé aplikace ve stejné oblasti, Tato instalace funguje jenom v případě, že se vaše aplikace kopírují v různých oblastech.

## <a name="enable-custom-domain"></a>Povolit vlastní doménu
Po rozšíření záznamů pro název domény pomocí prohlížeče ověřte, že se vlastní název domény přeloží na vaši aplikaci App Service.

> [!NOTE]
> Může trvat nějakou dobu, než se váš CNAME rozšíří přes systém DNS. Můžete použít službu, jako <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> je, k ověření, že je záznam CNAME k dispozici.
> 
> 

1. Po úspěšném vyřešení domény zpátky na stránku aplikace na webu [Azure Portal](https://portal.azure.com)
2. V levém navigačním panelu vyberte **vlastní domény**  >  **Přidat název hostitele**.
4. Zadejte vlastní název domény, který jste namapovali dříve, a vyberte **ověřit**.
5. Ujistěte se, že **typ záznamu názvu hostitele** je nastavený na **CNAME (webová \. example.com nebo libovolná subdoména)**.

6. Vzhledem k tomu, že aplikace App Service je teď integrovaná s Traffic Managerm koncovým bodem, měli byste vidět Traffic Manager název domény v části **Konfigurace CNAME**. Vyberte ji a klikněte na **Přidat vlastní doménu**.

    ![Přidání názvu DNS do aplikace](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS s využitím vazby SSL ve službě Azure App Service](configure-ssl-bindings.md)