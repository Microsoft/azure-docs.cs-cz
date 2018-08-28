---
title: Připojení k účtu SFTP z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které monitorování, vytvářet, spravovat, odesílat a přijímat soubory pro SFTP server pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 8f430477883543aa8f87eb3fb0fb49ab31e2d723
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042034"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorování, vytvářet a spravovat SFTP soubory pomocí Azure Logic Apps

S Azure Logic Apps a konektoru SFTP, můžete vytvořit automatizovaných úloh a pracovních postupů, které monitorování, vytvářet, odesílat a přijímat soubory pomocí vašeho účtu na [SFTP](https://www.ssh.com/ssh/sftp/) serverem a také další akce, například:

* Monitorování, když jsou soubory přidány nebo změněny.
* Získat, vytvářet, zkopírujte, seznam, aktualizovat a odstraňovat soubory.
* Získáte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které odpovědi ze serveru SFTP a zpřístupnit výstup dalších akcí. Akce ve službě logic apps můžete použít k provádění úloh se soubory na vašem serveru SFTP. Také můžete mít další akce pomocí výstupu z akce SFTP. Například pokud pravidelně Načtení souborů ze serveru SFTP, můžete odeslat e-mailu o těchto souborech a jejich obsah s použitím konektoru Office 365 Outlook nebo konektor Outlook.com.
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Vaše SFTP server adresu a účet přihlašovacím údajům hostitele

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k účtu SFTP.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k účtu SFTP. Spuštění pomocí aktivační událost SFTP [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SFTP, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-sftp"></a>Připojení k protokolu SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "sftp". V seznamu triggerů vyberte trigger, který chcete. 

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "sftp". 
   V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Zadejte nezbytné podrobnosti připojení a pak zvolte **vytvořit**.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="examples"></a>Příklady

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Aktivační událost SFTP: při přidání nebo změně souboru

Tato aktivační událost se spustí pracovní postup aplikace logiky Pokud trigger rozpozná souboru při přidání nebo změně na SFTP server. Takže například že můžete přidat podmínku, která zkontroluje obsah souboru a rozhodne, zda tento obsah získali založené na tom, jestli tento obsah splňují zadanou podmínku. Nakonec můžete přidat akci, která získá obsah souboru a vložit obsah do složky na serveru SFTP. 

**Příklad organizace**: Tato aktivační událost můžete použít k monitorování složky aplikace SFTP pro nové soubory, které představují objednávek zákazníků. Můžete pak použít akci SFTP jako **získat obsah souboru**, abyste mohli získat obsah pořadí pro další zpracování a uložení této objednávky do databáze objednávek.

### <a name="sftp-action-get-content"></a>Akce SFTP: získání obsahu

Tato akce načte obsah ze souboru na SFTP server. Takže například můžete přidat aktivační událost z předchozího příkladu a podmínku, která musí splňovat obsahu souboru. Pokud je podmínka pravdivá, můžete spustit akci, která získá obsah. 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/sftpconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)