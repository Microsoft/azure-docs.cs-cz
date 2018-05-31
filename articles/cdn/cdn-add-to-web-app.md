---
title: Kurz – Přidání Azure CDN do webové aplikace služby Azure App Service | Microsoft Docs
description: V tomto kurzu přidáte službu Azure Content Delivery Network (CDN) do webové aplikace služby Azure App Service, která umožňuje ukládání statických souborů do mezipaměti a jejich doručování ze serverů blízko zákazníkům po celém světě.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: de8b354cf0199d36d5e0b1410a9f79d4a9e3e05c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359777"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Kurz: Přidání Azure CDN do webové aplikace služby Azure App Service

V tomto kurzu se naučíte přidat službu [Azure Content Delivery Network (CDN)](cdn-overview.md) do [webové aplikace Azure App Service](../app-service/app-service-web-overview.md). Web Apps je služba pro hostování webových aplikací, rozhraní REST API a mobilních back-endů. 

Tady je domovská stránka ukázkového statického webu v HTML, se kterým budete pracovat:

![Domovská stránka ukázkové aplikace](media/cdn-add-to-web-app/sample-app-home-page.png)

Naučíte se:

> [!div class="checklist"]
> * Vytvořit koncový bod CDN.
> * Aktualizovat prostředky uložené v mezipaměti.
> * Spravovat verze uložené v mezipaměti pomocí řetězců dotazu.


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Nainstalovat Git](https://git-scm.com/).
- [Nainstalujte Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Vytvoření webové aplikace

Při vytváření webové aplikace, se kterou budete pracovat, postupujte podle [rychlého úvodu ke statickému HTML](../app-service/app-service-web-get-started-html.md) až do kroku **Přechod do aplikace**.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Otevřete prohlížeč a přejděte na web [Azure Portal](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Vytvoření koncového bodu a profilu CDN

Na levém navigačním panelu vyberte **App Services** a pak vyberte aplikaci, kterou jste vytvořili v [rychlém úvodu ke statickému HTML](../app-service/app-service-web-get-started-html.md).

![Výběr aplikace App Service na portálu](media/cdn-add-to-web-app/portal-select-app-services.png)

Na stránce **App Service** v části **Nastavení** vyberte **Sítě > Konfigurovat Azure CDN pro aplikaci**.

![Výběr CDN na portálu](media/cdn-add-to-web-app/portal-select-cdn.png)

### <a name="dynamic-site-acceleration-optimization"></a>Optimalizace akcelerace dynamického webu
Pokud chcete koncový bod CDN optimalizovat pro akceleraci dynamického webu (DSA), vytvořte koncový bod přímo na portálu CDN. Díky [optimalizaci DSA](cdn-dynamic-site-acceleration.md) se výkon webových stránek s dynamickým obsahem prokazatelně zlepší. Další informace o optimalizaci koncového bodu CDN pro akceleraci dynamického webu na portálu CDN najdete v tématu o [konfiguraci koncového bodu CDN kvůli rychlejšímu poskytování dynamických souborů](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). U profilů **Azure CDN od Verizonu** nemůžete změnit optimalizaci vytvořeného koncového bodu CDN.

Na stránce **Azure Content Delivery Network** zadejte pro **Nový koncový bod** nastavení tak, jak je uvedeno v tabulce.

![Vytvoření profilu a koncového bodu na portálu](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Nastavení | Navrhovaná hodnota | Popis |
| ------- | --------------- | ----------- |
| **Profil CDN** | myCDNProfile | Profil CDN je kolekce koncových bodů CDN se stejnou cenovou úrovní. |
| **Cenová úroveň** | Akamai Standard | [Cenová úroveň](cdn-features.md) určuje poskytovatele a dostupné funkce. Tento kurz používá *Akamai úrovně Standard*. |
| **Název koncového bodu CDN** | Libovolný název, který je jedinečný v doméně azureedge.net | Prostředky uložené v mezipaměti jsou přístupné v doméně *&lt;název_koncového_bodu&gt;*.azureedge.net.

Vyberte **Vytvořit** a vytvořte profil CDN.

Azure vytvoří profil a koncový bod. Nový koncový bod se zobrazí v seznamu **Koncové body** a až se zřídí, bude jeho stav **Spuštěno**.

![Nový koncový bod v seznamu](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testování koncového bodu CDN

 Vzhledem k tomu, že rozšíření registrace nějakou dobu trvá, koncový bod není okamžitě dostupný pro použití: 
   - Šíření profilů **Azure CDN Standard od Microsoftu** trvá většinou 10 minut. 
   - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut. 

Ukázková aplikace má soubor *index.html* a složky *css*, *img* a *js*, které obsahují další statické prostředky. Cesty k obsahu jsou v koncovém bodu CDN pro všechny tyto soubory stejné. Například obě následující adresy URL slouží k přístupu k souboru *bootstrap.css* ve složce *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Přejděte v prohlížeči na následující adresu URL:

```
http://<endpointname>.azureedge.net/index.html
```

![Domovská stránka ukázkové aplikace poskytnutá z CDN](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Zobrazí se stejná stránka, kterou jste spustili dříve ve webové aplikaci Azure. Služba Azure CDN načetla prostředky původní webové aplikace a poskytuje je z koncového bodu CDN.

Abyste zajistili uložení této stránky v mezipaměti v CDN, aktualizujte stránku. Někdy jsou k uložení požadovaného obsahu do mezipaměti v CDN potřeba dva požadavky na stejný prostředek.

Další informace o vytváření koncových bodů a profilů CDN najdete v tématu [Začínáme s Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Vyprázdnění CDN

CDN pravidelně aktualizuje prostředky z původní webové aplikace podle konfigurace hodnoty TTL (Time to Live). Výchozí hodnota TTL je sedm dní.

Čas od času může být nutné aktualizovat CDN před vypršením hodnoty TTL – například při nasazení aktualizovaného obsahu do webové aplikace. Aktualizaci aktivujte ručním vyprázdněním prostředků CDN. 

V této části kurzu nasadíte do webové aplikace změnu a vyprázdníte CDN, abyste v CDN aktivovali aktualizaci mezipaměti.

### <a name="deploy-a-change-to-the-web-app"></a>Nasazení změny do webové aplikace

Otevřete soubor *index.html* a do nadpisu H1 přidejte *– V2*, jak je znázorněno v následujícím příkladu: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Potvrďte změnu a nasaďte ji do webové aplikace.

```bash
git commit -am "version 2"
git push azure master
```

Po dokončení nasazení přejděte na adresu URL webové aplikace, abyste viděli změnu.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 v nadpisu ve webové aplikaci](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Pokud přejdete na adresu URL koncového bodu CDN pro domovskou stránku, změnu neuvidíte, protože platnost verze uložené v mezipaměti v CDN ještě nevypršela. 

```
http://<endpointname>.azureedge.net/index.html
```

![Žádné V2 v nadpisu v CDN](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Vyprázdnění CDN na portálu

Pokud chcete v CDN aktivovat aktualizaci verze uložené v mezipaměti, vyprázdněte CDN.

Na levém navigačním panelu portálu vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků vytvořenou pro vaši webovou aplikaci (myResourceGroup).

![Výběr skupiny prostředků](media/cdn-add-to-web-app/portal-select-group.png)

V seznamu prostředků vyberte koncový bod CDN.

![Výběr koncového bodu](media/cdn-add-to-web-app/portal-select-endpoint.png)

V horní části stránky **Koncový bod** vyberte **Vyprázdnit**.

![Výběr možnosti Vyprázdnit](media/cdn-add-to-web-app/portal-select-purge.png)

Zadejte cesty k obsahu, který chcete vyprázdnit. Můžete předat úplnou cestu k souboru pro vyprázdnění jednotlivých souborů, nebo část cesty pro vyprázdnění a aktualizaci veškerého obsahu ze složky. Vzhledem k tomu, že jste změnili soubor *index.html*, zkontrolujte, že se nachází v jedné z cest.

V dolní části stránky klikněte na **Vyprázdnit**.

![Stránka Vyprázdnit](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Ověření aktualizace CDN

Počkejte na dokončení zpracování žádosti o vyprázdnění, což obvykle trvá pár minut. Pokud chcete zobrazit aktuální stav, vyberte ikonu zvonku v horní části stránky. 

![Oznámení vyprázdnění](media/cdn-add-to-web-app/portal-purge-notification.png)

Když přejdete na adresu URL koncového bodu CDN pro soubor *index.html*, uvidíte řetězec *V2*, který jste přidali do nadpisu na domovské stránce. Ten označuje, že se mezipaměť CDN aktualizovala.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 v nadpisu v CDN](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Další informace najdete v tématu [Vyprázdnění koncového bodu Azure CDN](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Správa verzí obsahu pomocí řetězců dotazu

Azure CDN nabízí následující možnosti chování při ukládání do mezipaměti:

* Ignorovat řetězce dotazu
* Nepoužívat ukládání do mezipaměti pro řetězce dotazu
* Ukládat do mezipaměti každou jedinečnou adresu URL 

Výchozí je první z těchto možností, to znamená, že existuje pouze jedna verze prostředku uložená do mezipaměti bez ohledu na řetězec dotazu v adrese URL. 

V této části kurzu změníte chování při ukládání do mezipaměti tak, že se do mezipaměti bude ukládat každá jedinečná adresa URL.

### <a name="change-the-cache-behavior"></a>Změna chování mezipaměti

Na webu Azure Portal na stránce **Koncový bod CDN** vyberte **Mezipaměť**.

Z rozevíracího seznamu **Chování při ukládání řetězců dotazu do mezipaměti** vyberte **Ukládat do mezipaměti každou jedinečnou adresu URL**.

Vyberte **Uložit**.

![Výběr chování při ukládání řetězce dotazu do mezipaměti](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Ověření samostatného ukládání jedinečných adres URL do mezipaměti

V prohlížeči přejděte na domovskou stránku na koncovém bodu CDN a přidejte řetězec dotazu: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN vrátí obsah webové aplikace, který obsahuje text *V2* v nadpisu. 

Abyste zajistili uložení této stránky v mezipaměti v CDN, aktualizujte stránku. 

Otevřete soubor *index.html*, změňte *V2* na *V3* a pak změnu nasaďte. 

```bash
git commit -am "version 3"
git push azure master
```

V prohlížeči přejděte na adresu URL koncového bodu CDN s novým řetězcem dotazu, například `q=2`. Azure CDN získá aktuální soubor *index.html* a zobrazí *V3*. Pokud ale přejdete na koncový bod CDN s řetězcem dotazu `q=1`, uvidíte *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 v nadpisu v CDN, řetězec dotazu 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 v nadpisu v CDN, řetězec dotazu 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Tento výstup ukazuje, že se každý řetězec dotazu zpracovává jinak:

* Parametr q=1 se použil dříve, proto se vrátí obsah uložený v mezipaměti (V2).
* Parametr q=2 je nový, takže se načte a vrátí nejnovější obsah webové aplikace (V3).

Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](../cdn/cdn-query-string.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Vytvořit koncový bod CDN.
> * Aktualizovat prostředky uložené v mezipaměti.
> * Spravovat verze uložené v mezipaměti pomocí řetězců dotazu.

V následujících článcích se dozvíte, jak optimalizovat výkon sítě CDN:

> [!div class="nextstepaction"]
> [Kurz: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md)


