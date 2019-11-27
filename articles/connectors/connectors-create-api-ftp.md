---
title: Připojení k serveru FTP – Azure Logic Apps
description: Vytváření, sledování a Správa souborů na serveru FTP pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ac6ae1a3b00a4e7568bd7967105f202fbf2e4f9b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547493"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Vytváření, sledování a Správa souborů FTP pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru FTP můžete vytvářet automatizované úlohy a pracovní postupy, které umožňují vytvářet, monitorovat, odesílat a přijímat soubory prostřednictvím účtu na serveru FTP spolu s dalšími akcemi, například:

* Monitorování při přidání nebo změně souborů
* Získat, vytvořit, kopírovat, aktualizovat, zobrazit a odstranit soubory.
* Získá obsah souboru a metadata.
* Extrahuje archivy do složek.

Můžete použít triggery, které získávají odpovědi z vašeho serveru FTP a zpřístupnit výstup ostatním akcím. Pro správu souborů na serveru FTP můžete ve svých aplikacích logiky použít akce spuštění. Můžete také použít jiné akce výstup z akcí FTP. Pokud například pravidelně získáváte soubory ze serveru FTP, můžete odesílat e-maily o těchto souborech a jejich obsahu pomocí konektoru Office 365 Outlook Connector nebo konektoru Outlook.com. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Omezení

* Konektor FTP podporuje pouze explicitní FTP přes protokol SSL (FTPS) a není kompatibilní s implicitním FTPS.

* Ve výchozím nastavení můžou akce FTP číst nebo zapisovat soubory o *velikosti 50 MB nebo menší*. Aby bylo možné zpracovat soubory větší než 50 MB, akce FTP podporuje vytváření [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). Akce **získat obsah souboru** implicitně používá bloky dat.

* Aktivační události FTP nepodporují vytváření bloků dat. Při vyžádání obsahu souboru triggery vyberou pouze soubory, které jsou 50 MB nebo menší. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Použijte Trigger FTP, který vrátí vlastnosti souboru, například **při přidání nebo úpravě souboru (pouze vlastnosti)** .

  * Postupujte podle triggeru s akcí **získat obsah souboru** FTP, který načte kompletní soubor a implicitně použije bloky dat.

## <a name="how-ftp-triggers-work"></a>Jak fungují triggery FTP

Aktivační procedury FTP fungují při cyklickém dotazování systému souborů FTP a vyhledávání všech souborů, které byly od posledního cyklického dotazování změněny. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je nutné zakázat tuto funkci, aby mohla Trigger fungovat. Tady je několik běžných nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| WinSCP | Přejít na **možnosti** > **předvolby** > **přenos** > **Upravit** > **zachovat časové razítko** > **Zakázat** |
| FileZilly | Přejít na **přenos** > **zachovejte časová razítka přenesených souborů** > **Zakázat** |
|||

Pokud aktivační událost najde nový soubor, aktivační událost zkontroluje, jestli je nový soubor hotový, a ne částečně napsaný. Soubor může mít například probíhající změny, když aktivační událost kontroluje souborový server. Aby nedošlo k vrácení částečně napsaného souboru, aktivační událost zapisuje časové razítko pro soubor, který má poslední změny, ale tento soubor okamžitě nevrátí. Aktivační událost vrátí soubor pouze při opakovaném dotazování serveru. V některých případech může toto chování způsobit zpoždění až dvojnásobku intervalu dotazování triggeru.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa serveru hostitele FTP a přihlašovací údaje účtu

  Konektor FTP vyžaduje, aby server FTP byl přístupný z Internetu a nastavený pro provoz v *pasivním* režimu. Vaše přihlašovací údaje umožňují, aby aplikace logiky vytvořila připojení a měl přístup k vašemu účtu FTP.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu FTP. Pokud chcete začít s triggerem FTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci FTP, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-ftp"></a>Připojení k FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole "FTP" jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete.

   -nebo-

   Pro existující aplikace logiky klikněte v posledním kroku na místo, kam chcete přidat akci, zvolte **Nový krok**a potom vyberte **přidat akci**. Do vyhledávacího pole zadejte "FTP" jako filtr. V seznamu akce vyberte akci, kterou chcete.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Zvolte znaménko plus ( **+** ), které se zobrazí, a vyberte **přidat akci**.

1. Zadejte potřebné údaje pro připojení a pak zvolte **vytvořit**.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Aktivační událost serveru FTP: při přidání nebo úpravě souboru

Tato aktivační událost spustí pracovní postup aplikace logiky, když aktivační událost zjistí, kdy je soubor na serveru FTP přidán nebo změněn. Můžete například přidat podmínku, která zkontroluje obsah souboru a rozhodne, zda tento obsah získat na základě toho, zda tento obsah splňuje zadanou podmínku. Nakonec můžete přidat akci, která získá obsah souboru, a tento obsah umístit do složky na serveru SFTP.

**Podnikový příklad**: tuto aktivační událost můžete použít k monitorování složky FTP pro nové soubory, které popisují objednávky zákazníků. Pak můžete použít akci FTP, jako je například **získání obsahu souboru**, takže můžete získat obsah objednávky pro další zpracování a uložení tohoto pořadí v databázi objednávek.

Tady je příklad, který ukazuje tuto aktivační událost: **když se přidá nebo upraví soubor** .

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole "FTP" jako filtr. V seznamu triggery vyberte tuto aktivační událost: **když se přidá nebo upraví položka-FTP** .

   ![Vyhledání a výběr triggeru FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Zadejte potřebné údaje pro připojení a pak zvolte **vytvořit**.

   Ve výchozím nastavení tento konektor přenáší soubory v textovém formátu. Chcete-li přenášet soubory v binárním formátu, například, kde a kdy je použito kódování, vyberte **binární přenos**.

   ![Vytvořit připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Vedle pole **Složka** vyberte ikonu složky, aby se zobrazil seznam. Chcete-li najít složku, kterou chcete monitorovat pro nové nebo upravované soubory, vyberte šipku pravého úhlu ( **>** ), přejděte do této složky a potom vyberte složku.

   ![Najít a vybrat složku, která se má monitorovat](./media/connectors-create-api-ftp/select-folder.png)  

   Vybraná složka se zobrazí v poli **Složka** .

   ![Vybraná složka](./media/connectors-create-api-ftp/selected-folder.png)  

Teď, když má aplikace logiky Trigger, přidejte akce, které chcete spustit, když aplikace logiky najde nový nebo upravovaný soubor. V tomto příkladu můžete přidat akci FTP, která získá nový nebo aktualizovaný obsah.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Akce FTP: získání obsahu

Tato akce načte obsah ze souboru na serveru FTP při přidání nebo aktualizaci tohoto souboru. Například můžete přidat Trigger z předchozího příkladu a akci, která po přidání nebo úpravě souboru získá obsah souboru.

Tady je příklad, který ukazuje tuto akci: **získat obsah**

1. V aktivační události nebo jakékoli jiné akci vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte "FTP" jako filtr. V seznamu akce vyberte tuto akci: **získat obsah souboru-FTP**

   ![Vybrat akci FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Pokud už máte připojení k serveru FTP a účtu, pokračujte na další krok. V opačném případě zadejte potřebné údaje pro toto připojení a pak zvolte **vytvořit**.

   ![Vytvořit připojení k serveru FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Po otevření akce **získat obsah souboru** klikněte do pole **soubor** , aby se zobrazil seznam dynamického obsahu. Nyní můžete vybrat vlastnosti pro výstupy z předchozích kroků. V seznamu dynamický obsah vyberte vlastnost **obsah souboru** , která obsahuje obsah pro přidaný nebo aktualizovaný soubor.  

   ![Najít a vybrat soubor](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   Vlastnost **obsah souboru** se nyní zobrazí v poli **soubor** .

   ![Vybraná vlastnost obsah souboru](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Uložte svou aplikaci logiky. Pokud chcete otestovat pracovní postup, přidejte soubor do složky FTP, kterou vaše aplikace logiky teď monitoruje.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/ftpconnector/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)