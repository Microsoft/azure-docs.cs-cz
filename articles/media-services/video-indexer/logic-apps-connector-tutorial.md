---
title: Video Indexer konektory s využitím kurzu aplikace logiky a automatizace pro Power automat.
description: V tomto kurzu se dozvíte, jak odemknout nové prostředí a příležitosti finanční zhodnocení Video Indexer konektory pomocí aplikace logiky a automatizace Power automatu.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f557794265f3bbf48fae97fc04e5e9b068b54f63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540400"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Kurz: použití Video Indexer s aplikací logiky a automatickým automatickým zapnutím

Azure Media Services [video indexer v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) podporuje komunikaci mezi servery i mezi klientem a serverem, a umožňuje video indexer uživatelům integrovat video a zvukové poznatky do své aplikační logiky, a to díky podpoře nových prostředí a finanční zhodnocení příležitostí.

Abychom integraci zajistili ještě snazší, podporujeme [Logic Apps](https://azure.microsoft.com/services/logic-apps/)   a [Power](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)automaty   , které jsou kompatibilní s naším rozhraním API. Pomocí konektorů můžete nastavit vlastní pracovní postupy, abyste mohli efektivně indexovat a extrahovat poznatky z velkého množství videosouborů a zvukových souborů, aniž byste museli psát jediný řádek kódu. Navíc pomocí konektorů pro integraci získáte lepší přehled o stavu pracovního postupu a snadném způsobu jeho ladění.  

Abychom vám pomohli rychle začít s Video Indexer konektory, budeme postupovat podle ukázkové aplikace logiky a řešení Power Automate, které můžete nastavit. V tomto kurzu se dozvíte, jak nastavit toky pomocí Logic Apps. Editory a funkce jsou ale téměř identické v obou řešeních, takže se diagramy a vysvětlení vztahují i na Logic Apps i pro automatizaci.

Scénář "nahrát a indexovat video automaticky", který je popsaný v tomto kurzu, se skládá ze dvou různých toků, které vzájemně spolupracují. 
* První tok se aktivuje při přidání nebo úpravě objektu BLOB v účtu Azure Storage. Nahraje nový soubor, aby Video Indexer s adresou URL pro zpětné volání, aby po dokončení operace indexování poslal oznámení. 
* Druhý tok se aktivuje na základě adresy URL zpětného volání a uloží extrahované přehledy zpátky do souboru JSON v Azure Storage. Tento přístup ke dvěma tokům se používá pro zajištění efektivního nahrávání a indexování velkých souborů. 

V tomto kurzu se používá aplikace logiky k tomu, abyste si ukázali, jak:

> [!div class="checklist"]
> * Nastavení toku nahrávání souborů
> * Nastavení toku extrakce JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Abyste mohli začít, budete potřebovat účet Video Indexer společně s [přístupem k rozhraním API přes klíč rozhraní API](video-indexer-use-apis.md). 
* Budete také potřebovat účet Azure Storage. Poznamenejte si přístupový klíč pro váš účet úložiště. Vytvořte dva kontejnery – jeden pro ukládání videí do a jeden pro uložení přehledů vygenerovaných Video Indexer v.  
* V dalším kroku budete muset otevřít dva samostatné toky buď na Logic Apps nebo na automatizaci (podle toho, kterou používáte). 

## <a name="set-up-the-first-flow---file-upload"></a>Nastavení prvního nahrávání souboru Flow   

První tok se aktivuje pokaždé, když se do kontejneru Azure Storage přidá objekt BLOB. Po aktivaci vytvoří identifikátor URI SAS, který můžete použít k nahrání a indexování videa v Video Indexer. V této části vytvoříte následující tok. 

![Tok nahrávání souborů](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Pokud chcete nastavit první tok, budete muset zadat Video Indexer klíč rozhraní API a přihlašovací údaje Azure Storage. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Název připojení a klíč rozhraní API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Pokud jste dříve připojili účet Azure Storage nebo účet Video Indexer k aplikaci logiky, budou se vám podrobnosti o připojení ukládat a automaticky se připojí. <br/>Připojení můžete upravit kliknutím na **změnit připojení** v dolní části Azure Storage (okno úložiště) nebo v akci video indexer (okno přehrávače).

Jakmile se můžete připojit ke svým účtům Azure Storage a Video Indexer, najděte a vyberte v **návrháři Logic Apps**Trigger "při přidání nebo úpravě objektu BLOB".

Vyberte kontejner, ve kterém budou umístěny soubory videa. 

![Snímek obrazovky zobrazuje dialogové okno při přidání nebo úpravě objektu blob, kde můžete vybrat kontejner.](./media/logic-apps-connector-tutorial/container.png)

Potom vyhledejte a vyberte akci "vytvořit identifikátor URI SAS podle cesty". V dialogu pro akci vyberte možnost seznam cest souborů z možností dynamického obsahu.  

Přidejte také nový parametr "Shared Access Protocol". Jako hodnotu parametru vyberte HttpsOnly.

![Identifikátor URI SAS podle cesty](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Pokud chcete získat Video Indexer token účtu, vyplňte [umístění svého účtu](regions.md) a [ID účtu](./video-indexer-use-apis.md#account-id)   .

![Získání přístupového tokenu účtu](./media/logic-apps-connector-tutorial/account-access-token.png)

V části nahrát video a index zadejte požadované parametry a adresu URL videa. Vyberte Přidat nový parametr a vyberte URL zpětného volání. 

![Nahrát a indexovat](./media/logic-apps-connector-tutorial/upload-and-index.png)

Adresu URL zpětného volání teď vynecháte prázdné. Přidáte ho až po dokončení druhého toku, kde se vytvoří adresa URL zpětného volání. 

Můžete použít výchozí hodnotu pro ostatní parametry nebo je nastavit podle vašich potřeb. 

Klikněte na **Uložit**a pojďme přejít na, aby se nakonfigurovali druhý tok, aby se přehledy po nahrání a indexování dokončily. 

## <a name="set-up-the-second-flow---json-extraction"></a>Nastavení druhého toku – extrakce JSON  

Dokončením nahrávání a indexování z prvního toku se pošle požadavek HTTP se správnou adresou URL pro zpětné volání, aby se spustil druhý tok. Pak načte přehledy vygenerované Video Indexer. V tomto příkladu uloží výstup vaší úlohy indexování do Azure Storage.  Je to ale vše, co můžete s výstupem dělat.  

Vytvořte druhý tok oddělený od prvního z nich. 

![Průběh extrakce JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

K nastavení tohoto toku budete muset zadat Video Indexer klíč rozhraní API a přihlašovací údaje Azure Storage znovu. Budete muset aktualizovat stejné parametry jako u prvního toku. 

U triggeru se zobrazí pole Adresa URL příspěvku HTTP. Adresa URL se nevygeneruje až po uložení toku; adresu URL ale budete potřebovat nakonec. Vrátíme se k tomu. 

Pokud chcete získat Video Indexer token účtu, vyplňte [umístění svého účtu](regions.md) a [ID účtu](./video-indexer-use-apis.md#account-id)   .  

Přejdete na akci načíst index videa a vyplňte požadované parametry. Pro ID videa zadejte následující výraz: triggerOutputs () [' dotazy '] [' ID '] 

![informace o akci indexeru videa](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Tento výraz instruuje připojení, aby získalo ID videa z výstupu triggeru. V takovém případě bude výstup triggeru v první aktivační události výstupem možnosti nahrát video a index. 

Přejít na akci vytvořit objekt BLOB a vybrat cestu ke složce, do které budete ukládat přehledy. Nastavte název objektu blob, který vytváříte. V části obsah objektu BLOB vložte následující výraz: text (' Get_Video_Index ') 

![Akce vytvoření objektu BLOB](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Tento výraz vezme výstup akce získat index videa z tohoto toku. 

Klikněte na **Uložit tok**. 

Po uložení toku se v triggeru vytvoří adresa URL POST protokolu HTTP. Zkopírujte adresu URL z aktivační události. 

![Uložit Trigger adresy URL](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Nyní se vraťte k prvnímu toku a vložte adresu URL do akce Odeslat video a index pro parametr URL zpětného volání. 

Ujistěte se, že oba toky jsou uložené, a vy můžete začít! 

Vyzkoušejte si nově vytvořenou aplikaci logiky nebo řešení Power automat přidáním videa do kontejneru objektů blob Azure a vraťte se do několika minut později, abyste zjistili, že se přehledy zobrazí v cílové složce. 

## <a name="generate-captions"></a>Generovat titulky

Postup, [jak vygenerovat titulky pomocí video indexer a Logic Apps](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198), najdete v následujícím blogu. 

Článek také ukazuje, jak automaticky indexovat video tak, že ho zkopírujete na OneDrive a uložíte titulky vygenerované Video Indexer na OneDrivu.
 
## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto kurzem hotovi, můžete tuto aplikaci logiky zachovat a v případě potřeby je automatizovat a spouštět řešení. Pokud ale nechcete tuto činnost provozovat a nechcete se vám účtovat, vypněte oba toky, pokud používáte Power Automatizujte. Pokud používáte Logic Apps, zakažte oba toky. 

## <a name="next-steps"></a>Další kroky

Tento kurz ukázal pouze jeden příklad konektoru Video Indexer. Video Indexer konektory můžete použít pro jakékoli volání rozhraní API, které poskytuje Video Indexer. Například: nahrání a načtení přehledů, překlad výsledků, získání oblíbených pomůcek a dokonce přizpůsobení vašich modelů. Kromě toho můžete zvolit, aby se tyto akce aktivovaly na základě různých zdrojů, jako jsou aktualizace úložišť souborů nebo odeslaných e-mailů. Pak se můžete rozhodnout, že budete mít výsledky aktualizace naší relevantní infrastruktury nebo aplikace nebo vygenerovat libovolný počet položek akcí.  

> [!div class="nextstepaction"]
> [Použití rozhraní API Video Indexeru](video-indexer-use-apis.md)
