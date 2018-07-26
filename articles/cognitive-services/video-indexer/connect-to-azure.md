---
title: Vytvoření připojení k Azure účet služby Video Indexer | Dokumentace Microsoftu
description: Tento článek ukazuje, jak vytvořit připojení k Azure účet služby Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 849a8dde07c248b6d7b317de5908b3a46dfad777
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259006"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Vytvoření připojení k Azure účet služby Video Indexer

Pokud používáte Bezplatný zkušební účet Video Indexer, co vás omezuje kvótu a počet videí, která může indexovat. Tento článek ukazuje, jak vytvořit účet služby Video Indexer, který je propojený s předplatným Azure, díky které tato omezení, která používá ceny s průběžnými platbami.

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

## <a name="connect-to-azure"></a>Připojení k Azure

1. Přihlaste se pomocí tohoto uživatele a klikněte na **připojit se k Azure** tlačítka:

    ![Připojení k Azure](./media/create-account/connect-to-azure.png)

2. Jakmile se zobrazí seznam předplatných, vyberte předplatné, které chcete použít. 

    ![Video Indexer připojení k Azure](./media/create-account/connect-vi-to-azure-subscription.png)

3. Vyberte oblast Azure z podporovaných umístění: západní USA 2, Severní Evropa nebo jihovýchodní Asie.
4. V části **účtu Azure Media Services**, zvolte jednu z těchto možností:

    * Chcete-li vytvořit nový účet Media Services, vyberte **vytvořit novou skupinu prostředků**. Zadejte název vaší skupiny prostředků.

        Azure vytvoří nový účet v rámci vašeho předplatného, včetně nového účtu úložiště Azure. Výchozí počáteční konfiguraci s koncový bod streamování a 10 rezervovaných jednotek S3 má váš nový účet Media Services.
    * Chcete-li použít existující účet Media Services, vyberte **používat stávající prostředek**. Ze seznamu účtů vyberte svůj účet.

        Stejné oblasti jako váš účet služby Video Indexer musí mít váš účet Media Services. Chcete-li minimalizovat dobu trvání indexování a Nízká propustnost, upravte typ a počet rezervovaných jednotek, aby **10 rezervovaných jednotek S3** ve vašem účtu Media Services.
    * Chcete-li ručně konfigurovat připojení, klikněte na tlačítko **přepněte na ruční konfigurace** propojit a zadejte potřebné informace:

    ![Video Indexer připojení k Azure](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Jakmile budete hotovi, zvolte **připojit**. Tato operace může trvat až několik minut. 

    Po připojení k Azure, se zobrazí v seznamu účtů váš nový účet služby Video Indexer:

    ![nový účet](./media/create-account/new-account.png)

6. Přejděte do svého nového účtu služby: 

    ![Účet služby video Indexer](./media/create-account/vi-account.png)

## <a name="considerations"></a>Požadavky

Platí následující aspekty související služby Azure Media Services:

* Pokud jste připojeni k nového účtu Media Services, se zobrazí novou skupinu prostředků, účtu Media Services a účet úložiště ve vašem předplatném Azure.
* Pokud jste připojeni k nového účtu Media Services, Video Indexer bude nastavit **rezervovaných jednotek** na 10 jednotek S3:

    ![Jednotky rezervované pro Media Services](./media/create-account/ams-reserved-units.png)

* Pokud jste připojeni k existujícímu účtu Media Services, Video Indexer nezmění stávajícího multimediálního **rezervovaných jednotek** konfigurace.

    Možná budete muset upravit typ a počet médií **rezervovaných jednotek**, podle plánované zatížení. Uvědomte si, že pokud vysoké zatížení a nemáte dostatek jednotek nebo rychlost zpracování videa může způsobit chyby časového limitu.

* Pokud jste připojeni k nového účtu Media Services, Video Indexer se automaticky spustí **koncový bod streamování** v ní:

    ![Koncový bod streamování služby Media Services](./media/create-account/ams-streaming-endpoint.png)

* Pokud jste připojeni k existujícímu účtu Media Services, nezmění Video Indexer streamování konfigurace koncových bodů. Pokud není žádná spuštění **koncový bod streamování**, nebudete moci sledovat videa z tohoto účtu Media Services nebo v Video Indexer.

## <a name="use-video-indexer-apis-v2"></a>Použití rozhraní Video Indexer API v2

Můžete pracovat prostřednictvím kódu programu pomocí zkušebního účtu nebo s Video Indexer účty, které jsou připojené do azure pomocí následujících pokynů: [pomocí rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejné uživatele Azure AD, který jste použili při připojování ke službě Azure.

## <a name="next-steps"></a>Další postup

[Zkontrolujte podrobnosti ve výstupu JSON](video-indexer-output-json-v2.md).

