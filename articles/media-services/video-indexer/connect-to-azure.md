---
title: Vytvoření účtu Video Indexer připojeného k Azure
titleSuffix: Azure Media Services
description: Naučte se, jak vytvořit účet Video Indexer připojený k Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 35d01fd60d0549a528a45013add48bd203b86d6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654675"
---
# <a name="create-a-video-indexer-account"></a>Vytvoření účtu Video Indexer

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro webové uživatele a až 2 400 minut bezplatného indexování pro uživatele rozhraní API. Pomocí placené možnosti vytvoříte účet Video Indexer, který je připojený k vašemu předplatnému Azure. Platíte za minuty, další informace najdete v tématu [Media Services ceny](https://azure.microsoft.com/pricing/details/media-services/).

Tento článek ukazuje, jak vytvořit účet Video Indexer propojený s předplatným Azure a účtem Azure Media Services. Téma popisuje kroky pro připojení k Azure pomocí automatického (výchozího) toku. Také ukazuje, jak se připojit k Azure ručně (rozšířené).

Pokud přecházíte ze *zkušební verze* na *placený* účet video indexer, můžete si vybrat, jestli chcete zkopírovat všechna videa a přizpůsobení modelu na nový účet, jak je popsáno v části [Import obsahu z zkušebního účtu](#import-your-content-from-the-trial-account) .

Článek také popisuje [propojení video indexer účtu s Azure Government](#video-indexer-in-azure-government).

## <a name="prerequisites-for-connecting-to-azure"></a>Předpoklady pro připojení k Azure

* Předplatné Azure.

    Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).
* Doména Azure Active Directory (Azure AD).

    Pokud nemáte doménu služby Azure AD, vytvořte tuto doménu s vaším předplatným Azure. Další informace najdete v tématu [Správa vlastních názvů domén ve službě Azure AD](../../active-directory/enterprise-users/domains-manage.md) .
* Uživatel v doméně Azure AD s rolí **správce aplikace** Tento člen budete používat při připojování účtu Video Indexer k Azure.

    Tento uživatel by měl být uživatelem služby Azure AD, který má pracovní nebo školní účet. Nepoužívejte osobní účet, například outlook.com, live.com nebo hotmail.com.

    ![Všichni uživatelé Azure AD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Další předpoklady pro automatický tok

* Uživatel a člen v doméně služby Azure AD.

    Tento člen budete používat při připojování účtu Video Indexer k Azure.

    Tento uživatel by měl být členem v předplatném Azure pomocí role **vlastníka** nebo role **Správce přístupu** **přispěvatele** i uživatele. Uživatele lze přidat dvakrát se dvěma rolemi. Jednou s přispěvatelem a jednou u správce přístupu uživatele. Další informace najdete v tématu [zobrazení přístupu uživatele k prostředkům Azure](../../role-based-access-control/check-access.md).

    ![řízení přístupu](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Další požadavky na ruční tok

* Zaregistrujte poskytovatele prostředků EventGrid pomocí Azure Portal.

    V [Azure Portal](https://portal.azure.com/)přejít na **předplatná**-> [předplatné]->**ResourceProviders**.

    Vyhledejte **Microsoft. Media** a **Microsoft. EventGrid**. Pokud není ve stavu registrováno, klikněte na **zaregistrovat**. Registrace může trvat několik minut.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Vytvoření nového účtu v Azure 

> [!NOTE]
> Pokud vaše předplatné Azure používá vícefaktorové ověřování založené na certifikátech, je velmi důležité, abyste na zařízení s nainstalovanými požadovanými certifikáty prováděli následující kroky.

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
1. Vyberte tlačítko **vytvořit neomezený účet** :

    ![Vytvořit nový účet Video Indexer](./media/create-account/create-unlimited-account.png)
1. Když se zobrazí seznam předplatných, vyberte předplatné, které chcete použít.

    ![Připojení Video Indexer k Azure](./media/create-account/new-account-on-azure-subscription.png)
1. Vyberte oblast Azure z podporovaných umístění: Západní USA 2, Severní Evropa nebo Východní Asie.
1. V části **Azure Media Services účet** vyberte jednu z následujících možností:

    * Pokud chcete vytvořit nový účet Media Services, vyberte **vytvořit novou skupinu prostředků**. Zadejte název vaší skupiny prostředků.

        Azure vytvoří nový účet v předplatném, včetně nového účtu Azure Storage.  
    * Pokud chcete použít existující účet Media Services, vyberte **použít existující prostředek**. V seznamu účty vyberte svůj účet.

        Váš účet Media Services musí mít stejnou oblast jako váš Video Indexer účet.

        > [!NOTE]
        > Pro minimalizaci doby trvání indexování a nízké propustnosti se důrazně doporučuje upravit typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) v účtu Media Services na **10 jednotek rezervovaných v S3**. Pokud [chcete změnit rezervované jednotky](../previous/media-services-portal-scale-media-processing.md), přečtěte si téma použití portálu. Rezervované jednotky se účtují podle vašeho účtu, podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * Pokud chcete připojení nakonfigurovat ručně, vyberte odkaz **Přepnout na ruční konfiguraci** .

        Podrobné informace najdete v části věnované [ručnímu připojení k Azure](#connect-to-azure-manually-advanced-option) (rozšířené možnosti) níže.
1. Jakmile budete hotoví, vyberte **Vytvořit**. Tato operace může trvat až několik minut.

    Po připojení k Azure se nový účet Video Indexer zobrazí v seznamu účtů:

    ![nový účet](./media/create-account/new-account.png)
1. Před přehráním videí ve webové aplikaci Video Indexer se ujistěte, že je koncový bod streamování účtu Media Services spuštěný (Pokud se jedná o zastavený stav, stiskněte klávesu Start).

> [!TIP]
> Pokud chcete, aby se Váš účet regenerujte, můžete přejít na **Nastavení**.

## <a name="connect-to-azure-manually-advanced-option"></a>Ruční připojení k Azure (rozšířená volba)

Pokud se připojení k Azure nepovedlo, můžete se pokusit problém vyřešit ručním připojením.

> [!NOTE]
> Ve stejné oblasti se důrazně doporučuje mít následující tři účty: účet Video Indexer, ke kterému se připojujete pomocí účtu Media Services, a také účet úložiště Azure, který je připojený ke stejnému účtu Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Vytvoření a konfigurace účtu Media Services

1. Pomocí webu [Azure](https://portal.azure.com/) Portal vytvořte účet Azure Media Services, jak je popsáno v tématu [Vytvoření účtu](../previous/media-services-portal-create-account.md).

     Ujistěte se, že byl účet Media Services vytvořen s klasickými rozhraními API. 
 
    ![Rozhraní API pro Media Services Classic](./media/create-account/enable-classic-api.png)


    Při vytváření účtu úložiště pro účet Media Services vyberte **StorageV2** pro druh účtu a **geograficky redundantní** (GRS) pro pole replikace.

    ![Nový účet AMS](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Nezapomeňte zapsat Media Services názvů prostředků a účtů. Budete je potřebovat pro kroky v další části.
1. V účtu Media Services, který jste vytvořili, upravte typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) na **10 jednotek rezervovaných v S3** . Pokud [chcete změnit rezervované jednotky](../previous/media-services-portal-scale-media-processing.md), přečtěte si téma použití portálu.

    Rezervované jednotky se účtují podle vašeho účtu, podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/media-services/#analytics). s
1. Než budete moct videa přehrávat ve webové aplikaci Video Indexer, musíte spustit výchozí **koncový bod streamování** nového účtu Media Services.

    V novém účtu Media Services vyberte **koncové body streamování**. Pak vyberte koncový bod streamování a stiskněte spustit.

    ![Koncové body streamování](./media/create-account/create-ams-account-se.png)
4. Aby bylo možné Video Indexer ověřit pomocí rozhraní Media Services API, je potřeba vytvořit aplikaci AD. Následující kroky vás provedou procesem ověřování Azure AD popsaným v tématu [Začínáme s ověřováním Azure AD pomocí Azure Portal](../previous/media-services-portal-get-started-with-aad.md):

    1. V novém Media Services účtu vyberte přístup přes **rozhraní API**.
    2. Vyberte [metodu ověřování instančního objektu](../previous/media-services-portal-get-started-with-aad.md).
    3. Získat ID klienta a tajný klíč klienta

        Po výběru **Možnosti** -> **klíče**, přidat **Popis**, stiskněte **Uložit** a hodnota klíče se naplní.

        Pokud klíč vyprší, vlastník účtu bude muset kontaktovat Video Indexer podporu, aby se klíč obnovil.

        > [!NOTE]
        > Nezapomeňte zapsat klíčovou hodnotu a ID aplikace. Budete ho potřebovat pro kroky v další části.

### <a name="connect-manually"></a>Ruční připojení

V dialogovém okně **vytvořit nový účet v rámci předplatného Azure na** stránce [video indexer](https://www.videoindexer.ai/) vyberte odkaz **Přepnout na ruční konfiguraci** .

V dialogovém okně zadejte následující informace:

|Nastavení|Popis|
|---|---|
|Oblast účtu Video Indexer|Název oblasti účtu Video Indexer. Pro lepší výkon a snížení nákladů doporučujeme zadat název oblasti, kde se nachází Azure Media Services prostředek a Azure Storage účet. |
|Tenant Azure AD|Název tenanta Azure AD, například "contoso.onmicrosoft.com". Informace o tenantovi lze získat z Azure Portal. Umístěte ukazatel myši na jméno přihlášeného uživatele v pravém horním rohu. Vyhledá jméno napravo od **domény**.|
|ID předplatného|Předplatné Azure, v rámci kterého se má vytvořit připojení ID předplatného se dá načíst z Azure Portal. Na levém panelu vyberte **všechny služby** a vyhledejte "předplatná". Vyberte **předplatná** a v seznamu předplatných vyberte požadované ID.|
|Název skupiny prostředků Azure Media Services|Název skupiny prostředků, ve které jste vytvořili Media Services účet.|
|Název prostředku služby Media Service|Název účtu Azure Media Services, který jste vytvořili v předchozí části.|
|ID aplikace|ID aplikace Azure AD (s oprávněním pro zadaný účet Media Services), který jste vytvořili v předchozí části.|
|Klíč aplikace|Klíč aplikace služby Azure AD, který jste vytvořili v předchozí části. |

### <a name="import-your-content-from-the-trial-account"></a>Import obsahu z *zkušebního* účtu

Při vytváření nového účtu máte možnost importovat obsah z *zkušebního* účtu do nového účtu. Pokud v dialogovém okně **vytvořit nový účet v předplatném Azure** vyberete možnost *Import* , všechna vlastní nastavení médií a modelu obsahu se z *zkušebního* účtu zkopírují do nového účtu.

Možnost importu obsahu je platná pro automatizované i ruční postupy popsané výše.

> [!NOTE]
> Obsah se dá z každého účtu importovat jenom jednou.
>
> *Zkušební* účet není availagle v cloudu Azure Government.

## <a name="azure-media-services-considerations"></a>Azure Media Services hlediska

Platí následující Azure Media Services související s požadavky:

* Pokud se chystáte připojit ke stávajícímu účtu Media Services, ujistěte se, že byl účet Media Services vytvořen s rozhraními API Classic. 
 
    ![Rozhraní API pro Media Services Classic](./media/create-account/enable-classic-api.png)
* Pokud se připojíte ke stávajícímu účtu Media Services, Video Indexer nemění existující konfiguraci **rezervovaných jednotek** médií.

   V závislosti na plánovaném zatížení možná budete muset upravit typ a počet rezervovaných jednotek médií. Mějte na paměti, že pokud je vaše zatížení vysoké a nemáte dost jednotek nebo rychlostí, může zpracování videí způsobit selhání s časovým limitem.
* Pokud se připojíte k novému účtu Media Services, Video Indexer automaticky spustí výchozí **koncový bod streamování** :

    ![Koncový bod streamování Media Services](./media/create-account/ams-streaming-endpoint.png)

    Koncové body streamování mají značný čas spuštění. Proto může trvat několik minut od chvíle, kdy jste svůj účet připojili k Azure, dokud vaše videa nebude možné streamovat a sledovat v Video Indexer webové aplikaci.
* Pokud se připojíte ke stávajícímu účtu Media Services, Video Indexer nemění výchozí konfiguraci koncového bodu streamování. Pokud není spuštěný **koncový bod streamování**, nemůžete sledovat videa z tohoto účtu Media Services nebo v video indexer.
* Pokud se připojíte automaticky, Video Indexer nastaví **rezervované jednotky** médií na 10 jednotek S3:

    ![Rezervované jednotky Media Services](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>Automatizace vytváření Video Indexer účtu

K automatizaci vytváření účtu se jedná o proces dvou kroků:
 
1. Pomocí Azure Resource Manager můžete vytvořit účet Azure Media Services a aplikaci Azure AD.

    Podívejte se na příklad [šablony pro vytvoření účtu Media Services](https://github.com/Azure-Samples/media-services-v3-arm-templates).
1. [Pomocí Media Services a aplikace Azure AD zavolejte vytvořit účet](https://videoindexer.ai.azure.us/account/login?source=apim).

## <a name="video-indexer-in-azure-government"></a>Video Indexer v Azure Government

### <a name="prerequisites-for-connecting-to-azure-government"></a>Předpoklady pro připojení k Azure Government

-   Předplatné Azure v [Azure Government](../../azure-government/index.yml).
- Účet Azure AD v Azure Government.
- Všechny předběžné požadavky oprávnění a prostředků, jak je popsáno výše v části [požadavky pro připojení k Azure](#prerequisites-for-connecting-to-azure). Nezapomeňte zkontrolovat [Další předpoklady pro automatický tok](#additional-prerequisites-for-automatic-flow) a [Další požadavky na ruční tok](#additional-prerequisites-for-manual-flow).

### <a name="create-new-account-via-the-azure-government-portal"></a>Vytvoření nového účtu prostřednictvím portálu Azure Government

> [!NOTE]
> Azure Government Cloud nezahrnuje *zkušební* verzi video indexer.

Vytvoření placeného účtu prostřednictvím portálu Video Indexer:

1. Přejděte na https://videoindexer.ai.azure.us. 
1. Přihlaste se pomocí Azure Government účtu Azure AD.
1.  Pokud v Azure Government nemáte žádné účty Video Indexer, se kterými jste vlastníkem nebo přispěvatelem, získáte prázdné prostředí, ze kterého můžete začít vytvářet svůj účet. 

    Zbývající část toku je popsaná výše, jenom oblasti, ze kterých se vybere, se budou stát státními oblastmi, ve kterých je video indexer k dispozici. 

    Pokud již jste přispěvatelem nebo správcem existujícího účtu Video Indexer v Azure Government, bude se vám přicházet k tomuto účtu a odtud můžete v případě potřeby začít postupovat podle pokynů k vytvoření dalšího účtu, jak je popsáno výše.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Vytvořit nový účet prostřednictvím rozhraní API na Azure Government

Pokud chcete vytvořit placený účet v Azure Government, postupujte podle pokynů v části [Vytvoření-placeného účtu](). Tento koncový bod rozhraní API zahrnuje jenom oblasti cloudu státní správy.

### <a name="limitations-of-video-indexer-on-azure-government"></a>Omezení Video Indexer v Azure Government

*   V cloudu pro státní správu nejsou k dispozici žádné moderování ručního obsahu. 

    Pokud se ve veřejném cloudu na základě Moderování obsahu považuje obsah za urážlivý, může zákazník požádat uživatele, aby tento obsah vypadal a mohl by toto rozhodnutí vrátit zpátky.  
*   Žádné zkušební účty. 
* Popis Bingu – v cloudu gov nebudeme mít k dispozici popis identifikovaných celebrit a jmenovaných entit. Toto je pouze schopnost uživatelského rozhraní. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu odstraňte prostředky, které neplánujete použít.

### <a name="delete-a-video-indexer-account"></a>Odstranit účet Video Indexer

Pokud chcete odstranit účet Video Indexer, můžete účet odstranit z webu Video Indexer. Pokud chcete účet odstranit, musíte být vlastníkem.

Vyberte nastavení účtu->   ->  **Odstranit tento účet**. 

Účet se trvale odstraní během 90 dnů.

## <a name="next-steps"></a>Další kroky

Můžete programově spolupracovat s vaším zkušebním účtem a/nebo s účty Video Indexer, které jsou připojené k Azure, podle pokynů v části: [použití rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejného uživatele Azure AD, kterého jste použili při připojování k Azure.
