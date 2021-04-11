---
title: 'Rychlý Start: integrace účtu Azure Storage s Azure CDN'
description: V tomto rychlém startu se dozvíte, jak používat Azure Content Delivery Network (CDN) k doručování obsahu s vysokou šířkou pásma ukládáním objektů blob do mezipaměti z Azure Storage.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 444639d9e36b67f25ed9b82bd68102273c66ae00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951228"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>Rychlý Start: integrace účtu Azure Storage s Azure CDN

V tomto rychlém startu povolíte [Azure Content Delivery Network (CDN)](cdn-overview.md) pro ukládání obsahu do mezipaměti z Azure Storage. Azure CDN nabízí vývojářům globální řešení pro doručování širokopásmového obsahu. Do mezipaměti může ukládat objekty blob a statický obsah výpočetních instancí ve fyzických uzlech v USA, Evropě, Asii, Austrálii a Jižní Americe.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště poskytuje přístup ke službám Azure Storage. Účet úložiště představuje nejvyšší úroveň oboru názvů pro přístup k jednotlivým součástem služby Azure Storage: Azure Blob Storage, Azure Queue Storage a Azure Table Storage. Další informace najdete v tématu [Seznámení se službou Microsoft Azure Storage](../storage/common/storage-introduction.md).

Pokud chcete vytvořit účet úložiště, musíte být buď správcem služby, nebo spolusprávcem přidruženého předplatného.

1. V Azure Portal v levém horním rohu vyberte **vytvořit prostředek** . Otevře se podokno **Nový**.

1. Vyhledejte **účet úložiště** a v rozevíracím seznamu vyberte **účet úložiště – objekt blob, soubor, tabulka, fronta** . Pak vyberte **vytvořit**:
    
    ![Výběr prostředku úložiště](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. V **podokně vytvořit účet úložiště** zadejte následující podrobnosti:

    | Nastavení | Hodnota | 
    | --- | --- |
    | Podrobnosti o projektu > skupinu prostředků | Vyberte **vytvořit novou** a použijte název *CDNQuickstart-RG*. Pokud dáváte přednost, můžete použít také existující skupinu prostředků. |
    | Podrobnosti instance > název účtu úložiště | Zadejte název účtu, který bude obsahovat jenom 3-24 malých písmen a číslic. Název musí být v rámci Azure jedinečný a v adrese URL, která se používá k adresování prostředků blob, front nebo tabulek pro toto předplatné, se bude jednat o název hostitele. Pokud chcete vyřešit prostředek kontejneru v úložišti objektů blob, použijte identifikátor URI v následujícím formátu: http://*&lt; storageaccountname &gt;*. blob.Core.Windows.NET/*&lt; Container-name &gt;*.
    | Podrobnosti instance > umístění | V rozevíracím seznamu vyberte oblast Azure, kterou máte blízko. |
    
    Ponechte všechny ostatní podrobnosti nastavené na výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

1. Vytvoření účtu úložiště může trvat několik minut. Až se vytváření dokončí, vyberte **Přejít k prostředku** a otevřete stránku účtu úložiště pro další krok.

## <a name="enable-azure-cdn-for-the-storage-account"></a>Povolení Azure CDN pro účet úložiště

1. Na stránce svého účtu úložiště vyberte v nabídce vlevo **BLOB Service**  >  **Azure CDN** . Zobrazí se stránka **Azure CDN**.

    ![Vytvoření koncového bodu CDN](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. V části **Nový koncový bod** zadejte následující informace:

    | Nastavení  | Hodnota |
    | -------- | ----- |
    | **Profil CDN** | Vyberte **vytvořit nový** a zadejte název svého profilu, například *CDN-profil-123*. Profil je kolekce koncových bodů. |
    | **Cenová úroveň** | Vyberte jednu ze **standardních** možností, jako je například **standardní Microsoft**. |
    | **Název koncového bodu CDN** | Zadejte název hostitele koncového bodu, například *CDN-Endpoint-123*. Tento název musí být globálně jedinečný v rámci Azure, protože má přístup k prostředkům uloženým v mezipaměti na adrese URL _&lt; koncového bodu-name &gt;_. azureedge.NET. |
    | **Název počátečního hostitele** | Nový koncový bod CDN používá ve výchozím nastavení název hostitele vašeho účtu úložiště jako server původu. |

1. Vyberte **Vytvořit**. Koncový bod se po vytvoření zobrazí v seznamu koncových bodů.

    ![Nový koncový bod CDN úložiště](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> Pokud chcete pro koncový bod CDN specifikovat pokročilá nastavení konfigurace, jako je například [optimalizaci stahování velkých souborů](cdn-optimization-overview.md#large-file-download), můžete místo toho použít [rozšíření Azure CDN](cdn-create-new-endpoint.md) a vytvořit profil CDN a koncový bod.


## <a name="enable-additional-cdn-features"></a>Povolení dalších funkcí CDN

Na stránce **Azure CDN** účtu úložiště vyberte v seznamu koncový bod CDN a otevřete stránku konfigurace koncového bodu CDN.

Na této stránce můžete povolit další funkce CDN pro doručení, jako je například [komprese](cdn-improve-performance.md), [ukládání řetězců dotazů do mezipaměti](cdn-query-string.md) a [geografické filtrování](cdn-restrict-access-by-country.md). 
    
## <a name="enable-sas"></a>Povolení SAS

Pokud chcete udělit omezený přístup k soukromým kontejnerům úložiště, můžete použít funkci sdíleného přístupového podpisu (SAS) účtu Azure Storage. SAS je identifikátor URI, který uděluje omezená přístupová práva k vašim prostředkům Azure Storage bez odhalení vašeho klíče účtu. Další informace najdete v článku věnovaném [použití Azure CDN s SAS](cdn-sas-storage-support.md).

## <a name="access-cdn-content"></a>Přístup k obsahu CDN

Pokud chcete získat přístup k obsahu v mezipaměti ve službě CDN, použijte adresu URL CDN uvedenou na portálu. Adresa objektu blob uloženého v mezipaměti má následující formát:

http://<*koncový bod-Name* \> . azureedge.NET/<*myPublicContainer* \> /< *BLOB*\>

> [!NOTE]
> Jakmile Azure CDN povolíte přístup k účtu úložiště, budou všechny veřejně dostupné objekty vhodné pro ukládání CDN POP do mezipaměti. Pokud upravíte objekt, který je momentálně uložený v mezipaměti v CDN, nebude nový obsah dostupný přes Azure CDN, dokud Azure CDN po uplynutí období TTL (time-to-live) pro obsah v mezipaměti neaktualizuje svůj obsah.

## <a name="remove-content-from-azure-cdn"></a>Odebrání obsahu z Azure CDN

Pokud už nechcete objekt v Azure CDN ukládat do mezipaměti, můžete použít některý z následujících kroků:

- Nastavte kontejner jako privátní, nikoli veřejný. Další informace najdete v tématu [Správa anonymního přístupu pro čtení do kontejnerů a objektů BLOB](../storage/blobs/anonymous-read-access-configure.md).
- Zakažte nebo odstraňte koncový bod CDN pomocí webu Azure Portal.
- Upravte hostovanou službu tak, aby už nereagovala na žádosti tohoto objektu.

Objekt, který už je v mezipaměti v Azure CDN uložený, tam zůstane uložený, dokud neuplyne období TTL (Time-To-Live) objektu nebo dokud se koncový bod [nevyprázdní](cdn-purge-endpoint.md). Když uplyne období TTL, Azure CDN určí, jestli je koncový bod stále platný a jestli je objekt stále anonymně přístupný. Pokud tomu tak není, objekt se už v mezipaměti ukládat nebude.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste ve skupině prostředků vytvořili profil a koncový bod CDN. Pokud chcete přejít k části [Další kroky](#next-steps) a zjistit, jak do koncového bodu přidat vlastní doménu, uložte tyto prostředky. Pokud však předpokládáte, že už tyto prostředky nebudete používat, můžete je odstranit tak, že odstraníte skupinu prostředků, abyste se vyhnuli dalším poplatkům:

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků** a pak vyberte * CDNQuickstart-RG * *.

2. Na stránce **Skupina prostředků** vyberte **Odstranit skupinu prostředků**, do textového pole zadejte *CDNQuickstart-RG* a pak vyberte **Odstranit**.

    Tato akce odstraní skupinu prostředků, profil a koncový bod, které jste vytvořili v rámci tohoto rychlého startu.

3. Pokud chcete účet úložiště odstranit, vyberte ho na řídicím panelu a pak v horní nabídce vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: použití CDN k poskytování statického obsahu z webové aplikace](cdn-add-to-web-app.md)
