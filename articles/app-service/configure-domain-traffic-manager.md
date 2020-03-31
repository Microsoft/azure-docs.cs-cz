---
title: Konfigurace názvů DNS pomocí Traffic Manageru
description: Zjistěte, jak nakonfigurovat vlastní doménu pro aplikaci Služby Azure App Service, která se integruje se Správcem provozu pro vyrovnávání zatížení.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944132"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurace vlastního názvu domény ve službě Azure App Service s integrací Traffic Manageru

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> V případě cloudových služeb [najdete v tématu Konfigurace vlastního názvu domény pro cloudovou službu Azure](../cloud-services/cloud-services-custom-domain-name.md).

Když pomocí [Azure Traffic Manageru](/azure/traffic-manager/) vyrovnáváte provoz ve [službě Azure App Service](overview.md), aplikace App Service se k aplikaci App Service dá přístup pomocí ** \<>.trafficmanager.net**. Chcete-li uživatelům poskytnout lépe rozpoznatelný název domény, můžete k aplikaci App Service přiřadit vlastní název domény, například www\.contoso.com.

V tomto článku se ukazuje, jak nakonfigurovat vlastní název domény pomocí aplikace App Service, která je integrovaná se [Traffic Managerem](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Při konfiguraci názvu domény pomocí koncového bodu Traffic Manageru jsou podporovány pouze záznamy [CNAME.](https://en.wikipedia.org/wiki/CNAME_record) Vzhledem k tomu, že záznamy A nejsou podporovány, mapování kořenové domény, například contoso.com, také není podporováno.
> 

## <a name="prepare-the-app"></a>Příprava aplikace

Chcete-li namapovat vlastní název DNS na aplikaci integrovanou s Azure Traffic Manager, musí být [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) webové aplikace na úrovni **Standard** nebo vyšší. V tomto kroku se ujistíte, že je aplikace App Service na podporované cenové úrovni.

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**.

Na stránce **App Services** vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/select-app.png)

V levém navigačním panelu na stránce aplikace vyberte **Vertikálně navýšit kapacitu (plán služby App Service).**

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Zkontrolujte, zda je aplikace na úrovni **Standard** nebo vyšší (libovolná úroveň v kategorii **Produkční** nebo **Izolované).** Pokud ano, zavřete stránku **Vertika nahoru** a přeskočte na [Vytvoření mapování CNAME](#create-the-cname-mapping).

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Pokud potřebujete rozšířit kapacitu aplikace, vyberte některou z cenových úrovní v kategorii **Produkční.** Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na **Použít**.

## <a name="create-traffic-manager-endpoint"></a>Vytvořit koncový bod Traffic Manageru

Postupujte podle kroků v [aplikaci Přidat nebo odstranit koncové body](../traffic-manager/traffic-manager-endpoints.md)přidejte aplikaci App Service jako koncový bod do profilu Traffic Manageru.

Jakmile je vaše aplikace App Service v podporované cenové úrovni, zobrazí se v seznamu dostupných cílů služby App Service, když přidáte koncový bod. Pokud vaše aplikace není v seznamu, [ověřte cenovou úroveň aplikace](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Vytvoření mapování CNAME
> [!NOTE]
> Chcete-li nakonfigurovat [doménu služby App Service, kterou jste zakoupili](manage-custom-dns-buy-domain.md), přeskočte tuto část a přejděte na [Povolit vlastní doménu](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Zatímco specifika jednotlivých poskytovatelů domény se liší, mapujete *od* vlastního názvu domény (například **contoso.com)** *na* název domény Traffic Manager (**contoso.trafficmanager.net),** který je integrovaný s vaší aplikací.

> [!NOTE]
> Pokud je záznam již používán a potřebujete s ním preventivně svázat aplikace, můžete vytvořit další záznam CNAME. Chcete-li například preventivně svázat **contoso.com www\.** s aplikací, vytvořte záznam CNAME z **awverify.www** na **contoso.trafficmanager.net**. Pak můžete přidat\."www contoso.com" do aplikace bez nutnosti měnit "www" CNAME záznam. Další informace najdete [v tématu Migrace aktivního názvu DNS do služby Azure App Service](manage-custom-dns-migrate-domain.md).

Po dokončení přidávání nebo úpravy záznamů DNS u poskytovatele domény uložte změny.

## <a name="enable-custom-domain"></a>Povolení vlastní domény
Po rozšíření záznamů o názvu domény ověřte pomocí prohlížeče, zda se název vaší vlastní domény překládá do aplikace App Service.

> [!NOTE]
> Může chvíli trvat, než se váš CNAME rozšíří prostřednictvím systému DNS. Můžete použít službu, <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> například k ověření, že CNAME je k dispozici.
> 
> 

1. Jakmile bude řešení domény úspěšné, vraťte se na stránku aplikace na [Webu Azure Portal](https://portal.azure.com)
2. V levé navigaci vyberte **Vlastní domény** > **Přidat název hostitele**.
4. Zadejte vlastní název domény, který jste namapovali dříve, a vyberte **Ověřit**.
5. Ujistěte se, že **typ záznamu Hostname** je nastaven na **CNAME (www\.example.com nebo subdomény)**.

6. Vzhledem k tomu, že aplikace App Service je teď integrovaná s koncovým bodem Traffic Manageru, měli byste vidět název domény Traffic Manager v **konfiguraci CNAME**. Vyberte ji a klepněte na **přidat vlastní doménu**.

    ![Přidání názvu DNS do aplikace](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS s využitím vazby SSL ve službě Azure App Service](configure-ssl-bindings.md)
