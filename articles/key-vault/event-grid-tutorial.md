---
title: Příjem a reakce na oznámení o trezoru klíčů pomocí Azure Event Grid
description: Naučte se integrovat Key Vault s Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464105"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Postupy: příjem a reakce na oznámení trezoru klíčů pomocí Azure Event Grid (Preview)

Key Vault integrace s Azure Event Grid, která je v současnosti ve verzi Preview, umožňuje uživatelům upozornit na změnu stavu tajného klíče uloženého v trezoru klíčů. Přehled této funkce naleznete v tématu [Monitoring Key Vault with Azure Event Grid](event-grid-overview.md).

V této příručce se dozvíte, jak dostávat oznámení Key Vault prostřednictvím Azure Event Grid a jak reagovat na změny stavu pomocí Azure Automation.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Trezor klíčů ve vašem předplatném Azure. Nový trezor klíčů můžete rychle vytvořit podle kroků v části [nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](quick-create-cli.md) .

## <a name="concepts"></a>Koncepty

Azure Event Grid je služba zpracování událostí pro cloud. V tomto průvodci se přihlásíte k odběru událostí pro Trezor klíčů a události směrování pro Azure Automation. Když se brzo vyprší platnost jednoho z tajných kódů v trezoru klíčů, Event Grid se zobrazí oznámení o změně stavu a převede HTTP POST na koncový bod. Webhook pak aktivuje Azure Automation provádění skriptu PowerShellu.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

Pomocí [Azure Portal](https://portal.azure.com)vytvořte účet Azure Automation.

1.  Přejít na portal.azure.com a přihlaste se ke svému předplatnému.

1.  Do vyhledávacího pole zadejte text "účty služby Automation".

1.  V části "služby" v rozevíracím seznamu na panelu hledání vyberte účty Automation.

1.  Klikněte na Přidat.

    ![](media/image2.png)

1.  Požadované informace vyplňte v okně Přidat účet Automation a vyberte vytvořit.

## <a name="create-a-runbook"></a>Vytvoření Runbooku

Až bude váš účet Azure Automation připravený, vytvořte sadu Runbook.

![](media/image3.png)

1.  Vyberte účet Automation, který jste právě vytvořili.

1.  V části automatizace procesu vyberte Runbooky.

1.  Klikněte na tlačítko vytvořit sadu Runbook.

1.  Pojmenujte Runbook a jako typ Runbooku vyberte PowerShell.

1.  Klikněte na Runbook, který jste vytvořili, a vyberte tlačítko Upravit.

1.  Zadejte následující kód (pro účely testování) a klikněte na tlačítko publikovat. Výsledkem bude výstup přijatého požadavku POST.

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

![](media/image4.png)

## <a name="create-a-webhook"></a>Vytvoření Webhooku

Nyní vytvořte Webhook, který spustí nově vytvořenou sadu Runbook.

1.  V části Resources sady Runbook, kterou jste právě publikovali, vyberte Webhooky.

1.  Klikněte na Přidat Webhook.

    ![](media/image5.png)

1.  Vyberte vytvořit nový Webhook.

1. Pojmenujte Webhook, nastavte datum vypršení platnosti a **zkopírujte adresu URL**.

    > [!IMPORTANT] 
    > Po vytvoření se adresa URL nedá zobrazit. Ujistěte se, že jste uložili kopii na bezpečném místě, kde k ní máte přístup za zbytek tohoto průvodce.

1. Klikněte na parametry a nastavení spuštění a vyberte OK. Nezadávejte žádné parametry. Tím se povolí tlačítko vytvořit.

1. Vyberte OK a vyberte vytvořit.

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

Vytvořte Event Grid předplatné prostřednictvím [Azure Portal](https://portal.azure.com).

1.  Pomocí následujícího odkazu otevřete Azure Portal: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  Přejít do trezoru klíčů a vybrat kartu události. Pokud se nezobrazí karta události, ujistěte se, že používáte [verzi Preview portálu](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![](media/image7.png)

1.  Klikněte na tlačítko + odběr události.

1.  Vytvořte popisný název předplatného.

1.  Vyberte Event Grid schéma.

1.  "Prostředek tématu" by měl být Trezor klíčů, který chcete monitorovat pro změny stavu.

1.  U možnosti filtrovat na typ události ponechte zaškrtnuté políčko (9 vybrané).

1.  U možnosti "typ koncového bodu" vyberte Webhook.

1.  Vyberte možnost vybrat koncový bod. V podokně nový kontext vložte adresu URL Webhooku z kroku [Vytvoření Webhooku](#create-a-webhook) do pole koncový bod odběratele.

1.  V kontextovém podokně vyberte potvrdit výběr.

1.  Vyberte vytvořit.

    ![](media/image8.png)

## <a name="test-and-verify"></a>Testování a ověření

Ověřte, že je nastavená vlastnost předplatného Event Grid.  Tento test předpokládá, že jste se přihlásili k odběru oznámení "nově vytvořená tajná verze" v rámci [vytvoření předplatného Event Grid](#create-an-event-grid-subscription)a že máte potřebná oprávnění k vytvoření nové verze tajného klíče v trezoru klíčů.

![](media/image9.png)

![](media/image10.png)

1.  Přejít na váš Trezor klíčů na Azure Portal

1.  Vytvoří nový tajný kód. Pro účely testování nastavte datum vypršení platnosti na další den.

1.  V trezoru klíčů přejděte na kartu události.

1.  Vyberte odběr Event gridu, který jste vytvořili.

1.  V části metriky se podívejte, jestli se událost zachytila. Jsou očekávány dvě události: SecretNewVersion a SecretNearExpiry. Tím se ověří, že Event Grid úspěšně zachytil změnu stavu tajného kódu v trezoru klíčů.

    ![](media/image11.png)

1.  Přejít na účet Azure Automation.

1.  Vyberte kartu sady Runbook a vyberte sadu Runbook, kterou jste vytvořili.

1.  Vyberte kartu Webhooky a potvrďte, že časové razítko "naposledy aktivované" je během 60 sekund od okamžiku vytvoření nového tajného kódu.  Tím se potvrdí, že Event Grid provedl příspěvek na Webhook s podrobnostmi události změny stavu v trezoru klíčů a Webhook se aktivoval.

    ![](media/image12.png)

1. Vraťte se do Runbooku a vyberte kartu Přehled.

1. Podívejte se na seznam posledních úloh. Měla by se zobrazit, že se vytvořila úloha a že stav je dokončeno.  Tím se potvrdí, že Webhook aktivoval Runbook, aby zahájil provádění skriptu.

    ![](media/image13.png)

1. Vyberte poslední úlohu a Prohlédněte si požadavek POST, který byl odeslán z Event gridu do Webhooku. Zkontrolujte kód JSON a ujistěte se, že parametry trezoru klíčů a typu události jsou správné. Pokud parametr "typ události" v objektu JSON odpovídá události, ke které došlo v trezoru klíčů (v tomto příkladu Microsoft. klíčů. SecretNearExpiry), test byl úspěšný.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unable-to-create-event-subscription"></a>Nepovedlo se vytvořit odběr událostí.

Přeregistrujte poskytovatele Event Grid a Key Vault ve svých poskytovatelích prostředků předplatného Azure. Podívejte [se na téma poskytovatelé a typy prostředků Azure](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Další kroky

Blahopřejeme! Pokud jste postupovali podle všech kroků uvedených výše, jste teď připraveni programově reagovat na změny stavu tajných kódů uložených v trezoru klíčů.

Pokud jste používali systém založený na dotazech k vyhledání změny stavu tajných kódů v trezorech klíčů, proveďte migraci na používání této funkce oznámení. Můžete také nahradit testovací skript v Runbooku kódem pro programové obnovení tajných kódů, když brzy vyprší platnost.

Další informace:

- [Přehled Azure Key Vault](key-vault-overview.md)
- [Přehled Azure Event Grid](../event-grid/overview.md)
- [Monitorování Key Vault s využitím Azure Event Grid (Preview)](event-grid-overview.md)
- [Azure Event Grid schéma událostí pro Azure Key Vault (Preview)](../event-grid/event-schema-key-vault.md)
- [Přehled Azure Automation](../automation/index.yml)
