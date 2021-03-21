---
title: E-mail při Key Vault stavu změny v tajnosti
description: Průvodce pro použití Logic Apps k reakci na Key Vault změny tajných klíčů
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c522d870a25b3df34ab6a0cf1c1e944a6462685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013985"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Pomocí Logic Apps dostávat e-maily o změnách stavu tajných kódů trezoru klíčů

V této příručce se dozvíte, jak reagovat na události Azure Key Vault přijaté přes [Azure Event Grid](../../event-grid/index.yml) pomocí [Azure Logic Apps](../../logic-apps/index.yml). Až skončíte, budete mít aplikaci Azure Logic App nastavenou k odeslání e-mailového oznámení pokaždé, když se v Azure Key Vault vytvoří tajný kód.

Přehled Integrace Azure Key Vault/Azure Event Grid najdete v tématu [monitorování Key Vault s Azure Event Grid](event-grid-overview.md).

## <a name="prerequisites"></a>Předpoklady

- E-mailový účet od jakéhokoli poskytovatele e-mailu, který podporuje Azure Logic Apps (například Office 365 Outlook). Tento e-mailový účet se používá k posílání oznámení o událostech. Úplný seznam podporovaných konektorů aplikace logiky najdete v článku [Přehled konektorů](/connectors).
- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Trezor klíčů ve vašem předplatném Azure. Nový trezor klíčů můžete rychle vytvořit podle kroků v části [nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](../secrets/quick-create-cli.md).
- Registrované Event Grid jako poskytovatel prostředků, viz [registrace poskytovatelé prostředků](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Vytvoření aplikace logiky prostřednictvím Event Grid

Nejdřív vytvořte aplikaci logiky pomocí obslužné rutiny Event gridu a přihlaste se k odběru Azure Key Vault události "SecretNewVersionCreated".

K vytvoření předplatného Azure Event Grid použijte následující postup:

1. V Azure Portal přejděte do trezoru klíčů, vyberte **události > Začínáme** a klikněte na **Logic Apps**

    
    ![Stránka Key Vault – události](../media/eventgrid-logicapps-kvsubs.png)

1. V **návrháři Logic Apps** ověřte připojení a klikněte na **pokračovat** . 
 
    ![Návrhář aplikace logiky – připojení](../media/eventgrid-logicappdesigner1.png)

1. Na obrazovce **když dojde k události prostředku** , udělejte toto:
    - Ponechte **předplatné** a **název prostředku** jako výchozí.
    - Jako **typ prostředku** vyberte **Microsoft. webtrezor. trezory** .
    - Pro **položku Typ události-1** vyberte **Microsoft. klíčů trezor. SecretNewVersionCreated** .

    ![Návrhář aplikace logiky – obslužná rutina události](../media/eventgrid-logicappdesigner2.png)

1. Vyberte **+ Nový krok** . otevře se okno pro výběr akce.
1. Vyhledejte **E-mail**. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. V tomto kurzu se používá **Office 365 Outlook**. Kroky pro jiné poskytovatele e-mailu jsou podobné.
1. Vyberte akci **Odeslat e-mail (v2)** .

   ![Návrhář aplikace logiky – odeslání e-mailu](../media/eventgrid-logicappdesigner3.png)

1. Vytvoření e-mailové šablony:
    - **Na:** Zadejte e-mailovou adresu pro příjem e-mailů s oznámením. Pro účely tohoto kurzu použijte e-mailový účet, ke kterému máte při testování přístup.
    - **Předmět** a **Text**: Napište text e-mailu. V nástroji pro výběr vyberte vlastnosti JSON, které budou zahrnovat dynamický obsah na základě dat události. Data události můžete načíst pomocí `@{triggerBody()?['Data']}` .

    Vaše e-mailová šablona může vypadat jako v tomto příkladu.

    ![Návrhář aplikace logiky – tělo e-mailu](../media/eventgrid-logicappdesigner4.png)

8. Klikněte na **Uložit jako**.
9. Zadejte **název** nové aplikace logiky a klikněte na **vytvořit**.
    
    ![Návrhář aplikace logiky – vytvoření](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testování a ověření

1.  V Azure Portal klikněte na svůj Trezor klíčů a vyberte **události > odběry událostí**.  Ověření vytvoření nového předplatného
    
    ![Návrhář aplikace logiky – testování a ověřování](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Přejít do trezoru klíčů, vybrat **tajné** kódy a vybrat **+ Generovat/importovat**. Vytvořte nový tajný klíč pro účely testování a zachovejte zbývající parametry ve výchozím nastavení.

    ![Key Vault – vytvoření tajného kódu](../media/eventgrid-logicapps-kv-create-secret.png)

1. Na obrazovce **vytvořit tajný klíč** zadejte libovolný název, libovolnou hodnotu a vyberte **vytvořit**.

Po vytvoření tajného klíče se na nakonfigurované adresy dostane e-mail.

## <a name="next-steps"></a>Další kroky

- Přehled: [monitorování Key Vault s](event-grid-overview.md) využitím Azure Event Grid
- Postupy: [Směrování oznámení trezoru klíčů na Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid schéma událostí pro Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Další informace o službě [Azure Event Grid](../../event-grid/index.yml).
- Další informace o [funkci Logic Apps služby Azure App Service](../../logic-apps/index.yml).