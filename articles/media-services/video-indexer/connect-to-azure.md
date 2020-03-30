---
title: Vytvoření účtu Video Indexer připojeného k Azure
titleSuffix: Azure Media Services
description: Přečtěte si, jak vytvořit účet Video Indexer připojený k Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 7ec8004fa0a54265962e79320c02287b2fcf814a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499913"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Vytvoření účtu Video Indexer připojeného k Azure

Při vytváření účtu Video Indexer si můžete vybrat bezplatný zkušební účet (kde získáte určitý počet volných minut indexování) nebo placenou možnost (kde nejste omezeni kvótou). S bezplatnou zkušební verzi, Video Indexer poskytuje až 600 minut zdarma indexování pro uživatele webových stránek a až 2400 minut zdarma indexování pro uživatele ROZHRANÍ API. S placenou možností si vytvoříte účet Video Indexer, který je připojený k vašemu předplatnému Azure a účtu Azure Media Services. Platíte za minuty indexované, stejně jako poplatky související s mediálním účtem.

Tento článek ukazuje, jak vytvořit účet Video Indexer, který je propojený s předplatným Azure a účtem Azure Media Services. Téma obsahuje postup pro připojení k Azure pomocí automatického (výchozího) toku. Také ukazuje, jak se připojit k Azure ručně (pokročilé).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure.

    Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

* Doména Azure Active Directory (Azure AD).

    Pokud nemáte doménu Azure AD, vytvořte tuto doménu pomocí předplatného Azure. Další informace najdete [v tématu Správa názvů vlastních domén ve službě Azure AD.](../../active-directory/users-groups-roles/domains-manage.md)

* Uživatel ve vaší doméně Azure AD s rolí **správce aplikace.** Tento člen použijete při připojování účtu Video Indexer k Azure.

    Tento uživatel by měl být uživatel Azure AD s pracovním nebo školním účtem. Nepoužívejte osobní účet, například outlook.com, live.com nebo hotmail.com.

    ![všichni uživatelé AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Další předpoklady pro automatický tok

* Uživatel a člen ve vaší doméně Azure AD.

    Tento člen použijete při připojování účtu Video Indexer k Azure.

    Tento uživatel by měl být členem vašeho předplatného Azure s rolí **vlastníka** nebo s rolemi **přispěvatele** a **správce přístupu uživatelů.** Uživatele lze přidat dvakrát se dvěma rolemi. Jednou s přispěvatelem a jednou s uživatelským správcem přístupu.

    ![řízení přístupu](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Další předpoklady pro ruční tok

* Zaregistrujte poskytovatele prostředků EventGrid pomocí portálu Azure.

    Na [webu Azure Portal](https://portal.azure.com/)přejděte na **Subscriptions**->[subscription]->**ResourceProviders**.

    Vyhledejte **microsoft.media** a **Microsoft.EventGrid**. Pokud ne ve stavu "Registrováno", klepněte na **tlačítko Registrovat**. Registrace trvá několik minut.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Připojení k Azure

> [!NOTE]
> Pokud vaše předplatné Azure používá vícefaktorové ověřování založené na certifikátech, je důležité provést následující kroky na zařízení, které má nainstalované požadované certifikáty.

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.

2. Vyberte tlačítko **Vytvořit nový účet:**

    ![Vytvořit nový účet Video Indexer](./media/create-account/connect-to-azure.png)

3. Po zobrazení seznamu odběrů vyberte předplatné, které chcete použít.

    ![Připojení video indexeru k Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Vyberte oblast Azure z podporovaných umístění: Západní USA 2, Severní Evropa nebo Východní Asie.
5. V části **Účet Mediální služby Azure**zvolte jednu z těchto možností:

    * Chcete-li vytvořit nový účet Služby Media Services, vyberte **možnost Vytvořit novou skupinu prostředků**. Zadejte název skupiny prostředků.

        Azure vytvoří váš nový účet ve vašem předplatném, včetně nového účtu Azure Storage. Nový účet Služby Media Services má výchozí počáteční konfiguraci s koncovým bodem streamování a 10 vyhrazenými jednotkami S3.
    * Chcete-li použít existující účet služby Media Services, vyberte **použít existující prostředek**. Ze seznamu účtů vyberte svůj účet.

        Váš účet Media Services musí mít stejnou oblast jako účet Video Indexer.

        > [!NOTE]
        > Chcete-li minimalizovat dobu trvání indexování a nízkou propustnost, důrazně doporučujeme upravit typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) v účtu mediálních služeb na **10 rezervovaných jednotek S3**. Viz [Změna rezervovaných jednotek pomocí portálu](../previous/media-services-portal-scale-media-processing.md).

    * Chcete-li připojení nakonfigurovat ručně, vyberte odkaz **Přepnout na ruční konfiguraci.**

        Podrobné informace najdete v části [Připojení k Azure ručně](#connect-to-azure-manually-advanced-option) (upřesnit možnost), která následuje.
6. Až budete hotovi, zvolte **Připojit**. Tato operace může trvat až několik minut.

    Po připojení k Azure se váš nový účet Video Indexer zobrazí v seznamu účtů:

    ![nový účet](./media/create-account/new-account.png)

7. Přejděte na nový účet.

## <a name="connect-to-azure-manually-advanced-option"></a>Ruční připojení k Azure (upřesňující možnost)

Pokud se připojení k Azure nezdařilo, můžete se pokusit problém vyřešit ručním připojením.

> [!NOTE]
> Důrazně doporučujeme mít následující tři účty ve stejné oblasti: účet Video Indexer, který propojujete s účtem Media Services, a účet úložiště Azure připojený ke stejnému účtu Mediálních služeb.

### <a name="create-and-configure-a-media-services-account"></a>Vytvoření a konfigurace účtu Mediálních služeb

1. Na webu [Azure](https://portal.azure.com/) Portal vytvořte účet Azure Media Services, jak je popsáno v části [Vytvoření účtu](../previous/media-services-portal-create-account.md).

    Při vytváření účtu úložiště pro účet Mediální služby vyberte **možnost StorageV2** pro typ účtu a **geograficky redundantní (GRS)** pro replikační pole.

    ![Nový účet AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Nezapomeňte zapsat názvy prostředků a účtů mediálních služeb. Budete je potřebovat pro kroky v další části.

2. Upravte typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) na **10 rezervovaných jednotek S3** v účtu Mediálních služeb, který jste vytvořili. Viz [Změna rezervovaných jednotek pomocí portálu](../previous/media-services-portal-scale-media-processing.md).
3. Než budete moci přehrávat videa ve webové aplikaci Video Indexer, musíte spustit výchozí **koncový bod streamování** nového účtu Mediální služby.

    V novém účtu Media Services vyberte **položku Koncové body streamování**. Pak vyberte koncový bod streamování a stiskněte tlačítko start.

    ![Nový účet AMS](./media/create-account/create-ams-account2.png)

4. Aby se indexer videa mohl ověřit pomocí rozhraní API mediálních služeb, je třeba vytvořit aplikaci AD. Následující kroky vás provedou procesem ověřování Azure AD popsaným v [části Začínáme s ověřováním Azure AD pomocí webu Azure Portal](../previous/media-services-portal-get-started-with-aad.md):

    1. V novém účtu Media Services vyberte **možnost Přístup k rozhraní API**.
    2. Vyberte [metodu ověřování instančního objektu](../previous/media-services-portal-get-started-with-aad.md).
    3. Získání ID klienta a tajného klíče klienta

        Po **výběru**->nastavení**kláves**přidejte **popis**, stiskněte **tlačítko Uložit**a hodnota klíče se naplní.

        Pokud platnost klíče vyprší, vlastník účtu bude muset kontaktovat podporu video indexeru, aby klíč obnovil.

        > [!NOTE]
        > Ujistěte se, že jste si zapsali hodnotu klíče a ID aplikace. Budete ji potřebovat pro kroky v další části.

### <a name="connect-manually"></a>Ruční připojení

V **dialogovém okně Connect Video Indexer na** stránce předplatného Azure na stránce [Video Indexer](https://www.videoindexer.ai/) vyberte odkaz **Přepnout na ruční konfiguraci.**

V dialogovém okně zadejte následující informace:

|Nastavení|Popis|
|---|---|
|Oblast účtu Video Indexer|Název oblasti účtu Video Indexer. Pro lepší výkon a nižší náklady se důrazně doporučuje zadat název oblasti, kde jsou umístěny prostředky Azure Media Services a účet Azure Storage. |
|Klient Azure AD|Název klienta Azure AD, například "contoso.onmicrosoft.com". Informace o tenantovi se můžou načítat z webu Azure Portal. Umístěte kurzor na jméno přihlášeného uživatele v pravém horním rohu. Vyhledejte název napravo od **domény**.|
|ID předplatného|Předplatné Azure, pod kterým by mělo být toto připojení vytvořeno. ID předplatného lze načíst z webu Azure Portal. V levém panelu vyberte **Všechny služby** a vyhledejte "odběry". Vyberte **Předplatná** a ze seznamu předplatných vyberte požadované ID.|
|Název skupiny prostředků Azure Media Services|Název skupiny prostředků, ve které jste vytvořili účet Mediální chslužeb.|
|Název prostředku mediální služby|Název účtu Azure Media Services, který jste vytvořili v předchozí části.|
|ID aplikace|ID aplikace Azure AD (s oprávněními pro zadaný účet Media Services), které jste vytvořili v předchozí části.|
|Klíč aplikace|Klíč aplikace Azure AD, který jste vytvořili v předchozí části. |

## <a name="considerations"></a>Požadavky

Platí následující důležité informace související s Azure Media Services:

* Pokud se připojíte automaticky, zobrazí se ve vašem předplatném Azure nová skupina prostředků, účet Mediálních služeb a účet úložiště.
* Pokud se připojíte automaticky, video indexer nastaví **rezervované jednotky** médií na 10 jednotek S3:

    ![Rezervované jednotky mediálních služeb](./media/create-account/ams-reserved-units.png)

* Pokud se připojíte k existujícímu účtu Služby Media Services, program Video Indexer nezmění stávající konfiguraci **rezervovaných jednotek** médií.

   Možná budete muset upravit typ a počet rezervovaných jednotek médií podle plánovaného zatížení. Mějte na paměti, že pokud je vaše zatížení vysoké a nemáte dostatek jednotek nebo rychlosti, zpracování videí může mít za následek selhání časového omezení.

* Pokud se připojíte k novému účtu Služby Media Services, video indexer automaticky spustí výchozí **koncový bod streamování** v něm:

    ![Koncový bod streamování služby Media Services](./media/create-account/ams-streaming-endpoint.png)

    Koncové body streamování mají značnou dobu spuštění. Proto může trvat několik minut od okamžiku, kdy jste připojili svůj účet k Azure, dokud vaše videa můžete streamovat a sledovat ve webové aplikaci Video Indexer.

* Pokud se připojíte k existujícímu účtu Služby Media Services, program Video Indexer nezmění výchozí konfiguraci koncového bodu streamování. Pokud není spuštěn **koncový bod streamování**, nemůžete sledovat videa z tohoto účtu Mediální služby nebo v video indexeru.

## <a name="next-steps"></a>Další kroky

Můžete programově pracovat se svým zkušebním účtem a/nebo s účty Video Indexer, které jsou připojené k Azure podle pokynů v části: [Použití api](video-indexer-use-apis.md).

Měli byste použít stejného uživatele Azure AD, který jste použili při připojování k Azure.