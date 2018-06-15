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
ms.openlocfilehash: d782f5bf30160d85d7d9ef7a00190551f9a9843a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161240"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Kurz: Přidání Azure CDN do webové aplikace služby Azure App Service

Tento kurz ukazuje, jak přidat Azure CDN do [webové aplikace ve službě Azure App Service](../app-service/app-service-web-overview.md). [Azure Content Delivery Network (CDN)](cdn-overview.md) ukládá statický webový obsah do mezipaměti na strategicky umístěných místech, a tak poskytuje maximální propustnost pro doručování obsahu uživatelům. Azure CDN také snižuje zatížení serveru na webovou aplikaci. 

Tady je domovská stránka ukázkového statického webu v HTML, se kterým budete pracovat:

![Domovská stránka ukázkové aplikace](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

Naučíte se:

> [!div class="checklist"]
> * Vytvořit koncový bod CDN.
> * Aktualizovat prostředky uložené v mezipaměti.
> * Spravovat verze uložené v mezipaměti pomocí řetězců dotazu.
> * Použít vlastní doménu pro koncový bod CDN.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Nainstalovat Git](https://git-scm.com/).
- [Nainstalujte Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Vytvoření webové aplikace

Při vytváření webové aplikace, se kterou budete pracovat, postupujte podle [rychlého úvodu ke statickému HTML](../app-service/app-service-web-get-started-html.md) až do kroku **Přechod do aplikace**.

### <a name="have-a-custom-domain-ready"></a>Připravení vlastní domény

K dokončení kroku týkajícího se vlastní domény v tomto kurzu potřebujete mít vlastní doménu a přístup k registru DNS vašeho poskytovatele domény. Abyste například mohli přidat záznamy DNS pro contoso.com a www.contoso.com, musíte mít přístup ke konfiguraci nastavení DNS pro kořenovou doménu contoso.com.

Pokud ještě nemáte název domény, zvažte nákup domény pomocí webu Azure Portal podle postupu v [kurzu k doménám App Service](../app-service/custom-dns-web-site-buydomains-web-app.md). 

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Otevřete prohlížeč a přejděte na web [Azure Portal](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Vytvoření koncového bodu a profilu CDN

Na levém navigačním panelu vyberte **App Services** a pak vyberte aplikaci, kterou jste vytvořili v [rychlém úvodu ke statickému HTML](../app-service/app-service-web-get-started-html.md).

![Výběr aplikace App Service na portálu](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Na stránce **App Service** v části **Nastavení** vyberte **Sítě > Konfigurovat Azure CDN pro aplikaci**.

![Výběr CDN na portálu](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Na stránce **Azure Content Delivery Network** zadejte pro **Nový koncový bod** nastavení tak, jak je uvedeno v tabulce.

![Vytvoření profilu a koncového bodu na portálu](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Nastavení | Navrhovaná hodnota | Popis |
| ------- | --------------- | ----------- |
| **Profil CDN** | myCDNProfile | Vyberte **Vytvořit nový** a vytvořte profil CDN. Profil CDN je kolekce koncových bodů CDN se stejnou cenovou úrovní. |
| **Cenová úroveň** | Akamai Standard | [Cenová úroveň](cdn-features.md) určuje poskytovatele a dostupné funkce. Tento kurz používá *Akamai úrovně Standard*. |
| **Název koncového bodu CDN** | Libovolný název, který je jedinečný v doméně azureedge.net | K prostředkům uloženým v mezipaměti přistupujete na doméně *\<název_koncového_bodu>.azureedge.net*.

Vyberte **Vytvořit**.

Azure vytvoří profil a koncový bod. Nový koncový bod se zobrazí v seznamu **Koncové body** a až se zřídí, bude jeho stav **Spuštěno**.

![Nový koncový bod v seznamu](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testování koncového bodu CDN

Pokud jste vybrali cenovou úroveň Verizon, rozšíření koncového bodu obvykle trvá přibližně 90 minut. U Akamai trvá rozšíření několik minut.

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

![Domovská stránka ukázkové aplikace poskytnutá z CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

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

![V2 v nadpisu ve webové aplikaci](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Pokud přejdete na adresu URL koncového bodu CDN pro domovskou stránku, změnu neuvidíte, protože platnost verze uložené v mezipaměti v CDN ještě nevypršela. 

```
http://<endpointname>.azureedge.net/index.html
```

![Žádné V2 v nadpisu v CDN](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Vyprázdnění CDN na portálu

Pokud chcete v CDN aktivovat aktualizaci verze uložené v mezipaměti, vyprázdněte CDN.

Na levém navigačním panelu portálu vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků vytvořenou pro vaši webovou aplikaci (myResourceGroup).

![Výběr skupiny prostředků](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

V seznamu prostředků vyberte koncový bod CDN.

![Výběr koncového bodu](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

V horní části stránky **Koncový bod** vyberte **Vyprázdnit**.

![Výběr možnosti Vyprázdnit](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Zadejte cesty k obsahu, který chcete vyprázdnit. Můžete předat úplnou cestu k souboru pro vyprázdnění jednotlivých souborů, nebo část cesty pro vyprázdnění a aktualizaci veškerého obsahu ze složky. Vzhledem k tomu, že jste změnili soubor *index.html*, zkontrolujte, že se nachází v jedné z cest.

V dolní části stránky klikněte na **Vyprázdnit**.

![Stránka Vyprázdnit](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Ověření aktualizace CDN

Počkejte na dokončení zpracování žádosti o vyprázdnění, což obvykle trvá pár minut. Pokud chcete zobrazit aktuální stav, vyberte ikonu zvonku v horní části stránky. 

![Oznámení vyprázdnění](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Když přejdete na adresu URL koncového bodu CDN pro soubor *index.html*, uvidíte řetězec *V2*, který jste přidali do nadpisu na domovské stránce. Ten označuje, že se mezipaměť CDN aktualizovala.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 v nadpisu v CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

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

![Výběr chování při ukládání řetězce dotazu do mezipaměti](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

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

![V3 v nadpisu v CDN, řetězec dotazu 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 v nadpisu v CDN, řetězec dotazu 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Tento výstup ukazuje, že se každý řetězec dotazu zpracovává jinak:

* Parametr q=1 se použil dříve, proto se vrátí obsah uložený v mezipaměti (V2).
* Parametr q=2 je nový, takže se načte a vrátí nejnovější obsah webové aplikace (V3).

Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Mapování vlastní domény na koncový bod CDN

Na koncový bod CDN namapujete vlastní doménu vytvořením záznamu CNAME. Záznam CNAME je funkce DNS, která mapuje zdrojovou doménu na cílovou doménu. Doménu cdn.contoso.com nebo static.contoso.com můžete například namapovat na contoso.azureedge.net.

Pokud nemáte vlastní doménu, zvažte nákup domény pomocí webu Azure Portal podle postupu v [kurzu k doménám App Service](../app-service/custom-dns-web-site-buydomains-web-app.md). 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Zjištění názvu hostitele pro použití v záznamu CNAME

Na portálu Azure Portal se na stránce **Koncový bod** ujistěte, že je v levém navigačním panelu vybrán **Přehled**, a pak nahoře na stránce vyberte tlačítko **+ Vlastní doména**.

![Výběr možnosti Přidat vlastní doménu](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Na stránce **Přidat vlastní doménu** se zobrazí název hostitele koncového bodu, který použijete k vytvoření záznamu CNAME. Název hostitele je odvozený z adresy URL koncového bodu CDN: **&lt;název_koncového_bodu>.azureedge.net**. 

![Stránka Přidat doménu](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Konfigurace záznamu CNAME u registrátora domény

Přejděte na web registrátora vaší domény a vyhledejte část pro vytváření záznamů DNS. Tato část se může nazývat například **Název domény**, **DNS** nebo **Správa názvových serverů**.

Najděte část pro správu záznamů CNAME. Možná bude nutné přejít na stránku rozšířeného nastavení a hledat slova jako CNAME, Alias nebo Subdomény.

Vytvořte záznam CNAME mapující zvolenou subdoménu (například **static** nebo **cdn**) na **název hostitele koncového bodu**, který jste na portálu zobrazili dříve. 

### <a name="enter-the-custom-domain-in-azure"></a>Zadání vlastní domény v Azure

Vraťte se na stránku **Přidat vlastní doménu** a do dialogového okna zadejte vlastní doménu včetně poddomény. Zadejte například *cdn.contoso.com*.   
   
Azure ověří, že pro zadaný název domény existuje záznam CNAME. Pokud je záznam CNAME správný, vaše vlastní doména se ověří.

Rozšíření záznamu CNAME na názvové servery na internetu může nějakou dobu trvat. Pokud se vaše doména neověří okamžitě, počkejte několik minut a zkuste to znovu.

### <a name="test-the-custom-domain"></a>Testování vlastní domény

V prohlížeči přejděte na soubor *index.html* s použitím vlastní domény (například cdn.contoso.com/index.html) a ověřte, že výsledek je stejný, jako když přejdete přímo na &lt;název_koncového_bodu&gt;azureedge.net/index.html.

![Domovská stránka ukázkové aplikace s použitím adresy URL vlastní domény](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Další informace najdete v tématu [Mapování obsahu Azure CDN na vlastní doménu](../cdn/cdn-map-content-to-custom-domain.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

Naučili jste se:

> [!div class="checklist"]
> * Vytvořit koncový bod CDN.
> * Aktualizovat prostředky uložené v mezipaměti.
> * Spravovat verze uložené v mezipaměti pomocí řetězců dotazu.
> * Použít vlastní doménu pro koncový bod CDN.

V následujících článcích se dozvíte, jak optimalizovat výkon sítě CDN:

> [!div class="nextstepaction"]
> [Kurz: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md)


