---
title: Vytvoření účtu Azure Media Services pomocí webu Azure Portal | Dokumentace Microsoftu
description: Tento kurz vás provede kroky pro vytvoření účtu služby Azure Media Services pomocí webu Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 53f0063d1458c218324d867c1942ae0e044f10d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011914"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Vytvoření účtu Media Services pomocí Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Azure Portal nabízí rychlou možnost vytvoření účtu Azure Media Services (AMS). Účet můžete použít pro přístup ke službě Media Services, která vám umožní ukládat, šifrovat, kódovat, spravovat a streamovat mediální obsah v Azure. Ve chvíli, kdy vytvoříte účet Media Services, můžete také vytvořit přidružený účet úložiště (nebo použít existující). Pokud odstraníte účet Media Services, objekty blob v souvisejícím účtu úložiště odstraněny nebudou.

Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, abyste se vyhnuli další latenci a nákladům na výchozí přenos dat.

V tomto článku se dozvíte, jak vytvořit účet Media Services pomocí Azure Portal.

> [!NOTE]
> Informace o dostupnosti funkcí Azure Media Services v různých oblastech naleznete v tématu [funkce AMS napříč oblastmi](availability-regions-v-2.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-ams-account"></a>Vytvoření účtu AMS

Postup v této části ukazuje, jak vytvořit účet AMS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na **+ vytvořit prostředek**  >  **média**  >  **Media Services**.
3. V okně **VYTVOŘIT ÚČET MEDIA SERVICES** zadejte požadované hodnoty.

   1. Do pole **Název účtu** zadejte název nového účtu AMS. Název účtu Media Services musí obsahovat jenom malá písmena a číslice, nesmí obsahovat mezery a musí mít délku 3 až 24 znaků.
   2. V poli Předplatné si vyberte z různých předplatných Azure, ke kterým máte přístup.
   3. V poli **Skupina prostředků** vyberte nový nebo existující prostředek.  Skupina prostředků je kolekce prostředků, které sdílejí životní cyklus, oprávnění a zásady. Další informace najdete [tady](../../azure-resource-manager/management/overview.md#resource-groups).
   4. V poli **Umístění** vyberte zeměpisnou oblast, která se bude používat k ukládání médií a záznamů metadat pro váš účet Media Services. Tato oblast se bude používat ke zpracování a streamování vašeho média. V rozevíracím seznamu se vám zobrazí pouze ty oblasti Media Services, které jsou dostupné. 
   5. V poli **Účet úložiště** vyberte účet úložiště, který bude sloužit jako úložiště objektů blob mediálního obsahu z vašeho účtu Media Services. Můžete vybrat existující účet úložiště ve stejné zeměpisné oblasti jako váš účet Media Services, nebo můžete vytvořit účet úložiště. Nový účet úložiště bude vytvořen ve stejné oblasti. Pro názvy účtů úložiště platí stejná pravidla jako pro názvy účtů Media Services.

       Další informace o ukládání a úložištích najdete [tady](../../storage/common/storage-introduction.md).
   6. Zaškrtněte **Připnout na řídicí panel**, abyste viděli průběh nasazení účtu.
4. Klikněte na tlačítko **Vytvořit** dole na formuláři.

    Po úspěšném vytvoření účtu se načte stránka s přehledem. V tabulce koncových bodů streamování bude účet mít výchozí koncový bod streamování ve stavu **Zastaveno**. 

    >[!NOTE]
    >Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
   
## <a name="to-manage-your-ams-account"></a>Správa účtu AMS

Pokud chcete spravovat svůj účet AMS (například připojit se prostřednictvím kódu programu k rozhraní API pro AMS, nahrát videa, kódovat prostředky, konfigurovat ochranu obsahu nebo monitorovat průběh úloh), vyberte **Nastavení** na levé straně portálu. V **Nastavení** přejděte do některého z dostupných oken (například: **Přístup k rozhraní API**, **Prostředky**, **Úlohy** nebo **Ochrana obsahu**).

## <a name="next-steps"></a>Další kroky

Soubory teď můžete nahrát do účtu AMS. Další informace najdete v tématu [Nahrání souborů](media-services-portal-upload-files.md).

Pokud plánujete přistupovat k rozhraní API pro AMS prostřednictvím kódu programu, přečtěte si téma [Přístup k rozhraní API pro službu Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
