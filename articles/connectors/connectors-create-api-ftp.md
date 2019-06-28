---
title: Připojte se k serveru FTP – Azure Logic Apps
description: Vytvářet, monitorovat a spravovat soubory na serveru FTP s Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 66f1d726dcfa1a077abbff0d9f028036db43cc25
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293093"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Vytváření, monitorování a správu protokolu FTP souborů pomocí Azure Logic Apps

S Azure Logic Apps a konektor FTP můžete vytvořit automatizovaných úloh a pracovních postupů, které vytvářet, monitorovat, odesílat a přijímat soubory pomocí vašeho účtu na serveru FTP, společně s další akce, například:

* Monitorování, když jsou soubory přidány nebo změněny.
* Získat, vytvářet, zkopírujte, seznam, aktualizovat a odstraňovat soubory.
* Získáte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které odpovědi ze serveru FTP a zpřístupnit výstup dalších akcí. Spuštění akcí ve svých aplikacích logiky můžete použít pro správu souborů na vašem serveru FTP. Také můžete mít další akce, které používají výstup z FTP akcí. Například pokud pravidelně získání souborů ze serveru FTP, můžete odeslat e-mailu o těchto souborech a jejich obsah s použitím konektoru Office 365 Outlook nebo konektor Outlook.com. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

* Konektor FTP podporuje pouze explicitní FTP přes protokol SSL (FTPS) a není kompatibilní s implicitní FTPS.

* Ve výchozím nastavení, může číst nebo zapisovat soubory, které jsou akce FTP *50 MB nebo méně*. Zpracovat soubory větší než 50 MB, akce podporu protokolu FTP [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). **Získat obsah souboru** akce implicitně používá dělením dat do bloků.

* Aktivační události FTP nepodporují dělením dat do bloků. Pokud se požaduje obsah souboru, aktivační události vyberte pouze soubory, které jsou 50 MB nebo méně. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Pomocí aktivační událost FTP, který vrací vlastnosti souboru, například **kdy soubor se přidá nebo upraví (jen vlastnosti)** .

  * Postupujte podle aktivační událost s FTP **získat obsah souboru** akce, která načte kompletní soubor a implicitně používá dělením dat do bloků.

## <a name="how-ftp-triggers-work"></a>Jak FTP aktivuje pracovní

FTP aktivuje pracovní dotazování systém souborů protokolu FTP a vyhledáním každý soubor, který se změnil od posledního cyklického dotazování. Některé nástroje umožňují zachovat časové razítko, když se změní soubory. V těchto případech budete muset zakázat tuto funkci, aktivační událost můžete pracovat. Tady jsou některé běžné nastavení:

| Klient protokolu SFTP | Akce |
|-------------|--------|
| Winscp | Přejděte na **možnosti** > **Předvolby** > **přenos** > **upravit**  >  **Zachovat časové razítko** > **zakázat** |
| FileZilla | Přejděte na **přenos** > **zachovat časová razítka přenášených souborů** > **zakázat** |
|||

Když aktivační události vyhledá nový soubor, trigger zkontroluje, zda nový soubor úplné a částečně napsané. Soubor může například mít změny v průběhu při trigger bude kontrolovat souborového serveru. Aktivační událost se pokud chcete vyhnout, vrací částečně napsané souborů, poznámky časové razítko pro soubor, který obsahuje poslední změny, ale nevrací okamžitě tento soubor. Aktivační událost vrátí soubor pouze v případě, že dotazování serveru znovu. Toto chování může způsobit zpoždění, které je až dvakrát triggeru interval dotazování.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Vaše FTP server adresu a účet přihlašovacím údajům hostitele

  Konektor FTP vyžaduje, aby váš server FTP přístupný z Internetu a nastavte pracovat v *pasivní* režimu. Vaše přihlašovací údaje nechat aplikaci logiky vytvoříte připojení a přístup k vašemu účtu FTP.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu FTP. Spuštění pomocí aktivační událost FTP [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci FTP, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-ftp"></a>Připojte se k serveru FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "ftp". V seznamu triggerů vyberte trigger, který chcete.

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**a pak vyberte **přidat akci**. Do vyhledávacího pole zadejte "ftp" jako filtr. V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. Vyberte znaménko plus ( **+** ), který se zobrazí a vyberte **přidat akci**.

1. Zadejte nezbytné podrobnosti připojení a pak zvolte **vytvořit**.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Aktivační událost FTP: Při přidání nebo změně souboru

Tato aktivační událost se spustí pracovní postup aplikace logiky Pokud trigger rozpozná souboru při přidání nebo změně na serveru FTP. Takže například že můžete přidat podmínku, která zkontroluje obsah souboru a rozhodne, zda tento obsah získali založené na tom, jestli tento obsah splňují zadanou podmínku. Nakonec můžete přidat akci, která získá obsah souboru a vložit obsah do složky na serveru SFTP.

**Příklad organizace**: Tato aktivační událost můžete použít k monitorování složky FTP pro nové soubory, které popisují objednávek zákazníků. Můžete pak použít akci FTP jako **získat obsah souboru**, abyste mohli získat obsah pořadí pro další zpracování a uložení této objednávky do databáze objednávek.

Tady je příklad ukazující této aktivační události: **Při přidání nebo změně souboru**

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "ftp". V seznamu triggerů vyberte tento trigger: **Když archivované se přidá nebo upraví - FTP**

   ![Vyhledejte a vyberte FTP trigger](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Zadejte nezbytné podrobnosti připojení a pak zvolte **vytvořit**.

   Ve výchozím nastavení tento konektor převádí soubory ve formátu textu. K přenosu souborů v binárním souboru formátu, třeba where a pokud se používá kódování, vyberte **binární přenos**.

   ![Vytvoření připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Vedle položky **složky** vyberte ikonu složky, zobrazí se seznam. Najít složku, které je třeba sledovat pro nové nebo upravené soubory, vyberte šipku pravý úhel ( **>** ), přejděte do složky a potom vyberte složku.

   ![Vyhledejte a vyberte složku, kterou chcete monitorovat](./media/connectors-create-api-ftp/select-folder.png)  

   Vybrané složky se zobrazí v **složky** pole.

   ![Vybrané složky](./media/connectors-create-api-ftp/selected-folder.png)  

Teď, když vaše aplikace logiky má aktivační událost, přidání akce, které chcete spustit, když vaše aplikace logiky najde nové nebo upravené souboru. V tomto příkladu přidáte akci FTP, která získá obsah nová nebo aktualizovaná.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Akce FTP: Získat obsah

Tato akce získá obsah ze souboru na FTP server, když se tento soubor se přidá nebo aktualizuje. Takže například můžete přidat aktivační událost z předchozího příkladu a akci, která získá obsah souboru po souboru se přidal nebo upravil.

Tady je příklad, který obsahuje tato akce: **Získat obsah**

1. V aktivační události nebo jiné akce, zvolte **nový krok**.

1. Do vyhledávacího pole zadejte "ftp" jako filtr. V seznamu akcí vyberte tuto akci: **Získat obsah souboru - FTP**

   ![Vyberte akci FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Pokud už máte připojení k serveru FTP a účet, přejděte k dalšímu kroku. V opačném případě zadejte potřebné podrobnosti pro toto připojení a klikněte na tlačítko **vytvořit**.

   ![Vytvoření připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Po **získat obsah souboru** akce otevře, klikněte na tlačítko uvnitř **souboru** pole tak, aby zobrazil seznam dynamického obsahu. Teď můžete vybrat vlastnosti pro výstupy z předchozích kroků. Ze seznamu dynamického obsahu vyberte **obsah souboru** vlastnost, která má obsah souboru přidané nebo aktualizované.  

   ![Vyhledejte a vyberte soubor](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   **Obsah souboru** vlastnost se zobrazí v **souboru** pole.

   !["Obsah souboru" zvolená](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Uložte svou aplikaci logiky. K testování pracovního postupu, přidejte soubor do složky serveru FTP, který teď monitoruje vaše aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, zkontrolujte [konektoru referenční stránce](/connectors/ftpconnector/).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
