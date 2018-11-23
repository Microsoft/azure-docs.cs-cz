---
title: Správa účet služby Video Indexer
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak spravovat připojení k Azure účet služby Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 43e08298618f059ff706d11ce6bbb3d486642872
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292212"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Správa připojení k Azure účet služby Video Indexer

Tento článek ukazuje, jak spravovat účet služby Video Indexer, který je připojený ke svému předplatnému Azure a účet Azure Media Services.

> [!NOTE]
> Musíte být vlastníkem účtu Video Indexer účtu udělat změny konfigurace, které jsou popsané v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Připojení k Azure, váš účet služby Video Indexer, jak je popsáno v [připojení k Azure](connect-to-azure.md). 

Ujistěte se, že chcete postupovat podle [požadavky](connect-to-azure.md#prerequisites) a zkontrolujte [aspekty](connect-to-azure.md#considerations) v následujícím článku.

## <a name="examine-account-settings"></a>Zkontrolujte nastavení účtu

Tento oddíl se zabývá nastavení váš účet služby Video Indexer.

Chcete-li zobrazit nastavení:

1. Klikněte na ikonu uživatele v pravém horním rohu a vyberte **nastavení**.

    ![Nastavení](./media/manage-account-connected-to-azure/select-settings.png)

2. Na **nastavení** stránky, vyberte **účet** kartu.

Pokud váš účet služby video Indexer je připojená k Azure, najdete tady:

* Název základního účtu Azure Media Services.
* Počet úlohách indexování spuštěná a zařazeny do fronty.
* Počet a typ přidělené rezervovaných jednotek.

Pokud váš účet potřebuje některé změny, zobrazí se příslušné chyby a upozornění o konfiguraci vašeho účtu na **nastavení** stránky. Zprávy obsahují odkazy na přesné místa na webu Azure portal, kde je potřeba provést změny. Další informace najdete v tématu [chyby a upozornění](#errors-and-warnings) v následující části.

## <a name="auto-scale-reserved-units"></a>Automatické škálování rezervované jednotky

**Nastavení** stránky umožňuje, aby nastavení automatického škálování o rezervované jednotky médií (RU). Pokud je daná možnost **na**, můžete přidělit maximální počet jednotek ru a ujistěte se, že Video Indexer zastavení/spuštění ru automaticky. Pomocí této možnosti neplaťte peníze navíc za dobu nečinnosti, ale také nechcete čekat indexování úloh dokončete delší dobu při indexování je vysoké.

Automatické škálování nejsou adekvátní pod 1 RU nebo nad výchozí omezení účtu Media Services. Pokud chcete zvýšit limit, vytvořte žádost o službu. Informace o omezení a kvóty a tom, jak vytvořit lístek podpory najdete v tématu [kvóty a omezení](../../media-services/previous/media-services-quotas-and-limitations.md).

![Registrace](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Chyby a upozornění

Pokud váš účet potřebuje některé změny, se zobrazí relevantní chyby a upozornění o konfiguraci vašeho účtu na **nastavení** stránky. Zprávy obsahují odkazy na přesné místa na webu Azure portal, kde je potřeba provést změny. Tato část poskytuje další podrobnosti o chybě a upozornění.

* Event Grid

    Je nutné provést registraci poskytovatele prostředků EventGrid pomocí webu Azure portal. V [webu Azure portal](https://portal.azure.com/), přejděte na stránku **předplatná** > [předplatné] > **ResourceProviders** > **Microsoft.EventGrid**. Pokud není v **registrované** stavu, klikněte na tlačítko **zaregistrovat**. Trvá několik minut, než se registrace. 

* Koncový bod streamování

    Ujistěte se, že podkladového účtu Media Services má výchozí **koncový bod streamování** ve spuštěném stavu. V opačném případě nebude možné sledovat videa z tohoto účtu Media Services nebo v Video Indexer.

* Rezervované jednotky médií 

    Je třeba přiřadit rezervovaných jednotek médií pro váš prostředek mediálních služeb v pořadí pro index videa. Pro zajištění optimálního výkonu indexování doporučuje se přidělit minimálně 10 rezervovaných jednotek S3. Informace o cenách najdete v části Nejčastější dotazy týkající [ceny služby Media Services](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="next-steps"></a>Další postup

Můžete pracovat prostřednictvím kódu programu pomocí zkušebního účtu nebo s Video Indexer účty, které jsou připojené do azure pomocí následujících pokynů: [pomocí rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejné uživatele Azure AD, který jste použili při připojování ke službě Azure.
