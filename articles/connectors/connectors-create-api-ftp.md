---
title: Připojení k serveru FTP
description: Automatizace úloh a pracovních postupů, které vytvářejí, monitorují a spravují soubory na serveru FTP pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 5b61b51e79c71736e18aaa63ab032c05c512c8d7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656332"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Vytváření, monitorování a správa souborů FTP pomocí aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru FTP můžete vytvářet automatizované úlohy a pracovní postupy, které vytvářejí, monitorují, odesílají a přijímají soubory prostřednictvím vašeho účtu na serveru FTP, spolu s dalšími akcemi, například:

* Sledujte, kdy jsou soubory přidány nebo změněny.
* Získejte, vytvářejte, kopírujte, aktualizujte, seznamujte a odstraňujte soubory.
* Získejte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které získají odpovědi ze serveru FTP a zpřístupní výstup dalším akcím. Ke správě souborů na serveru FTP můžete použít spuštěné akce v aplikacích logiky. Můžete také mít jiné akce použít výstup z akcí FTP. Pokud například pravidelně dostáváte soubory ze serveru FTP, můžete odesílat e-maily o těchto souborech a jejich obsahu pomocí konektoru Office 365 Outlook nebo Outlook.com konektoru. Pokud s aplikacemi logiky tečte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Omezení

* Ftp konektor podporuje pouze explicitní FTP přes TLS/SSL (FTPS) a není kompatibilní s implicitní FTPS.

* Ve výchozím nastavení mohou akce FTP číst nebo zapisovat soubory o *50 MB nebo menší*. Chcete-li zpracovat soubory větší než 50 MB, akce FTP podporují [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). Akce **Získat obsah souboru** implicitně používá bloků.

* Aktivační události FTP nepodporují bloků. Při požadavku na obsah souboru aktivační události vybírají pouze soubory, které jsou 50 MB nebo menší. Chcete-li získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Použijte aktivační událost FTP, která vrací vlastnosti souboru, například **Když je soubor přidán nebo změněn (pouze vlastnosti).**

  * Postupujte podle aktivační události s akcí **ftp získat obsah souboru,** která přečte celý soubor a implicitně používá bloků.

* Pokud máte místní server FTP, zvažte vytvoření [prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) nebo pomocí [hybridních připojení služby Azure App Service](../app-service/app-service-hybrid-connections.md), které vám umožní přístup k místním zdrojům dat bez použití místní brány dat.

## <a name="how-ftp-triggers-work"></a>Jak aktivační události FTP fungují

FTP aktivuje práci dotazováním systému souborů FTP a hledáním jakéhokoli souboru, který byl změněn od posledního dotazování. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je třeba tuto funkci zakázat, aby aktivační událost mohla fungovat. Zde jsou některá běžná nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| Winscp | Přejít na **možnosti** >  >  > **Předvolby** > **Převést** > **Upravit Zachovat**časové**razítko****Zakázat** |
| Filezilla | Přejít na **zachovat** > **časová razítka přenesených souborů** > **Zakázat** |
|||

Když aktivační událost najde nový soubor, aktivační událost zkontroluje, zda je nový soubor dokončen a není částečně zapsán. Soubor může mít například probíhající změny, když aktivační událost zkontroluje souborový server. Aby se zabránilo vrácení částečně zapsaného souboru, aktivační událost zaznamená časové razítko souboru, který má nedávné změny, ale okamžitě tento soubor nevrátí. Aktivační událost vrátí soubor pouze při dotazování serveru znovu. Někdy toto chování může způsobit zpoždění, které je až dvojnásobek intervalu dotazování aktivační události.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa a pověření účtu hostitelského serveru FTP

  FtP konektor vyžaduje, aby váš FTP server byl přístupný z internetu a nastaven pro provoz v *pasivním* režimu. Vaše přihlašovací údaje umožňují aplikaci logiky vytvořit připojení a přístup k účtu FTP.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu FTP. Chcete-li začít s aktivační událostí FTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci FTP, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="connect-to-ftp"></a>Připojení k FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky.

1. U prázdných aplikací logiky zadejte `ftp` do vyhledávacího pole jako filtr. V seznamu **Aktivační události** vyberte požadovanou aktivační událost.

   -nebo-

   U existujících aplikací logiky vyberte v posledním kroku, ve kterém chcete přidat akci, **nový krok**a pak vyberte **Přidat akci**. Do vyhledávacího pole `ftp` zadejte jako filtr. V seznamu **Akce** vyberte požadovanou akci.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Zadejte informace o připojení a vyberte **Vytvořit**.

1. Zadejte informace o vybrané aktivační události nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Přidat aktivační událost FTP

Při **přidání nebo změně souboru (pouze vlastnosti)** spustí pracovní postup aplikace logiky, když aktivační událost zjistí, že soubor je přidán nebo změněn na serveru FTP. Můžete například přidat podmínku, která zkontroluje obsah souboru a rozhodne, zda tento obsah získá na základě toho, zda tento obsah splňuje zadanou podmínku. Nakonec můžete přidat akci, která získá obsah souboru a umístit tento obsah do jiné složky na serveru SFTP.

Tuto aktivační událost můžete například použít ke sledování složky FTP pro nové soubory, které popisují objednávky zákazníků. Potom můžete použít akci FTP, jako je **například získat metadata souboru** získat vlastnosti pro tento nový soubor a potom pomocí **získat obsah souboru** získat obsah z tohoto souboru pro další zpracování a uložit, že pořadí v databázi objednávek.

Zde je příklad, který ukazuje, jak používat při **přidání nebo úpravě souboru (pouze vlastnosti)** aktivační událost.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. U prázdných aplikací logiky zadejte `ftp` do vyhledávacího pole jako filtr. V seznamu aktivačních událostí vyberte tuto aktivační událost: **Při přidání nebo úpravě souboru (pouze vlastnosti)**

   ![Vyhledání a vyhledání aktivační události FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Zadejte potřebné podrobnosti pro připojení a pak vyberte **Vytvořit**.

   Ve výchozím nastavení tento konektor přenáší soubory v textovém formátu. Chcete-li přenést soubory v binárním formátu, například kde a při použití kódování, vyberte **možnost Binární přenos**.

   ![Vytvořit připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. V poli **Složka** vyberte ikonu složky, aby se zobrazil seznam. Chcete-li najít složku, kterou chcete sledovat pro nové nebo**>** upravené soubory, vyberte šipku pravého úhlu ( ), vyhledejte tuto složku a vyberte složku.

   ![Vyhledání a vyhledání složky ke sledování](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Vybraná složka se zobrazí v poli **Složka.**

   ![Vybraná složka se zobrazí ve vlastnosti "Složka"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Teď, když vaše aplikace logiky má aktivační událost, přidejte akce, které chcete spustit, když vaše aplikace logiky najde nový nebo upravený soubor. V tomto příkladu můžete přidat akci FTP, která získá nový nebo aktualizovaný obsah.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Přidat akci FTP

Akce **Získat metadata souboru** získá vlastnosti souboru, který je na serveru FTP, a akce **Získat obsah souboru** získá obsah souboru na základě informací o tomto souboru na serveru FTP. Můžete například přidat aktivační událost z předchozího příkladu a tyto akce, abyste získali obsah souboru po přidání nebo úpravě tohoto souboru.

1. Pod aktivační událostí nebo jinými akcemi vyberte **Nový krok**.

1. Do vyhledávacího pole `ftp` zadejte jako filtr. V seznamu akcí vyberte tuto akci: **Získání metadat souboru**

   ![Vyberte akci "Získat metadata souboru"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Pokud již máte připojení k ftp serveru a účtu, přejděte k dalšímu kroku. V opačném případě zadejte potřebné podrobnosti pro toto připojení a pak vyberte **Vytvořit**.

   ![Vytvořit připojení serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Po zobrazení akce **Získat metadata souboru** klikněte do pole **Soubor,** aby se zobrazil seznam dynamického obsahu. Nyní můžete vybrat vlastnosti výstupů z předchozích kroků. V seznamu dynamického obsahu vyberte v části **Získat metadata souboru**vlastnost **Seznam ID souborů,** která odkazuje na kolekci, do které byl soubor přidán nebo aktualizován.

   ![Najít a vybrat vlastnost "Seznam ID souborů"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   Vlastnost **Seznam ID souborů** se nyní zobrazí v poli **Soubor.**

   ![Vybraná vlastnost "Seznam ID souborů"](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Nyní přidejte tuto akci FTP: **Získat obsah souboru**

   ![Vyhledání a výběr akce Získat obsah souboru](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Po zobrazení akce **Nahrát obsah souboru** klikněte do pole **Soubor,** aby se zobrazil seznam dynamického obsahu. Nyní můžete vybrat vlastnosti výstupů z předchozích kroků. V seznamu dynamického obsahu vyberte v části **Získat metadata souboru**vlastnost **Id,** která odkazuje na soubor, který byl přidán nebo aktualizován.

   ![Najít a vybrat vlastnost "Id"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Vlastnost **ID** se nyní zobrazí v poli **Soubor.**

   ![Vybraná vlastnost "Id"](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Uložte svou aplikaci logiky.

## <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li zkontrolovat, zda pracovní postup vrací očekávaný obsah, přidejte další akci, která vám odešle obsah z nahraného nebo aktualizovaného souboru.

1. V části **Akce Načíst obsah souboru** přidejte akci, která vám může poslat obsah souboru. Tento příklad přidá akci **Odeslat e-mail** pro Office 365 Outlook.

   ![Přidání akce pro odesílání e-mailů](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Po zobrazí akce, zadejte informace a zahrnout vlastnosti, které chcete testovat. Zahrňte například vlastnost **Obsah souboru,** která se zobrazí v seznamu dynamického obsahu po výběru **zobrazit více** v části Získat **obsah souboru.**

   ![Poskytnutí informací o akci e-mailu](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Uložte svou aplikaci logiky. Chcete-li spustit a aktivovat aplikaci logiky, **vyberte**na panelu nástrojů spustit a pak přidejte soubor do složky FTP, kterou aplikace logiky nyní monitoruje.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/ftpconnector/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
