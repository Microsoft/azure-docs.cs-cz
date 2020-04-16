---
title: Příjem a reakce na oznámení trezoru klíčů pomocí Azure Event Grid
description: Přečtěte si, jak integrovat trezor klíčů s Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 300abc8ca6798866295c865e68bf5ec24a00cf5f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423252"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Příjem a reakce na oznámení trezoru klíčů pomocí Azure Event Grid (preview)

Integrace azure key vaultu s Azure Event Grid (aktuálně ve verzi Preview) umožňuje oznámení uživatele při změně stavu tajného klíče uloženého v trezoru klíčů. Přehled této funkce naleznete v [tématu Monitoring Key Vault with Event Grid](event-grid-overview.md).

Tato příručka popisuje, jak přijímat oznámení trezoru klíčů prostřednictvím event gridu a jak reagovat na změny stavu prostřednictvím Azure Automation.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
- Trezor klíčů ve vašem předplatném Azure. Nový trezor klíčů můžete rychle vytvořit podle kroků v [nastavení a načíst tajný klíč z azure key vault pomocí Azure CLI](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Koncepty

Event Grid je služba událostí pro cloud. Podle kroků v této příručce se přihlásíte k odběru událostí pro trezor klíčů a trasy událostí do automatizace. Pokud vyprší platnost jednoho z tajných klíčů v trezoru klíčů, bude událost mřížka upozorněna na změnu stavu a vytvoří http post do koncového bodu. Webový hák pak spustí spuštění automatizace skriptu prostředí PowerShell.

![Vývojový diagram HTTP POST](../media/image1.png)

## <a name="create-an-automation-account"></a>Vytvoření účtu Automation

Vytvořte si účet Automation prostřednictvím [portálu Azure](https://portal.azure.com):

1.  Přejděte na portal.azure.com a přihlaste se k předplatnému.

1.  Do vyhledávacího pole zadejte **Účty automatizace**.

1.  V části **Služby** v rozevíracím seznamu na panelu hledání vyberte **možnost Účty automatizace**.

1.  Vyberte **Přidat**.

    ![Podokno Účty automatizace](../media/image2.png)

1.  Do podokna **Přidat účet automatizace** zadejte požadované informace a pak vyberte **Vytvořit**.

## <a name="create-a-runbook"></a>Vytvoření runbooku

Po připravení účtu Automation vytvořte runbook.

![Vytvoření uzlého i.](../media/image3.png)

1.  Vyberte účet automatizace, který jste právě vytvořili.

1.  V části **Automatizace procesů**vyberte **Runbook** .

1.  Vyberte **Vytvořit runbook**.

1.  Pojmenujte runbook a vyberte **PowerShell** jako typ runbooku.

1.  Vyberte runbook, který jste vytvořili, a pak vyberte tlačítko **Upravit.**

1.  Zadejte následující kód (pro účely testování) a vyberte tlačítko **Publikovat.** Tato akce vrátí výsledek požadavku POST přijatého.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Publikování uj.](../media/image4.png)

## <a name="create-a-webhook"></a>Vytvoření webového háčku

Vytvořte webhooku pro aktivaci nově vytvořené sady Runbook.

1.  V části **Zdroje** v právě publikované sady Runbook vyberte **Webhooks.**

1.  Vyberte **Přidat webhook**.

    ![Tlačítko Přidat webhook](../media/image5.png)

1.  Vyberte **Vytvořit nový webhook**.

1. Pojmenujte webhook, nastavte datum vypršení platnosti a zkopírujte adresu URL.

    > [!IMPORTANT] 
    > Adresu URL nelze zobrazit po jeho vytvoření. Ujistěte se, že jste kopii uložili na bezpečném místě, kde k ní budete mít přístup po zbytek této příručky.

1. Vyberte **Parametry a spusťte nastavení** a pak vyberte **OK**. Nezadávejte žádné parametry. Tím **povolíte tlačítko Vytvořit.**

1. Vyberte **OK** a pak vyberte **Vytvořit**.

    ![Vytvořit nové uživatelské tlačítko Webhooku](../media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

Vytvořte předplatné Event Grid prostřednictvím [portálu Azure](https://portal.azure.com).

1.  Přejděte do trezoru klíčů a vyberte kartu **Události.** Pokud ji nevidíte, ujistěte se, že používáte [verzi náhledu portálu](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![Karta Události na webu Azure Portal](../media/image7.png)

1.  Vyberte tlačítko **Odběr událostí.**

1.  Vytvořte popisný název předplatného.

1.  Zvolte **schéma mřížky událostí**.

1.  **Zdroj tématu** by měl být trezor klíčů, který chcete sledovat pro změny stavu.

1.  V **případě filtru na typy událostí**ponechte všechny vybrané možnosti ( 9**vybraných**).

1.  Jako **Typ koncového bodu** vyberte **Webhook**.

1.  Zvolte **Vybrat koncový bod**. V novém podokně kontextu vložte adresu URL webhooku z pole [Vytvořit webhooku](#create-a-webhook) do pole **Koncový odběratel.**

1.  V podokně kontextu vyberte **Potvrdit výběr.**

1.  Vyberte **Vytvořit**.

    ![Vytvořit odběr událostí](../media/image8.png)

## <a name="test-and-verify"></a>Testování a ověření

Ověřte, zda je vaše předplatné služby Event Grid správně nakonfigurováno. Tento test předpokládá, že jste se přihlásili k odběru oznámení "Secret New Version Created" v [předplatném Vytvořit mřížku událostí](#create-an-event-grid-subscription)a že máte potřebná oprávnění k vytvoření nové verze tajného klíče v trezoru klíčů.

![Testovací konfigurace předplatného Event Grid](../media/image9.png)

![Podokno Vytvořit tajné klíče](../media/image10.png)

1.  Přejděte do trezoru klíčů na webu Azure Portal.

1.  Vytvořte nový tajný klíč. Pro účely testování nastavte vypršení platnosti na následující den.

1.  Na kartě **Události** v trezoru klíčů vyberte předplatné Služby Event Grid, které jste vytvořili.

1.  V části **Metriky**zkontrolujte, zda byla událost zachycena. Očekává se dvě události: SecretNewVersion a SecretNearExpiry. Tyto události ověřují, že služba Event Grid úspěšně zachytila změnu stavu tajného klíče v trezoru klíčů.

    ![Podokno Metriky: kontrola zachycených událostí](../media/image11.png)

1.  Přejděte na svůj účet Automation.

1.  Vyberte kartu **Runbook** a pak vyberte runbook, který jste vytvořili.

1.  Vyberte kartu **Webhooks** a potvrďte, že časové razítko "poslední spuštění" je do 60 sekund od vytvoření nového tajného klíče. Tento výsledek potvrzuje, že Event Grid udělal POST na webhooku s podrobnostmi o události změny stavu v trezoru klíčů a že webhook byl spuštěn.

    ![Karta Webhooks, poslední spouštěné časové razítko](../media/image12.png)

1. Vraťte se do runbooku a vyberte kartu **Přehled.**

1. Podívejte se na seznam **Posledních pracovních míst.** Měli byste vidět, že byla vytvořena úloha a že stav je dokončen. Tím se potvrdí, že webhook spustil runbook, aby začal spouštět svůj skript.

    ![Seznam nejnovějších úloh webhooku](../media/image13.png)

1. Vyberte poslední úlohu a podívejte se na požadavek POST, který byl odeslán z Event Grid do webhooku. Zkontrolujte JSON a ujistěte se, že parametry pro trezor klíčů a typ události jsou správné. Pokud parametr "typ události" v objektu JSON odpovídá události, ke které došlo v trezoru klíčů (v tomto příkladu Microsoft.KeyVault.SecretNearExpiry), test byl úspěšný.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="you-cant-create-an-event-subscription"></a>Odběr událostí nelze vytvořit.

Znovu zaregistrujte Event Grid a poskytovatele trezoru klíčů u poskytovatelů prostředků předplatného Azure. Viz [Poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Další kroky

Blahopřejeme! Pokud jste správně postupovali podle všech těchto kroků, jste nyní připraveni programově reagovat na změny stavu tajných kódů uložených v trezoru klíčů.

Pokud jste používali systém založený na dotazování k hledání změn stavu tajných kódů v trezorech klíčů, můžete nyní začít používat tuto funkci oznámení. Testovací skript v aplikaci Runbook můžete také nahradit kódem, který programově obnoví vaše tajné klíče, když brzy vyprší jejich platnost.

Další informace:


- Přehled: [Monitorování trezoru klíčů pomocí Azure Event Grid (preview)](event-grid-overview.md)
- Postup: [Příjem e-mailů při změně tajného klíče trezoru](event-grid-logicapps.md)
- [Schéma událostí Azure Event Grid pro Azure Key Vault (preview)](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault – přehled](overview.md))
- [Přehled Azure Event Gridu](../../event-grid/overview.md)
- [Azure Automation – přehled](../../automation/index.yml)
