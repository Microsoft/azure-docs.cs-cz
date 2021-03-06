---
title: Nastavení služby QnA Maker – QnA Maker
description: Než budete moct vytvořit QnA Maker znalostní báze, musíte nejdřív nastavit službu QnA Maker v Azure. Každý, kdo má oprávnění k vytváření nových prostředků v předplatném, může nastavit službu QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219263"
---
# <a name="manage-qna-maker-resources"></a>Správa prostředků QnA Maker

Než budete moct vytvořit QnA Maker znalostní báze, musíte nejdřív nastavit službu QnA Maker v Azure. Každý, kdo má oprávnění k vytváření nových prostředků v předplatném, může nastavit službu QnA Maker.

Před vytvořením prostředku je užitečné základní porozumění následujícím koncepcím:

* [Prostředky QnA Maker](../Concepts/azure-resources.md)
* [Vytváření a publikování klíčů](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Vytvoření nové služby QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Tento postup slouží k vytvoření prostředků Azure potřebných ke správě obsahu znalostní báze. Po dokončení tohoto postupu najdete klíče _předplatného_ na stránce **klíče** pro prostředek v Azure Portal.

1. Přihlaste se k Azure Portal a [vytvořte prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Po přečtení podmínek a ujednání vyberte **vytvořit** :

    ![Vytvoření nové služby QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. V **QnA maker** vyberte odpovídající úrovně a oblasti:

    ![Vytvoření nové služby QnA Maker – cenová úroveň a oblasti](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Do pole **název** zadejte jedinečný název, který identifikuje tuto QnA maker službu. Tento název taky identifikuje koncový bod QnA Maker, ke kterému se bude vaše znalostní báze přidružit.
    * Vyberte **předplatné** , pod kterým bude nasazen QnA maker prostředek.
    * Vyberte **cenovou úroveň** pro službu QnA maker Management Services (rozhraní API pro portál a správu). Podívejte se na [Další podrobnosti o cenách SKU](https://aka.ms/qnamaker-pricing).
    * Vytvořte novou skupinu prostředků (doporučeno) nebo použijte existující **skupinu prostředků** , ve které chcete tento prostředek QnA maker nasadit. QnA Maker vytvoří několik prostředků Azure. Když vytvoříte skupinu prostředků pro tyto prostředky, můžete tyto prostředky snadno najít, spravovat a odstranit podle názvu skupiny prostředků.
    * Vyberte **umístění skupiny prostředků**.
    * Vyberte **cenovou úroveň hledání** služby Azure kognitivní hledání. Pokud není dostupná možnost úrovně Free (zobrazí se šedě), znamená to, že už máte nasazenou bezplatnou službu prostřednictvím vašeho předplatného. V takovém případě bude nutné začít s úrovní Basic. Viz [Podrobnosti o cenách Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).
    * Vyberte **umístění pro hledání** , ve kterém chcete nasadit indexy Azure kognitivní hledání. Omezení, kde musí být zákaznická data uložená, vám pomůžou určit umístění, které si zvolíte pro Azure Kognitivní hledání.
    * Do pole **název aplikace** zadejte název instance Azure App Service.
    * Ve výchozím nastavení App Service výchozí úroveň Standard (S1). Plán můžete změnit po vytvoření. Přečtěte si další informace o [cenách App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Vyberte **umístění webu** , kam se má App Service nasadit.

        > [!NOTE]
        > **Umístění hledání** se může lišit od **umístění webu**.

    * Vyberte, zda chcete povolit **Application Insights**. Pokud je povolená **Application Insights** , QnA maker shromažďuje telemetrii o provozu, protokolech chatu a chybách.
    * Vyberte **umístění app Insights** , kde se bude Application Insights prostředek nasadit.
    * V případě míry úspory nákladů můžete [sdílet](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) některé, ale ne všechny prostředky Azure vytvořené pro QnA maker.

1. Po ověření všech polí vyberte **vytvořit**. Dokončení procesu může trvat několik minut.

1. Po dokončení nasazení se ve vašem předplatném vytvoří následující prostředky:

   ![Prostředek vytvořil novou službu QnA Maker.](../media/qnamaker-how-to-setup-service/resources-created.png)

    Prostředek s typem _Cognitive Services_ má vaše klíče _předplatného_ .

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Tento postup slouží k vytvoření prostředků Azure potřebných ke správě obsahu znalostní báze. Po dokončení tohoto postupu najdete klíče *předplatného* na stránce **klíče** pro prostředek v Azure Portal.

1. Přihlaste se k Azure Portal a [vytvořte prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Po přečtení podmínek a ujednání vyberte **vytvořit** :

    ![Vytvoření nové služby QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. V **QnA maker** zaškrtněte políčko spravované (Preview) a vyberte odpovídající úrovně a oblasti:

    ![Vytvoření nové QnA Maker spravované služby – cenová úroveň a oblasti](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * Vyberte **předplatné** , pod kterým bude nasazen QnA maker prostředek.
    * Vytvořte novou **skupinu prostředků** (doporučeno) nebo použijte existující, ve které chcete nasadit tento prostředek QnA maker spravovaném (ve verzi Preview). QnA Maker Managed (Preview) vytvoří několik prostředků Azure. Když vytvoříte skupinu prostředků pro tyto prostředky, můžete tyto prostředky snadno najít, spravovat a odstranit podle názvu skupiny prostředků.
    * Do pole **název** zadejte jedinečný název, který identifikuje tuto QnA maker spravovanou službu (Preview). 
    * Vyberte **umístění** , kam chcete nasadit službu QnA maker Managed (Preview). Rozhraní API pro správu a koncový bod služby budou hostovány v tomto umístění. 
    * Vyberte **cenovou úroveň** pro službu QnA maker Managed (Preview) (zdarma pro verzi Preview). Podívejte se na [Další podrobnosti o cenách SKU](https://aka.ms/qnamaker-pricing).
    * Vyberte **umístění pro hledání** , ve kterém chcete nasadit indexy Azure kognitivní hledání. Omezení, kde musí být zákaznická data uložená, vám pomůžou určit umístění, které si zvolíte pro Azure Kognitivní hledání.
    * Vyberte **cenovou úroveň hledání** služby Azure kognitivní hledání. Pokud není dostupná možnost úrovně Free (zobrazí se šedě), znamená to, že už máte nasazenou bezplatnou službu prostřednictvím vašeho předplatného. V takovém případě bude nutné začít s úrovní Basic. Viz [Podrobnosti o cenách Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).

1. Po ověření všech polí vyberte **zkontrolovat + vytvořit**. Dokončení procesu může trvat několik minut.

1. Po dokončení nasazení se ve vašem předplatném vytvoří následující prostředky:

    ![Prostředek vytvořil novou službu QnA Maker spravovaná (Preview).](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    Prostředek s typem _Cognitive Services_ má vaše klíče _předplatného_ .

---

## <a name="upgrade-azure-resources"></a>Upgrade prostředků Azure

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>Aktualizace QnA Maker SKU

Pokud chcete mít ve znalostní bázi více otázek a odpovědí mimo aktuální úroveň, upgradujte svou cenovou úroveň služby QnA Maker.

Postup při upgradu SKU QnA Maker Management:

1. V Azure Portal otevřete prostředek QnA Maker a vyberte **cenovou úroveň**.

    ![Prostředek QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Zvolte odpovídající SKU a stiskněte **Vybrat**.

    ![Ceny QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>App Service upgradu

Pokud vaše znalostní báze potřebuje k poskytování více požadavků z klientské aplikace, upgradujte si App Service cenovou úroveň.

Můžete [navýšení nebo horizontální navýšení](../../../app-service/manage-scale-up.md) kapacity App Service.

V Azure Portal klikněte na prostředek App Service a podle potřeby vyberte možnost **horizontální navýšení nebo navýšení** **kapacity.**

![Škálování QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Upgrade služby Azure Kognitivní hledání

Pokud plánujete mít velké množství znalostních bází, upgradujte cenovou úroveň služby Azure Cognitive Search.

V současné době nemůžete provést místní upgrade SKU služby Azure Search. Můžete ale vytvořit nový prostředek služby Azure Search s požadovanou skladovou jednotkou, obnovit data do nového prostředku a pak ho propojit s QnA Makerm zásobníkem. Postupujte takto:

1. Vytvořte nový prostředek Azure Search v Azure Portal a vyberte požadovanou SKU.

    ![Prostředek služby Azure Search QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Obnovte indexy z původního prostředku Azure Search do nového. Projděte si [vzorový kód pro zálohování a obnovení](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Po obnovení dat přejděte na nový prostředek služby Azure Search, vyberte **klíče** a zapište **název** a **klíč správce**:

    ![QnA Maker klíčů pro hledání Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Pokud chcete propojit nový prostředek služby Azure Search s QnA Makerm zásobníkem, přečtěte si instanci služby QnA Maker App Service.

    ![Instance QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Vyberte **Nastavení aplikace** a upravte nastavení v polích **Název služby Azure Search** a **Klíč správce služby Azure Search** s použitím hodnot z kroku 3.

    ![Nastavení App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Restartujte instanci služby App Service.

    ![Restartování instance QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Zásady nečinnosti pro bezplatné vyhledávání prostředků

Pokud nepoužíváte prostředek QnA maker, měli byste odebrat všechny prostředky. Pokud nepoužijete žádné nepoužívané prostředky, vaše znalostní báze přestane fungovat, pokud jste vytvořili prostředek pro vyhledávání.

Bezplatné prostředky vyhledávání se odstraní po 90 dnech bez přijetí volání rozhraní API.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Upgrade služby Azure Kognitivní hledání

Pokud plánujete mít velké množství znalostních bází, upgradujte cenovou úroveň služby Azure Cognitive Search.

V současné době nemůžete provést místní upgrade SKU služby Azure Search. Můžete ale vytvořit nový prostředek služby Azure Search s požadovanou skladovou jednotkou, obnovit data do nového prostředku a pak ho propojit s QnA Makerm zásobníkem. Postupujte takto:

1. Vytvořte nový prostředek Azure Search v Azure Portal a vyberte požadovanou SKU.

    ![Prostředek služby Azure Search QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Obnovte indexy z původního prostředku Azure Search do nového. Projděte si [vzorový kód pro zálohování a obnovení](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Postup propojení nového prostředku Azure Search se službou QnA Maker Managed (Preview) najdete v následujícím tématu.

### <a name="inactivity-policy-for-free-search-resources"></a>Zásady nečinnosti pro bezplatné vyhledávání prostředků

Pokud nepoužíváte prostředek QnA maker, měli byste odebrat všechny prostředky. Pokud nepoužijete žádné nepoužívané prostředky, vaše znalostní báze přestane fungovat, pokud jste vytvořili prostředek pro vyhledávání.

Bezplatné prostředky vyhledávání se odstraní po 90 dnech bez přijetí volání rozhraní API.

---

## <a name="delete-azure-resources"></a>Odstranění prostředků Azure

Pokud odstraníte některý z prostředků Azure, které se používají pro vaše QnA Maker znalostní báze, nebude již znalostní báze nadále fungovat. Před odstraněním jakýchkoli prostředků se ujistěte, že vaše znalostní báze vyexportujete ze stránky **Nastavení** .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o službě [App Service](../../../app-service/index.yml) a [službě vyhledávání](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Naučte se vytvářet s ostatními](../index.yml)
