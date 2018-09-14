---
title: Vytvoření připojení k Azure účet služby Video Indexer | Dokumentace Microsoftu
description: Tento článek ukazuje, jak vytvořit připojení k Azure účet služby Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 229624f4070c931b6ad892533aad269af49c738b
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544180"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Vytvoření připojení k Azure účet služby Video Indexer

Při vytváření účtu Video Indexer, můžete vybrat účet bezplatné zkušební verze (ve kterém získáte určitý počet minut zdarma indexování) nebo placenou variantu (Pokud nejste omezení kvóty). S bezplatnou zkušební verzi Video Indexer poskytuje až 600 minut zdarma indexování uživatelům webu a až 2 400 minut zdarma indexování uživatelům rozhraní API. S placenou variantu vytvoříte účet služby Video Indexer, který je připojený ke svému předplatnému Azure a účet Azure Media Services. Platíte za minut indexovat, stejně jako účet Media související poplatky. 

Tento článek ukazuje, jak vytvořit účet služby Video Indexer, který je propojený s předplatným Azure a účet Azure Media Services. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. 

    Pokud nemáte ještě předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

* Domény služby Azure Active Directory (AD). 

    Pokud nemáte doménu služby Azure AD, vytvořte tuto doménu ve vašem předplatném Azure. Další informace najdete v tématu [Správa vlastních názvů domén v Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Uživatel a člen ve vaší doméně Azure AD. Tento člen budete používat při připojování váš účet služby Video Indexer pro Azure.

    Tento uživatel by měl splňovat tato kritéria:

    * Být uživatele služby Azure AD pomocí svého pracovního nebo školního účtu, nikoli pomocí osobního účtu, jako jsou outlook.com, live.com nebo hotmail.com.
        
        ![všichni uživatelé AAD](./media/create-account/all-aad-users.png)

    *  Mít předplatné Azure s roli vlastníka nebo přispěvatele a správce uživatelských přístupů role. Uživatele můžete přidat dvakrát, s 2 rolemi. Jednou pomocí a jednou pro uživatel s oprávněním správce přístup přispěvatele.

        ![Řízení přístupu](./media/create-account/access-control-iam.png)

* Registrace poskytovatele prostředků EventGrid pomocí webu Azure portal.

    V [webu Azure portal](https://portal.azure.com/), přejděte na stránku **předplatná** > [předplatné] > **ResourceProviders** > **Microsoft.EventGrid**. Pokud není ve stavu "Registrováno", klikněte na tlačítko **zaregistrovat**. Trvá několik minut, než se registrace. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Připojení k Azure

1. Přejděte [Video Indexer](https://www.videoindexer.ai/) web a přihlaste se.

2. Klikněte na **připojit se k Azure** tlačítka:

    ![Připojení k Azure](./media/create-account/connect-to-azure.png)

3. Jakmile se zobrazí seznam předplatných, vyberte předplatné, které chcete použít. 

    ![Video Indexer připojení k Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Vyberte oblast Azure z podporovaných umístění: západní USA 2, Severní Evropa nebo jihovýchodní Asie.
5. V části **účtu Azure Media Services**, zvolte jednu z těchto možností:

    * Chcete-li vytvořit nový účet Media Services, vyberte **vytvořit novou skupinu prostředků**. Zadejte název vaší skupiny prostředků.

        Azure vytvoří nový účet v rámci vašeho předplatného, včetně nového účtu úložiště Azure. Výchozí počáteční konfiguraci s koncový bod streamování a 10 rezervovaných jednotek S3 má váš nový účet Media Services.
    * Chcete-li použít existující účet Media Services, vyberte **používat stávající prostředek**. Ze seznamu účtů vyberte svůj účet.

        Stejné oblasti jako váš účet služby Video Indexer musí mít váš účet Media Services. Chcete-li minimalizovat dobu trvání indexování a Nízká propustnost, upravte typ a počet rezervovaných jednotek, aby **10 rezervovaných jednotek S3** ve vašem účtu Media Services.
    * Chcete-li ručně konfigurovat připojení, klikněte na tlačítko **přepněte na ruční konfigurace**. 
    
        Můžete chtít ručně konfigurovat připojení, pokud z nějakého důvodu nepodaří možnost automatického dokončení, nebo pokud vaše nastavení a konfigurace se liší od běžné případy, nebo chcete mít kompletní přehled a kontrolu nad nastavení. 
        
        V **připojení Video Indexer s předplatným Azure**, zadejte následující informace.

        |Nastavení|Popis|
        |---|---|
        |Oblast účtu video Indexer|Název oblasti účtu Video Indexer. Pro lepší výkon a nižší náklady důrazně doporučujeme zadat název oblasti, kde se nachází na prostředek služby Azure Media Services a účet služby Azure Storage. |
        |Tenant Azure Active Directory (AAD)|Název tenanta Azure AD, například "contoso.onmicrosoft.com". Informace o tenantovi můžete získat z webu Azure portal. Umístěte ukazatel myši přes jméno přihlášeného uživatele v horním pravém rohu.|
        |ID předplatného|Předplatné Azure, ve kterém by měl vytvoří toto připojení. ID předplatného můžete získat z webu Azure portal. Klikněte na **všechny služby** v levém panelu a vyhledejte "předplatné". Vyberte, **předplatná** a pak vyberte požadovaného ID z seznam vašich předplatných.|
        |Název skupiny prostředků Azure Media Services|Název pro skupinu prostředků, ve kterém k účtu Media Services existuje.|
        |Název mediální služby prostředků|Název prostředku Azure Media Services.|
        |ID aplikace|ID aplikace Azure AD s oprávněními pro zadaný účet Media Services. Další informace najdete v tématu [použití ověřování instančních objektů](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|
        |Klíč aplikace|Další informace najdete v tématu [použití ověřování instančních objektů](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|

6. Jakmile budete hotovi, zvolte **připojit**. Tato operace může trvat až několik minut. 

    Po připojení k Azure, se zobrazí v seznamu účtů váš nový účet služby Video Indexer:

    ![nový účet](./media/create-account/new-account.png)

7. Přejděte do svého nového účtu služby: 

    ![Účet služby video Indexer](./media/create-account/vi-account.png)

## <a name="considerations"></a>Požadavky

Platí následující aspekty související služby Azure Media Services:

* Pokud jste připojeni k nového účtu Media Services, se zobrazí novou skupinu prostředků, účtu Media Services a účet úložiště ve vašem předplatném Azure.
* Pokud jste připojeni k nového účtu Media Services, Video Indexer bude nastavit **rezervovaných jednotek** na 10 jednotek S3:

    ![Jednotky rezervované pro Media Services](./media/create-account/ams-reserved-units.png)

* Pokud jste připojeni k existujícímu účtu Media Services, Video Indexer nezmění stávajícího multimediálního **rezervovaných jednotek** konfigurace.

    Možná budete muset upravit typ a počet médií **rezervovaných jednotek**, podle plánované zatížení. Uvědomte si, že pokud vysoké zatížení a nemáte dostatek jednotek nebo rychlost zpracování videa může způsobit chyby časového limitu.

* Pokud jste připojeni k nového účtu Media Services, Video Indexer se automaticky spustí výchozí **koncový bod streamování** v ní:

    ![Koncový bod streamování služby Media Services](./media/create-account/ams-streaming-endpoint.png)

* Pokud jste připojeni k existujícímu účtu Media Services, Video Indexer nedojde ke změně výchozí konfigurace koncového bodu streamování. Pokud není žádná spuštění **koncový bod streamování**, nebudete moci sledovat videa z tohoto účtu Media Services nebo v Video Indexer.

## <a name="next-steps"></a>Další postup

Můžete pracovat prostřednictvím kódu programu pomocí zkušebního účtu nebo s Video Indexer účty, které jsou připojené do azure pomocí následujících pokynů: [pomocí rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejné uživatele Azure AD, který jste použili při připojování ke službě Azure.


