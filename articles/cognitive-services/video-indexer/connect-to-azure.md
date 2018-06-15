---
title: Vytvoření účtu Video Indexer připojení k Azure | Microsoft Docs
description: Tento článek ukazuje, jak vytvořit účet Video Indexer připojení k Azure.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: ac9093d41a2e70905ea82c6d11f020696488ff27
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343859"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Vytvoření účtu Video Indexer připojení k Azure

Pokud používáte Video indexeru Bezplatný zkušební účet, jste omezeni kvóty a počet videa, které mohou indexu. Tento článek ukazuje, jak vytvořit účet Video Indexer, který je propojený k předplatnému Azure, který jste uvolní z těchto omezení a používá průběžnými platbami ceny.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. 

    Pokud nemáte předplatné Azure ještě, zaregistrujte si [bezplatná zkušební verze Azure](https://azure.microsoft.com/free/).

* Domény služby Azure Active Directory (AD). 

    Pokud nemáte doménu služby Azure AD, vytvořte tuto doménu ve vašem předplatném Azure.

* Uživatel a člen ve vaší doméně Azure AD. Tento člen budete používat při připojování účtu Video Indexer do Azure.

    Tento uživatel by měl splňovat tato kritéria:

    * Být uživatele Azure AD s pracovní nebo školní účet, nikoli osobní účet, jako jsou outlook.com, live.com nebo hotmail.com.
        
        ![všichni uživatelé AAD](./media/create-account/all-aad-users.png)

    *  Být členem ve vašem předplatném Azure se roli vlastníka nebo role Přispěvatel a správce uživatelského přístupu.

        ![Řízení přístupu](./media/create-account/access-control-iam.png)

## <a name="connect-to-azure"></a>Připojení k Azure

1. Přihlaste se pomocí tohoto uživatele a klikněte na **připojit k Azure** tlačítko:

    ![Připojit k Azure](./media/create-account/connect-to-azure.png)

2. Jakmile se zobrazí seznam předplatných, vyberte odběr, který chcete použít. 

    ![připojit k Azure Video Indexer](./media/create-account/connect-vi-to-azure-subscription.png)

3. Vyberte oblast Azure z podporovaných umístění: západní USA 2, Severní Evropa nebo jihovýchodní Asie.
4. V části **účtu Azure Media Services**, vyberte jednu z těchto možností:

    * Chcete-li vytvořit nový účet Media Services, vyberte **vytvořit novou skupinu prostředků**. Zadejte název skupiny prostředků.

        Azure vytvoří nový účet v rámci vašeho předplatného, včetně nového účtu úložiště Azure. Váš nový účet Media Services má výchozí počáteční konfigurace s koncový bod streamování a 10 jednotek rezervovaných S3.
    * Chcete-li použít existující účet Media Services, vyberte **použijte existující prostředek**. Ze seznamu účtů vyberte svůj účet.

        Váš účet Media Services musí mít stejné oblasti jako váš účet Video Indexer. Chcete-li minimalizovat indexování doba trvání a nízkou propustnost, upravit typ a počet jednotek rezervovaných pro **10 jednotek rezervovaných S3** ve vašem účtu Media Services.
    * Chcete-li ručně konfigurovat připojení, klikněte na tlačítko **přepněte na ruční konfigurace** propojit a zadejte potřebné informace:

    ![připojit k Azure Video Indexer](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Až budete hotoví, zvolte **Connect**. Tato operace může trvat několik minut. 

    Po připojení k Azure, se zobrazí v seznamu účet nového účtu Video Indexer:

    ![nový účet](./media/create-account/new-account.png)

6. Přejděte do nového účtu: 

    ![Video Indexer účtu](./media/create-account/vi-account.png)

## <a name="considerations"></a>Požadavky

Platí následující aspekty související Azure Media Services:

* Pokud jste připojeni k nového účtu Media Services, zobrazí se nové skupiny prostředků účtu Media Services a účet úložiště ve vašem předplatném Azure.
* Pokud jste připojeni k nového účtu Media Services, Video Indexer nastaví média **jednotek rezervovaných** 10 S3 jednotkách:

    ![Jednotky rezervované pro Media Services](./media/create-account/ams-reserved-units.png)

* Pokud jste připojení k existující účet Media Services, Video Indexer nezmění existující média **jednotek rezervovaných** konfigurace.

    Možná budete muset upravit typ a počet média **jednotek rezervovaných**, podle plánované zatížení. Uvědomte si, že pokud je vysoké zatížení a nemáte dostatečný počet jednotek nebo rychlost, videa zpracování může vést selhání časový limit.

* Pokud jste připojeni k nového účtu Media Services, Video Indexer se automaticky spustí **koncový bod streamování** v něm:

    ![Koncový bod streamování Media Services](./media/create-account/ams-streaming-endpoint.png)

* Pokud jste připojení k existující účet Media Services, Video Indexer nezmění konfiguraci koncových bodů streamování. Pokud máte žádné spuštěný **koncový bod streamování**, nebudete moci sledovat videa z tohoto účtu Media Services nebo Video Indexer.

## <a name="use-video-indexer-apis-v2"></a>Použít Video Indexer rozhraní API v2

Prostřednictvím kódu programu můžete pracovat s zkušební účet a s vaší Video Indexer účty, které jsou připojené k azure podle pokynů v: [použití rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejné uživatele Azure AD, který jste použili při připojení k Azure.

## <a name="next-steps"></a>Další postup

[Zkontrolujte podrobnosti o výstup JSON](video-indexer-output-json-v2.md).

