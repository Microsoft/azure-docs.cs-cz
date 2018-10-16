---
title: Připojte se k serveru FTP – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvářet, monitorovat a spravovat soubory na serveru FTP s Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: e72b7e5ac3c66283116925e8e36c1c33e777042c
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318907"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Vytváření, monitorování a správu protokolu FTP souborů pomocí Azure Logic Apps

S Azure Logic Apps a konektor FTP můžete vytvořit automatizovaných úloh a pracovních postupů, které vytvářet, monitorovat, odesílat a přijímat soubory pomocí vašeho účtu na serveru FTP, společně s další akce, například:

* Monitorování, když jsou soubory přidány nebo změněny.
* Získat, vytvářet, zkopírujte, seznam, aktualizovat a odstraňovat soubory.
* Získáte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které odpovědi ze serveru FTP a zpřístupnit výstup dalších akcí. Akce ve službě logic apps můžete použít k provádění úloh se soubory na vašem serveru FTP. Také můžete mít další akce, které používají výstup z FTP akcí. Například pokud pravidelně Načtení souborů ze serveru FTP, můžete odeslat e-mailu o těchto souborech a jejich obsah s použitím konektoru Office 365 Outlook nebo konektor Outlook.com. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Konektor FTP podporuje pouze soubory, které jsou 50 MB nebo méně, pokud nechcete použít [bloků pro zpracování velkých zpráv](../logic-apps/logic-apps-handle-large-messages.md). 
>
> Konektor FTP také podporuje pouze explicitní FTP přes protokol SSL (FTPS) a není kompatibilní s implicitní FTPS. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Vaše FTP server adresu a účet přihlašovacím údajům hostitele

  Konektor FTP vyžaduje, aby váš server FTP přístupný z Internetu a nastavte pracovat v *pasivní* režimu. Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k vašemu účtu FTP.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu FTP. Spuštění pomocí aktivační událost FTP [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci FTP, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-ftp"></a>Připojte se k serveru FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "ftp". V seznamu triggerů vyberte trigger, který chcete. 

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**a pak vyberte **přidat akci**. 
   Do vyhledávacího pole zadejte "ftp" jako filtr. 
   V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Zadejte nezbytné podrobnosti připojení a pak zvolte **vytvořit**.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="examples"></a>Příklady

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Aktivační událost FTP: při přidání nebo změně souboru

Tato aktivační událost se spustí pracovní postup aplikace logiky Pokud trigger rozpozná souboru při přidání nebo změně na serveru FTP. Takže například že můžete přidat podmínku, která zkontroluje obsah souboru a rozhodne, zda tento obsah získali založené na tom, jestli tento obsah splňují zadanou podmínku. Nakonec můžete přidat akci, která získá obsah souboru a vložit obsah do složky na serveru SFTP. 

**Příklad organizace**: Tato aktivační událost můžete použít k monitorování složky FTP pro nové soubory, které představují objednávek zákazníků. Můžete pak použít akci FTP jako **získat obsah souboru**, abyste mohli získat obsah pořadí pro další zpracování a uložení této objednávky do databáze objednávek.

Aplikace logiky platný a funkční vyžaduje aktivační události a alespoň jednu akci. Proto se ujistěte, že přidáte akci po přidání triggeru.

Tady je příklad, který ukazuje tento trigger: **při přidání nebo změně souboru**

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "ftp". V seznamu triggerů vyberte tento trigger: **když archivované se přidá nebo upraví - FTP**

   ![Vyhledejte a vyberte FTP trigger](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Zadejte nezbytné podrobnosti připojení a pak zvolte **vytvořit**.

   ![Vytvoření připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Vedle položky **složky** vyberte ikonu složky, zobrazí se seznam. Najít složku, které je třeba sledovat pro nové nebo upravené soubory, vyberte šipku pravý úhel (**>**), přejděte do složky a potom vyberte složku.

   ![Vyhledejte a vyberte složku, kterou chcete monitorovat](./media/connectors-create-api-ftp/select-folder.png)  

   Vybrané složky se zobrazí v **složky** pole.

   ![Vybrané složky](./media/connectors-create-api-ftp/selected-folder.png)  

Teď, když vaše aplikace logiky má aktivační událost, přidání akce, které chcete spustit, když vaše aplikace logiky najde nové nebo upravené souboru. V tomto příkladu přidáte akci FTP, která získá obsah nová nebo aktualizovaná.

### <a name="ftp-action-get-content"></a>Akce FTP: získání obsahu

Tato akce získá obsah ze souboru na FTP server, když se tento soubor se přidá nebo aktualizuje. Takže například můžete přidat aktivační událost z předchozího příkladu a akci, která získá obsah souboru po souboru se přidal nebo upravil. 

1. V aktivační události nebo jiné akce, zvolte **nový krok**. 

1. Do vyhledávacího pole zadejte "ftp" jako filtr. V seznamu akcí vyberte tuto akci: **získat obsah souboru - FTP**

   ![Vyberte akci FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Pokud už máte připojení k serveru FTP a účet, přejděte k dalšímu kroku. V opačném případě zadejte potřebné podrobnosti pro toto připojení a klikněte na tlačítko **vytvořit**. 

   ![Vytvoření připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Po **získat obsah souboru** akce otevře, klikněte na tlačítko uvnitř **souboru** pole tak, aby zobrazil seznam dynamického obsahu. Teď můžete vybrat vlastnosti pro výstupy z předchozích kroků. Ze seznamu dynamického obsahu vyberte **obsah souboru** vlastnost, která má obsah souboru přidané nebo aktualizované.  

   ![Vyhledejte a vyberte soubor](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   **Obsah souboru** vlastnost se zobrazí v **souboru** pole.

   !["Obsah souboru" zvolená](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Uložte svou aplikaci logiky. K testování pracovního postupu, přidejte soubor do složky serveru FTP, který teď monitoruje vaše aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/ftpconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)