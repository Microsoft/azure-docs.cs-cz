---
title: Vytvoření účtu Video Indexer v Azure Portal
titlesuffix: Azure Media Services
description: V tomto článku se dozvíte, jak vytvořit účet Video Indexer v Azure Portal.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 0f67b2e37e264febf11f3fa55b4469d392c59712
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815668"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Vytvoření účtu Video Indexer připojeného k Azure

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. Pomocí placené možnosti vytvoříte účet Video Indexer, který je připojený k vašemu předplatnému Azure a účet Azure Media Services. Platíte za indexované minuty a také poplatky související s účtem Media. 

Tento článek ukazuje, jak vytvořit účet Video Indexer propojený s předplatným Azure a účtem Azure Media Services. Téma popisuje kroky pro připojení k Azure pomocí automatického (výchozího) toku. Také ukazuje, jak se připojit k Azure ručně (rozšířené).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure.

    Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

* Doména Azure Active Directory (AD).

    Pokud nemáte doménu služby Azure AD, vytvořte tuto doménu s vaším předplatným Azure. Další informace najdete v tématu [Správa vlastních názvů domén v Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Uživatel v doméně Azure AD s rolí **správce aplikace** Tento člen budete používat při připojování účtu Video Indexer k Azure.

    Tento uživatel by měl být uživatelem Azure AD, který má pracovní nebo školní účet, ne osobní účet, jako je například outlook.com, live.com nebo hotmail.com.

    ![Všichni uživatelé AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Další předpoklady pro automatický tok

Uživatel a člen v doméně služby Azure AD. Tento člen budete používat při připojování účtu Video Indexer k Azure.

Tento uživatel by měl být členem v předplatném Azure pomocí role **vlastníka** nebo role **Správce přístupu** **přispěvatele** i uživatele. Uživatel může být přidán dvakrát s 2 rolemi. Jednou s přispěvatelem a jednou u správce přístupu uživatele.

![řízení přístupu](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Další požadavky na ruční tok

Zaregistrujte poskytovatele prostředků EventGrid pomocí Azure Portal.

V [Azure Portal](https://portal.azure.com/)přejít na předplatná-> [předplatné]->**ResourceProviders**. 

Vyhledejte **Microsoft. Media** a **Microsoft. EventGrid**. Pokud není ve stavu registrováno, klikněte na **zaregistrovat**. Registrace může trvat několik minut.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Připojení k Azure

> [!NOTE]
> Pokud vaše předplatné Azure používá vícefaktorové ověřování založené na certifikátech, je velmi důležité, abyste na zařízení s nainstalovanými požadovanými certifikáty prováděli následující kroky.

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.

2. Klikněte na tlačítko **vytvořit nový účet** :

    ![připojení k Azure](./media/create-account/connect-to-azure.png)

3. Když se zobrazí seznam předplatných, vyberte předplatné, které chcete použít.

    ![připojení Video Indexer k Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Vyberte oblast Azure z podporovaných umístění: Západní USA 2, Severní Evropa nebo Východní Asie.
5. V části **Azure Media Services účet**vyberte jednu z následujících možností:

    * Pokud chcete vytvořit nový účet Media Services, vyberte **vytvořit novou skupinu prostředků**. Zadejte název vaší skupiny prostředků.

        Azure vytvoří nový účet v předplatném, včetně nového účtu Azure Storage. Váš nový Media Services účet má výchozí počáteční konfiguraci s koncovým bodem streamování a 10 rezervovaných jednotek S3.
    * Pokud chcete použít existující účet Media Services, vyberte **použít existující prostředek**. V seznamu účty vyberte svůj účet.

        Váš účet Media Services musí mít stejnou oblast jako váš Video Indexer účet. 

        > [!NOTE]
        > Pro minimalizaci doby trvání indexování a nízké propustnosti se důrazně doporučuje upravit typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) na **10 rezervovaných** jednotek v účtu Media Services. Pokud [chcete změnit rezervované jednotky](../previous/media-services-portal-scale-media-processing.md), přečtěte si téma použití portálu.

    * Pokud chcete připojení nakonfigurovat ručně, klikněte na odkaz **Přepnout na ruční konfiguraci** .

        Podrobné informace najdete v části věnované [ručnímu připojení k Azure](#connect-to-azure-manually-advanced-option) (rozšířené možnosti) níže.
6. Až skončíte, klikněte na **připojit**. Tato operace může trvat až několik minut. 

    Po připojení k Azure se nový účet Video Indexer zobrazí v seznamu účtů:

    ![nový účet](./media/create-account/new-account.png)

7. Přejít na nový účet

## <a name="connect-to-azure-manually-advanced-option"></a>Ruční připojení k Azure (rozšířená volba)

Pokud se připojení k Azure nepovedlo, můžete se pokusit problém vyřešit ručním připojením.

> [!NOTE]
> Ve stejné oblasti se důrazně doporučuje mít následující tři účty: účet Video Indexer, ke kterému se připojujete pomocí účtu Media Services, a také účet úložiště Azure, který je připojený ke stejnému účtu Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Vytvoření a konfigurace účtu Media Services

1. Pomocí webu [Azure](https://portal.azure.com/) Portal vytvořte účet Azure Media Services, jak je popsáno v tématu [Vytvoření účtu](../previous/media-services-portal-create-account.md).

    Při vytváření účtu úložiště pro účet Media Services vyberte **StorageV2** pro druh účtu a **geograficky redundantní (GRS)** pro pole replikace.

    ![nový účet AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Nezapomeňte zapsat Media Services názvů prostředků a účtů. Budete ho potřebovat pro kroky v další části.

2. V účtu Media Services, který jste vytvořili, upravte typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) na **10 jednotek rezervovaných v S3** . Pokud [chcete změnit rezervované jednotky](../previous/media-services-portal-scale-media-processing.md), přečtěte si téma použití portálu.
3. Než budete moct videa přehrávat ve webové aplikaci Video Indexer, musíte spustit výchozí **koncový bod streamování** nového účtu Media Services.

    V novém Media Services účtu klikněte na **koncové body streamování**. Vyberte koncový bod streamování a stiskněte spustit.

    ![nový účet AMS](./media/create-account/create-ams-account2.png)

4. Aby bylo možné Video Indexer ověřit pomocí rozhraní Media Services API, je nutné vytvořit aplikaci služby Active Directory. Následující kroky vás provedou procesem ověřování Azure AD popsaným v tématu [Začínáme s ověřováním Azure AD pomocí Azure Portal](../previous/media-services-portal-get-started-with-aad.md):

    1. V novém Media Services účtu vyberte přístup přes **rozhraní API**.
    2. Vyberte [metodu ověřování instančního objektu](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Získejte ID klienta a tajný klíč klienta, jak je popsáno v části [získání ID klienta a tajného klíče klienta](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) .

        Po výběru **Možnosti**->**klíče**, přidání **popisu**, stisknutí tlačítka **Uložit**se hodnota klíče naplní.

        Pokud klíč vyprší, vlastník účtu bude muset kontaktovat Video Indexer podporu, aby se klíč obnovil.

        > [!NOTE]
        > Nezapomeňte zapsat klíčovou hodnotu a ID aplikace. Budete ho potřebovat pro kroky v další části.

### <a name="connect-manually"></a>Ruční připojení

V dialogovém okně **připojit video indexer k předplatnému Azure** stránky [video indexer](https://www.videoindexer.ai/) vyberte odkaz **Přepnout na ruční konfiguraci** .

V dialogovém okně zadejte následující informace:

|Nastavení|Popis|
|---|---|
|Oblast účtu Video Indexeru|Název oblasti účtu Video Indexer. Pro zajištění lepšího výkonu a snížení nákladů se důrazně doporučuje zadat název oblasti, kde se nachází Azure Media Services prostředek a Azure Storage účet. |
|Tenant Azure Active Directory (AAD)|Název tenanta Azure AD, například "contoso.onmicrosoft.com". Informace o tenantovi lze získat z Azure Portal. Umístěte ukazatel myši na jméno přihlášeného uživatele v pravém horním rohu. Vyhledá jméno napravo od **domény**.|
|ID předplatného|Předplatné Azure, v rámci kterého se má vytvořit připojení ID předplatného se dá načíst z Azure Portal. Na levém panelu klikněte na **všechny služby** a vyhledejte "předplatná". Vyberte **předplatná** a v seznamu předplatných vyberte požadované ID.|
|Název skupiny prostředků Azure Media Services|Název skupiny prostředků, ve které jste vytvořili Media Services účet.|
|Název prostředku mediální služby|Název účtu Azure Media Services, který jste vytvořili v předchozí části.|
|ID aplikace|ID aplikace Azure AD (s oprávněním pro zadaný účet Media Services), který jste vytvořili v předchozí části.|
|Klíč aplikace|Klíč aplikace služby Azure AD, který jste vytvořili v předchozí části. |

## <a name="considerations"></a>Požadavky

Platí následující Azure Media Services související s požadavky:

* Pokud se připojíte automaticky, zobrazí se ve vašem předplatném Azure nová skupina prostředků, účet Media Services a účet úložiště.
* Pokud se připojíte automaticky, Video Indexer nastaví **rezervované jednotky** médií na 10 jednotek S3:

    ![Rezervované jednotky Media Services](./media/create-account/ams-reserved-units.png)

* Pokud se připojíte ke stávajícímu účtu Media Services, Video Indexer nemění existující konfiguraci **rezervovaných jednotek** médií.

   V závislosti na plánovaném zatížení možná budete muset upravit typ a počet rezervovaných jednotek médií. Mějte na paměti, že pokud je vaše zatížení vysoké a nemáte dost jednotek nebo rychlostí, může zpracování videí způsobit selhání s časovým limitem.

* Pokud se připojíte k novému účtu Media Services, Video Indexer automaticky spustí výchozí **koncový bod streamování** :

    ![Koncový bod streamování služby Media Services](./media/create-account/ams-streaming-endpoint.png)

    Koncové body streamování mají značný čas spuštění. Proto může trvat několik minut od okamžiku, kdy jste svůj účet připojili k Azure, dokud vaše videa nebude možné streamovat a sledovat ve webové aplikaci Video Indexer.

* Pokud se připojíte ke stávajícímu účtu Media Services, Video Indexer nemění výchozí konfiguraci koncového bodu streamování. Pokud nepoužíváte žádný **koncový bod streamování**, nebudete moct sledovat videa z tohoto účtu Media Services nebo v video indexer.

## <a name="next-steps"></a>Další postup

Pomocí pokynů v části můžete programově spolupracovat s vaším zkušebním účtem a/nebo s účty Video Indexer, které jsou k Azure připojené. postupujte podle těchto pokynů: [Použijte rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejného uživatele Azure AD, kterého jste použili při připojování k Azure.


