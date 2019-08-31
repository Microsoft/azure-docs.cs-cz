---
title: Nastavení služby QnA Maker – QnA Maker
titleSuffix: Azure Cognitive Services
description: Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195321"
---
# <a name="manage-qna-maker-resources"></a>Správa prostředků QnA Maker

Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Typy klíčů v QnA Maker

Vaše služba QnA Maker se zabývá dvěma druhy klíče, **klíče předplatného** a **klíče koncového bodu**.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Location|Účel|
|--|--|--|
|Klíč předplatného|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Tyto klíče se používají pro přístup k [rozhraním API služby QnA maker Management](https://go.microsoft.com/fwlink/?linkid=2092179). Tato rozhraní API umožňují upravovat otázky a odpovědi ve znalostní bázi a publikovat znalostní bázi. Tyto klíče se vytvoří při vytvoření nové služby QnA Maker.<br><br>Vyhledejte tyto klíče na prostředku **Cognitive Services** na stránce **klíče** .|
|Klíč koncového bodu|[Portál QnA Maker](http://www.qnamaker.ai)|Tyto klíče se používají pro přístup k publikovanému koncovému bodu znalostní báze a získání odpovědi na otázku uživatele. Tento koncový bod obvykle používáte ve vaší robotice nebo v kódu klientské aplikace, který se připojuje ke službě QnA Maker. Tyto klíče se vytvoří při publikování QnA Maker znalostní báze Knowledge Base.<br><br>Tyto klíče najdete na stránce **nastavení služby** . Vyhledá tuto stránku v nabídce nahoře, v pravém horním rohu uživatele jako volba v rozevíracím seznamu.|

## <a name="create-a-new-qna-maker-service"></a>Vytvořit novou službu QnA Maker

Tento postup slouží k vytvoření prostředků Azure potřebných ke správě obsahu znalostní báze. Po dokončení tohoto postupu najdete klíče předplatného na stránce **klíče** pro prostředek v Azure Portal.

1. Přihlaste se k Azure Portal a [vytvořte prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Po přečtení podmínek a ujednání vyberte **vytvořit** .

    ![Vytvořit novou službu QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. V **QnA Maker**, vyberte příslušné úrovně a oblasti.

    ![Vytvořit novou službu QnA Maker – cenová úroveň a oblasti](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Zadejte **název** s jedinečný název pro identifikaci této služby QnA Maker. Tento název také identifikuje koncový bod QnA Maker, do kterého mají být asociována znalostních bází.
    * Zvolte **předplatné** ve kterém bude nasazen nástroj QnA Maker prostředků.
    * Vyberte **cenovou úroveň** pro službu QnA maker Management Services (rozhraní API pro portál a správu). Zobrazit [tady](https://aka.ms/qnamaker-pricing) podrobnosti o cenách za skladové jednotky.
    * Vytvořte nový **skupiny prostředků** (doporučeno) nebo použijte již existující, ve které chcete nasadit tento prostředek QnA Maker. QnA Maker vytvoří několik prostředků Azure; Když vytvoříte skupinu prostředků pro tyto prostředky, můžete tyto prostředky snadno najít, spravovat a odstranit podle názvu skupiny prostředků.
    * Vyberte **umístění skupiny prostředků**.
    * Zvolte **cenová úroveň hledání** služby Azure Search. Pokud se zobrazí možnost úrovně Free šedě, znamená to, že už máte bezplatnou Azure Search úrovni nasazené ve vašem předplatném. V takovém případě je potřeba spustit s úrovní Basic Azure Search. Zobrazit podrobnosti o cenách Azure search [tady](https://azure.microsoft.com/pricing/details/search/).
    * Zvolte **poloha při hledání** kde chcete data Azure Search k nasazení. Omezení kde musí být zákaznická data uložena informuje umístění, které jste vybrali pro Azure Search.
    * Zadejte název služby App service v **název aplikace**.
    * Ve výchozím nastavení výchozí služby App service na úrovni standard (S1). Tento plán můžete změnit po vytvoření. Viz podrobnosti o App service – ceny [tady](https://azure.microsoft.com/pricing/details/app-service/).
    * Zvolte **umístění webu** nasazená služby App Service.

        > [!NOTE]
        > Umístění pro hledání se může lišit od umístění webu.

    * Vyberte, jestli chcete povolit **Application Insights** nebo ne. Pokud **Application Insights** je povolené, nástroj QnA Maker shromažďuje telemetrická data na provoz, chatu a chyby.
    * Zvolte **umístění App insights** nasazená prostředku Application Insights.
    * V případě míry úspory nákladů můžete [sdílet](#share-existing-services-with-qna-maker) některé, ale ne všechny prostředky Azure vytvořené pro QnA maker. 

1. Po ověření všech polí vyberte **vytvořit**. Dokončení může trvat několik minut.

1. Jakmile se nasazení dokončí, zobrazí se následující prostředky vytvořené v rámci vašeho předplatného.

    ![Prostředek vytvoří nová služba QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    Prostředek s typem _Cognitive Services_ má vaše klíče předplatného.

## <a name="find-subscription-keys-in-azure-portal"></a>Najít klíče předplatného v Azure Portal

Můžete zobrazit a resetovat klíče předplatného, které upraví z Azure Portal, kde jste vytvořili prostředek QnA Maker. 

1. V Azure Portal otevřete prostředek QnA Maker a vyberte prostředek s typem _Cognitive Services_.

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Přejděte na **klíče**.

    ![Klíč předplatného](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>Hledání klíčů koncových bodů na portálu QnA Maker

Klíče koncového bodu je možné spravovat z [portál QnA Maker](https://qnamaker.ai).

1. Přihlaste se k [portálu QnA maker](https://qnamaker.ai), přejděte do svého profilu a klikněte na **nastavení služby**.

    ![Klíč koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zobrazení nebo resetovat vaše klíče.

    ![klíče správce koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Pokud se domníváte, že jsou ohrožené, aktualizujte svoje klíče. To může vyžadovat odpovídající změny vaší klientské aplikace nebo kódu bot.

## <a name="share-existing-services-with-qna-maker"></a>Sdílejte existující služby s QnA Maker

QnA Maker vytvoří několik prostředků Azure. Aby se snížila úroveň správy a výhoda sdílení nákladů, použijte následující tabulku, která vám pomůže pochopit, co můžete a nemůžete sdílet:

|Služba|Sdílení|Reason|
|--|--|--|
|Cognitive Services|X|Není možné podle návrhu|
|Plán služby App Service|✔|Pevné místo na disku přidělené pro plán služby App Service. Pokud jiné aplikace, sdílející stejný plán App Service, využijete významné místo na disku, v Qnamakerem App Service dojde k problémům.|
|App Service|X|Není možné podle návrhu|
|Application Insights|✔|Může být sdíleno|
|Služba vyhledávání|✔|1. `testkb` je rezervovaný název služby qnamakerem, kterou nemůžou použít jiní uživatelé.<br>2. Mapování synonym podle názvu `synonym-map` je vyhrazeno pro službu qnamakerem.<br>3. Počet publikovaných aktualizací KB je omezený na úrovni služby vyhledávání. Pokud jsou dostupné bezplatné indexy, můžou je používat i jiné služby.|

### <a name="using-a-single-search-service"></a>Použití jedné vyhledávací služby

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude vázána na službu QnA Maker. Po vytvoření těchto prostředků můžete aktualizovat nastavení služby App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat právě vytvořenou vyhledávací službu.

Pokud vytvoříte službu QnA prostřednictvím šablon Azure Resource Manager, můžete vytvořit všechny prostředky a řídit vytvoření služby App Service tak, aby používala existující vyhledávací službu. 

## <a name="upgrade-qna-maker"></a>QnA Maker upgradu

|Upgradovat|Reason|
|--|--|
|[Upgrade](#upgrade-qna-maker-sku) SKU správy QnA Maker|Ve znalostní bázi musíte mít další otázky a odpovědi.|
|[Upgrade](#upgrade-app-service) SKU App Service|Vaše znalostní báze musí poskytovat více žádostí z klientské aplikace, jako je například robot pro chat.|
|[Upgrade](#upgrade-azure-search-service) Služba Azure Search|Máte v plánu, abyste měli spoustu znalostní báze.|


### <a name="upgrade-qna-maker-sku"></a>Aktualizace QnA Maker SKU

Pokud potřebujete mít ve znalostní bázi více otázek a odpovědí za rámec vaší aktuální úrovně, upgradujte si cenovou úroveň služby QnA Maker. 

Postup při upgradu SKU QnA Maker Management:

1. V Azure Portal otevřete prostředek QnA Maker a vyberte **cenovou úroveň**.

    ![Prostředek QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Zvolte odpovídající SKU a stiskněte **Vybrat**.

    ![Ceny QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Upgradovat službu App Service

 Pokud vaše znalostní báze potřebuje k obsluze více požadavků z klientské aplikace, upgradujte si cenovou úroveň služby App Service.

Službu App Service můžete [škálovat směrem nahoru](https://docs.microsoft.com/azure/app-service/manage-scale-up) nebo dolů.

V Azure Portal přejděte do prostředku App Service a podle potřeby vyberte možnosti **horizontálního** navýšení nebo **snížení** kapacity.

![QnA Maker škálování služby App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>Aktualizace služby Azure Search

Pokud plánujete, že máte spoustu znalostní báze, upgradujte si cenovou úroveň služby Azure Search. 

V současné době není možné provést místní upgrade skladové položky služby Azure Search. Můžete ale vytvořit nový prostředek služby Azure Search s požadovanou skladovou jednotkou, obnovit data do nového prostředku a pak ho propojit s QnA Makerm zásobníkem.

1. Vytvořte nový prostředek Azure Search v Azure Portal a vyberte požadovanou SKU.

    ![Prostředek služby Azure Search QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Obnovte indexy z původního prostředku Azure Search do nového. [Tady](https://github.com/pchoudhari/QnAMakerBackupRestore)najdete ukázkový kód pro obnovení zálohování.

1. Po obnovení dat přejděte na nový prostředek služby Azure Search, vyberte **klíče**a poznamenejte si **název** a **klíč správce**.

    ![QnA Maker klíčů pro hledání Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Pokud chcete propojit nový prostředek služby Azure Search se zásobníkem QnA Maker, přečtěte si do QnA Maker App Service.

    ![QnA Maker AppService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Vyberte **nastavení aplikace** a v kroku 3 nahraďte pole **AzureSearchName** a **AzureSearchAdminKey** .

    ![Nastavení AppService QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Restartujte služby App service.

    ![QnA Maker AppService restartování](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>Získat nejnovější aktualizace modulu runtime

Modul runtime Qnamakerem je součástí Azure App Service nasazeného při [vytváření služby qnamakerem](./set-up-qnamaker-service-azure.md) v Azure Portal. Aktualizace probíhají pravidelně modulu runtime. QnA Maker App Service se v režimu automatické aktualizace 2019 po vydání kumulativního rozšíření webu (verze 5 +). Tato hodnota je již navržena tak, aby se během upgradů mohla zajímat žádná výpadky. 

Aktuální verzi můžete zjistit na adrese https://www.qnamaker.ai/UserSettings. Pokud je vaše verze starší než verze 5. x, je nutné restartovat App Service, aby se projevily nejnovější aktualizace.

1. Přejděte do služby QnA maker (skupinu prostředků) [webu Azure portal](https://portal.azure.com)

    ![Skupina prostředků Azure QnA maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Klikněte na tlačítko ve službě App Service a otevřete část – Přehled

     ![QnA maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Restartujte služby App service. By se měla dokončit během několika sekund. Všechny závislé aplikace nebo roboty, které používají tuto službu Qnamakerem, budou koncovým uživatelům během tohoto intervalu restartování nedostupné.

    ![Restartování služby App Service QnA maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Oblast služby správy

Služba správy QnA Maker, která se používá jenom pro QnA Maker portál & pro počáteční zpracování dat, je dostupná jenom v Západní USA. V této Západní USA službě se neukládají žádná zákaznická data.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření a publikování znalostní báze](../Quickstarts/create-publish-knowledge-base.md)
