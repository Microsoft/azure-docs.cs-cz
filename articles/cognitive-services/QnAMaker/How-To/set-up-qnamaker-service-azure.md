---
title: Nastavení služby QnA Maker – QnA Maker
description: Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 663cbce0e096c6189d97cf7872d466383d272f06
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650414"
---
# <a name="manage-qna-maker-resources"></a>Správa prostředků QnA Maker

Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.

Před vytvořením prostředku je užitečné základní porozumění následujícím koncepcím:

* [Prostředky QnA Maker](../Concepts/azure-resources.md)
* [Vytváření a publikování klíčů](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Vytvořit novou službu QnA Maker

Tento postup slouží k vytvoření prostředků Azure potřebných ke správě obsahu znalostní báze. Po dokončení tohoto postupu najdete klíče _předplatného_ na stránce **klíče** pro prostředek v Azure Portal.

1. Přihlaste se k Azure Portal a [vytvořte prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Po přečtení podmínek a ujednání vyberte **vytvořit** :

    ![Vytvořit novou službu QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. V **QnA maker**vyberte odpovídající úrovně a oblasti:

    ![Vytvořit novou službu QnA Maker – cenová úroveň a oblasti](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Do pole **název** zadejte jedinečný název, který identifikuje tuto QnA maker službu. Tento název taky identifikuje koncový bod QnA Maker, ke kterému se bude vaše znalostní báze přidružit.
    * Vyberte **předplatné** , pod kterým bude nasazen QnA maker prostředek.
    * Vyberte **cenovou úroveň** pro službu QnA maker Management Services (rozhraní API pro portál a správu). Podívejte se na [Další podrobnosti o cenách SKU](https://aka.ms/qnamaker-pricing).
    * Vytvořte novou skupinu prostředků (doporučeno) nebo použijte existující **skupinu prostředků** , ve které chcete tento prostředek QnA maker nasadit. QnA Maker vytvoří několik prostředků Azure. Když vytvoříte skupinu prostředků pro tyto prostředky, můžete tyto prostředky snadno najít, spravovat a odstranit podle názvu skupiny prostředků.
    * Vyberte **umístění skupiny prostředků**.
    * Vyberte **cenovou úroveň hledání** služby Azure kognitivní hledání. Pokud není dostupná možnost úrovně Free (zobrazí se šedě), znamená to, že už máte nasazenou bezplatnou službu prostřednictvím vašeho předplatného. V takovém případě bude nutné začít s úrovní Basic. Viz [Podrobnosti o cenách Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).
    * Vyberte **umístění pro hledání** , ve kterém chcete nasadit indexy Azure kognitivní hledání. Omezení, kde musí být zákaznická data uložená, vám pomůžou určit umístění, které si zvolíte pro Azure Kognitivní hledání.
    * Do pole **název aplikace** zadejte název instance Azure App Service.
    * Ve výchozím nastavení App Service výchozí úroveň Standard (S1). Tento plán můžete změnit po vytvoření. Přečtěte si další informace o [cenách App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Vyberte **umístění webu** , kam se má App Service nasadit.

        > [!NOTE]
        > **Umístění hledání** se může lišit od **umístění webu**.

    * Vyberte, zda chcete povolit **Application Insights**. Pokud je povolená **Application Insights** , QnA maker shromažďuje telemetrii o provozu, protokolech chatu a chybách.
    * Vyberte **umístění app Insights** , kde se bude Application Insights prostředek nasadit.
    * V případě míry úspory nákladů můžete [sdílet](#configure-qna-maker-to-use-different-cognitive-search-resource) některé, ale ne všechny prostředky Azure vytvořené pro QnA maker.

1. Po ověření všech polí vyberte **vytvořit**. Dokončení procesu může trvat několik minut.

1. Po dokončení nasazení se ve vašem předplatném vytvoří následující prostředky:

   ![Prostředek vytvoří nová služba QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    Prostředek s typem _Cognitive Services_ má vaše klíče _předplatného_ .

## <a name="find-subscription-keys-in-the-azure-portal"></a>Najít klíče předplatného v Azure Portal

Klíče předplatného můžete zobrazit a obnovit z Azure Portal, kde jste vytvořili prostředek QnA Maker.

1. V Azure Portal otevřete prostředek QnA Maker a vyberte prostředek, který má typ _Cognitive Services_ :

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Přejít k **klíčům**:

    ![Klíč předplatného](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Hledání klíčů koncových bodů na portálu QnA Maker

Koncový bod je ve stejné oblasti jako prostředek, protože klávesy koncových bodů slouží k volání znalostní báze.

Klíče koncového bodu je možné spravovat z [portálu QnA maker](https://qnamaker.ai).

1. Přihlaste se k [portálu QnA maker](https://qnamaker.ai), otevřete svůj profil a vyberte **nastavení služby**:

    ![Klíč koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zobrazení nebo resetování klíčů:

    > [!div class="mx-imgBorder"]
    > ![](../media/qnamaker-how-to-key-management/Endpoint-keys1.png) Správce klíčů koncového bodu

    >[!NOTE]
    >Pokud si myslíte, že jsou vaše klíče ohrožené, aktualizujte je. To může vyžadovat odpovídající změny vaší klientské aplikace nebo kódu bot.

### <a name="upgrade-qna-maker-sku"></a>Aktualizace QnA Maker SKU

Pokud chcete mít ve znalostní bázi více otázek a odpovědí mimo aktuální úroveň, upgradujte svou cenovou úroveň služby QnA Maker.

Postup při upgradu SKU QnA Maker Management:

1. V Azure Portal otevřete prostředek QnA Maker a vyberte **cenovou úroveň**.

    ![Prostředek QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Zvolte odpovídající SKU a stiskněte **Vybrat**.

    ![Ceny QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>App Service upgradu

 Pokud vaše znalostní báze potřebuje k poskytování více požadavků z klientské aplikace, upgradujte si App Service cenovou úroveň.

Můžete [navýšení nebo horizontální navýšení](https://docs.microsoft.com/azure/app-service/manage-scale-up) kapacity App Service.

V Azure Portal klikněte na prostředek App Service a podle potřeby vyberte možnost **horizontální navýšení nebo navýšení** **kapacity.**

![Škálování QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Upgrade služby Azure Kognitivní hledání

Pokud máte v úmyslu mít spoustu znalostní báze, upgradujte si cenovou úroveň služby Azure Kognitivní hledání.

V současné době nemůžete provést místní upgrade SKU služby Azure Search. Můžete ale vytvořit nový prostředek služby Azure Search s požadovanou skladovou jednotkou, obnovit data do nového prostředku a pak ho propojit s QnA Makerm zásobníkem. Postupujte přitom takto:

1. Vytvořte nový prostředek Azure Search v Azure Portal a vyberte požadovanou SKU.

    ![Prostředek služby Azure Search QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Obnovte indexy z původního prostředku Azure Search do nového. Podívejte se na [ukázkový kód obnovení zálohování](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Po obnovení dat přejděte na nový prostředek služby Azure Search, vyberte **klíče**a zapište **název** a **klíč správce**:

    ![QnA Maker klíčů pro hledání Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Pokud chcete propojit nový prostředek služby Azure Search s QnA Makerm zásobníkem, přečtěte si instanci služby QnA Maker App Service.

    ![Instance QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Vyberte **nastavení aplikace** a upravte nastavení v polích **AzureSearchName** a **AzureSearchAdminKey** z kroku 3.

    ![Nastavení App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Restartujte instanci App Service.

    ![Restartování instance QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Získat nejnovější aktualizace modulu runtime

Modul runtime Qnamakerem je součástí instance Azure App Service, která se nasadí při [vytváření služby qnamakerem](./set-up-qnamaker-service-azure.md) v Azure Portal. Aktualizace probíhají pravidelně modulu runtime. Instance QnA Maker App Service je v režimu automatické aktualizace po vydání rozšíření lokality z dubna 2019 (verze 5 +). Tato aktualizace je navržená tak, aby se při upgradech postaral o nulové výpadky.

Aktuální verzi můžete ověřit na https://www.qnamaker.ai/UserSettings. Pokud je vaše verze starší než verze 5. x, je nutné restartovat App Service, aby se projevily nejnovější aktualizace:

1. V [Azure Portal](https://portal.azure.com)přejdete do služby qnamakerem (skupina prostředků).

    > [!div class="mx-imgBorder"]
    > ![Qnamakerem skupiny prostředků Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte instanci App Service a otevřete část **Přehled** .

    > [!div class="mx-imgBorder"]
    > ![Qnamakerem App Service instance](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Restartujte App Service. Proces aktualizace by se měl dokončit během několika sekund. Všechny závislé aplikace nebo roboty, které používají tuto službu Qnamakerem, budou koncovým uživatelům během tohoto intervalu restartování nedostupné.

    ![Restartování instance App Service Qnamakerem](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurace QnA Maker pro použití jiného prostředku Kognitivní hledání

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude propojena s QnA Makerovou službou. Po vytvoření těchto prostředků můžete aktualizovat nastavení App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat tu, kterou jste právě vytvořili.

Prostředek QnA Maker **App Service** používá prostředek kognitivní hledání. Chcete-li změnit prostředek Kognitivní hledání používaný QnA Maker, je třeba změnit nastavení v Azure Portal.

1. Získejte **klíč správce** a **název** kognitivní hledání prostředku, který chcete QnA Maker použít.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyhledejte **App Service** přidružené k vašemu prostředku QnA maker. Oba mají stejný název.

1. Vyberte **Nastavení**a pak **Konfigurace**. Zobrazí se všechna existující nastavení App Service QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky Azure Portal se zobrazením nastavení konfigurace App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Změňte hodnoty následujících klíčů:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Chcete-li použít nové nastavení, je nutné restartovat službu App Service. Vyberte **Přehled**a pak vyberte **restartovat**.

    > [!div class="mx-imgBorder"]
    > ![snímku obrazovky Azure Portal restartování App Service po změně nastavení konfigurace](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Pokud vytváříte službu QnA prostřednictvím šablon Azure Resource Manager, můžete vytvořit všechny prostředky a řídit vytvoření App Service, aby se používala existující vyhledávací služba.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o službě [App Service](../../../app-service/index.yml) a [službě vyhledávání](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Vytvoření a publikování znalostní báze](../Quickstarts/create-publish-knowledge-base.md)