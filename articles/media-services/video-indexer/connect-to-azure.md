---
title: Na webu Azure Portal vytvořit účet Video Indexer
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak vytvořit účet služby Video Indexer na webu Azure Portal.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: f220aee3fa0d9a79723383fc31fec0eed2554bb4
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833682"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Vytvoření připojení k Azure účet služby Video Indexer

Při vytváření účtu Video Indexeru můžete zvolit účet bezplatné zkušební verze (ve kterém získáte určitý počet minut indexování zdarma) nebo placenou variantu (ve které nejste omezení kvótou). V bezplatné zkušební verzi Video Indexer poskytuje až 600 minut bezplatného indexování pro uživatele webu a až 2400 minut bezplatného indexování pro uživatele rozhraní API. S placenou variantu vytvoříte účet služby Video Indexer, který je připojený ke svému předplatnému Azure a účet Azure Media Services. Platíte za indexované minuty a také poplatky související s účtem Media. 

Tento článek ukazuje, jak vytvořit účet služby Video Indexer, který je propojený s předplatným Azure a účet Azure Media Services. Téma obsahuje postup pro připojení k Azure pomocí toku automaticky (výchozí). Také ukazuje, jak se připojit k Azure ručně (Upřesnit).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure.

    Pokud nemáte ještě předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

* Domény služby Azure Active Directory (AD).

    Pokud nemáte doménu služby Azure AD, vytvořte tuto doménu ve vašem předplatném Azure. Další informace najdete v tématu [Správa vlastních názvů domén v Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Uživatel a člen ve vaší doméně Azure AD. Tento člen budete používat při připojování váš účet služby Video Indexer pro Azure.

    Tento uživatel by měl být uživatele služby Azure AD pomocí svého pracovního nebo školního účtu, nikoli pomocí osobního účtu, jako jsou outlook.com, live.com nebo hotmail.com.

    ![všichni uživatelé AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Další požadavky pro automatické toku

Uživatel a člen ve vaší doméně Azure AD. Tento člen budete používat při připojování váš účet služby Video Indexer pro Azure.

Tento uživatel by měl být členem ve vašem předplatném Azure pomocí **vlastníka** role, nebo obojí **Přispěvatel** a **správce uživatelských přístupů** role. Uživatele můžete přidat dvakrát, s 2 rolemi. Jednou pomocí a jednou pro uživatel s oprávněním správce přístup přispěvatele.

![Řízení přístupu](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Další požadavky pro ruční toku

Registrace poskytovatele prostředků EventGrid pomocí webu Azure portal.

V [webu Azure portal](https://portal.azure.com/), přejděte na stránku **předplatná**-> [předplatné] ->**ResourceProviders**. 

Vyhledejte **Microsoft.Media** a **Microsoft.EventGrid**. Pokud není ve stavu "Registrováno", klikněte na tlačítko **zaregistrovat**. Trvá několik minut, než se registrace.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Připojení k Azure

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.

2. Klikněte na **připojit se k Azure** tlačítka:

    ![Připojení k Azure](./media/create-account/connect-to-azure.png)

3. Jakmile se zobrazí seznam předplatných, vyberte předplatné, které chcete použít.

    ![Video Indexer připojení k Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Vyberte oblast Azure z podporovaných umístění: západní USA 2, Severní Evropa nebo jihovýchodní Asie.
5. V části **účtu Azure Media Services**, zvolte jednu z těchto možností:

    * Chcete-li vytvořit nový účet Media Services, vyberte **vytvořit novou skupinu prostředků**. Zadejte název vaší skupiny prostředků.

        Azure vytvoří nový účet v rámci vašeho předplatného, včetně nového účtu úložiště Azure. Výchozí počáteční konfiguraci s koncový bod streamování a 10 rezervovaných jednotek S3 má váš nový účet Media Services.
    * Chcete-li použít existující účet Media Services, vyberte **používat stávající prostředek**. Ze seznamu účtů vyberte svůj účet.

        Stejné oblasti jako váš účet služby Video Indexer musí mít váš účet Media Services. 

        > [!NOTE]
        > Chcete-li minimalizovat dobu trvání indexování a Nízká propustnost, důrazně doporučujeme upravit typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) k **10 rezervovaných jednotek S3** ve vašem účtu Media Services. Zobrazit [chcete změnit rezervovaných jednotek, použijte portál](../previous/media-services-portal-scale-media-processing.md).

    * Chcete-li ručně konfigurovat připojení, klikněte na tlačítko **přepněte na ruční konfigurace** odkaz.

        Podrobné informace najdete v tématu [ručně připojit k Azure](#connect-to-azure-manually-advanced-option) v následující části (upřesňující možnosti).
6. Jakmile budete hotovi, zvolte **připojit**. Tato operace může trvat až několik minut. 

    Po připojení k Azure, se zobrazí v seznamu účtů váš nový účet služby Video Indexer:

    ![nový účet](./media/create-account/new-account.png)

7. Přejděte do svého nového účtu služby:

    ![Účet služby video Indexer](./media/create-account/vi-account.png)

## <a name="connect-to-azure-manually-advanced-option"></a>Připojení k Azure ručně (Upřesnit možnosti)

Pokud se nezdařilo připojení k Azure, můžete opakovat pokus o potíží připojení ručně.

### <a name="create-and-configure-a-media-services-account"></a>Vytvoření a konfigurace účtu Azure Media Services

1. Použití [Azure](https://portal.azure.com/) portál k vytvoření účtu Azure Media Services, jak je popsáno v [vytvořit účet](../previous/media-services-portal-create-account.md).

    Při vytváření účtu úložiště pro váš účet Media Services, vyberte **StorageV2** pro druh účtu a **geograficky redundantní (RGS)** pro pole replikace.

    ![nový účet AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Nezapomeňte si poznamenejte si názvy prostředků a účtu Media Services. Je třeba pro kroky v další části.

2. Upravit typ a počet [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md ) k **10 rezervovaných jednotek S3** v účtu Media Services, který jste vytvořili. Zobrazit [chcete změnit rezervovaných jednotek, použijte portál](../previous/media-services-portal-scale-media-processing.md).
3. Před přehrávání vašeho videa ve webové aplikaci Video Indexer, je nutné spustit výchozí **koncový bod streamování** nového účtu Media Services.

    Do nového účtu Media Services, klikněte na tlačítko **koncové body streamování**. Vyberte koncový bod streamování a stiskněte klávesu start.

    ![nový účet AMS](./media/create-account/create-ams-account2.png)

4. Pro Video Indexer k ověření pomocí rozhraní API služby Media Services, který je potřeba vytvořit aplikaci AD. Následující kroky průvodce vás provede procesem ověřování služby Azure AD je popsáno v [Začínáme s ověřováním Azure AD pomocí webu Azure portal](../previous/media-services-portal-get-started-with-aad.md):

    1. Do nového účtu Media Services, vyberte **přístup přes rozhraní API**.
    2. Vyberte [metoda ověřování instančních objektů služby](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Získání ID klienta a tajný kód klienta, jak je popsáno v [získat ID klienta a tajný kód klienta](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) oddílu.

        Po výběru **nastavení**->**klíče**, přidejte **popis**, stiskněte klávesu **Uložit**, naplní hodnoty klíče.

        Pokud vyprší platnost klíče vlastník účtu muset obraťte se na Video Indexer podporu obnovit klíč.

        > [!NOTE]
        > Ujistěte se, že zapište si hodnotu klíče a ID aplikace. Je třeba pro kroky v další části.

### <a name="connect-manually"></a>Připojte se ručně

V **připojení Video Indexer s předplatným Azure** dialog s vaší [Video Indexer](https://www.videoindexer.ai/) stránky, vyberte **přepněte na ruční konfigurace** odkaz.

V dialogovém okně zadejte následující informace:

|Nastavení|Popis|
|---|---|
|Oblast účtu video Indexer|Název oblasti účtu Video Indexer. Pro lepší výkon a nižší náklady důrazně doporučujeme zadat název oblasti, kde se nachází na prostředek služby Azure Media Services a účet služby Azure Storage. |
|Tenant Azure Active Directory (AAD)|Název tenanta Azure AD, například "contoso.onmicrosoft.com". Informace o tenantovi můžete získat z webu Azure portal. Umístěte ukazatel myši nad název přihlášeného uživatele v pravém horním rohu. Vyhledání názvu napravo od **domény**.|
|ID předplatného|Předplatné Azure, ve kterém by měl vytvoří toto připojení. ID předplatného můžete získat z webu Azure portal. Klikněte na **všechny služby** v levém panelu a vyhledejte "předplatné". Vyberte **předplatná** a pak vyberte požadovaného ID z seznam vašich předplatných.|
|Název skupiny prostředků Azure Media Services|Název pro skupinu prostředků, ve které jste vytvořili účet Media Services.|
|Název mediální služby prostředků|Název účtu Azure Media Services, kterou jste vytvořili v předchozí části.|
|ID aplikace|Azure AD ID aplikace (s oprávněními pro zadaný účet Media Services), který jste vytvořili v předchozí části.|
|Klíč aplikace|Klíč aplikace Azure AD, kterou jste vytvořili v předchozí části. |

## <a name="considerations"></a>Požadavky

Platí následující aspekty související služby Azure Media Services:

* Pokud připojení automaticky zobrazí novou skupinu prostředků, účtu Media Services a účet úložiště ve vašem předplatném Azure.
* Když se připojení automaticky Video Indexer nastaví média **rezervovaných jednotek** na 10 jednotek S3:

    ![Jednotky rezervované pro Media Services](./media/create-account/ams-reserved-units.png)

* Pokud se připojíte k existujícímu účtu Media Services, Video Indexer nezmění stávajícího multimediálního **rezervovaných jednotek** konfigurace.

   Můžete potřebovat upravit typ a počet rezervovaných jednotek médií, podle plánované zatížení. Uvědomte si, že pokud vysoké zatížení a nemáte dostatek jednotek nebo rychlost zpracování videa může způsobit chyby časového limitu.

* Pokud se připojíte k nového účtu Media Services, Video Indexer se automaticky spustí výchozí **koncový bod streamování** v ní:

    ![Koncový bod streamování služby Media Services](./media/create-account/ams-streaming-endpoint.png)

    Koncové body streamování mají značné spuštění. Proto může trvat několik minut od okamžiku připojení účtu Azure, dokud videa můžete Streamovat a sledované ve webové aplikaci Video Indexer.

* Pokud se připojíte k existujícímu účtu Media Services, Video Indexer nedojde ke změně výchozí konfigurace koncového bodu streamování. Pokud není žádná spuštění **koncový bod streamování**, nebudete moci sledovat videa z tohoto účtu Media Services nebo v Video Indexer.

## <a name="next-steps"></a>Další postup

Můžete pracovat prostřednictvím kódu programu pomocí zkušebního účtu nebo s Video Indexer účty, které jsou připojené do azure pomocí následujících pokynů: [pomocí rozhraní API](video-indexer-use-apis.md).

Měli byste použít stejné uživatele Azure AD, který jste použili při připojování ke službě Azure.


