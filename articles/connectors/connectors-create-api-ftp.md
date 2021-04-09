---
title: Připojit k serveru FTP
description: Automatizace úloh a pracovních postupů, které vytvářejí, monitorují a spravují soubory na serveru FTP pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999569"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Vytvoření, monitorování a správa souborů FTP pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru FTP můžete vytvářet automatizované úlohy a pracovní postupy, které umožňují vytvářet, monitorovat, odesílat a přijímat soubory prostřednictvím účtu na serveru FTP spolu s dalšími akcemi, například:

* Monitorování při přidání nebo změně souborů
* Získat, vytvořit, kopírovat, aktualizovat, zobrazit a odstranit soubory.
* Získá obsah souboru a metadata.
* Extrahuje archivy do složek.

Můžete použít triggery, které získávají odpovědi z vašeho serveru FTP a zpřístupnit výstup ostatním akcím. Pro správu souborů na serveru FTP můžete ve svých aplikacích logiky použít akce spuštění. Můžete také použít jiné akce výstup z akcí FTP. Pokud například pravidelně získáváte soubory ze serveru FTP, můžete odesílat e-maily o těchto souborech a jejich obsahu pomocí konektoru Office 365 Outlook Connector nebo konektoru Outlook.com. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Omezení

* Konektor FTP podporuje pouze explicitní FTP přes protokol TLS/SSL (FTPS) a není kompatibilní s implicitním FTPS.

* Ve výchozím nastavení můžou akce FTP číst nebo zapisovat soubory o *velikosti 50 MB nebo menší*. Aby bylo možné zpracovat soubory větší než 50 MB, akce FTP podporuje vytváření [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). Akce **získat obsah souboru** implicitně používá bloky dat.

* Aktivační události FTP nepodporují vytváření bloků dat. Při vyžádání obsahu souboru triggery vyberou pouze soubory, které jsou 50 MB nebo menší. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Použijte Trigger FTP, který vrátí vlastnosti souboru, například **při přidání nebo úpravě souboru (pouze vlastnosti)**.

  * Postupujte podle triggeru s akcí **získat obsah souboru** FTP, který načte kompletní soubor a implicitně použije bloky dat.

* Pokud máte místní server FTP, zvažte vytvoření [prostředí Integration Service (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) nebo pomocí [Azure App Service hybridních připojení](../app-service/app-service-hybrid-connections.md), které vám umožní přístup k místním zdrojům dat bez použití místní brány dat.

## <a name="how-ftp-triggers-work"></a>Jak fungují triggery FTP

Aktivační procedury FTP fungují při cyklickém dotazování systému souborů FTP a vyhledávání všech souborů, které byly od posledního cyklického dotazování změněny. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je nutné zakázat tuto funkci, aby mohla Trigger fungovat. Tady je několik běžných nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| WinSCP | Přejít na **Možnosti**  >  **Předvolby**  >  **přenos**  >  **Upravit**  >  **zachovat časové razítko**  >  **Zakázat** |
| FileZilly | Přejít na **přenos**–  >  zachovat zablokovaná **Časová razítka přenesených souborů**  >   |
|||

Pokud aktivační událost najde nový soubor, aktivační událost zkontroluje, jestli je nový soubor hotový, a ne částečně napsaný. Soubor může mít například probíhající změny, když aktivační událost kontroluje souborový server. Aby nedošlo k vrácení částečně napsaného souboru, aktivační událost zapisuje časové razítko pro soubor, který má poslední změny, ale tento soubor okamžitě nevrátí. Aktivační událost vrátí soubor pouze při opakovaném dotazování serveru. V některých případech může toto chování způsobit zpoždění až dvojnásobku intervalu dotazování triggeru.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa serveru hostitele FTP a přihlašovací údaje účtu

  Konektor FTP vyžaduje, aby server FTP byl přístupný z Internetu a nastavený pro provoz v *pasivním* režimu. Vaše přihlašovací údaje umožňují, aby aplikace logiky vytvořila připojení a měl přístup k vašemu účtu FTP.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu FTP. Pokud chcete začít s triggerem FTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci FTP, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-ftp"></a>Připojení k FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole `ftp` jako filtr. V seznamu **triggery** vyberte Trigger, který chcete.

   -nebo-

   Pro existující aplikace logiky klikněte v posledním kroku na místo, kam chcete přidat akci, vyberte **Nový krok** a potom vyberte **přidat akci**. Do vyhledávacího pole zadejte `ftp` jako filtr. V seznamu **Akce** vyberte akci, kterou chcete.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Zadejte informace o připojení a vyberte **vytvořit**.

1. Zadejte informace pro vybraný Trigger nebo akci a pokračujte ve vytváření pracovního postupu aplikace logiky.

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Přidat aktivační událost FTP

**Když se přidá nebo upraví soubor (jenom vlastnosti)** , spustí se aktivační procedura aplikace logiky, když aktivační událost zjistí, že se na serveru FTP přidal nebo změnil soubor. Můžete například přidat podmínku, která zkontroluje obsah souboru a rozhodne, zda tento obsah získá na základě toho, zda tento obsah splňuje zadanou podmínku. Nakonec můžete přidat akci, která získá obsah souboru, a tento obsah umístit do jiné složky na serveru SFTP.

Pomocí této aktivační události například můžete monitorovat složku FTP pro nové soubory, které popisují objednávky zákazníků. Pak můžete použít akci FTP, například **získat metadata souboru** , a získat tak vlastnosti pro tento nový soubor a potom pomocí **získat obsah souboru** získat obsah z tohoto souboru pro další zpracování a uložit ho v databázi objednávek.

Zde je příklad, který ukazuje, jak použít **při přidání nebo úpravě souboru (pouze vlastnosti)** aktivační událost.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole `ftp` jako filtr. V seznamu triggery vyberte tuto aktivační událost: **když se přidá nebo upraví dodané datum (jenom vlastnosti)** .

   ![Vyhledejte a vyberte aktivační událost FTP.](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Zadejte potřebné údaje pro připojení a pak vyberte **vytvořit**.

   Ve výchozím nastavení tento konektor přenáší soubory v textovém formátu. Chcete-li přenášet soubory v binárním formátu, například, kde a kdy je použito kódování, vyberte **binární přenos**.

   ![Vytvořit připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. V poli **Složka** vyberte ikonu složky, aby se zobrazil seznam. Chcete-li najít složku, kterou chcete monitorovat pro nové nebo upravované soubory, vyberte šipku pravého úhlu ( **>** ), přejděte do této složky a pak ji vyberte.

   ![Najít a vybrat složku, která se má monitorovat](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Vybraná složka se zobrazí v poli **Složka** .

   ![Vybraná složka se zobrazí ve vlastnosti "složka".](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Teď, když má aplikace logiky Trigger, přidejte akce, které chcete spustit, když aplikace logiky najde nový nebo upravovaný soubor. V tomto příkladu můžete přidat akci FTP, která získá nový nebo aktualizovaný obsah.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Přidat akci FTP

Akce **získat metadata souboru** získá vlastnosti souboru, který je na serveru FTP, a akce **získat obsah souboru** získá obsah souboru na základě informací o tomto souboru na serveru FTP. Můžete například přidat Trigger z předchozího příkladu a tyto akce k získání obsahu souboru po jeho přidání nebo úpravě.

1. V aktivační události nebo jakékoli jiné akci vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte `ftp` jako filtr. V seznamu akce vyberte tuto akci: **získat metadata souboru** .

   ![Vyberte akci získat metadata souboru.](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Pokud už máte připojení k serveru FTP a účtu, pokračujte na další krok. V opačném případě zadejte potřebné údaje pro toto připojení a pak vyberte **vytvořit**.

   ![Vytvořit připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Po zobrazení akce **získat metadata souboru** klikněte do pole **soubor** , aby se zobrazil seznam dynamického obsahu. Nyní můžete vybrat vlastnosti pro výstupy z předchozích kroků. V seznamu dynamického obsahu v části **získat metadata souboru** vyberte vlastnost **seznam ID souborů** , která odkazuje na kolekci, do které byl soubor přidán nebo aktualizován.

   ![Najde a vybere vlastnost seznam ID souborů.](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   V poli **soubor** se teď zobrazí **seznam vlastností ID souborů** .

   ![Vybraná vlastnost seznam ID souborů](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Teď přidejte tuto akci FTP: **získání obsahu souboru**

   ![Vyhledejte a vyberte akci načíst obsah souboru.](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Po zobrazení akce **získat obsah souboru** klikněte do pole **soubor** , aby se zobrazil seznam dynamického obsahu. Nyní můžete vybrat vlastnosti pro výstupy z předchozích kroků. V seznamu dynamického obsahu v části **získat metadata souboru** vyberte vlastnost **ID** , která odkazuje na soubor, který se přidal nebo aktualizoval.

   ![Najde a vybere vlastnost ID.](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Vlastnost **ID** se teď zobrazí v poli **soubor** .

   ![Vybraná vlastnost ID](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Uložte aplikaci logiky.

## <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete ověřit, že váš pracovní postup vrátí očekávaný obsah, přidejte další akci, která vám pošle obsah z odeslaného nebo aktualizovaného souboru.

1. V akci **získat obsah souboru** přidejte akci, která vám pošle obsah souboru. Tento příklad přidá akci **Odeslat e-mail** pro Office 365 Outlook.

   ![Přidat akci pro odeslání e-mailu](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Po zobrazení akce zadejte informace a přidejte vlastnosti, které chcete testovat. Například zahrňte vlastnost **Content Content (obsah souboru** ), která se zobrazí v seznamu dynamického obsahu po výběru **Zobrazit další** v části **získat obsah souboru** .

   ![Zadání informací o akci e-mailu](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Uložte aplikaci logiky. Pokud chcete aplikaci logiky spustit a aktivovat, na panelu nástrojů vyberte **Spustit** a pak přidejte soubor do složky FTP, kterou vaše aplikace logiky teď monitoruje.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/ftpconnector/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

