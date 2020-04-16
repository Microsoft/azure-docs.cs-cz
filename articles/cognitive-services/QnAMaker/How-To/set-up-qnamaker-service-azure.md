---
title: Nastavení služby QnA Maker - QnA Maker
description: Před vytvořením jakékoli znalostní báze QnA Maker, musíte nejprve nastavit službu QnA Maker v Azure. Služba QnA Maker může nastavit kdokoli s autorizací k vytváření nových prostředků v rámci předplatného.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 563a56fdb288568e7fe667fa54658400064a560f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402990"
---
# <a name="manage-qna-maker-resources"></a>Správa prostředků QnA Maker

Před vytvořením jakékoli znalostní báze QnA Maker, musíte nejprve nastavit službu QnA Maker v Azure. Služba QnA Maker může nastavit kdokoli s autorizací k vytváření nových prostředků v rámci předplatného.

Solidní pochopení následujících konceptů je užitečné před vytvořením prostředku:

* [Zdroje qnA maker](../Concepts/azure-resources.md)
* [Vytváření a publikování klíčů](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Vytvoření nové služby QnA Maker

Tento postup vytvoří prostředky Azure potřebné ke správě obsahu znalostní báze. Po dokončení těchto kroků najdete klíče _předplatného_ na stránce **Klíče** pro prostředek na webu Azure Portal.

1. Přihlaste se k portálu Azure a vytvořte prostředek [QnA Maker.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Po přečtení smluvních podmínek vyberte **Vytvořit:**

    ![Vytvoření nové služby QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. V **QnA Makervyberte**příslušné úrovně a oblasti:

    ![Vytvoření nové služby QnA Maker – cenová úroveň a oblasti](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Do pole **Název** zadejte jedinečný název pro identifikaci této služby QnA Maker. Tento název také identifikuje koncový bod QnA Maker, ke kterému budou přidruženy vaše znalostní báze.
    * Zvolte **předplatné,** pod kterým bude prostředek QnA Maker nasazen.
    * Vyberte **cenovou úroveň** pro služby správy QnA Maker (portál a řešení API pro správu). Další [podrobnosti o cenách skladových položk .](https://aka.ms/qnamaker-pricing)
    * Vytvořte novou **skupinu prostředků** (doporučeno) nebo použijte existující skupinu, ve které chcete nasadit tento prostředek qnA makeru. QnA Maker vytvoří několik prostředků Azure. Když vytvoříte skupinu prostředků pro uložení těchto prostředků, můžete tyto prostředky snadno vyhledat, spravovat a odstranit podle názvu skupiny prostředků.
    * Vyberte **umístění skupiny prostředků**.
    * Zvolte **cenovou úroveň vyhledávání** služby Azure Cognitive Search. Pokud možnost Free úroveň není k dispozici (zobrazí se šedě), znamená to, že už máte bezplatnou službu nasazenou prostřednictvím předplatného. V takovém případě budete muset začít s úrovní Basic. Viz [Podrobnosti o cenách Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).
    * Zvolte **umístění vyhledávání,** kde chcete nasadit indexy Azure Cognitive Search. Omezení týkající se toho, kde musí být uložena zákaznická data, vám pomůžou určit umístění, které zvolíte pro Azure Cognitive Search.
    * Do pole **Název aplikace** zadejte název instance služby Azure App Service.
    * Ve výchozím nastavení je služba App Service ve výchozím nastavení nastavena na úroveň standardu (S1). Plán můžete po vytvoření změnit. Přečtěte si další informace o [cenách služby App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Zvolte **umístění webu,** kde se bude služba App Service nasazovat.

        > [!NOTE]
        > **Umístění vyhledávání** se může lišit od **umístění webu**.

    * Zvolte, jestli chcete povolit **Application Insights**. Pokud je povoleno **Application Insights,** QnA Maker shromažďuje telemetrická data na provoz, protokoly chatu a chyby.
    * Zvolte **umístění přehledů aplikací,** kde se nasadí prostředek Application Insights.
    * Pro úspory nákladů opatření můžete [sdílet](#configure-qna-maker-to-use-different-cognitive-search-resource) některé, ale ne všechny prostředky Azure vytvořené pro QnA Maker.

1. Po ověření všech polí vyberte **vytvořit**. Proces může trvat několik minut.

1. Po dokončení nasazení se v předplatném zobrazí následující prostředky:

   ![Zdroj vytvořil novou službu QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    Prostředek s typem _služby Cognitive Services_ má klíče _předplatného._

## <a name="find-subscription-keys-in-the-azure-portal"></a>Vyhledání klíčů předplatného na webu Azure Portal

Klíče předplatného můžete zobrazit a resetovat z webu Azure Portal, kde jste vytvořili prostředek QnA Maker.

1. Přejděte na prostředek QnA Maker na webu Azure portal a vyberte prostředek, který má typ _služeb Cognitive Services:_

    ![Seznam zdrojů qnA makeru](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Přejít na **klávesy**:

    ![Klíč předplatného](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Vyhledání klíčů koncového bodu na portálu QnA Maker

Koncový bod je ve stejné oblasti jako prostředek, protože klíče koncového bodu se používají k volání znalostní báze.

Klíče koncového bodu lze spravovat z [portálu QnA Maker](https://qnamaker.ai).

1. Přihlaste se k [portálu QnA Maker](https://qnamaker.ai), přejděte na svůj profil a vyberte **Nastavení služby**:

    ![Klíč koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zobrazení nebo obnovení klíčů:

    > [!div class="mx-imgBorder"]
    > ![Správce klíčů koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Aktualizujte si klíče, pokud si myslíte, že byly ohroženy. To může vyžadovat odpovídající změny klientské aplikace nebo kódu robota.

## <a name="upgrade-qna-maker-sku"></a>Upgrade skladové položky výrobce QnA Maker

Pokud chcete mít další otázky a odpovědi ve znalostní bázi, mimo aktuální úroveň, upgradujte cenovou úroveň služby QnA Maker.

Aktualizace skladové položky správy makeru QnA Maker:

1. Přejděte na svůj prostředek QnA Maker na webu Azure portal a vyberte **Cenová úroveň**.

    ![Zdroj QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Zvolte příslušnou skladovou položku a stiskněte **klávesu Select**.

    ![Ceny QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Upgrade App Service

 Když vaše znalostní báze potřebuje obsluhovat další požadavky z klientské aplikace, upgradujte cenovou úroveň služby App Service.

Můžete [vertikálně navýšit](https://docs.microsoft.com/azure/app-service/manage-scale-up) kapacitu nebo vertikálně navýšit kapacitu App Service.

Přejděte na prostředek služby App Service na webu Azure Portal a podle potřeby vyberte možnost **Vertikálně navýšit** kapacitu nebo **Vertikálně navýšit kapacitu.**

![Měřítko služby QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Upgrade služby Azure Cognitive Search

Pokud máte v plánu mít mnoho znalostních bází, upgradujte cenovou úroveň služby Azure Cognitive Search.

V současné době nelze provést upgrade na místě sku vyhledávání Azure. Můžete však vytvořit nový prostředek vyhledávání Azure s požadovanou skladovou položkou, obnovit data do nového prostředku a pak je propojit s balíčkem QnA Maker. Postupujte přitom takto:

1. Vytvořte nový prostředek vyhledávání Azure na webu Azure Portal a vyberte požadovanou skladovou položku.

    ![Prostředek vyhledávání Azure pro QnA Maker Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Obnovte indexy z původního prostředku vyhledávání Azure do nového. Podívejte se na [ukázkový kód obnovení zálohy](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Po obnovení dat přejděte do nového prostředku vyhledávání Azure, vyberte **Klíče**a poznamenejte si klíč **Name** and **admin**:

    ![Vyhledávací klíče Azure pro QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Pokud chcete propojit nový prostředek vyhledávání Azure s balíčkem QnA Maker, přejděte na instanci služby QnA Maker App Service.

    ![Instance služby QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Vyberte **nastavení aplikace** a upravte nastavení v polích **AzureSearchName** a **AzureSearchAdminKey** z kroku 3.

    ![Nastavení služby QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Restartujte instanci služby App Service.

    ![Restartování instance služby QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Získejte nejnovější aktualizace runtime

Běhový čas QnAMakeru je součástí instance služby Azure App Service, která se nasadí při [vytváření služby QnAMaker](./set-up-qnamaker-service-azure.md) na webu Azure Portal. Aktualizace jsou prováděny pravidelně za běhu. Instance služby QnA Maker App Service je v režimu automatické aktualizace po vydání rozšíření webu z dubna 2019 (verze 5+). Tato aktualizace je navržena tak, aby se postarala o prostoje ZERO během upgradů.

Aktuální verzi můžete zkontrolovat https://www.qnamaker.ai/UserSettingsna adrese . Pokud je vaše verze starší než verze 5.x, musíte restartovat službu App Service, abyste použili nejnovější aktualizace:

1. Přejděte na službu QnAMaker (skupinu prostředků) na [webu Azure Portal](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Skupina prostředků QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte instanci služby App Service a otevřete část **Přehled.**

    > [!div class="mx-imgBorder"]
    > ![Instance služby QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Restartujte službu App Service. Proces aktualizace by měl být dokončen během několika sekund. Všechny závislé aplikace nebo roboty, kteří používají tuto službu QnAMaker, nebudou během tohoto období restartování k dispozici koncovým uživatelům.

    ![Restartování instance služby QnAMaker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurace aplikace QnA Maker tak, aby používala jiný prostředek kognitivního vyhledávání

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vyhledávací služba se vytvoří pro vás a propojí se službou QnA Maker. Po vytvoření těchto prostředků můžete aktualizovat nastavení služby App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat tu, kterou jste právě vytvořili.

Prostředek **služby App Service** výrobce QnA používá prostředek kognitivního vyhledávání. Chcete-li změnit prostředek kognitivního vyhledávání používaný qnA makerem, musíte změnit nastavení na webu Azure Portal.

1. Získejte **klíč správce** a **název** prostředku kognitivního vyhledávání, který má QnA Maker používat.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a najděte **službu App Service** přidruženou k vašemu prostředku QnA Maker. Oba mají stejné jméno.

1. Vyberte **nastavení**a potom **konfigurace**. Zobrazí se všechna existující nastavení pro službu aplikace QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky portálu Azure zobrazující nastavení konfigurace služby App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Změňte hodnoty pro následující klávesy:

    * **Klíč AzureSearchAdmin**
    * **AzureSearchName**

1. Chcete-li použít nové nastavení, je třeba restartovat službu App service. Vyberte **Přehled**, pak vyberte **Restartovat**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s restartováním služby App Service na webu Azure Portal po změně nastavení konfigurace](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Pokud vytvoříte službu QnA prostřednictvím šablon Azure Resource Manager, můžete vytvořit všechny prostředky a řídit vytváření služby App Service pro použití existující vyhledávací služby.

Přečtěte si další informace o konfiguraci [nastavení aplikací](../../../app-service/configure-common.md#configure-app-settings)služby App Service .

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Konfigurace nastavení nečinnosti služby App Service, aby se zabránilo časovému limitu

Služba aplikace, která slouží běhu predikce QnA Maker pro publikovanou znalostní bázi, má konfiguraci časového limitu nečinnosti, která se ve výchozím nastavení automaticky vypočtou, pokud je služba nečinná. Pro QnA Maker to znamená, že vaše předpověď runtime generateAnswer API občas časový úsek po období bez provozu.

Chcete-li zachovat předpověď koncového bodu aplikace načtena i v případě, že není žádný provoz, nastavte nečinnosti vždy zapnutý.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte službu aplikace zdroje QnA Maker. Bude mít stejný název jako prostředek QnA Maker, ale bude mít jiný **typ** služby App Service.
1. Najít **nastavení** pak vyberte **Konfigurace**.
1. V podokně Konfigurace vyberte **Obecné nastavení**, pak najděte **Vždy zapnuto**a jako hodnotu vyberte **Zapnuto.**

    > [!div class="mx-imgBorder"]
    > ![V konfiguračním podokně vyberte **Obecné nastavení**, pak najděte **Always on**a jako hodnotu vyberte **On**.On configuration panel select **General settings**, then find **Always on**, and select **On** as the value.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Chcete-li uložit konfiguraci, vyberte **uložit.**
1. Budete dotázáni, zda chcete restartovat aplikaci, abyste použili nové nastavení. Vyberte **Pokračovat**.

Přečtěte si další informace o konfiguraci [nastavení Obecné](../../../app-service/configure-common.md#configure-general-settings)služby aplikací .

## <a name="delete-azure-resources"></a>Odstranění prostředků Azure

Pokud odstraníte některý z prostředků Azure používaných pro znalostní báze QnA Maker, znalostní báze již nebudou fungovat. Před odstraněním všech zdrojů se ujistěte, že exportujete znalostní báze ze stránky **Nastavení.**

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [službě App service](../../../app-service/index.yml) a [Search](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Vytvoření a publikování znalostní báze](../Quickstarts/create-publish-knowledge-base.md)
