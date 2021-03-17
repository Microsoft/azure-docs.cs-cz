---
title: 'Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure Portal'
description: Použití Azure Event Grid k přihlášení k odběru mezipaměti Azure pro události Redis, odesílání událostí Webhooku a zpracování událostí ve webové aplikaci
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055525"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure Portal

Azure Event Grid je služba zpracování událostí pro cloud. V tomto rychlém startu použijete Azure Portal k vytvoření mezipaměti Azure pro instanci Redis, přihlášení k odběru událostí této instance, aktivaci události a zobrazení výsledků. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto rychlého startu ale odešlete události do webové aplikace, která bude shromažďovat a zobrazovat zprávy. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Škálování prohlížeče Azure Event Grid ve formátu JSON.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Vytvoření mezipaměti Azure pro instanci mezipaměti Redis Cache 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí pro instanci mezipaměti vytvoříme koncový bod pro zprávu události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **nasadit do Azure** v souboru Readme pro GitHub a nasaďte řešení do vašeho předplatného. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Tlačítko nasadit do Azure":::

2. Na stránce **vlastní nasazení** proveďte následující kroky: 
    1. V poli **Skupina prostředků** vyberte skupinu prostředků, kterou jste vytvořili při vytváření instance mezipaměti. Až budete s kurzem hotovi, budete ho moct vyčistit tím, že odstraníte skupinu prostředků.  
    2. Jako **název lokality** zadejte název webové aplikace.
    3. Jako **název plánu hostování** zadejte název app Serviceho plánu, který se má použít pro hostování webové aplikace.
    4. Zaškrtněte políčko pro souhlasím **s podmínkami a ujednáními uvedenými nahoře**. 
    5. Vyberte **Koupit**. 
    
    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato webová aplikace | 
    | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
    | **Název lokality** | Zadejte název vaší webové aplikace. | Tato hodnota nemůže být prázdná. | 
    | **Název plánu hostování** | Zadejte název App Serviceho plánu, který se má použít pro hostování webové aplikace. | Tato hodnota nemůže být prázdná. | 

1. Na portálu vyberte možnost výstrahy (ikona zvonku) a pak vyberte **Přejít do skupiny prostředků**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Oznámení o nasazení Azure Portal.":::

4. Na stránce **Skupina prostředků** v seznamu prostředků vyberte webovou aplikaci, kterou jste vytvořili. V tomto seznamu se zobrazí také plán App Service a instance mezipaměti. 

5. Na stránce **App Service** vaší webové aplikace vyberte adresu URL, která se má přejít na web. Adresa URL by měla být v tomto formátu: `https://<your-site-name>.azurewebsites.net` .

6. Ověřte, že se vám zobrazuje web, ale ještě se do něj nepublikovaly žádné události.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Prázdný web nástroje Event Grid Viewer":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Přihlášení k odběru instance Azure cache for Redis

V tomto kroku se přihlásíte k odběru tématu, které informuje Event Grid události, které chcete sledovat, a kam se mají události odesílat.

1. Na portálu přejděte na instanci mezipaměti, kterou jste vytvořili dříve. 
2. Na stránce **Azure cache for Redis** vyberte v nabídce vlevo možnost **události** . 
3. Vyberte **Webhook**. Události posíláte do aplikace prohlížeče pomocí webového zavěšení pro koncový bod. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Stránka Azure Portal události":::

4. Na stránce **vytvořit odběr události** zadejte následující: 

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Zadejte název odběru události. | Hodnota musí být v rozmezí 3 až 64 znaků dlouhá. Může obsahovat jenom písmena, číslice a spojovníky. | 
    | **Typy událostí** | Rozevírací seznam a vyberte typy událostí, které chcete přenést do svého cíle. V tomto rychlém startu se škáluje naše instance mezipaměti. | Dostupné možnosti jsou opravy, škálování, import a export. | 
    | **Typ koncového bodu** | Vyberte **Webhook**. | Obslužná rutina události pro příjem událostí. | 
    | **Koncový bod** | Klikněte na **Vybrat koncový bod** a zadejte adresu URL webové aplikace a přidejte `api/updates` ji na domovskou stránku URL (například: `https://cache.azurewebsites.net/api/updates` ) a pak vyberte **potvrdit výběr**. | Toto je adresa URL vaší webové aplikace, kterou jste vytvořili dříve. | 

5. Nyní na stránce **vytvořit odběr události** vyberte **vytvořit** a vytvořte odběr události. 

6. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Pro instanci Redis budeme škálovat mezipaměť Azure.

1. V Azure Portal přejděte do mezipaměti Azure pro instanci Redis a v levé nabídce vyberte **škálovat** .

1. Na stránce **škálování** vyberte požadovanou cenovou úroveň a klikněte na **Vybrat**. 

    Můžete škálovat na jinou cenovou úroveň s následujícími omezeními:
    
    * Nemůžete škálovat z vyšší cenové úrovně na nižší cenovou úroveň.
      * Mezipaměť **Premium** nejde škálovat do **úrovně Standard** nebo **Basic** .
      * **Standardní** mezipaměť nejde škálovat do **základní** mezipaměti.
    * Můžete škálovat ze **základní** mezipaměti na **standardní** mezipaměť, ale nemůžete změnit velikost současně. Pokud potřebujete jinou velikost, můžete provést následnou operaci škálování na požadovanou velikost.
    * Nemůžete škálovat ze **základní** mezipaměti přímo do mezipaměti **Premium** . Nejprve Škálujte z úrovně **Basic** na **Standard** v rámci jedné operace škálování a pak od **Standard** na **Premium** v následné operaci škálování.
    * Velikost se nedá škálovat z větší velikosti na velikost **C0 (250 MB/s)** .
 
    I když je mezipaměť škálovatelná na novou cenovou úroveň, v okně **Azure cache pro Redis** se zobrazí stav **škálování** . Po dokončení škálování se stav změní z **škálování** na **spuštěno**.

1. Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Zpráva je ve formátu JSON a obsahuje pole s jednou nebo více událostmi. V následujícím příkladu zpráva JSON obsahuje pole s jednou událostí. Podívejte se na webovou aplikaci a Všimněte si, že byla přijata událost **ScalingCompleted** . 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Škálování prohlížeče Azure Event Grid ve formátu JSON.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s touto událostí, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. V opačném případě odstraňte prostředky, které jste vytvořili v rámci tohoto rychlého startu.

Vyberte skupinu prostředků a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet vlastní témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Reakce na události Redis v mezipaměti Azure](cache-event-grid.md)
- [Informace o službě Event Grid](../event-grid/overview.md)

