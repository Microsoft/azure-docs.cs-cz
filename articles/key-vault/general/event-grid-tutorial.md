---
title: Příjem a reakce na oznámení o trezoru klíčů pomocí Azure Event Grid
description: Naučte se integrovat Key Vault s Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: d7bb697879f40b45c886cd90bbb1e34906d35f66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187370"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Příjem a reakce na oznámení o trezoru klíčů pomocí Azure Event Grid

Azure Key Vault Integration with Azure Event Grid povolí uživateli oznámení, když se změní stav tajného kódu uloženého v trezoru klíčů. Přehled této funkce naleznete v tématu [Monitoring Key Vault with Event Grid](event-grid-overview.md).

Tato příručka popisuje, jak dostávat oznámení o Key Vault prostřednictvím Event Grid a jak reagovat na změny stavu prostřednictvím Azure Automation.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Trezor klíčů ve vašem předplatném Azure. Nový trezor klíčů můžete rychle vytvořit podle kroků v části [nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Koncepty

Event Grid je služba Eventing Service pro Cloud. Podle kroků v tomto průvodci se můžete přihlásit k odběru událostí pro Key Vault a směrování událostí do automatizace. Když se brzo vyprší platnost jednoho z tajných kódů v trezoru klíčů, Event Grid se zobrazí oznámení o změně stavu a převede HTTP POST na koncový bod. Webhook pak aktivuje spuštění automatizace skriptu PowerShellu.

![Vývojový diagram HTTP POST](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Vytvoření účtu Automation

Vytvořte účet Automation pomocí [Azure Portal](https://portal.azure.com):

1.  Přejít na portal.azure.com a přihlaste se ke svému předplatnému.

1.  Do vyhledávacího pole zadejte **účty Automation**.

1.  V části **služby** v rozevíracím seznamu na panelu hledání vyberte **účty Automation**.

1.  Vyberte **Přidat**.

    ![Podokno účty služby Automation](../media/event-grid-tutorial-2.png)

1.  V podokně **Přidat účet Automation** zadejte požadované informace a pak vyberte **vytvořit**.

## <a name="create-a-runbook"></a>Vytvoření runbooku

Až bude váš účet služby Automation připravený, vytvořte sadu Runbook.

![Vytvoření uživatelského rozhraní Runbooku](../media/event-grid-tutorial-3.png)

1.  Vyberte účet Automation, který jste právě vytvořili.

1.  V části **Automatizace procesu** vyberte **Runbooky** .

1.  Vyberte **vytvořit Runbook**.

1.  Pojmenujte Runbook a jako typ Runbooku vyberte **PowerShell** .

1.  Vyberte Runbook, který jste vytvořili, a pak vyberte tlačítko **Upravit** .

1.  Zadejte následující kód (pro účely testování) a klikněte na tlačítko **publikovat** . Tato akce vrátí výsledek přijaté žádosti POST.

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

![Publikování uživatelského rozhraní Runbooku](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Vytvoření Webhooku

Vytvořte Webhook, který aktivuje nově vytvořenou sadu Runbook.

1.  V části **Resources (prostředky** ) sady Runbook, kterou jste právě publikovali, vyberte **Webhooky** .

1.  Vyberte **Přidat Webhook**.

    ![Tlačítko Přidat Webhook](../media/event-grid-tutorial-5.png)

1.  Vyberte **vytvořit nový Webhook**.

1. Pojmenujte Webhook, nastavte datum vypršení platnosti a zkopírujte adresu URL.

    > [!IMPORTANT] 
    > Po vytvoření se adresa URL nedá zobrazit. Nezapomeňte uložit kopii v zabezpečeném umístění, kde k ní máte přístup za zbytek tohoto průvodce.

1. Vyberte **parametry a nastavení spuštění** a pak vyberte **OK**. Nezadávejte žádné parametry. Tím se povolí tlačítko **vytvořit** .

1. Vyberte **OK** a pak vyberte **vytvořit**.

    ![Vytvořit nové uživatelské rozhraní Webhooku](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

Vytvořte Event Grid předplatné prostřednictvím [Azure Portal](https://portal.azure.com).

1.  Přejít do trezoru klíčů a vybrat kartu **události** .

    ![Karta události v Azure Portal](../media/event-grid-tutorial-7.png)

1.  Vyberte tlačítko **odběr události** .

1.  Vytvořte popisný název předplatného.

1.  Vyberte **Event Grid schéma**.

1.  **Prostředkem tématu** by měl být Trezor klíčů, který chcete monitorovat pro změny stavu.

1.  Pro možnost **Filtr na typ události** ponechte vybrané všechny možnosti (počet **vybraných: 9**).

1.  Jako **Typ koncového bodu** vyberte **Webhook**.

1.  Zvolte **Vybrat koncový bod**. V podokně nový kontext vložte adresu URL Webhooku z kroku [Vytvoření Webhooku](#create-a-webhook) do pole **koncový bod odběratele** .

1.  V kontextovém podokně vyberte **potvrdit výběr** .

1.  Vyberte **Vytvořit**.

    ![Vytvořit odběr události](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testování a ověření

Ověřte, že je vaše předplatné Event Grid správně nakonfigurované. Tento test předpokládá, že jste se přihlásili k odběru oznámení "nově vytvořená tajná verze" v [předplatném vytvoření Event Grid](#create-an-event-grid-subscription)a že máte potřebná oprávnění k vytvoření nové verze tajného klíče v trezoru klíčů.

![Test konfigurace předplatného Event Grid](../media/event-grid-tutorial-9.png)

![Podokno vytvořit – tajný kód](../media/event-grid-tutorial-10.png)

1.  Přejít na svůj Trezor klíčů na Azure Portal.

1.  Vytvoří nový tajný kód. Pro účely testování nastavte datum vypršení platnosti na další den.

1.  Na kartě **události** v trezoru klíčů vyberte Event Grid předplatné, které jste vytvořili.

1.  V části **metriky** ověřte, zda byla událost zachycena. Jsou očekávány dvě události: SecretNewVersion a SecretNearExpiry. Tyto události ověřují, že Event Grid úspěšně zachytil změnu stavu tajného kódu v trezoru klíčů.

    ![Podokno metriky: kontrolovat zaznamenané události](../media/event-grid-tutorial-11.png)

1.  Přejít na svůj účet Automation.

1.  Vyberte kartu **Runbooky** a potom vyberte Runbook, který jste vytvořili.

1.  Vyberte kartu **Webhooky** a potvrďte, že časové razítko "naposledy aktivované" je během 60 sekund od okamžiku vytvoření nového tajného kódu. Tento výsledek potvrzuje, že Event Grid provedl příspěvek na Webhook s podrobnostmi události změny stavu v trezoru klíčů a při aktivovaném Webhooku.

    ![Karta Webhooky, časové razítko posledního aktivovaného běhu](../media/event-grid-tutorial-12.png)

1. Vraťte se do Runbooku a vyberte kartu **Přehled** .

1. Podívejte se na seznam **posledních úloh** . Měla by se zobrazit, že se vytvořila úloha a že stav je dokončeno. Tím se potvrdí, že Webhook aktivoval Runbook, aby zahájil provádění skriptu.

    ![Seznam posledních úloh Webhooku](../media/event-grid-tutorial-13.png)

1. Vyberte poslední úlohu a podívejte se na požadavek POST, který byl odeslán z Event Grid Webhooku. Zkontrolujte kód JSON a ujistěte se, že parametry trezoru klíčů a typu události jsou správné. Pokud parametr "event type" v objektu JSON odpovídá události, ke které došlo v trezoru klíčů (v tomto příkladu je to Microsoft. klíčů trezor. SecretNearExpiry), test byl úspěšný.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="you-cant-create-an-event-subscription"></a>Nemůžete vytvořit odběr událostí.

Znovu zaregistrujte Event Grid a poskytovatele trezoru klíčů ve svých poskytovatelích prostředků předplatného Azure. Podívejte [se na téma poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Další kroky

Gratulujeme! Pokud jste správně postupovali podle všech těchto kroků, jste teď připraveni programově reagovat na změny stavu tajných kódů uložených ve vašem trezoru klíčů.

Pokud jste k hledání změn stavu tajných kódů v trezorech klíčů používali systém založený na cyklické dotazování, můžete nyní začít používat tuto funkci oznámení. Můžete také nahradit testovací skript v Runbooku kódem pro programové obnovení tajných kódů, když brzy vyprší platnost.

Další informace:


- Přehled: [monitorování Key Vault s](event-grid-overview.md) využitím Azure Event Grid
- Postupy: [příjem e-mailu, když se změní tajný kód trezoru klíčů](event-grid-logicapps.md)
- [Azure Event Grid schéma událostí pro Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Přehled Azure Key Vault](overview.md)
- [Přehled Azure Event Gridu](../../event-grid/overview.md)
- [Přehled Azure Automation](../../automation/index.yml)
