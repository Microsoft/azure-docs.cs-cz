---
title: E-mail, když se změní stav tajného klíče v trezoru klíčů
description: Průvodce pro použití logic apps reagovat na změny tajných kódů trezoru klíčů
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 53e8586486d9a9ebf870de350d5607f58977c0f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423273"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Použití aplikací Logiky k přijímání e-mailů o změnách stavu tajných kódů trezoru klíčů

V této příručce se dozvíte, jak reagovat na události služby Azure Key Vault, které jsou přijímány prostřednictvím [služby Azure Event Grid](../../event-grid/index.yml) pomocí [Azure Logic Apps](../../logic-apps/index.yml). Na konci budete mít aplikaci logiky Azure nastavenou pro odesílání e-mailu s oznámením při každém vytvoření tajného klíče v úložišti Azure Key Vault.

Přehled integrace Azure Key Vault / Azure Event Grid najdete v [tématu Monitorování trezoru klíčů pomocí Azure Event Grid (preview).](event-grid-overview.md)

## <a name="prerequisites"></a>Požadavky

- E-mailový účet od libovolného poskytovatele e-mailu, který je podporovaný Azure Logic Apps (například Office 365 Outlook). Tento e-mailový účet se používá k posílání oznámení o událostech. Úplný seznam podporovaných konektorů aplikace logiky najdete v článku [Přehled konektorů](/connectors).
- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
- Trezor klíčů ve vašem předplatném Azure. Nový trezor klíčů můžete rychle vytvořit podle kroků v [nastavení a načíst tajný klíč z azure key vault pomocí Azure CLI](../secrets/quick-create-cli.md).

## <a name="create-a-logic-app-via-event-grid"></a>Vytvoření aplikace logiky prostřednictvím mřížky událostí

Nejprve vytvořte aplikaci logiky s obslužnou rutinou mřížky událostí a přihlaste se k odběru událostí Azure Key Vault "SecretNewVersionCreated".

Pokud chcete vytvořit předplatné Azure Event Grid, postupujte takto:

1. Na webu Azure Portal přejděte do trezoru klíčů, vyberte **Události > Začínáme** a klikněte na **Logic Apps.**

    
    ![Trezor klíčů - stránka událostí](../media/eventgrid-logicapps-kvsubs.png)

1. V **Návrháři aplikací logiky** ověřte připojení a klikněte na **Pokračovat** 
 
    ![Návrhář aplikace logiky - připojení](../media/eventgrid-logicappdesigner1.png)

1. Na obrazovce **Při výskytu události prostředku** postupujte takto:
    - Ponechat **odběr** a **název prostředku** jako výchozí.
    - Vyberte **soubor Microsoft.KeyVault.vaults** pro **typ prostředku**.
    - Vyberte **microsoft.KeyVault.SecretNewVersionCreated** pro **položku typu události - 1**.

    ![Návrhář aplikace logiky – obslužná rutina události](../media/eventgrid-logicappdesigner2.png)

1. Vybrat **+ Nový krok** Tím se otevře okno pro volbu akce.
1. Vyhledejte **E-mail**. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Tento kurz používá **Office 365 Outlook**. Kroky pro jiné poskytovatele e-mailu jsou podobné.
1. Vyberte akci **Odeslat e-mail (V2).**

   ![Návrhář aplikace logiky – přidání e-mailu](../media/eventgrid-logicappdesigner3.png)

1. Vytvořte si šablonu e-mailu:
    - **Chcete-li:** Zadejte e-mailovou adresu pro příjem e-mailů s oznámením. V tomto kurzu použijte e-mailový účet, který máte dostupný pro testování.
    - **Předmět** a **Text**: Napište text e-mailu. Vyberte vlastnosti JSON z nástroje pro výběr, aby se zahrnul dynamický obsah na základě dat událostí. Data události můžete načíst `@{triggerBody()?['Data']}`pomocí aplikace .

    Šablona e-mailu může vypadat takto.

    ![Návrhář aplikace logiky – přidání e-mailu](../media/eventgrid-logicappdesigner4.png)

8. Klepněte na **tlačítko Uložit jako**.
9. Zadejte **název** nové aplikace logiky a klepněte na **tlačítko Vytvořit**.
    
    ![Návrhář aplikace logiky – přidání e-mailu](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testování a ověření

1.  Přejděte do trezoru klíčů na webu Azure Portal a vyberte **Události > předplatným událostí**.  Ověření, zda bylo vytvořeno nové předplatné
    
    ![Návrhář aplikace logiky – přidání e-mailu](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Přejděte do trezoru klíčů, vyberte **Tajné položky**a vyberte **+ Generovat/Importovat**. Vytvořte nový tajný klíč pro účely testování pojmenujte klíč a zachovat zbývající parametry v jejich výchozí nastavení.

    ![Trezor klíčů – vytvoření tajného klíče](../media/eventgrid-logicapps-kv-create-secret.png)

1. Na obrazovce **Vytvořit tajný klíč** uveďte libovolný název, libovolnou hodnotu a vyberte **Vytvořit**.

Po vytvoření tajného klíče bude na konfigurovaných adresách přijat e-mail.

## <a name="next-steps"></a>Další kroky

- Přehled: [Monitorování trezoru klíčů pomocí Azure Event Grid (preview)](event-grid-overview.md)
- Postup: [Směrování oznámení trezoru klíčů do Azure Automation](event-grid-tutorial.md).
- [Schéma událostí Azure Event Grid pro Azure Key Vault (preview)](../../event-grid/event-schema-key-vault.md)
- Další informace o službě [Azure Event Grid](../../event-grid/index.yml).
- Další informace o [funkci Logic Apps služby Azure App Service](../../logic-apps/index.yml).
