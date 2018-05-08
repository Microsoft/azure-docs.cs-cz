---
title: Vytvoření účtu Azure Media Services pomocí webu Azure Portal | Dokumentace Microsoftu
description: Tento kurz vás provede kroky pro vytvoření účtu služby Azure Media Services pomocí webu Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/03/2017
ms.author: juliako
ms.openlocfilehash: fcf02e39a305281501773db51383dff66c485493
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Vytvoření účtu Azure Media Services pomocí webu Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-create-account.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Azure Portal nabízí rychlou možnost vytvoření účtu Azure Media Services (AMS). Účet můžete použít pro přístup ke službě Media Services, která vám umožní ukládat, šifrovat, kódovat, spravovat a streamovat mediální obsah v Azure. V okamžiku vytvoření účtu Media Services si současně vytvoříte i přidružený účet úložiště (nebo použijete existující) ve stejné zeměpisné oblasti jako účet Media Services.

Tento článek popisuje některé obvyklé koncepty a ukazuje jak vytvořit účet Media Services pomocí webu Azure Portal.

> [!NOTE]
> Informace o dostupnosti funkcí služby Azure Media Services v různých oblastech najdete v tématu popisujícím [dostupnost funkcí AMS v datových centrech](scenarios-and-availability.md#availability).

## <a name="concepts"></a>Koncepty
Přístup ke službě Media Services vyžaduje dva přidružené účty:

* Účet Media Services. Váš účet umožňuje přístup k sadě cloudových služeb Media Services, které jsou dostupné v Azure. Účet Media Services neukládá samotný mediální obsah. Místo toho na vašem účtu ukládají metadata o mediálním obsahu a úlohách zpracování médií v účtu. Při vytváření účtu vybíráte dostupnou oblast služby Media Services. Oblast, kterou vyberete, je datové centrum, které ukládá záznamy metadat vašeho účtu.
  
* Účet úložiště Azure. Účty úložiště pro mediální soubory musí být umístěné ve stejné zeměpisné oblasti jako účet Media Services. Při vytváření účtu Media Services můžete zvolit buď stávající účet úložiště ve stejné oblasti, nebo můžete vytvořit nový účet úložiště ve stejné oblasti. Pokud odstraníte účet Media Services, objekty blob v souvisejícím účtu úložiště odstraněny nebudou.

  > [!NOTE]
  > Služba Media Services omezuje primární účet úložiště tak, že může být pouze **obecným účtem Storage** s tabulkami a frontami. Další informace o typech úložiště najdete v článku [Informace o účtech Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="create-an-ams-account"></a>Vytvoření účtu AMS
Postup v této části ukazuje, jak vytvořit účet AMS.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Klikněte na **+Nové** > **Web + mobilní zařízení** > **Media Services**.
   
    ![Media Services – vytvoření](./media/media-services-create-account/media-services-new1.png)
3. V okně **VYTVOŘIT ÚČET MEDIA SERVICES** zadejte požadované hodnoty.
   
    ![Media Services – vytvoření](./media/media-services-create-account/media-services-new3.png)
   
   1. Do pole **Název účtu** zadejte název nového účtu AMS. Název účtu Media Services musí obsahovat jenom malá písmena a číslice, nesmí obsahovat mezery a musí mít délku 3 až 24 znaků.
   2. V poli Předplatné si vyberte z různých předplatných Azure, ke kterým máte přístup.
   3. V poli **Skupina prostředků** vyberte nový nebo existující prostředek.  Skupina prostředků je kolekce prostředků, které sdílejí životní cyklus, oprávnění a zásady. Další informace najdete [tady](../../azure-resource-manager/resource-group-overview.md#resource-groups).
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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

