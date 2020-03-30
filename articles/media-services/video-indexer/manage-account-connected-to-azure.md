---
title: Správa účtu videoindexeru
titleSuffix: Azure Media Services
description: Přečtěte si, jak spravovat účet Video Indexer připojený k Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499665"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Správa účtu Video Indexer připojeného k Azure

Tento článek ukazuje, jak spravovat účet Video Indexer, který je připojený k předplatnému Azure a účtu Azure Media Services.

> [!NOTE]
> Musíte být vlastník účtu Video Indexer provést úpravy konfigurace účtu popsané v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Připojte svůj účet Video Indexer k Azure, jak je popsáno v [connected to Azure](connect-to-azure.md).

Ujistěte se, že [postupujte požadavky](connect-to-azure.md#prerequisites) a přečtěte si [aspekty](connect-to-azure.md#considerations) v článku.

## <a name="examine-account-settings"></a>Zkontrolovat nastavení účtu

Tato část zkoumá nastavení účtu Video Indexer.

Zobrazení nastavení:

1. Klikněte na ikonu uživatele v pravém horním rohu a vyberte **Nastavení**.

    ![Nastavení v video indexeru](./media/manage-account-connected-to-azure/select-settings.png)

2. Na stránce **Nastavení** vyberte kartu **Účet.**

Pokud je váš účet Vinár připojený k Azure, uvidíte následující věci:

* Název základního účtu Azure Media Services.
* Počet úloh indexování spuštěných a zařazených do fronty.
* Počet a typ přidělených rezervovaných jednotek.

Pokud váš účet potřebuje nějaké úpravy, zobrazí se na stránce **Nastavení** relevantní chyby a upozornění týkající se konfigurace účtu. Zprávy obsahují odkazy na přesná místa na webu Azure Portal, kde je potřeba provést změny. Další informace naleznete v následující části [chyb a upozornění.](#errors-and-warnings)

## <a name="repair-the-connection-to-azure"></a>Oprava připojení k Azure

V dialogovém okně **Aktualizovat připojení k Mediální službě Azure** na stránce Video [Indexer](https://www.videoindexer.ai/) budete vyzváni k zadání hodnot pro následující nastavení:

|Nastavení|Popis|
|---|---|
|ID předplatného Azure|ID předplatného lze načíst z webu Azure Portal. Klikněte na **Všechny služby** v levém panelu a vyhledejte "předplatné". Vyberte **Předplatná** a ze seznamu předplatných vyberte požadované ID.|
|Název skupiny prostředků Azure Media Services|Název skupiny prostředků, ve které jste vytvořili účet Mediální chslužeb.|
|ID aplikace|ID aplikace Azure AD (s oprávněními pro zadaný účet Media Services), které jste vytvořili pro tento účet Video Indexer. <br/><br/>Pokud chcete získat ID aplikace, přejděte na Portál Azure. V části Účet mediálních služeb zvolte svůj účet a přejděte na **adresu API Access**. Vyberte **Připojit k rozhraní API mediálních služeb pomocí hlavního servisního objektu** -> Azure**AD App**. Zkopírujte příslušné parametry.|
|Klíč aplikace|Klíč aplikace Azure AD přidružený k účtu mediální chmediální služby, který jste zadali výše. <br/><br/>Pokud chcete získat klíč aplikace, přejděte na Portál Azure. V části Účet mediálních služeb zvolte svůj účet a přejděte na **adresu API Access**. Vyberte **možnost Připojit k rozhraní API mediálních služeb s hlavním objektem** -> služby Spravovat certifikáty**aplikací** -> **& tajných kódů**. Zkopírujte příslušné parametry.|

## <a name="autoscale-reserved-units"></a>Rezervované jednotky automatického škálování

Stránka **Nastavení** umožňuje nastavit automatické škálování rezervovaných jednotek médií (RU). Pokud je tato možnost **zapnutá**, můžete přidělit maximální počet ru a ujistěte se, že video indexer zastaví/spustí ru automaticky. S touto volbou neplatíte peníze navíc za dobu nečinnosti, ale také nečekejte na dokončení úloh indexování po dlouhou dobu, kdy je zatížení indexování vysoké.

Automatické škálování se neškáluje pod 1 RU nebo nad výchozí limit účtu Mediální ch služeb. Chcete-li zvýšit limit, vytvořte žádost o službu. Informace o kvótách a omezeních a o tom, jak otevřít lístek podpory, naleznete v [tématu Kvóty a omezení](../../media-services/previous/media-services-quotas-and-limitations.md).

![Automatické škálování rezervovaných jednotek Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Chyby a varování

Pokud váš účet potřebuje nějaké úpravy, zobrazí se na stránce **Nastavení** relevantní chyby a upozornění týkající se konfigurace účtu. Zprávy obsahují odkazy na přesná místa na webu Azure Portal, kde je potřeba provést změny. Tato část obsahuje další podrobnosti o chybových a varovných zprávách.

* EventGrid

    Je třeba zaregistrovat zprostředkovatele prostředků EventGrid pomocí portálu Azure. Na [webu Azure Portal](https://portal.azure.com/)přejděte na **předplatná** > [předplatné] > **Zprostředkovatelé** > prostředků**Microsoft.EventGrid**. Pokud ne ve stavu **Registrováno,** vyberte **registrovat**. Registrace trvá několik minut.

* Koncový bod streamování

    Ujistěte se, že podkladový účet Služby Media Services má výchozí **koncový bod streamování** v spuštěném stavu. V opačném případě nelze sledovat videa z tohoto účtu Mediální služby nebo v video indexeru.

* Rezervované jednotky médií

    Chcete-li indexovat videa, je nutné přidělit rezervované jednotky médií v prostředku mediální služby. Pro optimální výkon indexování se doporučuje přidělit alespoň 10 rezervovaných jednotek S3. Informace o cenách naleznete v části Nejčastější dotazy na stránce [s cenami mediálních služeb.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="next-steps"></a>Další kroky

S účtemi zkušebníverze účtu nebo videoindexeru, které jsou připojené k Azure, můžete programově pracovat podle pokynů v části: [Použití řešení API](video-indexer-use-apis.md).

Použijte stejného uživatele Azure AD, který jste použili při připojování k Azure.
