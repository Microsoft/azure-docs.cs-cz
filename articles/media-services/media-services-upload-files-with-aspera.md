---
title: Nahrávání souborů do účtu Azure Media Services pomocí Aspery | Dokumentace Microsoftu
description: Tento kurz vás provede jednotlivými kroky pro nahrávání souborů do účtu úložiště přidruženého k účtu Media Services pomocí služby **Aspera Server On Demand** v Azure.
services: media-services
documentationcenter: ''
author: johndeu
manager: cfowler
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/17/2017
ms.author: juliako
ms.openlocfilehash: 58fec9746828863c4d0bff223fb6e9fb122256c3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169369"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Nahrávání souborů do účtu Media Services pomocí služby Aspera Server On Demand v Azure

## <a name="overview"></a>Přehled

**Aspera** je software pro vysokorychlostní přenos souborů. **Aspera Server On Demand** pro Azure umožňuje vysokorychlostní nahrávání a stahování velkých souborů přímo do úložiště objektů Azure Blob. Informace o službě **Aspera On Demand** najdete na stránce [Aspera Cloud](http://cloud.asperasoft.com/). 
  
**Aspera Server On Demand** pro Azure se dá koupit na webu [Azure Marketplace](https://azure.microsoft.com/marketplace/). Abyste mohli dokončit nákup služby **Aspera Server On Demand** pro Azure, přihlaste se na webu Azure Marketplace pomocí svého Windows Live ID.

Tento kurz vás provede jednotlivými kroky pro nahrávání souborů do účtu úložiště přidruženého k účtu Media Services pomocí služby **Aspera Server On Demand** v Azure. 

Příklad, který ukazuje, jak používat Azure Functions s Asperou a službou Media Services můžete najít [zde](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest).

>[!NOTE]
>Maximální velikost souboru podporovaná při zpracování pomocí procesorů médií služby Azure Media Services je omezená. Podrobnosti o omezení velikosti souboru najdete v [tomto článku](media-services-quotas-and-limitations.md).
>

## <a name="prerequisites"></a>Požadavky 

Pro absolvování tohoto kurzu potřebujete:

* Windows Live ID
* [Účet Azure](https://azure.microsoft.com). Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
* [Účet Azure Media Services](media-services-portal-create-account.md).

## <a name="purchase-aspera-on-demand-for-azure"></a>Nákup služby Aspera On Demand pro Azure

Po přihlášení na webu Azure Marketplace postupujte podle těchto základních kroků a dokončete nákup služby Aspera On Demand pro Azure.

1. Vyhledejte Asperu a vyberte „Server On Demand“.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. Zkontrolujte plány předplatného a klikněte na „Zaregistrovat“.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Zadejte podrobné informace o předplatném služby Server On Demand.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. Klikněte na **Cenová úroveň** a v dílčím panelu vyberte požadovaný měsíční objem. Na panelu **Podrobnosti plánu** vyberte **OK**. Pak na panelu **Volba cenové úrovně** klikněte na **Vybrat**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. Klikněte na **Právní podmínky** a v dílčím panelu si přečtěte a přijměte právní podmínky. Až si přečtete právní podmínky, klikněte na **Koupit**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. Dokončete nákup kliknutím na **Vytvořit**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. Na řídicím panelu Azure se zobrazí oznámení o zřizování služby.  Po dokončení zřizování najdete nové předplatné vyhledáním názvu služby ve vašich prostředcích. Jakmile službu najdete, poklikejte na ni a spustí se portál pro správu služby.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Spusťte portál pro správu Aspery. Jakmile najdete novou službu Aspera, přístup k portálu pro správu získáte poklikáním na službu.  Otevře se nový panel. Na tomto novém panelu je nutné kliknout na **Název prostředku** nové služby.  Na následujícím snímku obrazovky je název prostředku AsperaTransferDemo. Po kliknutí na název prostředku se otevře nový panel. Na nově otevřeném panelu uvidíte odkaz Správa. Kliknutím na odkaz Správa spustíte portál pro správu Aspery.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. Kliknutím na odkaz Správa se dostanete na registrační stránku. Registrace je nutná pro přístup ke službě.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. V tomto okamžiku byste měli mít přístup k portálu pro správu služby Aspera, na němž můžete vytvořit přístupové klíče, stáhnout klienty a licence Aspery, zobrazit využití a zjistit více o rozhraních API.

    Následující snímek obrazovky ukazuje vytvoření přístupu. 

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    Následující snímek obrazovky ukazuje rozhraní sestav využívání na portálu. 

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Nahrávání souborů pomocí Aspery

1. Stáhněte a nainstalujte klientský software Aspery:
    
    * [Modul plug-in prohlížeče](http://downloads.asperasoft.com/connect2/)
    * [Plně funkční klient](http://downloads.asperasoft.com/en/downloads/2)

2. Proveďte svůj první přenos. Abyste mohli k přenosu pomocí přenosové služby Aspera použít klienta Aspery, je třeba provést následující: 

    1. Vytvořit přístupový klíč pomocí portálu Aspera.  
    2. Stáhnout, nainstalovat a licencovat klienta Aspery (software najdete na portálu Aspera).  

    >[!NOTE]
    >Informace o konfiguraci najdete v příručce klienta Aspery.
    
    3. Načíst některé informace o účtu úložiště přidruženém k účtu Azure Media Services pomocí webu [Azure Portal](https://portal.azure.com/). Konkrétně název, klíč a název kontejneru objektů blob úložiště, do kterého chcete obsah umístit. 

        * Získání informací o úložišti z portálu: vyhledejte svůj účet úložiště, klikněte na Přístupové klíče a zkopírujte název a klíč účtu.
        * Získání názvu kontejneru: vyhledejte svůj účet úložiště, vyberte **Objekty blob** a vyberte název kontejneru, do kterého chcete obsah nahrát. 

    Zde je snímek obrazovky **Správce připojení** klienta Aspery, kde je nutné zadat typ úložiště „Azure“, přihlašovací údaje a kontejner objektů blob.

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>Zdroje a prostředky

V tomto článku byly zmíněny následující zdroje informací. 

* [Modul plug-in prohlížeče Connect](http://downloads.asperasoft.com/connect2/)
* [Příručka modulu Connect](http://downloads.asperasoft.com/en/documentation/8)
* [Klient Aspera](http://downloads.asperasoft.com/en/downloads/2)
* [Příručka klienta](http://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>Další kroky

Teď můžete [zkopírovat objekty blob z účtu úložiště do účtu AMS](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

