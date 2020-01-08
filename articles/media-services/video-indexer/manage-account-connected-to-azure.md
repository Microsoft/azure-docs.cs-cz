---
title: Správa účtu Video Indexer
titleSuffix: Azure Media Services
description: Tento článek ukazuje, jak spravovat účet Video Indexer připojený k Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: f3825f6c9186c5e04807dd3890a14fcc6d370989
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454667"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Správa účtu Video Indexer připojeného k Azure

Tento článek ukazuje, jak spravovat účet Video Indexer, který je připojený k vašemu předplatnému Azure a účet Azure Media Services.

> [!NOTE]
> Musíte být vlastníkem účtu Video Indexer, abyste mohli provádět úpravy konfigurace účtu popsané v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Připojte svůj účet Video Indexer k Azure, jak je popsáno v tématu [připojení k Azure](connect-to-azure.md). 

Ujistěte se, že [splňujete požadavky](connect-to-azure.md#prerequisites) a Projděte si [pokyny](connect-to-azure.md#considerations) v článku.

## <a name="examine-account-settings"></a>Kontrola nastavení účtu

Tato část prověřuje nastavení účtu Video Indexer.

Zobrazení nastavení:

1. Klikněte na ikonu uživatele v pravém horním rohu a vyberte **Nastavení**.

    ![Nastavení](./media/manage-account-connected-to-azure/select-settings.png)

2. Na stránce **Nastavení** vyberte kartu **účet** .

Pokud je váš účet indexeru videí připojený k Azure, zobrazí se následující:

* Název podkladového účtu Azure Media Services.
* Počet spuštěných úloh indexování a zařazených do fronty.
* Počet a typ přidělených rezervovaných jednotek.

Pokud váš účet potřebuje nějaké úpravy, zobrazí se na stránce **Nastavení** relevantní chyby a upozornění týkající se konfigurace vašeho účtu. Zprávy obsahují odkazy na přesná místa v Azure Portal, kde je třeba provést změny. Další informace najdete v části [chyby a upozornění](#errors-and-warnings) , která následují.

## <a name="repair-the-connection-to-azure"></a>Oprava připojení k Azure

V dialogovém okně **aktualizace připojení k Azure Media Services** stránky [video indexer](https://www.videoindexer.ai/) budete požádáni o zadání hodnot pro následující nastavení: 

|Nastavení|Popis|
|---|---|
|ID předplatného Azure|ID předplatného se dá načíst z Azure Portal. Na levém panelu klikněte na **všechny služby** a vyhledejte "předplatná". Vyberte **předplatná** a v seznamu předplatných vyberte požadované ID.|
|Název skupiny prostředků Azure Media Services|Název skupiny prostředků, ve které jste vytvořili Media Services účet.|
|ID aplikace|ID aplikace Azure AD (s oprávněním pro zadaný účet Media Services), který jste vytvořili pro tento Video Indexer účet. <br/><br/>Pokud chcete získat ID aplikace, přejděte na Azure Portal. V části Media Services účet vyberte svůj účet a přejděte na **přístup přes rozhraní API**. Klikněte na **připojit k Media Services rozhraní API s instančním objektem** -> **aplikace Azure AD**. Zkopírujte příslušné parametry.|
|Klíč aplikace|Klíč aplikace služby Azure AD přidružený k vašemu Media Services účtu, který jste zadali výše. <br/><br/>Klíč aplikace získáte tak, že přejdete na Azure Portal. V části Media Services účet vyberte svůj účet a přejděte na **přístup přes rozhraní API**. Klikněte na **připojit k Media Services rozhraní API s instančním objektem** -> **Spravovat** -> **certifikátů aplikací & tajných**kódů. Zkopírujte příslušné parametry.|

## <a name="auto-scale-reserved-units"></a>Jednotky rezervované pro automatické škálování

Stránka **Nastavení** vám umožní nastavit automatické škálování rezervovaných jednotek médií (ru). Pokud je tato možnost **zapnutá**, můžete přidělit maximální počet ru a ujistit se, že video indexer zastaví nebo spustí ru automaticky. Pomocí této možnosti neplatíte za dobu nečinnosti další peníze, ale nečekáte na dokončení úloh indexování, pokud je zatížení indexu vysoké.

Automatické škálování nemění škálu pod 1 RU nebo vyšší než výchozí limit Media Services účtu. Aby bylo možné tento limit zvýšit, vytvořte žádost o služby. Informace o kvótách a omezeních a o tom, jak otevřít lístek podpory, najdete v tématu [kvóty a omezení](../../media-services/previous/media-services-quotas-and-limitations.md).

![Zaregistrujte se](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Chyby a upozornění

Pokud váš účet potřebuje nějaké úpravy, zobrazí se na stránce **Nastavení** relevantní chyby a upozornění týkající se konfigurace vašeho účtu. Zprávy obsahují odkazy na přesná místa v Azure Portal, kde je třeba provést změny. Tato část obsahuje další podrobnosti o chybách a upozorněních.

* Event Grid

    Je nutné zaregistrovat poskytovatele prostředků EventGrid pomocí Azure Portal. V [Azure Portal](https://portal.azure.com/)přejít na **předplatná** > [předplatné] > **ResourceProviders** > **Microsoft. EventGrid**. Pokud není v **zaregistrovaném** stavu, klikněte na **zaregistrovat**. Registrace může trvat několik minut. 

* Koncový bod streamování

    Ujistěte se, že základní účet Media Services má výchozí **koncový bod streamování** v počátečním stavu. V opačném případě nebudete moci sledovat videa z tohoto Media Services účtu nebo v Video Indexer.

* Rezervované jednotky médií 

    Aby bylo možné indexovat videa, musíte v prostředku Media Service přidělit rezervované jednotky médií. Pro zajištění optimálního výkonu indexování doporučujeme přidělit aspoň 10 rezervovaných jednotek S3. Informace o cenách najdete v části Nejčastější dotazy stránky s [cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-steps"></a>Další kroky

Můžete programově spolupracovat s vaším zkušebním účtem a/nebo s účty Video Indexer, které jsou připojené k Azure, podle pokynů v části: [použití rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejného uživatele Azure AD, kterého jste použili při připojování k Azure.
