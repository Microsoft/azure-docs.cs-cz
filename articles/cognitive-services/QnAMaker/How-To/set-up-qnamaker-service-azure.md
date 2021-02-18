---
title: Nastavení služby QnA Maker – QnA Maker
description: Než budete moct vytvořit QnA Maker znalostní báze, musíte nejdřív nastavit službu QnA Maker v Azure. Každý, kdo má oprávnění k vytváření nových prostředků v předplatném, může nastavit službu QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 0f03cd536d329a94ec80ef884c380c79b5687289
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096641"
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
    * V případě míry úspory nákladů můžete [sdílet](#configure-qna-maker-to-use-different-cognitive-search-resource) některé, ale ne všechny prostředky Azure vytvořené pro QnA maker.

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

## <a name="recommended-settings-for-network-isolation"></a>Doporučené nastavení pro izolaci sítě

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

1. Chraňte prostředek služby Service od veřejného přístupu [konfigurací virtuální sítě](../../cognitive-services-virtual-networks.md?tabs=portal).
2. Chraňte App Service (QnA Runtime) z veřejného přístupu.

   ##### <a name="add-ips-to-app-service-allowlist"></a>Přidání IP adres do App Service povolených

    * Povoluje provoz jenom z IP adres služeb rozpoznávání. Tyto jsou již zahrnuty do tagu služby `CognitiveServicesManagement` . To se vyžaduje pro vytváření rozhraní API (vytvořit/aktualizovat KB) pro vyvolání služby App Service a aktualizaci služby Azure Search. Podívejte se na [Další informace o značkách služby.](../../../virtual-network/service-tags-overview.md)
    * Ujistěte se, že taky povolíte další vstupní body, jako je robot Service, QnA Maker Portal (může to být váš Corpnet) atd. předpověď přístupu k rozhraní API GenerateAnswer.
    * Pokud chcete přidat rozsahy IP adres do povolenýchu, postupujte prosím podle těchto kroků:

      * Stáhněte si [rozsahy IP adres pro všechny značky služeb](https://www.microsoft.com/download/details.aspx?id=56519).
      * Vyberte IP adresy "CognitiveServicesManagement".
      * Přejděte do části sítě v prostředku App Service a kliknutím na možnost konfigurovat omezení přístupu přidejte IP adresy do povolených.

    ![výjimky portů pro příchozí spojení](../media/inbound-ports.png)    

    K dispozici je také automatizovaný skript, který bude pro váš App Service stejný. [Skript PowerShellu můžete nakonfigurovat tak, aby povolených](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) na GitHubu. Je potřeba zadat ID předplatného, skupinu prostředků a skutečný název App Service jako parametry skriptu. Po spuštění skriptu se tyto IP adresy automaticky přidají App Service povolených.

    ##### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Konfigurace App Service Environment pro hostování QnA Maker App Service
    K hostování služby QnA Maker App Service se dá použít App Service Environment (pomocného mechanismu). Postupujte následovně:

    1. Vytvořte App Service Environment a označte ji jako "externí". Pokyny najdete v tomto [kurzu](../../../app-service/environment/create-external-ase.md) .
    2.  Vytvořte službu App Service v rámci App Service Environment.
        * Ověřte konfiguraci služby App Service a jako nastavení aplikace přidejte ' PrimaryEndpointKey '. Hodnota pro ' PrimaryEndpointKey ' by měla být nastavena na hodnotu \<app-name\> -PrimaryEndpointKey. Název aplikace je definovaný v adrese URL služby App Service. Pokud je například adresa URL služby App Service "mywebsite.myase.p.azurewebsite.net", pak název aplikace je "mywebsite". V tomto případě by měla být hodnota pro ' PrimaryEndpointKey ' nastavena na "mywebsite-PrimaryEndpointKey".
        * Vytvořte službu Azure Search.
        * Ujistěte se, že Azure Search a nastavení aplikace jsou správně nakonfigurované. 
          Postupujte prosím podle tohoto [kurzu](../reference-app-service.md?tabs=v1#app-service).
    3.  Aktualizace skupiny zabezpečení sítě přidružené k App Service Environment
        * Aktualizujte předem vytvořená příchozí pravidla zabezpečení podle vašich požadavků.
        * Přidejte nové příchozí pravidlo zabezpečení se zdrojem jako značkou služby a označením zdrojové služby jako "CognitiveServicesManagement".
    4.  Vytvořte instanci služby QnA Maker rozpoznávání (Microsoft. Cognitiveservices Account/Accounts) pomocí Azure Resource Manager, kde QnA Maker koncový bod by měl být nastaven na App Service koncový bod, který jste vytvořili výše (https://mywebsite.myase.p.azurewebsite.net).
    
3. Konfigurace Kognitivní hledání privátního koncového bodu v rámci virtuální sítě

    Když se vytvoří instance hledání během vytváření prostředku QnA Maker, můžete vynutit Kognitivní hledání pro podporu konfigurace privátního koncového bodu, která se zcela vytvořila v rámci virtuální sítě zákazníka.

    Pro použití privátního koncového bodu musí být všechny prostředky vytvořeny ve stejné oblasti.

    * Prostředek QnA Maker
    * nový prostředek Kognitivní hledání
    * nový prostředek Virtual Network

    V [Azure Portal](https://portal.azure.com)proveďte následující kroky:

    1. Vytvořte [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
    1. Vytvořte nový prostředek Kognitivní hledání s možností připojení koncových bodů (data) nastavenou na _Private_. Vytvořte prostředek ve stejné oblasti, jako je QnA Maker prostředek vytvořený v kroku 1. Přečtěte si další informace o [vytvoření prostředku kognitivní hledání](../../../search/search-create-service-portal.md)a pak použijte tento odkaz k přechodu přímo na [stránku vytváření prostředku](https://ms.portal.azure.com/#create/Microsoft.Search).
    1. Vytvořte nový [prostředek Virtual Network](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
    1. Nakonfigurujte virtuální síť u prostředku služby App Service vytvořeného v kroku 1 tohoto postupu.
        1. Vytvořte novou položku DNS ve virtuální síti pro nový prostředek Kognitivní hledání vytvořený v kroku 2. do Kognitivní hledání IP adresa.
    1. [Přidružte službu App Service k novému kognitivní hledání prostředku](#configure-qna-maker-to-use-different-cognitive-search-resource) vytvořenému v kroku 2. Pak můžete odstranit původní prostředek Kognitivní hledání vytvořený v kroku 1.

    Na [portálu QnA maker](https://www.qnamaker.ai/)vytvořte svou první znalostní bázi.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

1. Chraňte prostředek služby Service od veřejného přístupu [konfigurací virtuální sítě](../../cognitive-services-virtual-networks.md?tabs=portal).
2. [Vytvořte privátní koncové body](../reference-private-endpoint.md) pro prostředek Azure Search.

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

## <a name="configure-azure-resources"></a>Konfigurace prostředků Azure

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurace QnA Maker pro použití jiného prostředku Kognitivní hledání

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude propojena s QnA Makerovou službou. Po vytvoření těchto prostředků můžete aktualizovat nastavení App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat tu, kterou jste právě vytvořili.

Prostředek QnA Maker **App Service** používá prostředek kognitivní hledání. Chcete-li změnit prostředek Kognitivní hledání používaný QnA Maker, je třeba změnit nastavení v Azure Portal.

1. Získejte **klíč správce** a **název** kognitivní hledání prostředku, který chcete QnA Maker použít.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyhledejte **App Service** přidružené k vašemu prostředku QnA maker. Oba mají stejný název.

1. Vyberte **Nastavení** a pak **Konfigurace**. Zobrazí se všechna existující nastavení App Service QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky Azure Portal zobrazující nastavení konfigurace App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Změňte hodnoty následujících klíčů:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Chcete-li použít nové nastavení, je nutné restartovat službu App Service. Vyberte **Přehled** a pak vyberte **restartovat**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky Azure Portal restartování App Service po změně nastavení konfigurace](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Pokud vytváříte službu QnA prostřednictvím šablon Azure Resource Manager, můžete vytvořit všechny prostředky a řídit vytvoření App Service, aby se používala existující vyhledávací služba.

Přečtěte si další informace o tom, jak nakonfigurovat App Service [nastavení aplikace](../../../app-service/configure-common.md#configure-app-settings).

### <a name="get-the-latest-runtime-updates"></a>Získat nejnovější aktualizace modulu runtime

Modul runtime Qnamakerem je součástí instance Azure App Service, která se nasadí při [vytváření služby qnamakerem](./set-up-qnamaker-service-azure.md) v Azure Portal. Aktualizace se pravidelně provádějí do modulu runtime. Instance QnA Maker App Service je v režimu automatické aktualizace po vydání rozšíření lokality z dubna 2019 (verze 5 +). Tato aktualizace je navržená tak, aby se při upgradech postaral o nulové výpadky.

Aktuální verzi můžete zjistit na adrese https://www.qnamaker.ai/UserSettings . Pokud je vaše verze starší než verze 5. x, je nutné restartovat App Service, aby se projevily nejnovější aktualizace:

1. V [Azure Portal](https://portal.azure.com)přejdete do služby qnamakerem (skupina prostředků).

    > [!div class="mx-imgBorder"]
    > ![Qnamakerem skupina prostředků Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte instanci App Service a otevřete část **Přehled** .

    > [!div class="mx-imgBorder"]
    > ![Instance App Service Qnamakerem](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Restartujte App Service. Proces aktualizace by se měl dokončit během několika sekund. Všechny závislé aplikace nebo roboty, které používají tuto službu Qnamakerem, budou koncovým uživatelům během tohoto intervalu restartování nedostupné.

    ![Restartování instance App Service Qnamakerem](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Nakonfigurujte nastavení nečinné služby App Service tak, aby nevypršel časový limit.

Služba App Service, která slouží jako modul runtime předpovědi QnA Maker pro publikovanou znalostní bázi, má konfiguraci časového limitu nečinnosti, při které se výchozí hodnota automaticky vyprší, pokud je služba nečinná. V případě QnA Maker to znamená, že rozhraní API pro generateAnswer prostředí předplatného občas vyprší časový limit po obdobích bez provozu.

Aby se zajistilo, že se aplikace koncového bodu předpovědi načetla i v případě, že nedochází k provozu, nastavte nečinné na Always On.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte službu App Service prostředku QnA Maker. Bude mít stejný název jako prostředek QnA Maker, ale bude mít jiný **typ** App Service.
1. Najděte **Nastavení** a pak vyberte **Konfigurace**.
1. V podokně Konfigurace vyberte **Obecná nastavení** a pak najít **vždycky zapnuto** **a jako hodnotu** vyberte.

    > [!div class="mx-imgBorder"]
    > ![V podokně Konfigurace vyberte možnost * * Obecné nastavení * *, vyhledejte * * Always On * * a jako hodnotu vyberte * * * * *.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Kliknutím na **Uložit** uložte konfiguraci.
1. Zobrazí se dotaz, jestli chcete aplikaci restartovat, aby používala nové nastavení. Vyberte **Pokračovat**.

Přečtěte si další informace o tom, jak nakonfigurovat App Service [Obecná nastavení](../../../app-service/configure-common.md#configure-general-settings).

### <a name="business-continuity-with-traffic-manager"></a>Kontinuita podnikových aplikací pomocí Traffic Manageru

Hlavním cílem plánu kontinuity podnikových aplikací je vytvořit odolný koncový bod ve znalostní bázi Knowledge Base, který by pro něj neměl nic trvat ani to, jestli ho aplikace spotřebovává.

> [!div class="mx-imgBorder"]
> ![Plán QnA Maker BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Nejdůležitější nápad, jak je znázorněno výše, je následující:

1. Nastavte dvě paralelní [QnA maker služby](set-up-qnamaker-service-azure.md) v [spárovaných oblastech Azure](../../../best-practices-availability-paired-regions.md).

1. [Zálohujte](../../../app-service/manage-backup.md) primární QnA maker App Service a [obnovte](../../../app-service/web-sites-restore.md) ji v sekundární instalaci. Tím se zajistí, že obě nastavení budou fungovat se stejným názvem hostitele a klíči.

1. Udržování primárních a sekundárních indexů Azure Search v synchronizaci. Pomocí ukázky na GitHubu [tady](https://github.com/pchoudhari/QnAMakerBackupRestore) zjistíte, jak zálohovat a obnovit indexy Azure.

1. Zazálohujte Application Insights pomocí [průběžného exportu](../../../azure-monitor/app/export-telemetry.md).

1. Po nastavení primárních a sekundárních zásobníků nakonfigurujte pomocí [Traffic Manageru](../../../traffic-manager/traffic-manager-overview.md) dva koncové body a nastavte metodu směrování.

1. Museli byste vytvořit protokol TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL), certifikát pro koncový bod služby Traffic Manager. [Navažte certifikát TLS/SSL](../../../app-service/configure-ssl-bindings.md) ve vašich aplikačních službách.

1. Nakonec v robotu nebo v aplikaci použijte koncový bod Traffic Manageru.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Konfigurace služby QnA Maker spravovaná (Preview) pro použití jiného prostředku Kognitivní hledání

Pokud vytvoříte spravovanou službu QnA (ve verzi Preview) a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude propojena se službou QnA Maker Managed (Preview). Po vytvoření těchto prostředků můžete službu Search aktualizovat na kartě **Konfigurace** .

1. V Azure Portal přejdete do služby QnA Maker Managed (Preview).

1. Vyberte **Konfigurace** a vyberte službu Azure kognitivní hledání, kterou chcete propojit se službou QnA maker Managed (Preview).

    ![Snímek obrazovky se stránkou konfigurace spravovaného QnA Maker (Preview)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Klikněte na **Uložit**.

> [!NOTE]
> Pokud změníte Azure Search službu přidruženou k QnA Maker, ztratíte přístup ke všem poznatkům, které už jsou v ní obsažené. Před změnou služby Azure Search se ujistěte, že jste exportovali stávající znalostní báze.

---

## <a name="delete-azure-resources"></a>Odstranění prostředků Azure

Pokud odstraníte některý z prostředků Azure, které se používají pro vaše QnA Maker znalostní báze, nebude již znalostní báze nadále fungovat. Před odstraněním jakýchkoli prostředků se ujistěte, že vaše znalostní báze vyexportujete ze stránky **Nastavení** .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o službě [App Service](../../../app-service/index.yml) a [službě vyhledávání](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Naučte se vytvářet s ostatními](../index.yml)
